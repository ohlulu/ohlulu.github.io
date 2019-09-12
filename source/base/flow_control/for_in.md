# 循環式控制 Loop

* [For 迴圈](#for_loop)
* [While 迴圈](#while_loop)
* [控制轉移語句（Control Transfer Statements](#control_ransfer_statements)

<h2 id="for_loop"> For 迴圈 </h2>

for迴圈用來按照指定的次數來執行一系列的程式碼。

for-in用來遍歷一個區間（range），序列（sequence），集合（collection）裡面所有的元素，執行一系列的程式碼。

你可以使用for-in迴圈來遍歷一個集合裡面的所有元素，例如由數字表示的區間、陣列中的元素、字串中的字元。

使用格式如下：
```swift
for `取出來的值的名稱` in `區間(0...10)` | `陣列(Array)` | `字典(Dictionary)` | `很多很多` {

}
```

#### 飯粒1 :
下面的範例用來輸出乘 5 乘法表前面一部分內容：
```swift
for index in 1...5 {
    print("\(index) * 5 = \(index * 5)")
}
// 1 * 5 = 5
// 2 * 5 = 10
// 3 * 5 = 15
// 4 * 5 = 20
// 5 * 5 = 25
```

用來進行遍歷的元素是一組使用閉區間運算子（...）表示的從1到5的數字。

迴圈在一起的的時候，index 被指派為第一個數字（1），然後迴圈中的語句被執行一次。

在上面的例子中，這個迴圈只包含一個語句 `印出當前 index 值所對應的 *5，的乘法表結果`。該語句執行後，index的值被更新為閉區間中的第二個數字（2），之後print方法會再執行一次。整個過程會進行到閉區間結尾為止。

#### 飯粒2 :
使用for-in遍歷一個陣列所有元素：
```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
for name in names {
    print("Hello, \(name)!")
}
// Hello, Anna!
// Hello, Alex!
// Hello, Brian!
// Hello, Jack!
```

---
<h2 id="while_loop">While 迴圈</h2>

while迴圈執行一系列語句直到條件變成false。這類別迴圈適合使用在第一次迭代前迭代次數未知的情況下。Swift 提供兩種while迴圈形式：

* `while`：每次在迴圈 `開始` 時計算條件是否符合

* `repeat-while`：每次在迴圈 `結束` 時計算條件是否符合。

#### 下面是 while 迴圈格式：
```swift
while `判斷式(condition)` {
    // ...
}
```

while 使用 🌰：
```swift
var number = 0
print("number initial is \(number)")
print("---------------------------")
while number < 5 {
    number = number + 1
    print("current number is \(number)")
}
```

#### repeat while 迴圈格式，則是下面這樣：
```swift
repeat {
    // ...
} while `判斷式(condition)`
```

repeat while 使用 🌰：
```swift
// 猜數字
var luckyNumber = 0
repeat {
    luckyNumber = Int.random(in: 0...1000)
    print(luckyNumber)
} while luckyNumber != 777
```
---
<h2 id="control_ransfer_statements">控制轉移語句（Control Transfer Statements）</h2>

控制轉移語句改變你程式碼的執行順序，通過它你可以實作程式碼的跳轉。Swift有四種控制轉移語句。

* continue (跳過本次迭代，但繼續迴圈)
* break (終止本次迭代，並跳出迴圈)
* fallthrough (貫穿)
* return (用在 func)

### continue 
`continue` 語句告訴一個迴圈立刻停止本次迴圈迭代，重新開始下次迴圈迭代。就好像在說「本次迴圈迭代我已經執行完了」，但是並不會離開整個迴圈。

> 注意：
> 在一個for條件遞增（for-condition-increment）迴圈中，在呼叫continue語句後，迭代增量仍然會被計算求值。迴圈繼續像往常一樣工作，僅僅只是迴圈中的執行程式碼會被跳過。

下面的飯粒把一個小寫字串中的母音字母和空格字元移除，生成了一個含義模糊的短句：

```swift
let puzzleInput = "great minds think alike"
var puzzleOutput = ""
for character in puzzleInput {
    switch character {
    case "a", "e", "i", "o", "u", " ":
        continue
    default:
         puzzleOutput = puzzleOutput + String(character)
         // 等價於 puzzleOutput += String(character)
    }
}
print(puzzleOutput)
```

### Break 
break語句會立刻結束整個控制流程的執行。當你想要更早的結束一個 `switch程式碼區塊` 或者一個 `迴圈` 時，你都可以使用break語句。


#### 迴圈語句中的 break
當我們在一個迴圈中使用 break 時，會立刻中斷迴圈的執行，然後跳轉到表示迴圈結束的大括號 `}` 後的第一行程式碼。不會再有本次迴圈迭代的程式碼被執行，也不會再有下次的迴圈迭代產生。

飯粒：
```swift
let names = ["peter", "alen", "QQ", "henrry", "falco"]

for name in names {
    if name == "QQ" {
        print("Find \(name) !")
        break
    }
    print("searching ...")
}
print("Finish.")
```


#### Switch 語句中的 break
當在一個 `switch程式碼區塊` 中使用 `break` 時，會立刻中斷 `switch程式碼區塊` 的執行，並且跳轉到表示 switch 程式碼區塊結束的大括號 `}` 後的第一行程式碼。

這種特性可以被用來匹配或者忽略一個或多個分支。因為 Swift 的 switch 需要包含所有的分支而且不允許有為空的分支，有時為了使你的意圖更明顯，需要特意匹配或者忽略某個分支。那麼當你想忽略某個分支時，可以在該分支內寫上break語句。當那個分支被匹配到時，分支內的break語句立即結束switch程式碼區塊。

下面的範例通過switch來判斷一個 String 值是否代表一個數字。

```swift
let numberSymbol: String = "三"  // 中文裡的數字 3
var intValue: Int?
switch numberSymbol {
case "1", "one", "一":
    intValue = 1
case "2", "two", "二":
    intValue = 2
case "3", "three", "三":
    intValue = 3
case "4", "four", "四":
    intValue = 4
default:
    break
}
if let intValue = intValue {
    print("The numberSymbol(\(numberSymbol)) is a integer. value is \(intValue)")
} else {
    print("The numberSymbol(\(numberSymbol)) is `not` a integer.")
}
// 輸出 "The integer value of 三 is 3."
```

### Fallthrough

> 比較少用到，有空再自己看即可ＸＤＤ

因為 Swift 中的 switch 不會從上一個 case 分支落入到下一個 case 分支中。所以，只要第一個匹配到的 case 分支完成了它需要執行的語句，整個 switch 程式碼區塊完成了它的執行。

如果你今天不想要這個特性；即便捕捉到了並且執行完成了，還是想要執行下一個區塊的程式碼，就可以使用。

```swift
let integerToDescribe = 5
var description = "The number \(integerToDescribe) is"
switch integerToDescribe {
case 2, 3, 5, 7, 11, 13, 17, 19:
    description += " a prime number, and also"
    fallthrough
default:
    description += " an integer."
}
print(description)
// 輸出 "The number 5 is a prime number, and also an integer."
```

> 注意：使用 fallthrough 時，Swift 並不會淨息下一個 case 的匹配判斷，而是直接執行該區塊的程式碼。

> ps: 當你有空然後讀到這邊的時候，call 我，我再貼範例給你。這邊先保留一下，可以想想看。

# 練習

1. 用一個迴圈，印出 
    ```
    2 * 1 = 2
    2 * 2 = 4
    2 * 3 = 6
    2 * 4 = 8
    2 * 5 = 10
    2 * 6 = 12
    2 * 7 = 14
    2 * 8 = 16
    2 * 9 = 18
    ```

1. `let number = 13`，判斷 13 是不是質數。

1. 輸入一個正整數 N，計算出 N!

1. 輸入一個正整數，計算出 1...N 的奇數和。（ex: N=8 -> 1+3+5+7）

1. 求 s=a+aa+aaa+aaaa+aa...a的值，其中a是一個數字。例如2+22+222+2222+22222，要加幾次，由變數控制

1. 印出九九乘法表（進階一點：格式排好）
    > 提示1：`terminator: ""`，可以取消換行。

    > 提示2: `String(format: "%2d", i)`，可以讓數字即便是個位數，也可以依照兩位數的方式來印出。

    ```swift
    print("x", terminator: "")
    print("y", terminator: "")
    // 輸出 xy ，不會換行
    ```

    ```swift
    let a = String(format: "%2d", 1)
    print(a)
    print(1)
    // 輸出：
    //  1
    // 1
    ``` 

1. 列印圖形：
    最多只能用兩個 print()
    ```
    * * * * *
    *       *
    *       *
    *       *
    *       *
    * * * * *
    ```