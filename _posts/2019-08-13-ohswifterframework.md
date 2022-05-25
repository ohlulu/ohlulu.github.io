---
title: "心路歷程-打造一個優雅的初始化框架"
date: "2019-08-13"
categories: 
  - "tip"
img_path: /assets/img/post/
---

最近因為手上的案子告一段落，距離下一個案子進入開發階段也還有一點時間，所以興起了整理自己常用的 extension 的想法。

以目前待在接案公司來說，重要的就是畫面的快速產出，邏輯的部分相對產品來說，其實沒有那麼複雜。

所以在畫面瘋狂的建立&設置的情況下，一個方便好用的設置方式，其實滿重要的。在重複建立相似但卻不一樣的 UI 時，也會相對的輕鬆，且愉悅。

所以這篇會簡述一下，我從 學習 -> 工作 -> 產出一個框架的心路歷程。

> 待在一間接案公司，快速、有效率的產出，是一件非常重要的事。
{: .prompt-tip }

### \# 蹣跚學步

在初學 Swift 的時候，我總是在檔案的上方宣告UIKit類的元件，並在 `ViewDidLoad`中作原件樣式的設定，看起來會是這樣👇。

```swift
let emailLabel = UILabel()

override func viewDidLoad() {
    super.viewDidLoad()
    emailLabel.text = "z30262226@gmail.com"
    emailLabel.font = UIFont.systemFont(ofSize: 14, weight: .regular)
    emailLabel.textColor = .black
    emailLabel.textAlignment = .center
    emailLabel.numberOfLines = 0
    emailLabel.backgroundColor = .white
    emailLabel.layer.cornerRadius = 5
    view.addSubview(emailLabel)
    // 然後是 Autolayout ...
}
```

如果今天畫面上只有一兩個 UI元件，這樣寫或許不是太大的問題，但實際案子中不太可能有這樣的需求，那在畫面上的東西多起來之後，整個 `ViewDidLoad` 會變的非常混亂。

### \# 邯鄲學步

人總是會進步的，曾經看過這麼一句話，具體怎麼說忘了，但意思大概是。

> 如果三個月後，看三個月前寫的程式碼，還可以感覺寫得真好，這是一件很恐怖的事。

這句話也可以看出工程師們學不完的人生 😂。

隨著看過的程式碼、文章、教學、Stack Overflow越來越多，開始有了用 func 包裝程式碼的觀念，於是我把所有關於 UI 的樣式設置、Autolayout 都用一個 func 包起來，並放在整個檔案的最下方。看起來會是下面這樣👇。

```swift
private let emailLabel = UILabel()

override func viewDidLoad() {
    super.viewDidLoad()
    setupUI()
}

// MARK: - SetupUI

private extension ViewController {
    func setupUI() {
        emailLabel.text = "z30262226@gmail.com"
        emailLabel.font = UIFont.systemFont(ofSize: 14, weight: .regular)
        emailLabel.textColor = .black
        emailLabel.textAlignment = .center
        emailLabel.numberOfLines = 0
        emailLabel.backgroundColor = .white
        emailLabel.layer.cornerRadius = 5
        view.addSubview(emailLabel)
        // 然後是 Autolayout ...
    }
}
```

漸漸的、開始加上權限的宣告、使用 // MARK: - 、以及 extension 來切割程式碼。

一瞬間，class 宣告變數的地方，看起來 很乾淨。

但很快的，我發現 `setupUI` 裡面其實問題依舊。

1. 宣告與樣式的設定，被拆成了兩部分。
2. 當大量的 UI 元件被放入的時候，樣式設置+Autolayout 一樣讓程式碼變的雜亂。

> 結論：我只是把雜亂的程式碼換了一個地方擺而已。
{: .prompt-warning }

### \# 日積月累

接下來的一段時間，其實看了滿多文章，但其中 coding style 的部分通常只有片段的規範。

