# 4.2 閉包 closure

閉包是自包含的函式程式碼區塊，也稱作匿名函式，可以在程式碼中被傳遞和使用。 Swift 中的閉包與 Objective-C 中的程式碼區塊（blocks）很像

如果覺得難以理解沒有關係，這邊屬於比較進階的用法，會隨著對語法的認識，慢慢熟悉它的

首先介紹格式：
```swift
{ (`參數`) -> `回傳值` in
    // ...
}
```

閉包是沒有名字的，但是他可以當作參數傳遞，那當作參數的時候，型別會對應 closure 的格式，像下面這樣：

```swift
let addOne: (Int) -> Int = { (aInt: Int) -> Int in
    return aInt + 1
}

// 使用的時候如下
addOne(1)
```

[這邊](http://fuckingclosuresyntax.com/) <- 這個網站對 closure 的語法做了一個不錯的介紹，當你對語法漸漸熟悉之後，可以嘗試去理解它！
