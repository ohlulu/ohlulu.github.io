# print() 函數說明

`print()` 就是一個方法，一個印出資料的方法。

把你想要印出的東西放在 `( )` 中間，就可以了


try it !
```swift
// 印出：Hello World !
print("Hello, World !")
```

print 還有許多用法：
```swift
// print var -> 印出一個變數
let aData = 100
print(aData)
```

```swift
// print string with var -> 印出一個 字串 + 變數
let age = 18
print("age is \(age)")
```

```swift
// print multiple parameter -> 印出多個變數
let pi = "pi:"
let piValue = 3.1415926
print(pi, piValue)

let name = "Diane"
let age = 18
print("\(name)'s age is \(age)")
```

以上是常用的幾種 print() 使用方法。

### 跳脫字元

1. `\n` : 換行
2. `\"` : 雙引號
3. `\\` : 反斜線
4. `\t` : tab
5. `\r` : enter

# 練習

試著在一段字串中`加上`上面列出的跳脫字元並且印出來觀察結果。
```swift
// ex1
print("try\rtry\rtry")

// ex2
let tryStr = "try\rtry\rtry"
print(tryStr)
```