這時候，換了工作，終於有了同樣寫 iOS 的同事😭，發現他的程式碼有許多方便的 func & property 可以呼叫，看了一下原始碼發現多數是來自於 [SwifterSwift](https://github.com/SwifterSwift/SwifterSwift) ，我參考了套件裡面的做法，自己寫自己常用的方法，選擇自己寫的原因是希望自己可以理解的更透徹，並且把這種重用的概念養成一種習慣。

同時，對 Swift 的語法也瞭解的更深了。因此產生了下面這種寫法。

```swift
private let emailLabel: UILabel = {
    let label = UILabel()
    label.text = "z30262226@gmail.com"
    label.font = UIFont.systemFont(ofSize: 14, weight: .regular)
    label.textColor = .black
    label.textAlignment = .center
    label.numberOfLines = 0
    label.backgroundColor = .white
    label.cornerRadius = 5 // 利用 extension 將 layer 的使用省略
    return label
}()

override func viewDidLoad() {
    super.viewDidLoad()
    setupUI()
}

// MARK: - SetupUI

private extension ViewController {
    func setupUI() {
        // emailLabel
        view.addSubview(emailLabel)
        // 然後是 Autolayout ...
    }
}
```

這樣的好處是，我把宣告與樣式的設置，寫在一起了。因為佈局的部分，有時候會一層疊一層，因此我選擇另外包成一塊，並放在最下面，補充一下放在最下面的原因是：通常佈局的部分寫好之後，就不太需要去動到的，會修改的部分通常是邏輯。

這樣的寫法維持了好長一段時間，但久了問題也漸漸浮出來了。

1. 首先是 init closure 裡面 `label` 重複輸入的很多次。
    
2. 再來是 emailLabel 後面必須宣告型別，即便 closure 回傳了 UILabel 的物件。就算宣告了型別，內部的 `let label = UILabel()` & `return label` 看起來也顯得有點多餘。
    
3. 算上 `init` & `return` & `closure尾部的()` 總共多了三行個人認為沒什麼意義的 code
    
4. 字型與文字顏色的部分，基本上是每個 UILabel & UIButton 都需要設置的屬性，每次初使用的時候都要重新打一次；我覺得不行。
    

### \# 異想天開—曾經走錯的那段路。

這是一個我自己創造，卻在用過之後立刻放棄的寫法，會拿出來講是因為：

儘管被我棄用了，但在這個過程中也了解怎樣是不好的設計。為日後累積了些許的基石？

```swift
enum AppFont {
    // 利用 Font size + 流水號，來為每個 case 命名
    case style2401, style2402, style2403, style2001, style2002

    var attributes: [NSAttributedString.Key: Any] {
        switch self {

        case .style2401:
            return createAttribute(weight: .regular, 
                                   size: 24, color: .black, 
                                   backgroundColor: .white, 
                                   alignment: .left)
        case .style2402:
            return createAttribute(weight: .semibold, 
                                   size: 24, 
                                   color: .words, 
                                   alignment: .right)
        // case ....
}

// 然後為每一個可以設置 Font 的 UIKit 元件寫一個方法去接收，並實際設置這些樣式設定

```

這邊我曾經把用到的組合都用一個 enum 來包裝，然後利用 font size 來當作命名的開頭。

曾經的我以為這樣很完美。

但隨著案子的推進，樣式錯誤的時候，追蹤困難，許多樣式其實只是置左置右的差別，但卻多了一個 enum case。

有極小的差異，但組合起來，數量很驚人。

加上設計師有時候會調整或新增樣式時，這樣的寫法再新增及修改上極為困難，因此用了一段後時間立刻放棄。

> 不要試圖在一行 code 裡面包山包海。
{: .prompt-tip }

### \# Ready go

接下來的這個時期，有兩個點同時給了我靈感

1. 在 iOS@Taipei 時聽到了一位講者，在分享的時候介紹了一下他自製的套件 [ChainsKit](https://github.com/kuotinyen/ChainsKit) 。
2. 在一個全新的案子中我開始使用 RxSwift 了。

老實說在第一次看到 ChainsKit 之後，並沒有太多的感覺。但在我漸漸熟悉 RxSwift，並著迷於 RxSwift 之後，我發現 ChainsKit 是一種很棒寫法。

那為什麼我沒有使用 ChainsKit 而是選擇打造一個自己的框架呢。

因為很不巧的，在我使用從 SwifterSwift 學習到的一個 UIView 擴展: `var borderColor` 不小心與案子中另一個套件的命名衝突了。雖然我改用 xx\_borderColor 這樣的命名來解決了這個問題。但底線式的命名方式實在很不符合個人的喜好...。

接著再查詢 RxSwift 資料的時候，發現了一篇很棒的文章 [medium][https://medium.com/@DianQK/%E5%86%99%E6%9B%B4%E4%BC%98%E9%9B%85%E7%9A%84-swift-%E6%A1%86%E6%9E%B6-rx-tap-rx-tap-af0c417da3d6]，可以自訂一個 namespacing 的實作方法。

總結了一下幾個點（用 Rx tap 舉例）：

- rx\_Tap：一種反 swift 風格的風格
- rxTap：符合 swift 風格的風格，但語意上容易造成困惑
- reactiveTap：太冗長了

**以上幾點都還有兩個問題就是：** 
    **1\. 為原本就很多的自動補全選項，又添上了許多選擇** 
    **2\. 都只是換湯不換藥**

* * *

綜合以上種種原因，最終決定使用 rx.tap 這種方式 （其實只是用了別人的結論來實作）並且認真的實作成一個框架，而不是每次新案子都是整包檔案的拉來拉去。

所以，[OhSwifter](https://github.com/z30262226/OhSwifter) 誕生啦！

它用起來會像這樣 👇

```swift
private let ohButton = UIButton().oh
    .title("Button Wording", for: .normal)
    .titleColor(.black, for: .normal)
    .titleColor(.gray, for: .normal)
    .cornerRadius(12)
    .border(color: .darkGray, width: 1)
    .done()
```

這個框架解決了幾個問題：

1. 以點鏈式的方式，將UI元件的宣告與樣式設置放在一起。
2. 以返回 self 的方式，解決了 init closure 中重複輸入 label 的問題。
3. 將 border color & width 放在同一個 fun 中，因為這兩個屬性幾乎是互相綁定的，諸如此類的還有陰影的設置。

當然，在實作框架的時候發現，必須在宣告鏈的尾部使用 `.base` 來做解包的動作，看起來有點怪，不太符合語意，所以我加上了 `.done()` 方法來取出 base 的值。但本質上一樣是換湯不換藥；有點多餘，但目前沒有更好的想法了，好在整體而已，看起來還算順眼。

好了，一個按照自己心意打造的框架，用起來就是舒服。

儘管在不久的將來可能會因為種種原因，讓我棄用這個框架。 但這總歸是一個新開始。😁

如果你有更好的想法，歡迎提出來一起討論。
