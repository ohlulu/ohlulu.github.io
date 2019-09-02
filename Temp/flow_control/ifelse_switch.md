# 條件式 if-else / switch

#### if else

簡單來說就是判斷 是非。

完整格式如下：
```swift
// 1.
if {判斷式} {

}

// 2.
if {判斷式} {

} else {

}

// 3.
if {判斷式} {

} else if {判斷式} {

} 
```

實際使用看起來會像這樣：

```swift
let age = 18

if age < 18 {
    print("未滿 18")
} else if age == 18 {
    print("剛好 18")
} else {
    print("大於 18")
}
```
> if 做開頭，後面可以加上很多，或者不加 else if，最後再依照需求來決定是否使用 else 做結尾

---

#### Switch

如果判斷的結果，數量較少的話，使用上的 if else 沒有任何問題，但是當判斷的數量一多，程式碼就會變得非常的雜亂。

所以還有另外一種判斷的方式 `switch case`。

格式如下：
``` swift
switch 值 {
case 比對情況1:
    // 當比對情況1，為 true 的時候會執行這段
case 比對情況2, 比對情況3: 
    // 多個情況可以用逗號 , 隔開
    // 當比對情況1 or 3，為 true 的時候會執行這段
default:
    // 以上情況比對都不成功時，執行的程式
}
```

來點 🌰 吧：
```swift
// 1.
let score = 87
switch score {
case 0, 1...10:
    print("打屁股！")
case 11...60: 
    print("不及格")
case 61...80:
    print("馬馬呼呼")
case 81...99:
    print("婆費特")
case 100:
    print("Ｅ級棒")
default:
    print("作弊！")
}
```

case 後面可以擺放：單一情況，也可以放區間，也可以用逗號同時包含多總情況。是實際使用情況而定。

# 練習

1. `let number = 0`

    用 if else 判斷是否為偶數
    > ps: 改變 nummber 的值，驗證看看你的 if else 判斷式，是否正確

2. `let year = 2019`

    用 if else 判斷是否為閏年
    * 年分除以 4 可整除，為潤年。
    * 年分除以 4 可整除，但除以100不可整除，為閏年
    > ps: 改變 year 的值，驗證看看你的 if else 判斷式，是否正確

3. `let constellation = "射手座"`

    用 swift 判斷 12 星座，並印出，"我是 xx座" ，以題目為例子，就會印出 "我是 射手座"。
    
    note：考慮一下如果有人輸入 "太陽座" 呢？