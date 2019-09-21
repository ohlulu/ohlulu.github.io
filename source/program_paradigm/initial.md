#  9. 建構子

建構過程是為了使用某個類別、結構或列舉型別的實例而進行的準備過程。這個過程包含了為實例中的每個屬性設置初始值和為其執行必要的準備和初始化任務。

建構過程是通過定義建構器（Initializers）來實作的，這些建構器可以看做是用來創建特定型別實例的特殊方法。它們的主要任務是保證新實例在第一次使用前完成正確的初始化。

類別實例也可以通過定義析構器（deinitializer）在類別實例釋放之前執行特定的清除工作。

## 建構器
建構器在創建某特定型別的新實例時呼叫。它的最簡形式類似於一個不帶任何參數的實例方法，以關鍵字init命名。

下面範例中定義了一個用來保存華氏溫度的結構Fahrenheit，它擁有一個Double型別的儲存型屬性temperature：

```swift
struct Fahrenheit {
    var temperature: Double
    init() {
        temperature = 32.0
    }
}

var f = Fahrenheit()
println("The default temperature is \(f.temperature)° Fahrenheit")
// 輸出 "The default temperature is 32.0° Fahrenheit」
```
這個結構定義了一個不帶參數的建構器init，並在裡面將儲存型屬性temperature的值初始化為32.0（華攝氏度下水的冰點）。

## 預設屬性值
如前所述，你可以在建構器中為儲存型屬性設置初始值；同樣，你也可以在屬性宣告時為其設置預設值。

你可以使用更簡單的方式在定義結構Fahrenheit時為屬性temperature設置預設值：

```swift
struct Fahrenheit {
    var temperature = 32.0
}
```

### 建構參數
你可以在定義建構器時提供建構參數，為其提供定制化建構所需值的型別和名字。建構器參數的功能和語法跟函式和方法參數相同。

下面範例中定義了一個包含攝氏度溫度的結構Celsius。它定義了兩個不同的建構器：init(fromFahrenheit:)和init(fromKelvin:)，二者分別通過接受不同刻度表示的溫度值來創建新的實例：
```swift
struct Celsius {
    var temperatureInCelsius: Double = 0.0
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
}
```

```swift
let boilingPointOfWater = Celsius(fromFahrenheit: 212.0)
// boilingPointOfWater.temperatureInCelsius 是 100.0
let freezingPointOfWater = Celsius(fromKelvin: 273.15)
// freezingPointOfWater.temperatureInCelsius 是 0.0」
```

第一個建構器擁有一個建構參數，其外部名字為fromFahrenheit，內部名字為fahrenheit；第二個建構器也擁有一個建構參數，其外部名字為fromKelvin，內部名字為kelvin。這兩個建構器都將唯一的參數值轉換成攝氏溫度值，並保存在屬性temperatureInCelsius中

### 部和外部參數名
跟函式和方法參數相同，建構參數也存在一個在建構器內部使用的參數名字和一個在呼叫建構器時使用的外部參數名字。

然而，建構器並不像函式和方法那樣在括號前有一個可辨別的名字。所以在呼叫建構器時，主要通過建構器中的參數名和型別來確定需要呼叫的建構器。正因為參數如此重要，如果你在定義建構器時沒有提供參數的外部名字，Swift 會為每個建構器的參數自動生成一個跟內部名字相同的外部名，就相當於在每個建構參數之前加了一個雜湊符號。

> 注意：
如果你不希望為建構器的某個參數提供外部名字，你可以使用`底線_`來顯示描述它的外部名，以此覆蓋上面所說的預設行為。

以下範例中定義了一個結構Color，它包含了三個常數：red、green和blue。這些屬性可以儲存0.0到1.0之間的值，用來指示顏色中紅、綠、藍成分的含量。

Color提供了一個建構器，其中包含三個Double型別的建構參數：

```swift
struct Color {
    let red = 0.0, green = 0.0, blue = 0.0
    init(red: Double, green: Double, blue: Double) {
        self.red   = red
        self.green = green
        self.blue  = blue
    }
}
```

每當你創建一個新的Color實例，你都需要通過三種顏色的外部參數名來傳值，並呼叫建構器。

```swift
let magenta = Color(red: 1.0, green: 0.0, blue: 1.0)
```

注意，如果不通過外部參數名字傳值，你是沒法呼叫這個建構器的。只要建構器定義了某個外部參數名，你就必須使用它，忽略它將導致編譯錯誤：

```swift
let veryGreen = Color(0.0, 1.0, 0.0)
// 報編譯時錯誤，需要外部名稱
```

### 可選屬性型別
如果你定制的型別包含一個邏輯上允許取值為空的儲存型屬性--不管是因為它無法在初始化時賦值，還是因為它可以在之後某個時間點可以賦值為空--你都需要將它定義為可選型別optional type。可選型別的屬性將自動初始化為空nil，表示這個屬性是故意在初始化時設置為空的。

下面範例中定義了類別SurveyQuestion，它包含一個可選字串屬性response：

```swift
class SurveyQuestion {
    var text: String
    var response: String?
    init(text: String) {
        self.text = text
    }
    func ask() {
        println(text)
    }
}
let cheeseQuestion = SurveyQuestion(text: "Do you like cheese?")
cheeseQuestion.ask()
// 輸出 "Do you like cheese?"
cheeseQuestion.response = "Yes, I do like cheese."
```
調查問題在問題提出之後，我們才能得到回答。所以我們將屬性回答response宣告為String?型別，或者說是可選字串型別optional String。當SurveyQuestion實例化時，它將自動賦值為空nil，表明暫時還不存在此字串。