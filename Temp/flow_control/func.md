# 函數 func

函數：又叫做 function、方法

用途是執行一段程式碼，可以有參數，有回傳，也可以都沒有。

#### 函數的宣告

完整的格式如下：
```swift
func `函數名稱`(`參數外部名稱` `參數內部名稱`: `參數型別`) -> `返回型別（可不寫）`{

}
```

示範一些變化：
```swift
// 1. 無參數，也無回傳
func `函數名稱`() {

}

// 2. 有參數，也無回傳
// 內外部共用一個參數名稱，可以只寫一個參數名稱。
func `函數名稱`(`參數名稱1`: `參數型別1`, `參數名稱2`: `參數型別2`) {
    
}

// 3. 無參數，有回傳
func `函數名稱`() -> `回傳型別` {
    return `回傳值`
}

// 2 跟 3 可以結合成為：有參數也有回傳的 func
```

實際宣告的樣子：

```swift
// 無參數，也無回傳
func doSomething() {
    print("doSomething")
}

// 有參數，也無回傳
func printSum(by x: Int, and y: Int) {
    print(x + y)
}

// 有參數，也無回傳
func printSum(x: Int, y: Int) {
    print(x + y)
}

// 有參數，有回傳
func getSum(x: Int, y: Int) -> Int {
    return x + y
}
```

#### 函數的使用

簡單來說，就是呼叫，並且執行，我們剛剛宣告且寫好的程式碼片段。

使用的完整樣子會像下面這樣：

```swift
// 呼叫 `無參數，也無回傳` 的 func
func doSomething() {
    print("doSomething")
}
doSomething()


// 呼叫 `有參數，也無回傳` 的 func
// 內外部參數共用名稱
func printSum(x: Int, y: Int) {
    print(x + y)
}
printSum(x: 10, y: 5)

// 呼叫 `有參數，也無回傳` 的 func
// 內外部 `不` 共用名稱
func printSum(by x: Int, and y: Int) {
    print(x + y)
}
printSum(by: 10, and: 5)

// 呼叫 `有參數，有回傳` 的 func
func getSum(x: Int, y: Int) -> Int {
    return x + y
}
let resule = getSum(x: 10, y: 5)
```

因為正常程式碼執行是由上往下照順序執行。

但實際情況並不是這樣，因應各種情況、各種判斷，所需要執行的程式碼片段可能有所不同，這時候用 func 來包裝程式碼，是常玉的一種做法。

再來就是某些程式碼可能會重複，且不斷地執行，那每次使用就每次寫重複的程式碼，是一件非常愚蠢的行為。將需要重複執行的程式碼用 func 包裝起來，也是常用作法。

### 一些比較特別的用法

```swift
// 當我們在外部呼叫的時候，不想要有參數名稱
// 可以使用底線
func noParameterName(_ x: Int) {
    print(x)
}

```

> Coding style 建議：盡量使用動詞，作為 func name 的開頭

# 練習

1. 宣告一個 func，名字是 printPhone，功能是印出一串 "+886 0912345678"。

2. 將上面那個 func 改成，由外部傳入 "0912345678" 這個字串參數，並在 func 內部印出 "+886 0912345678"。

3. 由外部傳數參數 "0912345678"，func 返回 "+886 0912345678" 的結果，並用一個變數儲存，然後印出來看看。