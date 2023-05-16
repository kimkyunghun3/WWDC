# UIKit: Apps for Every Size and Shape
[영상 보러가기](https://developer.apple.com/videos/play/wwdc2018/235/)

![](https://hackmd.io/_uploads/HyI3LfZBn.png)

> 모든 크기와 모양의 기기에 맞는 앱을 만드는 방법에 대해 알아보자!


<목차>
- Safe area and layout margins
- Scroll views
- Building adaptive apps


## Safe Area and Layout Margins

### Safe Area

![](https://hackmd.io/_uploads/B1h1PzWrh.png)

**safe area**란 '컨텐츠를 안전하게 배치할 수 있는 영역'을 의미.
컨텐츠를 안전하게 배치한다는 것은 무언가에 가려지지 않는다는 것이다.
iPhone 8의 경우 스크린 전체가 safe area이며, iPhone X의 경우 스크린에서 상단과 하단 일부가 제거된 영역이 safe area이다.

![](https://hackmd.io/_uploads/BJX-vMbHh.png)

iPhone의 가로 모드에서는 home indicator가 배치되는 하단 영역을 제외한 영역이 safe area가 된다.

![](https://hackmd.io/_uploads/Skh4vfZHn.png)


임의의 뷰는 top, bottom, left, right(또는 leading, trailing) 네 개의 inset이 존재한다. 
이 네 값을 `safeAreaInsets`라고 한다. 

![](https://hackmd.io/_uploads/rkDGvGWrh.png)

inset 위에 임의의 컨텐츠를 배치하면 safe area 밖에 위치하므로 가려질 것이다. 
따라서 뷰의 내부 컨텐츠를 배치하려면 반드시 safe area 내부에 배치해야 하는데, 안전한 영역인 safe area를 전체 사각형 모습으로 나타내면 다음과 같다.
그리고 이 영역을 나타내는 속성은 `.safeAreaLayoutGuide`이다. 

![](https://hackmd.io/_uploads/SyR8DMbr2.png)

subview를 추가하는데 임의로 배치한다면 이 뷰는 safe area를 침범할 수 있다. 따라서 하위 뷰를 배치할 때 safe area를 계산해서 `safeAreaInset` 만큼 떨어뜨려 배치해야 한다. 

![](https://hackmd.io/_uploads/ryHuDf-B3.png)

따라서 추가할 뷰는 (뷰 계층구조 상의) 부모에게서 inset을 상속받는다. 
이를 안전 영역 전파(Safe Area Propagation)이라고 한다.


![](https://hackmd.io/_uploads/H1BnDfWr2.png)

뷰 컨트롤러에서는 safeAreaInset을 조정할 수 있다.
view controller에서는 safe area에 대해 추가적인 작업(add 또는subtract)을 지원하기 위한 프로퍼티인 `additionalSafeAreaInsets`이 존재한다.

> additionalSafeAreaInsets
> 이 프로퍼티를 사용하여 view controller의 view의 safeAreaInset을 조정한다.

![](https://hackmd.io/_uploads/BJjTDGZBh.png)

부모의 safe area 안에 '안전하게' 배치되어 있는 하나의 뷰를 이동시켜 볼 것이다.
이 뷰를 안전 영역 밖으로 이동시킨다 하더라도 safe area는 늘어나지 않는다. 그 이유는 자식 뷰를 이동시킬 때 부모 뷰가 함께 이동하는 것이 아니기 때문이다.

따라서 뷰의 safe area는 부모가 제공하는 것보다 더 커지지 않는다.

![](https://hackmd.io/_uploads/S1F-uGbB3.png)

safeAreaInset이 변경될 때 뷰가 반응해야 하는 경우`safeAreaInsetsDidChange()`를 재정의해서 사용한다.
일반적으로는 `setNeedsLayout()`을 호출하여 뷰를 다시 작업하지만, 추가적인 로직이 들어가야할 때 사용한다.

```swift
override func safeAreaInsetsDidChange() {
    //
}
```

또한 뷰 컨트롤러에서 뷰의 safeAreaInset 변경에 응답해야 할 경우 `viewSafeAreaInsetsDidChange()`를 재정의한다.
![](https://hackmd.io/_uploads/H1ugyJuV2.png)


마지막으로 Interface Builder를 사용하는 경우, `Safe Area` 속성을 통해 안전 영역에 대한 제약 조건을 구축할 수 있다.


### Layout Margins

![](https://hackmd.io/_uploads/S127_fZr3.png)

layout margins는 패딩을 의미한다. 일반적으로 사용자가 완전히 제어할 수 있으며 뷰의 가장자리에서 여백을 지정하는 방법이다.

`.layoutMargins`와 `.directionalLayoutMargins`의 차이
- `.layoutMargins`: UIEdgeInsets 값인 left/right를 사용
- `.directionalLayoutMargins`: NSDirectionalEdgeInsets 값인 leading/trailing을 사용

![](https://hackmd.io/_uploads/B1wSuMZBh.png)

기본적으로 safe area를 먼저 구축한 다음, 그 뒤에 margin이 적용된다. 즉 margin은 안전 영역 위의 추가 패딩을 의미한다.

![](https://hackmd.io/_uploads/SyqIdMZH3.png)

이 기능은 `.insetsLayoutMarginsFromSafeArea` 프로퍼티를 통해 제어되는데, 기본값이 true인 이 프로퍼티를 false로 변경하면 margins의 기준점이 safe area에서 뷰의 bound로 변경된다.


![](https://hackmd.io/_uploads/HJGudMWB2.png)

Safe Area와 마찬가지로 여백 전파(Margins Propagation)이 발생한다.
그러나 이 기능은 선택적이다. 하위 뷰가 상위 뷰의 margins를 그대로 준수하고자 한다면 `.preservesSuperviewLayoutMargins`의 값을 true로 변경하면 된다.(기본값은 false)
그림과 같이 뷰 계층 구조를 통해 하위 뷰를 추가하지만, 레이아웃이 독립적으로 배치할 때 여백 전파를 무시할 수 있을 것이다.

([preservesSuperviewLayoutMargins](https://developer.apple.com/documentation/uikit/uiview/1622653-preservessuperviewlayoutmargins))

![](https://hackmd.io/_uploads/S1YuFMZSh.png)

시스템에서 사용하는 margin에는 뷰 컨트롤러에서 루트 뷰에 제공하는 최소한의 여백을 나타내는 `systemMinimumLayoutMargins`이 있다.

![](https://hackmd.io/_uploads/Sy_ttz-H3.png)

이것에 대한 제어를 할 필요가 있다면, `viewRespectsSystemMinimumLayoutMargins`의 값을 false로 변경하여 systemMinimumLayoutMargins의 적용을 무효화 시키면 된다.

![](https://hackmd.io/_uploads/Sk4cKz-Bn.png)

앞에서 봤던 safe area와 마찬가지로, margins를 활용하고 제어하는 다양한 방법들이 있다. layoutMargins이 변경되는 경우에 대한 로직을 추가해야 하는 경우 `layoutMarginsDidChange()`를 재정의하고 로직을 수행한다.


## Scroll views

![](https://hackmd.io/_uploads/H1nitG-Sn.png)

iOS에서의 사용자 경험을 이야기할 때 스크롤 뷰는 빠질 수 없는 요소이다. UICollectionView나 UITableView, UITextView 그리고 앱의 여러 커스텀 요소에 포함된다.


![](https://hackmd.io/_uploads/SJC2Yfbr2.png)

왼쪽은 Device에서 사용자에게 표시되는 화면, 오른쪽은 Behind Story이다.

- 스크롤 뷰의 높이가 400이라고 할 때, 내부의 컨텐츠가 500으로 초과한다면 세로 스크롤이 가능해진다. 따라서 스크롤이 가능해지는 조건은 컨텐츠가 스크롤 뷰의 높이보다 더 큰 경우라고 할 수 있다.

- 스크롤 뷰 안에서 현재 스크롤 위치를 나타내는 방법: `contentOffset`. top-leading position을 기준(x:0, y:0)으로 사용한다.

![](https://hackmd.io/_uploads/rJFkcMWS3.png)

스크롤 뷰의 기능 확장은 대부분 contentInset 라는 속성을 활용한다.
contentInset이란 스크롤 가능한 영역부터 내부 컨텐츠까지의 Inset을 제공할 수 있는 매커니즘이다. contentInset의 존재로 인해 스크롤 뷰의 contentOffset의 좌표가 음수가 된다. 

이 기능을 중요하게 짚고 넘어가는 이유는, iOS 7에서 스테이터스 바를 제외한 내비게이션 바, 탭 바, 툴바, 서치바 등이 반투명하게 바뀌었기 때문이다.
컨텐츠가 스크롤되면서 반투명한 바 아래에 흐릿한 효과와 함께 표시되고 스크롤을 사용하는 사용자 입장에서는 컨텐츠의 끝을 알 수 있어야 했다. 

![](https://hackmd.io/_uploads/HktZ9zWr3.png)

그렇기 때문에 ViewController의 내부 프로퍼티로 `automaticallyAdjustsScrollViewInsets`가 존재했었다.
이 속성은 뷰 컨트롤러가 자동적으로 스크롤 뷰의 Inset을 조정하게끔 하는 속성인데,
[이런 문제점들이 있었다고 한다.](https://m.blog.naver.com/jegumhon/220862079502)

![](https://hackmd.io/_uploads/H1MmqGbBh.png)

이제는 이 속성은 deprecated되었고 대체하는 `adjustedContentInset: UIEdgeInssets`가 추가되었다.
adjustedContentInset을 쉽게 설명하면,
**adjustedContentInset = contentInset + systemInset**


![](https://hackmd.io/_uploads/SyfIcGWB3.png)

일반적인 케이스는 safeAreaInsets이다.
스크롤 뷰의 top edge에 safeAreaInset가 추가되어 있다. 

![](https://hackmd.io/_uploads/rJTD5f-Sn.png)

그렇게 하면 adjustedContentInset은 조정된 safeAreaInset를 자동으로 흡수한다. 따라서 스크롤 가능한 영역을 늘리고 컨텐츠가 바 아래에 가려지지 않게 한다.

![](https://hackmd.io/_uploads/H1ZFcGbB2.png)

다음은 스크롤 뷰에 하위 뷰를 하나 추가했다. 추가된 뷰는 앞서 말했던 safe area 전파를 통해 스크롤 뷰의 top edge의 safeAreaInset을 보게 되고 스크롤을 통해 아래로 내려가면 안전 영역을 초과하게 될 것이다.

그러나 실제 동작은 그렇지 않다. 이 또한 adjustedContentInset 덕분인데, safeAreaInset를 전파할 때 동일한 inset을 하위 뷰로 두 번 전파하지 않기 때문이다.

결론적으로 삽입된 Subview는 스크롤 뷰에 safeAreaInset이 있다는 것을 전혀 알지 못하게 된다.

> 이 부분을 여러 번 돌려봐도 잘 이해가 안되었는데, 스크롤 뷰에 안전한 컨텐츠 표시를 위해 추가되는 Inset이 20이라고 한다면 adjustedContentInset은 contentInset + systemInset이기 때문에 20 + '부모로부터 상속받은 Inset'(근데 부모도 스크롤 뷰에 있으므로 20이 늘어난 상태) = 40이라는 Inset을 가져야 함. 그러나 adjustContentInset은 스크롤 뷰 자체에 추가된 Inset 20을 안전 영역 전파를 통해 하위 뷰에 전달하지 않고 한 번만 추가해주기 때문에 결과적으로 20의 Inset을 갖는다는 뜻인 듯 하다.

![](https://hackmd.io/_uploads/H1_jqM-H3.png)

위 매커니즘은 UIScrollView의 하위 속성인 `contentInseAdjustmentBehavior`에 의해 수행되며 기본값은 `Automatic`이다. 옵션은 총 4가지(`always`, `scrollableAxes`, `automatic`, `never`)가 있는데, 특별한 경우가 아니라면 기본값을 변경하지 않는 것이 좋다.


## Building adaptive apps

![](https://hackmd.io/_uploads/ry2vhzWB3.png)

이 화면에는 현재 3개의 View Controller가 있다.
이들은 서로 겹치지 않아야 한다. 따라서 각각의 safe area는 서로 다르게 적용된다. 이러한 safe area의 계층 구조는 뷰 컨트롤러의 계층 구조를 따라 아래로 전파된다.

![](https://hackmd.io/_uploads/ryl93zZr3.png)

화면을 구현할 때 뷰를 그릴 수 있는 영역으로는 전적으로 safe area만이 주어진다. 구동하는 장치가 iPhone X인지 8인지 알지 못해야 하며 심지어 컨테이너 뷰 컨트롤러에 포함되어 있다는 사실조차 알지 못해야 한다.

![](https://hackmd.io/_uploads/S1Fj2G-Bn.png)

스테이터스 바를 숨기는 것은 20포인트를 사용 가능한 영역으로 회수하는 것과 같다. `prefersStatusBarHidden` 속성값을 재정의하여 true로 전환하여 스테이터스 바를 숨길 수 있다.

![](https://hackmd.io/_uploads/rkeah2MWrn.png)


아이폰 X 이상 모델에서 스테이터스 바를 숨기는 기능은 센서 하우징까지 숨기지 않는다. 따라서 스테이터스 바를 숨기는 UI를 구현하고자 한다면 내비게이션 바까지 함께 숨겨야 한다.
일반적으로 몰입형 환경을 만들고 싶다면 모든 오버레이와 컨트롤을 함께 숨기는 것이 좋다.

![](https://hackmd.io/_uploads/ByzChMZB2.png)

'읽을 수 있는 범위'란 뷰 내에서 한 눈에 읽을 수 있는 적절한 영역을 의미한다. UIView의 `readableContentGuide` API를 사용하여 얻을 수 있다. 이 크기는 사용자가 선택한 동적 타입에 따라 달라진다.


![](https://hackmd.io/_uploads/HJxWpfWS3.png)

readableContentGuide가 TableView에도 어떻게 적용이 되는지 알아보자. TableView는 layout margins를 사용한다. 테이블 뷰에서는 `cellLayoutMarginsFollowReadableWidth` API를 제공한다.

이 속성은 iOS 12 이전까지는 true가 기본값이었으나, iOS 12에서 false로 변경되었다. 

![](https://hackmd.io/_uploads/HysXTGWB2.png)

TableView의 Cell을 보면, cell의 컨텐츠가 safe area를 침범하면서까지 확장되지는 않는 것을 볼 수 있다. 
기본적으로 cell은 배경 뷰의 아래에 컨텐트 뷰가 존재하고 우리는 이 영역을 사용한다. 

![](https://hackmd.io/_uploads/H1urTMbH3.png)

TableView의 insetsContentViewsToSafeArea를 false로 변경하면 cell의 컨텐츠를 바깥 영역에 배치할 수 있다.


![](https://hackmd.io/_uploads/rk1waGbSh.png)

마지막으로 safe area 또는 화면 가장자리에 가까운 위치 지정과 버튼에 관한 내용이다.
우선 일반적으로 사용하는 패딩을 추가해준다.
그 다음으로 bottom에서 safe area 하단까지의 부등식 제약 조건을 걸어준다.
