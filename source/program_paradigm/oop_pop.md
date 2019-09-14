#  6. 物件導向，協定導向

## 物件導向
物件導向程式設計（Object-oriented programming，縮寫 OOP）大陸叫：面向對象程序設計。

是一種以物件為概念的程式設計方式。

### 類別與物件

物件導向有兩個很重要的特點

* 類（Class）：定義了一個物件的抽象特點。類的定義包含了資料的形式（變數）以及對資料的操作（func）。
* 物件（Instance）：是類的實體。


```swift

class `類別的名稱` {

    let `公有變數名稱`: `變數型別`

    private let `私有變數名稱`: `變數型別`

    func `公有方法名稱`() { }

    private func `私有方法名稱`() { }
}
```

舉個例子：
今天我們想要建構一隻狗，以物件導向的概念，會宣告一個 class ，定義狗的特徵，以及行為。

```swift
class Dog {
    var name: String = "QQ"
    var year: Int = 8
    
    func run() {
        print("running...")
    }

    func stop() {
        print("stop")
    }
}
```

宣告完狗的類別，接著就是實體化：
```swift
let dog = Dog()
```

接著我們就可以對狗(dog) 這個物件下指令。
```swift
dog.run()
// print: running...
```

## 協定導向

協議導向程式設計（Protocol Oriented Programming，縮寫：POP）

POP 是程式設計方法，概念在於協定，一種規範、一種約定。
通俗一點的解釋是：Ａ定義協議，Ｂ遵守協議，Ｂ即可享有遵守協議所擁有的服務。

就像我們常看到的進口協議，當商家以及商品遵守了進口協議，就可以將商品進口至台灣販售，可以享受到法律的保護(?)

以程式中的例子來說：
我們定義一個 protocol Equatable。這個協定中規範了一個方法，方法的功能是比較兩個物件是否相等。

那麼只要我們的 class Dog ，遵守了這個協議，那麼相同的類別 Dog ，但是不同的 dog 物件 ，就可以享有比較這個方法。


# 練習
1. 宣告一個人的類別，並實體化。要擁有名字、年齡、性別等資料，以及吃飯、睡覺等行為。