# wwdc: Customize and resize sheets in UIkit

## sheet란?
[HIG: Sheet](https://developer.apple.com/design/human-interface-guidelines/components/presentation/sheets)
Apple의  human interface guidelines 에 따르면, 

> A sheet helps people perform a scoped task that’s closely related to their current context.
> 시트는 사람들이 현재 작업와 밀접하게 관련된 범위가 지정된 작업을 수행하도록 도와줍니다.

라고 한다. 
![](https://i.imgur.com/hdwqoJj.jpg)

iphone 에서 특정 작업을 할 때, 새로운 뷰가 올라오면서 원래 있던 뷰에 반투명 회색 레이어가 깔리는 듯한 연출이 되던 객체. 그게 바로 sheet 이다.
바로 위의 사진과 같은 것들...

iOS15 부터는 이 sheet 를 customize 할 수 있는 다양한 옵션들은 한다. 
* 세로 detent 를 조정
* dimming view 제거
* corner radius 설정 
* grab indicator 설정 
* landscape 일때, sheet 의 옵션 

하나씩 살펴보자.

## Detent 조정하기

우선 `UISheetPresentationController` 사용해서 sheet 를 만들어 보자.

![](https://i.imgur.com/xN7V42V.png)


detent 의 사전적인 정의를 찾아보면 

> detent 멈춤쇠 

라고 나오는데, sheet 의 멈춤쇠(높이를 움직일 수 있는 회색 가로 바 같은 것) 가 있는 위치를 말하는 것이니, sheet 의 height 를 의미한다고 봐도 된다. 
영상에도 기본적인 높이를 의미한다고 소개한다.

![](https://i.imgur.com/FaXnhdz.png) 
![](https://i.imgur.com/0YpYKOf.png)
![](https://i.imgur.com/wB94q4O.png)

화면에 따른 비율로 높이가 결정 된다. 
`.medium`, `.large` 가 존재한다. 

![](https://i.imgur.com/apQs2w2.png)

detent 는 배열을 통해서 받는다. 
이유는 `.medium` 처럼 하나의 detent 값만 줄 수도 있지만, 
위에와 같이 2가지 값을 다 주게 되면, 손가락 터치에 따라서 detent 가 커졌다가 작아졌다 하는 것이 가능하다. 

---

화면에 이미지가 들어가 있는 카드가 존재하고 picker 에서 이미지를 선택하면 카드의 이미지가 바뀌는 샘플 앱이 있다. 

![](https://i.imgur.com/x6dkzlJ.png)
위와 같은 코드를 작성했다. 

![](https://i.imgur.com/K5HZMi4.png)
위와 같이 앱이 작동한다. 

그런데 우리는 변경 되는 이미지를 바로바로 확인하고 싶다.

![](https://i.imgur.com/Tppvb2k.png)
`.medium` detent 를 추가하고 dimiss 되는 코드를 삭제 해보자.


![](https://i.imgur.com/BTjU2ac.png)
이제 sheet 가 떠있어도 뒤에 뷰가 보이고, dimiss 되기 전에도 카드의 이미지가 바뀐다. 

그리고 detent 배열에 `.large` 옵션도 존재하기 때문에 sheet 를 크게 키울 수도 있다. 

## `preferScrollingExpandsWhenScrolledToEdge`

그런데 스크롤 뷰라서, 스크롤을 할 시 무조건 sheet 가 `.large` 로 움직이게 된다. 

![](https://i.imgur.com/KJOQZ4d.png)
`preferScrollingExpandsWhenScrolledToEdge` 옵션을 false 로 바꿔주면 스크롤 할 때 sheet 가 엣지로 가는 것을 막아준다. 
(이 옵션의 default 는 true 이기 때문에 명시적으로 바꿔주어야 한다.)

이제 sheet 의 사이즈는 키우지 않는 상태에서도 스크롤을 할 수 있어졌다!

## `selectedDetentIdentifier`

근데 이제 새로운 문제가 생겼다. 
바로 large 모드에 가면 사진을 선택한 것을 바로 볼 수가 없어진다. 

large 모드에서 이미지를 선택하면, 자동으로 medium 모드로 가도록 변경해보자. 

![](https://i.imgur.com/Bkj74UT.png)
이 코드를 추가해 주면 된다. 

그럼 이제 large 일 때 사진을 선택하게 되면 sheet 가 자동으로 medium 으로 바뀐다. 
## `sheet.animateChanges`

근데 애니메이션이 없어서 너무 부드럽지 못하게 바뀐다.

![](https://i.imgur.com/sfOf46t.png)
애니메이션 효과를 주고 싶으면 이렇게 해주면 된다. 

## remove dimming view

이제 dimming view 를 없애보자.

dimming View 라는 것은 sheet가 올라올 때 회색이 되는 뷰를 말한다. 회색이 되면 아무런 터치도 먹지 않고, 뷰와 인터렉션을 할 수 없다. 
dimming 을 없애서, sheet 가 올라와 있는 상태에서도 인터렉션을 할 수 있도록 하자.

![](https://i.imgur.com/fpXsnnE.png)
이렇게 설정을 해주면, 여전히 large 일 때는 dimming 현상이 일어나지만, medium 일때는 일어나지 않는다. 

![](https://i.imgur.com/9fDA3OJ.png)

이렇게 만들면, 아래서 sheet 로 font 를 바꾸면서 font text 를 동시에 바꾸는 것이 가능하다. 


![](https://i.imgur.com/GUY3pjj.png)
sheet 의 search bar 를 달게 되면, keyboard 사용해야 할때는 자동으로 sheet 의 크기를 키우고, 아닐 때는 medium 사이즈로 바꿔준다. 

## landscape option
landscape 모드일 때는 기본적으로 꽉찬 sheet 가 나타난다. 
![](https://i.imgur.com/qBTym6U.png)
이제는 아래와 같은 작은 모양의 sheet 도 지원한다. 
![](https://i.imgur.com/nU7ZXju.png)

![](https://i.imgur.com/KwjJkBr.png)
withFollowsPreferredContentSizeWhenEdgeAttached = true

라는 옵션으로 sheet 의 옵션을 설정할 수 있다. 

## show grabber
sheet 의 잡고 움직이는 것 같은 회색 가로를 grabber 라고 한다. 
이것도 보이게도 숨기게도 할 수 있다. 

![](https://i.imgur.com/i1hajZl.png)
```swift
sheet.preferGrabberVisible = true
```

## corner Radius 
코너둥글기를 설정할 수도 있다. 
![](https://i.imgur.com/xrbDrN9.png)
```swift
sheet.preferredCornerRadius = 24.0
```

## adapt a popover to a customized sheet
ipad 의 경우 popover 를 뷰의 사이즈에 따라서, sheet 로 변경할 수도 있다.
![](https://i.imgur.com/Y6mIG2i.png)

![](https://i.imgur.com/RgKPBqk.png)

![](https://i.imgur.com/kqT77Xu.png)
![](https://i.imgur.com/gbR3qVD.png)
이렇게 하면 변경 가능하다. 
그런데 또 large 에서 이미지 선택하는데에 위에서와 같이 안보이는 이슈가 생기면 
![](https://i.imgur.com/zBpIm0h.png)
위의 코드를 추가해서 medium 으로 낮춰주면 된다. 

sheet 의 관한 다양한 옵션들이 iOS15에서 추가 되었다. 
