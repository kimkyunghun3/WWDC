# Mysteries of Auto Layout, Part 1

[**WWDC Mysteries of Auto Layout, Part 1**](https://developer.apple.com/videos/play/wwdc2015/218/)

> Auto Layout is a powerful constraint-based layout engine that can drive complex and dynamic interfaces on both iOS and OS X.

AutoLayout은 constraint-based 레이아웃 엔진이다.

## StackView

StackView는 자식 레이아웃을 알아서 관리하기 때문에 제약을 간소화할 수 있다.
stackView로 axis에 따른 alignment와 distribution을 설정할 수 있다.

### alignment

* horizontal
<img src="https://i.imgur.com/ixRacmK.png" height="300">

*  vertical
<img src="https://i.imgur.com/3RlBUC0.png" width="400">

### distribution

<img src="https://i.imgur.com/oyG3IR7.png" height="300">

### demo: buid와 maintain 이 용이

* **목표**
<img src="https://i.imgur.com/jvBhhVn.png" height="400">

복잡한 제약을 가지는 UI를 StackView를 통해서 간단히 표현해보자!

* **최상위 StackView와 상위 뷰와의 오토 레이아웃만을 설정한다.**

* **문제 상황 1** : Segmented Control이 늘어났다.

<img src="https://i.imgur.com/8iu7Dqb.png" height="400">

* 이유: 모든 priority가 동일하다.
* 해결1: segmented control의 content-hugging priority 와 content compresstion priority를 올린다.

* **문제 상황 2**: 레이블과 별점 사이에 새로운 버튼을 추가하라는 요청!

* 해결2: StackView에 버튼 drop!
<img src ="https://i.imgur.com/oDE9r16.png" width ="400">

### animation 적용 쉽다.

레이아웃이 망가지지 않고 애니메이션이 적용된다.  
<img src ="https://i.imgur.com/UUIZjtJ.png" width ="400">

스택뷰의 장점은 아래와 같다

- 만들기 쉽다
- 유지하기 쉽다
- 많은 구성요소를 넣을 수 있다
- 가볍다(모든 부분 background render할 필요 없고 특별한 transform Layer class가 render 할 필요 없게 해준다. 그래서 이게 스택뷰를 빠르게 동작하도록 해준다)


## layout engine
  
<img src ="https://i.imgur.com/SvPgCj1.jpg" width="400">

* 뷰에 제약조건을 추가 삭제하는 것 보다는 activate, deactivate를 하는 것이 더 좋다!

* 장점
contraint가 container를 알아서 찾아간다.
더 효율적이다.
레이아웃 조절을 위해 모든 뷰를 알 필요가 없다.

* add, remove 했을 때 문제 예시

예를 들어 아이패드앱인데 두개의 앱을 켜놓고 add, remove로 하도록 했다.

그랬더니 두개의 앱중 하나가 이상하게 나오고 그것을 다시 보이게 했더니 이상하게 나온다.

<img src ="https://user-images.githubusercontent.com/52434820/184474058-32361519-532e-4235-8169-6f24473f97ae.png" width="400">

이처럼 이상하게 나오는 것을 확인할 수 있다. 그러므로 remove로 하게된다면 constarints가 이상하게 바뀐다.

모든 레이아웃을 deactivate(`deactivate self.view.constraints`)하지 마라! -> 레이아웃이 이상하게 잡힌다.  
<img src ="https://i.imgur.com/sLEWTIM.png" width ="400">


대신에 조절하려는 참조값을 가져라!  
<img src ="https://i.imgur.com/88MO0fD.png" width ="400">


## view sizing: intrinsic content size

<img src="https://i.imgur.com/z7Rhxgb.png" height = "400">

contents에 맞게 사이즈를 결정한다.

-> 정확한 레이아웃 사이즈를 보장하지는 못한다.
-> 이유: adaptability 떄문이다. 이것이 strectch, shirnk 뷰를 하기 때문에 보장할 수 없다.

* intinsicContentSize를 override해서 사용할 수 있는 3가지 요인들이 있다.

1. 사이즈 정보가 constraints에서부터 오지 않을 경우에 사용한다.
2. 만약 뷰가 커스텀으로 drawing될 때 사용한다.
3. invalidate를 책임져야할 떄 사용해야한다. 왜냐하면 이것이 dynamic size, localization에 따라 사이즈가 동적으로 변하기 떄문이다.

## self sizing table view cell

문제:   
<img src="https://i.imgur.com/iDSIZCq.png" height="300">

해결: 레이블 레이아웃 조절: top space to container margin & bottm space to container margin

여전히 문제 존재: 레이블이 충분히 크지 않아서 이미지가 잘림  
<img src="https://i.imgur.com/TA9oxXA.jpg" height="400">

해결: 레이블이 최소한 이미지 높이는 되어야 한다는 조건이 필요하다!

이미지와 레이블 높이를 맞춘다.  
<img src="https://i.imgur.com/wZQQPBm.png" height="300">

관계식이 ">="이 되도록 변경  
<img src="https://i.imgur.com/nIB1C6n.png" height="400">

해결:   
<img src="https://i.imgur.com/bbNVc9U.png" height="400">


## ambiguity: priority

ambiguity는 언제 발생?
* 레이아웃 조건이 부족할 때
* equal-nonrequired priority
=> layout engine이 임의로 레이아웃을 선택  
<img src="https://i.imgur.com/CwldJdq.png" height="400">
<img src="https://i.imgur.com/QbcXFTo.png" height="400">

레이아웃 수치가 같으면 모호함!  
<img src="https://i.imgur.com/sE8Am2r.png" height="400">

=> view content 우선도를 required 하지마라
ex. localization에 대응 못함

기본적으로 세팅하는 것을 요구하지 않는다. 그러나 이는 만족스럽지 못한 constraints를 보여줄 수 있다.  
<img src ="https://i.imgur.com/tOIrlVR.jpg" width="400">

문제: 기본 content hugging 우선도가 250으로 동일 -> 레이아웃 엔진은 뭘 선택할지 몰라서 발생하는 문제  
<img src ="https://i.imgur.com/VDP0oSu.png" width="400">

해결: 버튼의 content hugging을 낮춘다.  
<img src ="https://i.imgur.com/wZBn1BS.png" width="400">

문제: 기본 compression resistance가 750으로 동일  
<img src ="https://i.imgur.com/2uV8KLU.png" width="400">

해결: 모든 text가 보인다!  
<img src ="https://i.imgur.com/Yvp4rTk.png" width="400">

## text alignmnet

1. baseline

first baseline: 첫 줄의 글자 아래 부분  
<img src ="https://i.imgur.com/YOqP7NY.png" width="400">
=> 텍스트 줄이 늘어도 버튼의 위치는 동일

last baseline: 마지막 줄의 글자 아래 부분  
<img src="https://i.imgur.com/IzX6fEG.png" height="150">

2. leading & trailing > right & left
=> localization(글 작성 방향에 따라)

아랍어 같은 부분에서 오른쪽에서 왼쪾으로 읽으므로 지역화에 대응되어있지 않다  
<img src ="https://i.imgur.com/cflBVYT.jpg" width="400">

leading, trailing으로 해주면 다른 나라에 맞게 설정해줄 수 있다  
<img src ="https://i.imgur.com/g9d3aqy.jpg" width="400">

3. Alignment Rect

보통은 Frame과 동일 

=> alignmnetRectInset 오버라이딩 해라  
<img src="https://i.imgur.com/YgsTa6C.png" height="300">

> 💡frame, alignment rectangle은 일반적으로 다르지 않지만, 뱃지/그림자와 같은 효과가 추가된다면 달라진다. 
> Auto Layout은 alignment rectangle을 사용한다.
> 만약 위와 같은 체크박스를 frame 기준으로 view의 center로 맞춘다면 우리가 원하는 중앙에 배치되지 않고 약간 왼쪽으로 쏠리게 될 것이다.
> 따라서 alignment rectangle을 기준으로 Auto Layout을 적용한다.

[참고자료](https://developer.apple.com/documentation/uikit/uiview/1622576-alignmentrect)

xcode menu -> Debug menu -> show Alignment Rect
or AlignmnetRectForFrame로 디버깅 모드에서 확인

### 레이아웃 엔진 빌드

아래와 같이 레이아웃 엔진이 어떤 것이 포함되는지 볼 수 있다.  
<img src="https://i.imgur.com/zk0vSA7.png" height="200">

이러면 이제 원하는 레이아웃이 만들어지는 프로세스를 이해할 수 있다.  
<img src ="https://i.imgur.com/qQumfCV.png" width="400">

### 요약  
<img src ="https://i.imgur.com/rSWRWRm.jpg" width="400">

---

# Mysteries of Auto Layout, Part 2

[**Mysteries of Auto Layout, Part 2**](https://developer.apple.com/videos/play/wwdc2015/219/)

## layout cycle

![](https://i.imgur.com/B6YILjs.png)

레이아웃이 실제 바뀌어도 바로 UI에 적용되지 않는다.
(레이아웃 엔진의 변화와 뷰 계층에서의 변화에는 딜레이가 존재한다.)

* 간단한 예시

<img src ="https://i.imgur.com/Cgd9gP7.png" width ="400">


먼저 constraint를 수정하면 레이아웃 엔진이 이를 알게 되고 이미 이곳에서 변한것을 알게되지만 UI에서 업데이트가 아직 안된다.
하지만 그 이후에 매치되어서 맞는 곳을 업데이트 시킨다.

constraints를 바꾸게하게 되면 무슨 일이 일어날까?

0. constraints 변경
1. 레이아웃 엔진이 다시 계산을 시작한다.(즉 특정 뷰의 origin, size 다시 확인하게된다.)
2. 그리고 다시 계산할 때 이 변수들이 다시 새로운 값이 나올 것이다.
3. 그래서 superview에게 layout 변화가 필요하다고 알려준다.
4. 이는 **Deferred layout pass**를 일으킨다.

<img src ="https://i.imgur.com/gdai5AX.png" width ="400">

프레임이 레이아웃 엔진에서 변하면 구조에서는 안바뀌어서 아직 위치가 재조정되어 있지 않다.

이때 **deferred layout pass**를 통해 위치가 재조정되며 이것이 끝나게 되면 잘못 위치한 것을 올바른 곳으로 옮겨지게 된다.

pass1: 뷰는 메서드를(superview.setNeedsLayout()) 통해 요청을 한다.
pass2: 보통 next layout pass(=pass2)에 메서드가 호출되며 뷰의 레이아웃이 업데이트된다.


> 💡 deferred layout pass가 오면
    1 ) layout의 constraints를 업데이트 한 다음,
    2 ) view 계층 구조의 모든 view에 대한 프레임을 계산한다.
라고 함.
Deferred Layout Pass는 실제로 view 계층 구조를 통과하는 2개의 Pass를 포함한다.
>The update pass updates the constraints, as necessary == Update Constraints
>The layout pass repositions the view’s frames, as necessary = Reassign view frames 이다.
⭐️ 즉, update pass + layout pass = Deferred Layout Pass

[ZeddiOS:티스토리](https://zeddios.tistory.com/1202 )

하지만 이렇게 부르지않고 되는 경우가 있다.

1. 초기의 constraints 세팅은 interface builder안에서 이상적으로 이루어져 있다.
2. 아니면 viewDidLoad와 같은 곳에서 호출되어서 코드적으로 할당되는 경우도 있다.

그래서 주기적으로 layout update가 이루어진다.

그리고 만약 constraint 변화가 즉시 필요할 때가 있다.

반면에 로직때문에 코드에 의해 반복으로 분리된 메서드로 인해 나중에 호출되는 경우가 있다.

이렇게 하면 유지하고 다른사람이 이해하기 어렵다.
그러면 **어디에서 constraints를 업데이트 하는 것**을 구현하는 것이 좋을까?

constraints 바꾸는 곳이 너무 느려서 문제가 생길 수 있는데, constraints 바꾸는 것을 **constraints 업데이트 하는 내부**에서 이루어지면 훨씬 빠르게 된다.

이유: 모든 것을 한번에 다루기 떄문에 더 빠른 것이다.

**예**를 들어, activate, deactivate를 array 한번에 하는 것이 하나씩 일일이 하는 것보다 더 빠른 것과 같은 의미다.

그리고 만약에 뷰의 구조를 바꾸어야할 때 하나씩 rebuild를 하게 된다면 이는 매우 많은 시간을 낭비하는 것이다.

그래서 이렇게 하지말고 setNeedsUpdateConstraints를 통해 rebuild가 필요한 부분 모두를 한번에 하게 된다면 위와 다르게 시간 낭비가 줄어들 것이다. 하나씩 하지 않고 한번에 변화가 필요한 것을 한번에 해주기 떄문이다

layoutSubviews() 메서드는 reposition이 필요할 때 슈퍼뷰를 호출하는데, 이는 변화가 필요한 것 하나씩을 호출하는 것이 아니라 슈퍼뷰를 호출해서 변화가 필요한 곳을 한번에 업데이트 시켜준다.

그래서 layout engin를 통해 subview frames를 복사한 다음 setFrame를 통해 해준다.

그리고 **커스텀 layout**를 위해 override한 layoutSubViews()를 호출하는 것을 조심해야한다.

구현하기 쉽지만 문제 생기기 쉽다. 일부는 layout update가 되어있고 일부는 되어있지 않는 경우가 있다.

그 순간이 **문제**가 될 것이다.

그래서 이 문제를 **해결**하기 위해서는 규칙을 따라야 한다.

<img src ="https://i.imgur.com/8Zdwz7x.png" width ="400">

Do 1: super.layoutSubviews()를 호출해야한다.

Do 2: subtree내에서 invalidate layout를 하는 것은 괜찮다. 하지만 이것은 superview 구현을 부르기 전에 이루어져야한다.

Don't 1: setNeedsUpdateConstraints() 메서드를 부르면 안된다.
update constraints pass가 있었는데 이걸 끝내고 만약 놓친게 있으면 이미 끝난거라 어쩔수없다.

Don't 2,3: 바깥의 subtree에 있는 것을 invalidate layout를 하면 안된다. 이렇게 하게되면 쉽게 layout feedback loops를 일으킬 수 있다. 이는 layout를 수행하는 행동에서 실제로 레이아웃을 다시 지저분하게 만든다.
이렇게 하면 무한 반복이 이루어질 수 있다.

**결론**적으로 Layout Cycle에서 기억해야할 부분이 있다.  
<img src ="https://i.imgur.com/H7rNFeG.png" width ="400">

1. 즉시 모든 프레임이 변할 것이라고 예측하면 안된다. constraints가 변하게 되면 과정을 걸쳐서 레이아웃이 변하게 되는 것이다. 그래서 즉시 변하지 않는 다는 것을 알아야한다!
2. layoutSubviews()를 override해서 사용하면 layout feedback loop에 빠질 수 있으니 조심해야한다.


## interacting with legacy layout

원래는 autoresizing mask를 사용해서 레이아웃을 설정했다.

Autolayout를 활용해서 SuperView와의 레이아웃 설정을 해준다.

하지만 가끔 Views의 frame를 설정해주어야 할 때가 있다.  
<img src ="https://i.imgur.com/T7nTaY7.png" width ="400">

예를 들어 layoutSubviews를 overrride하게 되면 view의 frame를 설정이 필요할 때가 있다.

이거에 대한 flag가 있다. 이는 translateAutoresizingMaskIntoConstraints라는 프로퍼티다.

이것은 View에게 어떻게 행동할지를 정할 수 있는데 오토레이아웃에서 legacy Layout System에서 설정할 수 있다.

View의 프레임을 이 프로퍼티와 같이 설정하게 되면 프레임워크는 constraints를 만들고 layout engine에서 frame를 enforce한다.

이게 의미하는 바는 frame를 설정할 때 Auto layout를 count하고 뷰를 내가 넣을 곳에 유지한다.

게다가 이 constraints는 autoresizingmask과 유사하게 동작한다.

그리고 Auto layout engine 이용해서 뷰와 상대적인 위치를 파악해서 뷰의 frame를 설정해준다.

만약에 engine에게 어디에서 이 레이아웃이 필요하고 위치가 어딘지 말해주지 않는다면 시스템은 정확하게 파악할 수 없다.


* 레이아웃 설정하는 법은 어떻게 **변화**했을까?
(맨 처음) 하드코딩으로 frame 세팅 -> autoresizingMask(superview size바뀌면 어떻게 하위 뷰의 사이즈를 바꿀지 결정) -> autoLayout(constraints 이용!)(layoutSubviews 오버라이딩 할 때는 frame 설정 가능)  
<img src ="https://i.imgur.com/dzB30F7.png" width="400">

* translateAutoresizingMaskIntoContraints를 false해주기!

코드로 작성할 때 frame을 직접 설정하는 경우, translateAutoresizingMaskIntoContraints를 false로 주지 않으면 resizingMask와 contraints 세팅한 것이 충돌나서 제대로 레이아웃이 세팅되지 않는다!

* anchor 등장  
<img src ="https://i.imgur.com/v95D4hw.png" width="400">

anchor는 factory method로 이전보다 읽기 편해짐
컴파일 에러로 알려줌(ex. location과 size끼리 제약사항 설정하려고 할 때)

## contraining Negative Space: layoutGuide/ layoutMarginGuide

이전의 비효율적인 레이아웃 구성: dummy view 사용  
<img src ="https://i.imgur.com/qwVbCdH.png" width="400">

해결: layoutGuide/ layoutMarginGuide

새로운 방식의 layout guide class 제공한다.

예시 1:layout guide가 있다.  

<img src ="https://i.imgur.com/u80pDDX.png" width ="400">

예시 2: layoutMarginGuide라는 것이 생기게 되었다.

이것들을 사용해서 레이아웃 에러를 해결할 수 있다.

이전의 방식보다 더 가볍고 뷰들의 뷰 구조를 망가트리지 않고도 레이아웃을 해줄 수 있다.

dummy View의 문제점은 뷰의 구조에 포함된다. 왜냐하면 empty View이기 떄문에 결국 다른 뷰와 동일하게 추가해야하기 떄문이다.

이는 결국 다른 뷰처럼 overhead에 추가되는 단점이 존재한다.

또한 다른 뷰들과 같이 있기 떄문에 가끔 message를 방해해서 문제가 생기면 어느 부분에서 생긴 것인지 파악이 어렵다.

그래서 나온 것이 LayoutGuide, layoutMarginGuide이다.

* Layout guides 장점
1: a **lightweight method** for encapsulating
2: **separate** the layout

[layout guide 공식문서](https://developer.apple.com/documentation/uikit/uilayoutguide)

## debug layout

이런 경험 다들 해본적 있을 것이다.

왼쪽처럼 디자인했는데 실제로 실행해보면 오른쪽 처럼 나오는 것을 볼 수 있었을 것이다.

<img src ="https://i.imgur.com/hv96q9A.jpg" width ="600">

코드로 설정하는 법  
![](https://i.imgur.com/D70QdHx.png)
명시적으로 identifier를 작성하면 레이아웃 모호하다고 에러 생겼을 때 확인 용이

<img src="https://i.imgur.com/7F3Et8L.png" width="500"/>

IB에서 설정하는 법  
<img src="https://i.imgur.com/fgRz4by.png" width="500"/>

contraintsAffectingLayoutForAxis 로 디버깅하기  
<img src="https://i.imgur.com/OBWUIYg.png" width="500"/>

## resolve ambiguity
언제 모호한 상황이 생길까?
-> 레이아웃 부족 or 너무 많을 때(conflict)

* 해결법들  
<img src="https://i.imgur.com/ivDJZkJ.png" width="400"/>
<img src="https://i.imgur.com/canV45Z.png" width="400"/>
<img src="https://i.imgur.com/b3LmNNK.jpg" width="400"/>
<img src="https://i.imgur.com/Df3e8q5.png" width="400"/>

=> size 문제인지 location 문제인지 확인 가능  
<img src="https://i.imgur.com/GDKqgo7.png" width="400"/>
<img src="https://i.imgur.com/vu1Ib1k.png" width="400"/>


=> lldb 로 solution에 대한 힌트를 제안받을 수 있음  
<img src="https://i.imgur.com/oTUh10D.png" width="400"/>

디버그 관련 Summary  
<img src ="https://i.imgur.com/aJ8jTMI.jpg" width ="400">
