# 集合型別

Array, Dictionary, Set

## Array 陣列

* Array 陣列
* 一個`有序`的資料集合，利用 Index 來取出陣列中的值
* 利用 , 來區分每一組數值
* 使用 index 來取值，注意是從0開始計算，使用方式
> note: 存放的內容，必須明確的讓 swift 知道：陣列中存放的型別是什麼

### 宣告方式

宣告的時候，就給值；利用型別推斷，讓 swift 知道 Array 中存放的是 Int
```swift
let arr0 = [1, 1, 2, 3, 5, 8] // ✅
```


宣告的時候，只說明存放的型別，不給值，表示空陣列
```swift
let arr1 = [Int]() // [元素的型別]()
Array<Int>() // Array<元素的型別>()

// 上面兩個是等價的
```

宣告 Array 的型別，並指明存放 Int，所以就算給空的陣列，swift 依然理解
```swift
let arr3: Array<Int> = []
```

宣告型別是 Array ，但沒有說明 Array 中的型別，然後利用型別推斷，來讓 swift 知道 Array 中存放的是什麼型別

```swift
let arr2: Array = [1, 1, 2, 3, 5, 8]
// 半殘的宣告，不建議這麼使用
```


### 取值的方式

```swift
let arr0 = [0, 1, 2]
print(arr[0], arr[1])
// 0 1

let a = 2
let arr1: Array<Int> = ["我", "是", "誰"]
print(arr[a])
// 誰

let b = 0
let arr2 = [Int]()
print(arr[b])
// 🚫 out of index 
```

### 新增、修改、刪除 陣列中的值
新增的方式有很多種，這邊介紹幾個常用的
1. append：在陣列的尾端加上新的元素
    ```swift
    var arr0 = [0]
    print(arr0)

    arr0.append(-1)
    print(arr0)
    ```
2. `+` 用加號，將兩個陣列相加
    ```swift
    let arr1 = ["hello", "word"]
    let arr2 = ["!"]
    let arr3 = arr1 + arr2
    print(arr3)
    ```
3. insert(_ newElement: \<Element\>, at index: Int)，使用 Array 提供的方法，在指定位置，加上元素。
    ```swift
    var arr4 = [1, 1, 2]
    arr4.insert(3, at: 3)
    print(arr4)

    arr4.insert(0, at: 0)
    print(arr4)
    ```

4. 修改指定位置中的值：

    ```swift
    var arr5 = [1, 1, 2]
    arr5[0] = 8
    print(arr5)
    ```
---
## Dictionary

* Dictionary 字典
* 一種`無序`的資料集合，是一組 key + value
* 用 : 區分 key & value，左邊是 key，右邊是 value
* 一樣用 , 來區分每一組數值
* key 必須是唯一
* 用 key 來取值

### 宣告方式
宣告的時候，就給值

✅ 利用型別推斷，讓 swift 知道 Dictionary 中存放的 key 是 Int；value 是 String
```swift
let dic1 = [1: "小明", 2: "小王", 3: "小花"]
```

✅ 宣告完整的型別，所以給予空字典時，swift 會知道 key value 分別是什麼型別
```swift
let dic3: Dictionary<Int, String> = [:]
```

完整的宣告，但不必要，因為顯得多餘
```swift
let dic2: Dictionary<Int, String> = [1: "小明", 2: "小王", 3: "小花"]
```

半殘的宣告，一樣沒必要，但允許
```swift
let dic4: Dictionary = ["小明": 1, "小王": 2, "小花": 3]
```

### 取值方式
使用 key 來取值

```swift
let dic1 = [1: "小明", 2: "小王", 3: "小花"]
print(dic1[2])
```

```swift
let dic5 = ["small": 1, "mid": 2, "height": 3]
let key = "small"
print(dic5[key])
```

### 新增、修改、刪除 字典中的值
新增跟修改很像，字典中預設；有值就修改，無職就新增
```swift
var dic6 = ["small": 1, "mid": 0]

dic6["height"] = 3 // 新增
print(dic6)

dic6["mid"] = 2 // 修改
print(dic6)

dic6["height"] = nil // 刪除
print(dic6)
```

3. Set

* 一種`無序`的資料集合，跟 Array 很像，但存放的值必須是唯一

因使用較少，之後有機會再講 😂