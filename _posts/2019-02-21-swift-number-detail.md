---
title: "Swift 數字處理大全"
date: "2019-02-21"
categories: 
  - "筆記"
tags: 
  - "swift"
  - "tip"
img_path: /assets/img/post/
toc: true
---

也可以到我的 [Gist](https://gist.github.com/ohlulu/48b4dda28fdf5211a0b31554b8e0a261) 看完整的 code 😄

### 無條件進位 (小數，整數)

整數的無條件進位

```swift
ceil(11.2)
// print 12
// ceil 的意思是天花板
```

無條件進位至 小數第x位

```swift
extension Double {
    func ceiling(toDecimal decimal: Int) -> Double {
        let numberOfDigits = abs(pow(10.0, Double(decimal)))
        if self.sign == .minus {
            return Double(Int(self * numberOfDigits)) / numberOfDigits
        } else {
            return Double(ceil(self * numberOfDigits)) / numberOfDigits
        }
    }
}

123.12345.ceiling(toDecimal: 3)
// print 123.124
 
(-123.12345).ceiling(toDecimal: 3)
// print -123.123
```

無條件進位至 整數第x位

```swift
extension Double {
    func ceiling(toInteger integer: Int = 1) -> Double {
        let integer = integer - 1
        let numberOfDigits = pow(10.0, Double(integer))
        return Double(ceil(self / numberOfDigits)) * numberOfDigits
    }
}

123.12345.ceiling(toInteger: 2)
// print 130.0

(-123.12345).ceiling(toInteger: 2)
// print -120.0
```

  

### 四捨五入 (小數，整數)

```swift
lround(11.2) -> Int
// print 11

round(11.5) -> Double
// print 12.0

```

四捨五入至 小數第x位

```swift
extension Double {
    func rounding(toDecimal decimal: Int) -> Double {
        let numberOfDigits = pow(10.0, Double(decimal))
        return (self * numberOfDigits).rounded(.toNearestOrAwayFromZero) / numberOfDigits
    }
}

24.141.rounding(toDecimal: 2)
// 24.14

24.145.rounding(toDecimal: 2)
// 24.15

(-24.141).rounding(toDecimal: 2)
// -24.14

(-24.145).rounding(toDecimal: 2)
// -24.15
```

四捨五入至 整數第x位

```swift
extension Double {
    func rounding(toInteger integer: Int) -> Double {
        let integer = integer - 1
        let numberOfDigits = pow(10.0, Double(integer))
        return (self / numberOfDigits).rounded(.toNearestOrAwayFromZero) * numberOfDigits
    }
}

124.141.rounding(toInteger: 2)
// 120.0
 
125.141.rounding(toInteger: 2)
// 130.0
 
(-124.141).rounding(toInteger: 2)
// -120.0
 
(-125.141).rounding(toInteger: 2)
// -130.0
```

### 無條件捨去 (小數，整數)

```swift
Int(11.24)
// print 11
```

無條件捨去至 小數第x位

```swift
extension Double {
    func floor(toDecimal decimal: Int) -> Double {
        let numberOfDigits = pow(10.0, Double(decimal))
        return (self * numberOfDigits).rounded(.towardZero) / numberOfDigits
    }
}

124.141.floor(toDecimal: 2)
// print 124.14

(-124.141).floor(toDecimal: 2)
// print -124.14
```

無條件捨去至 整數第x位

```swift
extension Double {
    func floor(toInteger integer: Int) -> Double {
        let integer = integer - 1
        let numberOfDigits = pow(10.0, Double(integer))
        return (self / numberOfDigits).rounded(.towardZero) * numberOfDigits
    }
}

124.141.floor(toInteger: 2)
// print 120.0
 
(-124.141).floor(toInteger: 2)
// print -120.0
```
