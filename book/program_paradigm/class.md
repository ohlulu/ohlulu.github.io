#  7. 類別 Class

## 定義
類別通過關鍵字class表示，並在一對大括號中定義它們的具體內容：
```swift
class SomeClass {
    // ...
}
```
> 注意：
> 在定義一個新類別或者結構的時候，實際上就是定義了一個新的 Swift 型別。所以我們使用 UpperCamelCase 這種方式來命名（如 SomeClass 和SomeStructure等），以便符合標準Swift 型別的大寫命名風格（如String，Int和Bool）

以下是定義類別的例子：

struct Resolution {
    var width = 0
    var heigth = 0
}
class VideoMode {
    var resolution = Resolution()
    var interlaced = false
    var frameRate = 0.0
    var name: String?
}