#  9. 列舉 Enum

enum 用簡單一點的方式來說明的話，他是一組選項的集合。

## 列舉語法
使用 enum 關鍵字定義，並把選項放在一對大括號內：

```swift
enum SomeEnumeration {
  // enumeration definition goes here
}
```

以下是指南針四個方向的一個範例：
```swift
enum CompassPoint {
  case north
  case south
  case east
  case west
}
```

使用起來會像這樣：
```swift
let point = CompassPoint.north
// or
let point2: CompassPoint = .north
```

## enum & switch
```swift
let point3 = CompassPoint.south
switch point3 {
case .north:
    print("point3 is north")
case .south:
    print("point3 is south")
case .east:
    print("point3 is east")
case .west:
    print("point3 is west")
}
// 輸出 point3 is south
```

> 注意：這邊沒有使用 `default:`，編譯器也沒警告我們需要加上 `default:`，是因為 enum 所有的情況都已經被 case 了，所以不可能有 default 的狀況出現，自然就不需要加上 default 了

當然，你也可以選擇不 case 所有情況，然後使用 default 關鍵字：

```swift
let point3 = CompassPoint.south
switch point3 {
case .north:
    print("point3 is north")
case .west:
    print("point3 is west")
default:
    print("not catch")
}
```

## 原始值（Raw Values）

上面的例子使用了東西南北四個方向，那東西南北其實也是可以用 `角度` 來表示，所以我們可以為 enum case 加上 rawValue，來表示 case 的原始值是什麼。
語法如下：

```swift
enum CompassPointWithRawValue: Int {
  case north = 0
  case south = 90
  case east = 180
  case west = 270
}
```
原始值可以是字串，整數，浮點數，或者任何型別。每個原始值在它的列舉宣告中必須是唯一的。當整數值被用於原始值，如果其他列舉成員沒有值時，它們會自動遞增。

```swift
enum Month: Int {
    case January = 1
    case February
    case March
    case April
    case May
    case June
    case July
    case August
    case September
    case October
    case November
    case December
}

enum Month: Int {
    case January = 1, February, March, April, May, June, July, August, September, October, November, December
}
```

## 使用 raw value 初始化 enum 實體
在定義 enum 時，如果使用了 rawValue，那這個 enum 會有一個初始化方法(method)，這個方法有一個名稱為 rawValue 的參數，參數型別就是列舉原始值的型別，返回值為列舉成員或nil。例子如下：

```swift
let month1 = Month(rawValue: 1)
let month2 = Month(rawValue: 13)
```
> 注意： Month(rawValuw: Int) 返回的是一個 Month?，因為有可能，傳入的參數，並不在我們定義的 rawValue 中，因此有可能 nil

# 練習 

1. 試著用一個迴圈把 Month 所有的 case 都印出來，並且包含 rawValue