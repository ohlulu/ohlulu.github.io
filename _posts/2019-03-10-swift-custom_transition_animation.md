---
title: "Swift 自訂轉場動畫，手勢(上)"
date: "2019-03-10"
categories: 
  - "動畫"
tags: 
  - "animation"
  - "swift"
  - "transition"
img_path: /assets/img/post/
coverImage: "FeaturedImage.png"
---

* * *

# Demo GIF

![](DiffusionAnimation.gif)

* * *

本篇主要講述動畫的實作，手勢的部分留在下篇

動畫的原理是使用 maskView 畫出一個圓，然後對圓進行放大縮小

# 開始！

首先我們先建立一個 class 用來管理我們 present & dismiss 的動畫要透過誰來動。

```swift
class CustomTransition: NSObject, UIViewControllerTransitioningDelegate {

    // 擴散的中心點
    var destinationPoint = CGPoint.zero

    private lazy var presentAnimation = CustomPresentAnimation(startPoint: destinationPoint)
    private lazy var dismissAnimation = CustomDismissAnimation(endPoint: destinationPoint)

    func animationController(
        forPresented presented: UIViewController,
        presenting: UIViewController,
        source: UIViewController
    ) -> UIViewControllerAnimatedTransitioning? {
        return presentAnimation
    }

    func animationController(forDismissed dismissed: UIViewController)
        -> UIViewControllerAnimatedTransitioning? {
        return dismissAnimation
    }
}
```

點`UIViewControllerTransitioningDelegate` 進去看，可以發現裡面有五個 func 都是 `optional`，是這樣的，如果不實作，那系統將會使用原生的效果來顯示。

我在網路上查的多數範例，是直接在 `class CustomTransition` 實作這個 protocol 然後 `return self`，但為了讓 code 看起來簡單一點，我選擇另外建立兩個 class confirm 這個 protocol

## Present

```swift
class CustomPresentAnimation: NSObject , UIViewControllerAnimatedTransitioning {

    var startPoint: CGPoint // 擴散的起始點
    private let durationTime = 0.45 // 動畫時間

    init(startPoint: CGPoint) {
        self.startPoint = startPoint
        super.init()
    }

    func transitionDuration(using transitionContext: UIViewControllerContextTransitioning?) -> TimeInterval {
        return durationTime
    }

    func animateTransition(using transitionContext: UIViewControllerContextTransitioning) {
        // ...
    }
}
```

接著我們在 `func animateTransition` 裡面實作我的們想要的動畫效果啦

```swift
func animateTransition(using transitionContext: UIViewControllerContextTransitioning) {

        // 取出 toView, 在上面的示意圖中代表的就是 A 畫面
        guard let toView = transitionContext.viewController(forKey: .to)?.view else { return }

        // 取出 container view
        let containerView = transitionContext.containerView

        // 建立我們的 mask view，並坐初步設置
        let maskView = UIView()
        maskView.frame.size = CGSize(width: 1, height: 1)
        maskView.center = startPoint
        maskView.backgroundColor = .black
        maskView.layer.cornerRadius = 0.5
        toView.mask = maskView
        containerView.addSubview(toView)

        // 因為最終 mask view 的圓，需要覆蓋到整個畫面，所以計算出 mask view需要的大小
        let containerFrame = containerView.frame
        let maxY = max(containerFrame.height - startPoint.y, startPoint.y)
        let maxX = max(containerFrame.width - startPoint.x, startPoint.x)
        let maxSize = max(maxY, maxX) * 2.1

        // 最後我們使用 UIView.animation 顯式動畫，將我們的 mask view 擴散到整個畫面
        UIView.animate(
            withDuration: durationTime,
            delay: 0,
            options: [.curveEaseOut],
            animations: {
                    maskView.frame.size = CGSize(width: maxSize, height: maxSize)
                    maskView.layer.cornerRadius = maxSize / 2.0
                    maskView.center = self.startPoint
        }) { flag in
            transitionContext.completeTransition(flag)
        }
    }
```

* * *

## 反之，Dismiss 如下

```swift
class CustomDismissAnimation: NSObject, UIViewControllerAnimatedTransitioning {

    let endPoint: CGPoint
    var grayView: UIView!
    private let durationTime = 0.45

    init(endPoint: CGPoint) {
        self.endPoint = endPoint
        super.init()
    }

    func transitionDuration(using transitionContext: UIViewControllerContextTransitioning?) -> TimeInterval {
        return durationTime
    }

    func animateTransition(using transitionContext: UIViewControllerContextTransitioning) {
        guard let fromView = transitionContext.viewController(forKey: .from)?.view else { return }

        let containerView = transitionContext.containerView

        let containerFrame = containerView.frame
        let maxY = max(containerFrame.height - endPoint.y, endPoint.y)
        let maxX = max(containerFrame.width - endPoint.x, endPoint.x)
        let maxSize = max(maxY, maxX) * 2.1

        let maskView = UIView()
        maskView.frame.size = CGSize(width: maxSize, height: maxSize)
        maskView.center = endPoint
        maskView.backgroundColor = .black
        maskView.layer.cornerRadius = maxSize / 2.0

        fromView.mask = maskView

        containerView.addSubview(fromView)

        UIView.animate(
            withDuration: durationTime,
            delay: 0,
            options: [.curveEaseOut],
            animations: {
                maskView.frame.size = CGSize(width: 1, height: 1)
                maskView.layer.cornerRadius = 0.5
                maskView.center = self.endPoint
        }) { flag in
            transitionContext.completeTransition(!transitionContext.transitionWasCancelled)
        }
    }
}

```

* * *

## 萬事俱備，只欠東風

東西都建立好啦，接著就是如何使用了 我們在Ａ畫面建立 CustomTransition，然後將Ｂ畫面的 transitioningDelegate 改為 CustomTransition， 並將 modalPresentationStyle 設置為 .custom

```swift
class PrepareToPushViewController: UIViewController {

    let button = UIButton(type: .system)
    let animation = CustomTransition()

    override func viewDidLoad() {
        super.viewDidLoad()
        // set view layout
        }
    }
}

// MARK: Setup UI methods

extension PrepareToPushViewController {
    @objc func buttonPressed() {
        let vc = PresentSecondViewController()
        animation.destinationPoint = button.center
        vc.transitioningDelegate = animation
        vc.modalPresentationStyle = .custom
        animation.interation.wire(viewController: vc)
        present(vc, animated: true, completion: nil)
    }
}
```
