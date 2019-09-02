# Optional - 可選型別

在之前的課程有說明過，所謂的變數，其實就像是一個盒子；值，就是盒子的內容物。

那既然有盒子，有內容物，就還有一種可能：盒子沒有內容物。

---

### 宣告的部分

##### 基本宣告

Optional 完整的宣告是長這樣

```swift
let maybeNil: Optional<Type>
```

> Optional 也是一種型別，型別本身包含兩個值，一個值是 none 表示 nil， 一個值是 some 表示解開之後的值。

但使用上有點冗長，所以 swift 很貼心的提供語法糖 `?`，使用方法是在`型別的後面`加上 `?` 。

值的部分，則是使用 `nil` 表示。

```swift
let intMaybeNil: Int? = nil

// 對照一般情況
let intAbsValue: Int = 100

// 當然 optinoal 是表示 `有可能` 沒有值，所以你也可以這樣宣告
let intMaybeNil2: Int? = 100
```

> Note: Optional 如果宣告為 let 並且一開始值就設定為 nil，那它將永遠是 nil ，因為 let 不可變。
>
> 所以使用到 optional 的時候通常會宣告為 var。

##### 隱式宣告（提前解析）

有一種情況是，我們確定變數會有值，但一開始我們不確定初始值是什麼，或者物件 init 的成本過大，我們不希望在一開始就給予值。
但是可以確定在使用的時候，一定會有值。那們我們在宣告的時候可以再行別的後面加上 `!`

```Swift
var number: Int!
number = 0
```
> 對初學者而言這不是一個太好的用法，除非你掌握了這個用法特性。

##### 一些 🌰

如果我們在定義型別的時候沒有指明變數是 Optional ，那麼在未來不管何時何地，這個變數永遠不可能為 nil

```swift
var number = 100
number = nil // 🚫 編譯器會出現警告，因為我們並沒有宣告 number 可能為 nil

var number: Int? = 100
number = nil // ✅ 合法的宣告與使用
```
<br > 

那什麼時候會出現 nil 呢？

```Swift
var str = "123"
var number = Int(str)
print(number)
// Optional(123)

str = "一二三"
number = Int(str)
print(number)
// nil

print(type(of: number))
// Optional<Int>
```

因為系統不知道你的 `str` 中會存放的是什麼，如果是 `一二三` 這種字串，系統是無法自動幫忙轉成 `Int` 的，所以在這種情況下，`number` 的型別會是 `Int?`

---
### 使用的部分

##### 強制解析

假設你確定一個 Optional 變數一定有值，可以在這個變數後面加上一個驚嘆號`!`，表示這個可選型別有值，稱為強制解析(forced unwrapping):
```swift
// 宣告變數 number 為 Int?
var number: Int? = 100

// 在使用的時候強制解析
print(number!) // ✅

// 將 number 設置為 nil
number = nil

// 下面這行編譯器不會報錯，但在執行期間則會出現錯誤，就是我們常在說的 閃退 或 crash
print(number!) // 🚫
```
這是一種很偷懶、且不好的做法，通常不建議初學者這麼使用，但是當你對程式越來越了解與熟悉之後，某些情況下是可以使用 `!` 來做強制解析的

##### 可選鏈式解析

```swift
var name: String? = "Diane"
print(name?.count)
// Optional(5)

name = nil
print(name?.count)
// nil
```
這樣的使用方式，就算 name 變數裡面沒有值，執行期間也不會出現錯誤，
也不會導致 APP 的閃退，使用者的體驗也會比較好。

##### 條件式解析(if-let)

當然一個變數我們可能會重複呼叫許多次，每次都加上 `？` 其實滿麻煩的，所以我們可以使用 Swift 提供的語法糖 `if let`

完整格式如下：
```swift
if let `變數名稱` = `變數名稱` {
    // ...
}
```

實際使用情況：
```swift
let number: Int? = 100
if let number = number {
    print("\(number), \(number+1), \(number+2)")
}
```

上面的這種寫法，表示當 number 有值的時後，我需要印出三個 number 的值，分別是；原始、加一，加二
如果 number 沒有值，就什麼事情也不做，因為我們把 print 這行程式碼，包在 `{ } ` 裡面了。

##### 條件式解析(guard-let-else)

那有時候出現 nil 並不是我們期望的結果，因此需要對 nil 做一些處理，並在有值得時候，執行原定的程式碼。Swift 很貼心的一樣提供了語法糖。

完整格式如下：
```swift
guard let {變數名稱} = {變數名稱} else {
    // ...
}
// ...
```

實際使用情況：
```swift
let number: Int? = nil
guard let number = number else {
    print("number == nil")
}
print("number != nil")
```

這種用法通常在當出現非預期的結果時，我們需要提示使用者：可能出現問題了。


