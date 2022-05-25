---
title: "CoderEngin-漂亮的方式取用圖片"
date: "2020-04-19"
categories: 
  - "technology"
tags: 
  - "python"
  - "swift"
  - "xcode"
  - "script"
  - "automation"
img_path: /assets/img/post/
---

## #起

在 iOS 開發的過程中，取用圖片的時候我們經常使用 `UIImage(named: "add")` 這樣的方式，使用圖片檔名+字串的方式，雖然現在多數的設計師會使用 zeplin 等工具方便工程師複製檔名，但使用字串總是不那麼漂亮。

而且每次使用都區要先把視窗切到 zeplin 在切回來。個人使用起來總感覺不是那麼流暢。

## #承

因此，在之後的專案中，想到使用 UIImage static compute property 的方式來取用圖片，字串的部分只要寫一次就好。

```swift
extension UIImage {
    static var save: UIImage {
        return UIImage(named: "save")!
    }
}

// 使用起來會像這樣
let imageView = UIImageView(image: .save)
```

當然開源套件 R.swfit 也可以達到這樣的效果，不過 R.swift 是另外宣告一個自己的型別，所以 Swift 的型別推斷沒辦法直接使用 .save 的方式來取用圖片。必須 `R.image.safe()` 像這樣使用，感覺上稍微冗長了一點。

## #轉

由於專案中的圖片數量可能不止十幾張、幾十張，可能是上百張，如果每個圖片都要自己打一次，容易出錯不說，新增修改刪除圖片的時候不好維護，由於這個動作相對單純，於是想到使用腳本的方式來幫我們解決。

所以有兩個問題需要解決

1. 手動產生這個檔案很麻煩寫容易出錯
2. 增修改刪除圖片的時候不好維護

剛好想玩一下 python，就決定使用 python 來完成這件事啦。初代的腳本 用起來大概像是這樣 [CoderEngine](https://github.com/ohlulu/CoderEngine/tree/Release-v2.0.2) 那時候是請設計師，將專案所有的圖片打包一份給我，直接 parse 這份檔案，這個做法解決了 問題1 ，但 問題2 依然存在。不過還算堪用，所以也將就的先用在專案上了。

## #合

後來 [KuoChingHao](https://github.com/KuoChingHao) 大大，聽到我寫了一個這樣的腳本之後，說也想玩一下腳本，我們兩合計了一下，由 KuoChingHao 大大操刀，做出了以下修改。

1. 直接 parse Assets.xcasset
2. 將檔名的底線改為駝峰（add\_pressed -> addPressed）
3. 去掉檔名中的 dash（add-1 -> add1）
4. 在專案中使用 build pre-action script

我後續更新了 deep parse ，解決了 Assets 中存在資料夾的情況。

使用起來會像是這樣

![](ImageEngineDemo.gif)

原始碼在 [Github](https://github.com/ohlulu/CoderEngine)
