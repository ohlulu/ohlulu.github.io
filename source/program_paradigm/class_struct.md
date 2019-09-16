#  7. 類別 & 結構 (Class & Struct )

## 定義
類別通過關鍵字 `class` or `Struct` 表示，並在一對大括號中定義它們的具體內容：
```swift
class SomeClass {
    // ...
}

struct SomeStruct {
    // ...
}
```
> 注意：
> 在定義一個新類別或者結構的時候，實際上就是定義了一個新的 Swift 型別。所以我們使用 UpperCamelCase 這種方式來命名（如 SomeClass 和SomeStructure等），以便符合標準Swift 型別的大寫命名風格（如String，Int和Bool）

以下是定義類別的例子：

``` swift
struct Size {
    var width = 0
    var height = 0
}

class Frame {
    var x = 0
    var y = 0
    var size = Size()
}
```

實體化的方式：
```swift
let size = Size()
let frame = Frame()
```

結構和類別都使用建構器語法來生成新的實例。建構器語法的最簡單形式是在結構或者類別的型別名稱後跟隨一個空括弧，例如 `Size()` 或 `Frame()` 。通過這種方式所創建的類別或者結構實例，屬性均會被初始化為預設值（所有的屬性也必須給預設值）。更進階的建構方式，會在後面的[建構子章節](initial.md)做詳細的介紹

## 屬性存取
通過使用點語法（dot syntax），可以存取實例中所含有的屬性。語法規則是，在物件的名子後面接著使用屬性的名子，兩者通過點號(.)連接：

```swift
print("height is \(frame.size.height) ")
// height is 0 
```

當然，也可以用點語法來為變數賦值：
```swift
frame.size.height = 10
print("height is \(frame.size.height) ")
// height is 10 
```