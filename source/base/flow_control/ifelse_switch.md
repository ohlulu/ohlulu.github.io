# 條件式 if-else / switch

* [if-else](#if_else)
* [switch](#switch)

<h2 id="if_else">if else</h2>

簡單來說就是判斷 是非。

完整格式如下：
```swift
// 1.
if `判斷式` {

}

// 2.
if `判斷式` {

} else {

}

// 3.
if `判斷式` {

} else if `判斷式` {

} 
```

上面表示的判斷式，通常就是之前講過的，比較運算子（複習一下）
``` swift
1 == 1   // true, 因為 1 等於 1
2 != 1   // true, 因為 2 不等於 1
2 > 1    // true, 因為 2 大於 1
1 < 2    // true, 因為 1 小於2
1 >= 1   // true, 因為 1 大於等於 1
2 <= 1   // false, 因為 2 並不小於等於 1
```

所以以判斷式，判斷的是一個 `是(true)` and `非(false)`

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

腦筋急轉彎：

```swift
let age = 18
if age == 18 {
    print("age: \(age), 成年了")
} else {
    print("age: \(age), 未成年")
}
```
試著不使用 `==` 比較運算子，但一樣達到上面的效果

---

<h2 id="switch">Switch</h2>

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
case 0, 1...10: // 用逗點分隔兩個條件
    print("打屁股！")
case 11...60: // 用 ... 表示一個區間（數字適用）
    print("不及格")
case 61...80:
    print("馬馬呼呼")
case 81...99:
    print("婆費特")
case 100: // 也可以 case 單一數值
    print("Ｅ級棒")
default: // 當所有 case 都捕捉不到的時候，會進到 default
    print("作弊！")
}
```

case 後面可以擺放：單一情況，也可以放區間；也可以用逗號同時包含多總情況。是實際使用情況而定。

# 練習

1. `let number = 0`

    用 if else 判斷是否為偶數
    > ps: 改變 number 的值，驗證看看你的 if else 判斷式，是否正確

3. `let year = 2019`

    用 if else 判斷是否為閏年
    
    規則：年分除以 4 可整除，為閏年。
    
    > ps: 改變 year 的值，驗證看看你的 if else 判斷式，是否正確

4. `let constellation = "射手座"`

    用 swift 判斷 12 星座，並印出，"我是 xx座" ，以題目為例子，就會印出 "我是 射手座"。
    
    note：考慮一下如果有人輸入 "太陽座" 呢？

5. (進階)

    有一個學生的分數 `let score = (x)` (x 自行填入)，使用 switch-case 對學生的成績進行評等，規則如下：
    ```
    100 -> A+
    90~99 -> A
    80~89 -> B
    70~79 -> C
    60~69 -> D
    0~59 -> E
    ```

    條件限制：不使用 range 的方式，也就是 case 不能使用 0...59，這樣的表示方式。