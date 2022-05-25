---
title: "當 Decodable 遇上 @propertyWrapper"
date: "2020-07-18"
categories: 
  - "tip"
img_path: /assets/img/post/
---

在 Swift 5.1 之後，蘋果爸爸推出了 @propertyWrapper ( [SE-0258](https://github.com/apple/swift-evolution/blob/master/proposals/0258-property-wrappers.md) )，屬性包裝器，詳細的使用及說明，這邊就不多做介紹了。剛開始的時候最常看到的應用是 Userdefaults，我自己在專案中應用的部分則是 Begin/End of date，因為有選擇日期區間的需求，所以做了一個這樣的屬性，在 init 的時候指定好是 Begin or End，get 的時候就會先整理一次日期，詳細的實作我放在 [Gist](https://gist.github.com/ohlulu/33cb78e298744d6fc4c8fae23e6fdd65) 有興趣的同學可以參考看看。

## \# 情境

當我待在 Agency 的時候經常遇到一個問題，後端的 API 沒有照 Spec 做，尤其是型別的問題，我想這應該很多人都有遇過，Spec 明明定義的是 Int，後端傳卻是 String。或是因為某些不明的原因，後端漏傳了某個欄位，但實際上這個欄位是必須存在的。甚至是後端 key 打錯了。更淒慘的是，如果是 Array 中某個值錯了，整大包就會解析失敗。

在以前，我們有幾個方式可以解決這些問問題：

1. 自己定義一個型別： 可能像是 StringOrInt 這種，並實作 Decodable。但這只能解決 String ↔ Int，這種錯誤。
2. Decode 失敗就失敗：但每次失敗我都會將 DecodingError 直接用 Alert show 在畫面上，所以只要有人測到 DecoginError 相關的問題，就可以直接螢幕截圖並傳給我，我可以比較經鬆的定位失敗的 API，並通知後端修正。（當然這種作法必須限定在開發環境）
3. 把所有的變數都宣告成 Optional：但這只能解決 KeyNotFound，而且後續的開發上需要處理一堆 Optional。
4. 自己實作 init(from decoder: Decoder) ：但我不可能、也不想為每個 response model 實做這個 func，費時又費力，很不工程師。

## \# 還是有一些問題

上面的解法在某種程度上分別解決了 DecodingError 的問題，但依然有一些問題：

1. 解決方法沒有統一的介面。（醜）
2. 某些方法實作不易。（麻煩）
3. 某些方法出錯的時候定位不容易，因為本質上是 Bug ，是需要被解決的。
4. 後端的鍋為毛是我們扛？為毛是我們要找出問題在哪，再請後端改？（😠 ）

## \# DecodeStrategy

Of course, DecodeStrategy 誕生了。

最初的想法是想用「一個 @propertyWrapper」來解決所有問題，但嘗試到一半發現真的做不到 ( 或許是我太菜了QQ )，加上[之前](https://www.ohlulu.tw/2019/08/13/ohswifterframework/)學到的教訓 「不要試圖在一行 code 裡面包山包海」。我決定針對各個狀況各自使用一個 @propertyWrapper。

讓我們一個一個來看：

1. #### 首先是預設值的部分：在某些情況之下，我希望解析失敗的時候，Model 可以有預設值。
    
    最早的想法是，在 propertyWrapper init 的時候，順便指定預設值是什麼。 `@DecodeHasDefault("Something error")` 但這樣的做法會跟 `init(from decoder: Decoder)` 衝突，所以後來改用 Provider 的方式來提供預設值。
    
    ```swift
    public protocol DecodeDefaultProvider {
        associatedtype Value: Decodable
        static var defaultValue: Value { get }
    }
    
    @propertyWrapper
    public struct DecodeHasDefault: Decodable {
    
        public var wrappedValue: Provider.Value
    
        public init(wrappedValue: Provider.Value) {
            self.wrappedValue = wrappedValue
        }
    
        public init(from decoder: Decoder) throws {
    
            let container = try decoder.singleValueContainer()
    
            do {
                wrappedValue = try container.decode(Provider.Value.self)
            } catch {
                wrappedValue = Provider.defaultValue
            }
        }
    }
    ```
    
    使用起來會像是這樣
    
    ```swift
    struct User: Decodable {
        struct NameDefault: DecodeDefaultProvider {
            static var defaultValue: String = "ohlulu"
        }
    
        @DecodeHasDefault
        var name: String
    
        struct AgeDefault: DecodeDefaultProvider {
            static var defaultValue: Int = 18
        }
    
        @DecodeHasDefault
        var age: Int
    }
    ```
    
    這部分比較單純，就是提供一個 `DecodeDefaultProvider`，讓 `DecodeHasDefault` 在 catch error 的時候使用 defaultValue
    
2. #### 再來是 DecodeArray 的部分。
    
    這邊有兩種情況：一種是解析失敗的時候，直接忽略該 Element。另一種是使用預設的 Element。
    
    1. 第一種：DecodeArrayIgnore
    
    ```swift
    @propertyWrapper
    public struct DecodeArrayIgnore: Decodable {
    
        public var wrappedValue: [Value]
    
        public init(from decoder: Decoder) throws {
    
            var container = try decoder.unkeyedContainer()
            var result = [Value]()
    
            while !container.isAtEnd {
                do {
                    let element = try container.decode(Value.self)
                    result.append(element)
                } catch {
                    _ = try container.decode(AnyDecodable.self)
                }
            }
            wrappedValue = result
        }
    }
    ```
    
    這邊需要注意，catch error 的時候還是要 decode 成功一次，不然 element 不會從 container 中移除，會陷入一個無窮迴圈，所以用一個空的 `struct AnyDecodable` 來做這件事，如果還是失敗了，就 throw 出去吧。
    
    2. 第二種：DecodeArrayHasDefault
    
    ```swift
    @propertyWrapper
    public struct DecodeArrayHasDefault: Decodable {
    
        public var wrappedValue: [Provider.Value]
    
        public init(from decoder: Decoder) throws {
    
            var container = try decoder.unkeyedContainer()
            var result = [Provider.Value]()
    
            while !container.isAtEnd {
                do {
                    let element = try container.decode(Provider.Value.self)
                    result.append(element)
                } catch {
                    _ = try container.decode(AnyDecodable.self)
                    result.append(Provider.defaultValue)
                }
            }
    
            wrappedValue = result
        }
    }
    
    ```
    
    跟上面很像，只是 Generic 的部分改成了 `DecodeDefaultProvider`，這邊會選擇拆成理個 `@propertyWrapper` 是因為 ignore 不需要 Provider ，當然 Provider 也可以提供一個類似 Optional 的 enum 來達成通用的目的，但考慮到需求是 ignore 的時候，我希望使用上可以更單純一點，不用再指定 Provider。 所以最終選擇拆成兩個。
    
3. #### 最後一種最麻煩：DecodeUniversal
    
    直接上 Code
    
    ```swift
    public typealias LosslessAndDecodable = LosslessStringConvertible & Decodable
    
    @propertyWrapper
    public struct DecodeUniversal: Decodable {
    
        public var wrappedValue: Value
    
        public init(from decoder: Decoder) throws {
    
            let container = try decoder.singleValueContainer()
    
            do {
                wrappedValue = try container.decode(Value.self)
            } catch {
    
                let temp: String
                if let strValue = try? container.decode(String.self) {
                    temp = strValue
                } else if let intValue = try? container.decode(Int.self) {
                    temp = "\(intValue)"
                } else if let doubleValue = try? container.decode(Double.self) {
                    temp = "\(doubleValue)"
                } else {
                    throw error
                }
    
                if let value = Value.init(temp) {
                    wrappedValue = value
                } else {
                    throw error
                }
            }
        }
    }
    ```
    
    這邊有一個比較少看到的 `protocol LosslessStringConvertible`，詳細的定義可以參考 [Document](https://developer.apple.com/documentation/swift/losslessstringconvertible)，簡單來說它提供了我們用字串建立 Int, Double 等類型的能力。
    
    所以我們先 `decode(Value.self)` 一次看看，失敗的話，我們從 String -> Int -> Double 依次 decode，只要 decode 成功，就把值轉成 String 存起來。如果依然 decode 失敗，勇敢的 throw 出去吧！
    
    接著利用 `LosslessStringConvertible` 提供的 `init?(_ description: String)` 來嘗試建立物件，如果還是失敗，勇敢的 throw 出去吧！
    
    到這邊就完成啦 😄
    

## \# 讓我們回頭看看

✅ 1. 解決方法沒有統一的介面。

✅ 2. 某些方法實作不易。

❌ 2. 某些方法出錯的時候定位不容易，因為本質上是 Bug ，是需要被解決的。

❌ 3. 後端的鍋為毛是我們扛？為毛是我們要找出問題在哪，再請後端改？

忙了老半天，我們只解決了一半的問題？

別緊張，還有後續。

## \# 讓錯誤出現時有一個 Handler

我們先宣告一個 `protocol DecodeErrorDelegate`，並宣告 `DecodeStrategy` 裡面有一個 `static var` 是 `DecodeErrorDelegate`。

```swift
public protocol DecodeErrorDelegate {

    func onCatch(error: Error)
}

public struct DecodeStrategy {

    public static var errorDelegate: DecodeErrorDelegate?
}

```

接著我們在 catch error 的時候，把 error 透過 `DecodeStrategy.errorDelegate?.onCatch(error:)` 傳出去。

```swift
do {
    wrappedValue = try container.decode(Provider.Value.self)
} catch {
    DecodeStrategy.errorDelegate?.onCatch(error: error)
    wrappedValue = Provider.defaultValue
}
```

完美，這樣我們就可以在使用的時候有一個統一的接口可以做事了。你可以在自己的 DecodeErrorDelegate 設置 flag ，標注現在準備 deocde 哪個 response model，並在 onCatch 的時候印出來，或者請後端直接再開一隻接收 JSON Bug 的 API，每次 onCatch 都呼叫 API 通知後端修正。想怎麼玩就怎麼玩。

完整的專案在 [Github](https://github.com/ohlulu/DecodeStrategy)。如果你覺得不錯的話，歡迎給個 Star 支持一下 😄
