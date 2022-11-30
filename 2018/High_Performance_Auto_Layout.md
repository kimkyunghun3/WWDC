# High Performance Auto Layout

[WWDC 바로가기](https://developer.apple.com/videos/play/wwdc2018/220/)

## 목표: iOS 12 에서 레이아웃 성능 향상을 위해 어떤 것을 제공할까?
view, constraints를 사용해서 레이아웃을 설정한다.
* Constrints는 무엇인가? 뷰 간의 관계를 정의한다.

* 직관(intuition) << analyze(intuition -> practice)

## 개선 사항 미리보기: UICollection에서 self-sizing view

iOS 11에서는 self-sizing이 제대로 적용되지 않았지만 12에서는 개선했다.

![](https://i.imgur.com/DGGvM2W.png)
시간도 더 적게 걸린다.
(파란색이 iOS 12, 회색이 iOS 11)

## 문제 제시
![](https://i.imgur.com/M9IMGgL.png)
IB로(Interface Builder => storyboard) 레이아웃 작성했다고 가정해보자!
위 레이아웃 세팅은 아무런 문제가 없다!

![](https://i.imgur.com/WijsDl6.png)
위 코드처럼 작성하면 문제가 있다!
* 문제1: UIView 메서드인 updateConstraints를 오버라이딩하고 있다.
* 문제2: 기존 레이아웃을 모두 deactivate 시키고 새로운 제약을 추가한다.
    * super.updateConstraints 호출은 매우 중요

먼저 updateConstraints가 무엇인지 알아보자!

![](https://i.imgur.com/8mytkcw.png)
![](https://i.imgur.com/S9vXLyJ.png)


updateConstraints는 Render loop의 구성요소 중 하나이다.
Render loop는 무엇인가?
contents가 각 frame에 잘 자리 잡도록 최대 초당 120번 돌아갈 수 있는 process이다.
총 3단계로 이뤄져있다.(Update Constraints, Layout, Display)
1. Update Constraints
* 먼저 모든 view는 updateConstraints를 받는다.(receive)
* view 계층 하단에서(leaf) 부모로(window)
2. layout
* layout을 호출
* window에서 leaf로
3. Display
* draw 호출
* window에서 leaf로

## 왜 이렇게 하나?
![](https://i.imgur.com/gx69cHT.png)

쓸데없는 일을 하지 않도록(avoid wasted work)

### 예를 들어보자
UILabel은 text size가 필요하다.
text property, font, text size 등이 있다.
하나의 방법: 하나의 프로퍼티라도 변경되면 다시 측정(measure)해서 확인한다.
하지만, 위 방법은 보통 비효율적이다.(이유: 순차적으로 다 불릴 것이다.)
해결: setNeedsUpdateConstraints 호출하자 -> frame이 screen으로 가기 전 가장 마지막에 constraints를 갱신할 것을 보장한다.

### "왜 이렇게 하나? 정리
render loop는 너무 많이 반복(120번/s)
메서드들이 병렬적으로 동작한다.

### 다시 문제로 돌아와서 해결하자

![](https://i.imgur.com/lTKNGxH.png)
모든 레이아웃을 deactivate하고 activate하는 것(= churning the constaints)의 문제가 뭘까?

![](https://i.imgur.com/wMU320f.png)
모든 subviews를 제거하고 다시 subviews를 넣는 것과 동일하다. => 즉, 성능에 나쁘다

![](https://i.imgur.com/FeoJOsW.png)
마지막에 딱 한번만 하도록 수정하자

render loop로 불필요한 일을 하지 않아서 좋지만 "너무 자주 동작하는 코드"라서 세밀하게 다뤄야한다.

IB를 사용하면 알아서 해주기 떄문에 신경쓸 필요가 없다!

## constraints를 추가하면("activate") 내부적으로 무슨일이 일어나는지 알아보자
![](https://i.imgur.com/AcUaZL6.png)

레이아웃을 설정하려는 view가 있다.
view는 window에 있다.
window는 engine을 가진다.
engine은 autolayout의 핵심이다
view에서 constraints를 만들면 equation이 생성된다.
equation는 engine에 추가된다.
equation는 variable과 관련되어 있다.

![](https://i.imgur.com/elGORO2.png)
ex. equation에서 x가 뭔지 알려달라고 하면 view의 freme 데이터를 알아야한다.
frame에는 minX, minY, width, height가 있다.

### 간단히 살펴보기 위해 수평 레이아웃만 살펴보자!
![](https://i.imgur.com/UmIR14G.png)

먼저, equation을 만든다.
두 view 간의 간격에 대한 equation이 이상해 보일 수 있지만, variable관점에서의 방정식이다!

이제 각 equation은 engine에 추가돼야한다.
engine은 variable을 풀기 위해 노력한다.

## engine 내부를 살펴보자
![](https://i.imgur.com/KYHnKJC.png)
variable을 표함한 equation의 경우 subtitutes를 찾아서 값을 찾아낸다.
즉, 아래처럼 치환된다.
![](https://i.imgur.com/JNDcefP.png)

=> engine에서 variable 해석하는 것은 이제 끝났다.(layout은 아직 아니다.)

## 이제 알게된 variable을 가지고 process를 끝내보자!

![](https://i.imgur.com/sGNzu5t.png)

이때까지는 upateConstraints 단계에서 일어나는 일!
엔진 내부에서 variabe이 뭔지 알면 view에게 값이 바뀌었다고 알린다.
그러면 view는 superview의 setNeedsLayout()을 호출한다.
그러면 나중에 layout 단계로 이동한다.

![](https://i.imgur.com/zl2WVgO.png)
engine에서 해당 데이터들(위에서 계산해서 얻은 값들)을 frame에 복사한다.
그러면 subView는 setBounds(), setCenter()를 호출한다.
레이아웃 프로세스 끝!

## 위 문제로 다시 돌아가자
churning!(쓸데없이 같은 연산 반복)
개별적인 process는 간단하지만 불필요한 동일한 작업이 매번 반복된다.

## 뷰를 추가해서 문제를 다시 살펴보자
![](https://i.imgur.com/VwdvaOf.png)
이제 뷰가 4개인 상황
동일한 superView가 없는데 상대적인 constraints 설정 -> 성능상 나쁘다.

일반적인 경우는 부모, 자식 사이에만 제약을 설정한다.

![](https://i.imgur.com/LN1cde9.png)
![](https://i.imgur.com/tlpO1Bi.png)


2개의 독립적인 engine을 형성한다.
독립적으로 equation을 계산한다.
variable도 겹치는 것(overlap)이 없고, 상호작용(interact)도 하지 않는다.

![](https://i.imgur.com/nOFau7r.png)


서로 겹치는 부분이 없으면(의존성이 없어서) 상호작용하지 않아서, linear하게 성능에 영향을 미침
즉, 2개면 2배로 걸리는 것! 3개면 3배!...

![](https://i.imgur.com/HiSsfCa.png)
레이아웃 엔진은 특정 레이아웃을 업데이트하면 딱 필요한 것만 업데이트한다.

![](https://i.imgur.com/J5Zms65.png)
제약 설정을 줄이려는 노력을 하지 말라, 의미없다.
필요한 제약을 그저 세팅하면 됨

![](https://i.imgur.com/yZJrGLK.png)
![](https://i.imgur.com/hf8eZcR.png)
![](https://i.imgur.com/IGSbAJ9.png)

2개의 제약조건을 하나로 표현하다보니 전체적으로 불명확하고 복잡한 제약이 구성된 상태
우선순위도에 따라 여러 레이아웃을 설정한 상태
이것도 좋은 생각은 아니다.
가짜 의존성을 만들게 되는데, 네가 레이아웃 오류를 발견 못하면 debug도 알려주지 못한다.

결론: 최대한 직관적으로(directly) 제약을 설정하라
natural way로 제약을 설정하라! 그래야 퍼포먼스에도 좋고, 이해하기도 쉽다.

## 다른 것들에 대한 성능도 알아보자
### inequality(이하, 이상)
동등 비교와 차이가 없다.
정확하게는 one more variable의 차이

### set constant
engine이 dependancy tracker라서
performance 최적화로 레이아웃을 자주 바꿔도 신속히 업데이트가 된다.

### priority
이건 위에 것들과 다르게 조금 더 cost가 든다.
priority를 부여하는 것은 `특정 사이즈 + error`를 요청하되 error 를 최소화하라고 하는 것과 동일하다.
즉, error 최소화하는 단계가 추가적으로 필요한 것이다.

![](https://i.imgur.com/nJsInAI.png)

## 정리
레이아웃이 반복적으로 세팅되지 않도록하라(nil 확인)
레이아웃 계산은 간단하다.
Auto layout은 layout을 위한 cache이다.
즉, engine은 계산된 값들을 가지고 있고 dependency tracker라서 어떤 값이 바뀌면 딱 바꿔야할 것들만 변경(super targeted way)

## intuition to practice(analyze)
직관만 믿고 레이아웃을 작성하면 성능에 나쁠수도...

## tableViewCell 예시로 복잡한 레이아웃 구성을 잘 설정하는 법에 대해 알아보자
<img src="https://i.imgur.com/PGbbOm4.png" height="500">

![](https://i.imgur.com/CAyyDJx.png)

![](https://i.imgur.com/1YvepPB.png)

![](https://i.imgur.com/8lxMA3U.png)

![](https://i.imgur.com/aKTI0uK.png)

4개의 비슷한 레이아웃을 구성해야하는 상황

-> scroll performance가 좋지 못하다.

### performance를 자세히 살펴보자
![](https://i.imgur.com/OSs3nWj.png)
사용되는 CPU 개수를 알 수 있다.
peak이 높으면 성능이 좋지 않은 것
얼마나 많은 view가 영향을 받고 있는지를 보여준다.

여러 peak이 있지만 churning이 유독 높은 것을 살펴보자

![](https://i.imgur.com/jnijIKZ.png)

detailView를 살펴보는 것

![](https://i.imgur.com/Ishg4tR.png)

label 3개가 churn 되는 중인 것을 확인할 수 있다.

### 지금은 어떤 상황이냐면
updateConstraints가 override 된 상태이다.
레이아웃이 바뀌면 모든 view의 레이아웃을 제거했다가 다시 배치시켜서 발생하는 성능적 issue

![](https://i.imgur.com/xr5okdw.png)
위 상태는 모든 레이아웃이 없어진 상태

![](https://i.imgur.com/vEnpM1E.png)


### hidden으로 레이아웃 issue 줄여보자
![](https://i.imgur.com/dGYQgeF.png)
기존 레이아웃에서 하나의 뷰에 대한 interaction -> 모든 뷰 레이아웃을 없앨 필요가 있을까?
![](https://i.imgur.com/PbKASH8.png)

hidden으로 처리하자(view 계층에서 해당 view를 제거하는 것보다 숨기는 것이 훨씬 cheap)

### 특정 제약만 활성화, 비활성화
모든 뷰가 기존과 같은 위치에 있는데도 동일한 연산을 진행하기 때문에 churn이 발생하는 것!

![](https://i.imgur.com/X9SArMG.png)

초록색 선은 기존이랑 동일하기 때문에 건드리리 말자

![](https://i.imgur.com/wL7dBTg.png)

노란색을 deactivate하고 빨간색을 activate


### UILabel이 본인 size 계산할 때 걸리는 시간이 어떻게 되는지 알아보자
![](https://i.imgur.com/aPYfYWY.png)
UIImageView는 내부의 image 사이즈를 통해 intrinsic content size를 계산한다.
UILabel은 text를 사용한다.

![](https://i.imgur.com/mUV8bg0.png)
text measurement가 오래 걸리는 경우는 overriding하는 것이 더 빠르다.
위 코드는 text measurement가 없이 부모 view에게 size를 알려주면서 성능을 높이는 방법
* [nointrinsicmetric 공식문서](https://developer.apple.com/documentation/uikit/uiview/1622486-nointrinsicmetric)

### Intrinsic content size vs system layout size fitting size
![](https://i.imgur.com/Loyql0m.png)

전자는 engine 안에 값을 넣을 때
후자는 engine에서 size information을 꺼낼 때 사용
둘은 반대 개념!
=> 후자는 사용할 일이 잘 없지만 성능상 좋지 않아서 조심해야한다.

## 왜 좋지 않을까?
![](https://i.imgur.com/UpsTnM4.png)
system layout size fitting size 호출할 때마다 engine이 생성되고 없어진다.
self-sizing collection 이나 table view cell에서 content view로 호출할 때 사용하는데, 이러면 scrolling 최적화를 무시하고 따로 overriding하는 거 + 추가적인 engine을 생성하는 것이라 성능에는 좋지 않다.

![](https://i.imgur.com/r2I6Ifn.png)


### 참고: instruments -> 성능 확인 tool 관련 링크
* https://www.raywenderlich.com/16126261-instruments-tutorial-with-swift-getting-started
* https://zeddios.tistory.com/522
* https://zeddios.tistory.com/529?category=682196
* https://zeddios.tistory.com/523

