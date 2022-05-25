---
title: "Swift 自訂轉場動畫，手勢(下)"
date: "2019-03-31"
categories: 
  - "animation"
tags: 
  - "animation"
  - "swift"
  - "transition"
img_path: /assets/img/post/
coverImage: "FeaturedImage.png"
---

## Demo GIF

![](DiffusionAnimation.gif)

* * *

這篇主要講手勢的使用，關於動畫實作的部分，在 [上篇](https://www.ohlulu.tw/2019/03/10/swift-custom_transition_animation/) 有詳細的code

接下來要在我們建立的 `CustomTransition` 裡面，跟系統說：我們的互動事件要透過誰來管理互動。

```swift
class CustomTransition: NSObject, UIViewControllerTransitioningDelegate {

    // ...

    let interaction = UpToDownIneraction()

    func interactionControllerForPresentation(using animator: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
        return nil
    }


    func interactionControllerForDismissal(using animator: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
        print(interation.interacting)
        return interaction.interacting ? interaction : nil
    }
}
```

這邊使用 `interaction.interacting ? interaction : nil` 這樣的寫法，翻成中文大概的意思是：如果已經在處理互動了，那就不用多嘴跟系統說誰來處理互動。所以在 UpToDownIneraction 裡面我們宣告一個 interacting 來負責是否在互動中。

注意這兩個 func 在 UIViewControllerTransitioningDelegate 裡面其實是 optional 的，如果不實做的話，系統會使用原生的手勢，ex: navigation 會有右滑返回的手勢。回傳 nil 同理。
****
那這邊只使用 interactionControllerForDismissal ，也就是 dismiss 需要的透過誰來管理。因為在出現的部分如果使用手勢，不熟悉的使用者可能會產生疑惑，除非UI上有明顯的提示，例如一個箭頭，加上一個抖動的動畫，不然使用點擊事件來當作出現的觸發條件會是比較好的選擇。

* * *

## 接下來就是實作互動管理了

##### 先把 class 建立起來，並寫好我們需要的 property

```swift
class UpToDownIneraction: UIPercentDrivenInteractiveTransition {

    var interacting: Bool = false

    private var couldComplete: Bool = false
    private weak var presentingViewController: UIViewController? = nil
}
```

細心的人可能發現我們繼承的 class 是 `UIPercentDrivenInteractiveTransition` 而不上面 func 需要回傳的 `UIViewControllerInteractiveTransitioning` 這是蘋果提供的一個方便的類別，如果動畫中使用的是 `UIView.animation`，那我們就只需要告訴系統現在要 run 到第幾 % 。

- interacting 是管理是否在互動中
    
- couldComplete 負責的是，如果滑到一半，就放開手指，這時候是要跑完整個動畫，還是恢復動畫前的狀態。
    
- presentingViewController 是我們需要加上手勢的 ViewController
    
    （除了配合手勢，執行動畫，還需要跟系統說我們要 dismiss 還是 pop，所以需要一個 property 存起來方便之後判斷）
    

#### 然後實作我們需要的方法

首先加上手勢，並做好手勢需要執行的空方法

```swift
class UpToDownIneraction: UIPercentDrivenInteractiveTransition {

    // ...

    func wireGesture(on viewController: UIViewController) {
        presentingViewController = viewController
        let gesture = UIPanGestureRecognizer(target: self, action: #selector(handleGesture(_:)))
        viewController.view.addGestureRecognizer(gesture)
    }

    @objc func handleGesture(_ gestureRecoginizer: UIPanGestureRecognizer) {
        // ...
    }
}
```

接著實作手勢執行的方法， `UIPercentDrivenInteractiveTransition` 類別提供了我們幾個方法

- update(\_ percentComplete: CGFloat)
- cancel()
- finish()

很明顯我們只需要算好手勢在畫面上移動的比例，然後使用 update 來更新，並在適當的時機跟系統說我們要 取消 或是 完成 動畫，即可。

```swift
class UpToDownIneraction: UIPercentDrivenInteractiveTransition {

    // ...

    @objc func handleGesture(_ gestureRecoginizer: UIPanGestureRecognizer) {
        let gestureView = gestureRecoginizer.view!
        let trainsiton = gestureRecoginizer.translation(in: gestureView)
        switch gestureRecoginizer.state {
        case .began:
            print("began")
            interacting = true
            if let naviController = presentingViewController?.navigationController {
                naviController.popViewController(animated: true)
            } else {
                presentingViewController?.dismiss(animated: true, completion: nil)
            }
        case .changed:
            var fraction = trainsiton.y / gestureView.frame.height
            print("\(fraction)")
            fraction = max(fraction, 0.0)
            fraction = min(fraction, 1)
            couldComplete = fraction > 0.4
            update(fraction)
        case .cancelled, .ended:
            interacting = false
            if couldComplete == false || gestureRecoginizer.state == .cancelled {
                cancel()
            } else {
                finish()
            }
        default:
            break
        }
    }
}
```

484很簡單？這邊特別提醒一下，如果動畫不是使用 UIView.animation 來實作，那麽 `UIPercentDrivenInteractiveTransition` 就不適用了。這個坑害我搞了好久。

## 萬事俱備只欠東風

```swift
@objc func buttonPressed() {
    let vc = PresentSecondViewController()
    animation.destinationPoint = button.center
    vc.transitioningDelegate = animation
    vc.modalPresentationStyle = .custom
    animation.interaction.wireGesture(on: vc)
    present(vc, animated: true, completion: nil)
}
```

完整的範例在 [Github](https://github.com/z30262226/TransitioningDemo/tree/master)
