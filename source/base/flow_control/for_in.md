# 循環式控制 Loop

* [For 迴圈](#for_loop)
* [While 迴圈](#while_loop)

<h2 id="for_loop"> For 迴圈 </h2>

for迴圈用來按照指定的次數多次執行一系列語句。Swift 提供兩種for迴圈形式：

for-in用來遍歷一個區間（range），序列（sequence），集合（collection）裡面所有的元素執行一系列語句。
for條件遞增（for-condition-increment）語句，用來重複執行一系列語句直到達成特定條件達成，一般通過在每次迴圈完成後增加計數器的值來實作。

For-In
你可以使用for-in迴圈來遍歷一個集合裡面的所有元素，例如由數字表示的區間、陣列中的元素、字串中的字元。

使用格式如下：
```swift
for `取出來的值的名稱` in `Indicater` {

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

用來進行遍歷的元素是一組使用閉區間運算子（...）表示的從1到5的數字。index被賦值為閉區間中的第一個數字（1），然後迴圈中的語句被執行一次。在本例中，這個迴圈只包含一個語句，用來輸出當前index值所對應的乘 5 乘法表結果。該語句執行後，index的值被更新為閉區間中的第二個數字（2），之後print方法會再執行一次。整個過程會進行到閉區間結尾為止。

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

<h2 id="while_loop">While 迴圈</h2>

while迴圈執行一系列語句直到條件變成false。這類別迴圈適合使用在第一次迭代前迭代次數未知的情況下。Swift 提供兩種while迴圈形式：

while迴圈，每次在迴圈開始時計算條件是否符合；
do-while迴圈，每次在迴圈結束時計算條件是否符合。