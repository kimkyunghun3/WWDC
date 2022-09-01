## 🎁 intro 
![](https://i.imgur.com/IG07Wwy.png)

SwiftUI 는 바퀴를 재발명하려고 하지 않는다. 
우리가 전에 UIKit 에서 사용하던 다양한 컴포넌트들 그리고 다양항 기능들이 이미 모두 들어가 있다.
그저 방법을 바꿨을 뿐이다. 


![](https://i.imgur.com/HPFBoFw.png)
기본적인 것은 쉽게 구현할 수 있게 도와주고 
자유롭게 커스텀 할 수 있도록 만들었다고 한다. 

## 🥑 View and modifiers

아보카도 토스트 주문 앱을 만들면서 SwiftUI와 친해져 보자.

![](https://i.imgur.com/Gwypwiy.png)

> 이 화면에서 보이는 모든 것은 View 로 구성되어 있다. 

이 말을 이해하기 위해서 이야기를 더 들어보자. 

![](https://i.imgur.com/IA0ywqs.png)

일단 UIKit 에는 UIView 가 있고 AppKit 에는 NSView 가 있다. 

SwiftUI 에는 View 가 있다!

![](https://i.imgur.com/szJJGqg.png)
![](https://i.imgur.com/JJ7xJwI.png)

SwiftUI 에서 이런 식으로 계층구조와 코드가 작성된다. 

아직 SwiftUI 를 잘 모른다고 해도 UIKit 을 안다면 여기서 이상한 점을 발견할 수 있다.

addSubView 같은 함수가 없다는 것이다. 

계층 구조를 위해서 그 함수가 필요한데 말이다.

왜냐하면 SwiftUI는 View 계층 구조를 하나씩 구축하는 것이 아니라, 완성된 구조체를 만들고 그것을 초기화 하기 때문이다. 

이 말은 즉 

그건 UIKit 은 imperatively(명시적으로) 하게 작동하고 
SwiftUI 는 declaratively(선언적으로) 하게 작동하기 때문이다. 

(하나도 이해 안되는것이 정상 계속 읽어보자...)

### 🥑 imperatively VS declaratively

그건 UIKit 은 imperatively(명시적으로) 하게 작동하고 
SwiftUI 는 declaratively(선언적으로) 하게 작동하기 때문이다. 

명시적이고 선언적인게 무엇인지 알기 위한 예시를 보자. 

명시적으로 아보카도 토스트를 만들어 보자. 

![](https://i.imgur.com/frJsSWQ.png)
![](https://i.imgur.com/ltDaDQD.png)

명시적으로 아보카도 토스트를 만든다는 것은 친구한테 전화로 어떻게 아보카도 토스트를 만드는지 설명하는 것과 같다. 

그럼 선언적으로 아보카도 토스트를 만드는 것을 보자.  

선언적으로 아보카도 토스트를 만드는 것은 아보카도 장인에게 아보카도 토스트를 주문하는 것이다. 

![](https://i.imgur.com/V1Wgfxc.png)


이제 다시 코드를 하나한 뜯어보자. 
SwiftUI 가 그 아보카도 장인 역할이다! 알아서 해줄 것이다.

![](https://i.imgur.com/spPGlca.png)

Contianer 에 closure 는 ViewBuilder 라는 아주 특수한 녀석으로 되어 있다.
일단은 그 클로저 안에 선언을 하기만 하면 View 에서 볼 수 있다는 것을 알아 두면 된다. 

![](https://i.imgur.com/Hm2xgVk.png)
Container 에 대표적인 예를 VStack 을 보자. 
안에 다른 View 들이 들어가 있다. (SwiftUI 에서는 Image 도 Text 도 View 이다.)

![](https://i.imgur.com/RWz8LdI.png)
좀 더 깊은 이해를 위해 제 VStack 의 내부구현을 살펴보자. 

content 라는 파라미터가 있고, 
클로저 타입을 받고 있지만, @ViewBuilder 가 붙어 있다.

Swift 컴파일러가 이 클로저안에 있는 모든 뷰의 값을 모아서 하나의 VStack 으로 다시 리턴하는 법을 알고 있다. 

![](https://i.imgur.com/vgbrq5N.png)

우리는 이 클로저 안에 Text Toggle Stepper 등 어떤 종류의 View 든 넣을 수 있다.

![](https://i.imgur.com/PAByh0Z.png)
익숙하지 않은 문법인 $ (달러싸인) 이 있다.

달러싸인은 일반적인 값 대신 바인딩 값을 전달하고 있다는 것을 의미한다. 
![](https://i.imgur.com/YXaAAIE.png)

@State를 사용한 proerty를 SwiftUI가 보면, 
자동으로 Scene 뒤에서 지속 상태를 생성 및 관리한 다음에 이 속성을 통해 해당 상태값을 노출한다. 

스테퍼를 누를 때 값을 수정 할 수도 있어야 하기 때문에, 바인딩한 값을 받는다. 

> 바인딩에 대해서 자세한 것을 알고 싶으면 SwiftUI Data Flow WWDC 를 시청하라고 한다.

### 🥑 modifier 

SwiftUI는 View 계층 구조를 하나씩 구축하는 것이 아니라, 완성된 구조체를 만들고 그것을 초기화 한다. 

라는 말을 이해하려면 modifier 에 대해서 알아야 한다. 

![](https://i.imgur.com/rx4LeJJ.png)

SwiftUI 에서 modifier 을 이용해 새로운 뷰를 만든다. 

![](https://i.imgur.com/bR4q965.png)

예시를 보자.
.modifer 를 사용해서 새로운 개념을 계속해서 추가할 수 있다. 
chaining도 할 수 있다!

UIKit을 계속 썻다면 이상하다고 생각 할 수 있다. 
왜냐하면 여태껏 계층구조를 가볍게 유지하도록 했으니까. 

그러나 우리는 선언적 코드를 작성하고 있다. 선언적 코드는 다르게 작동한다.

마치 고차함수의 return 값 여러개를 체이닝 하듯이 모든 modifier 는 View 를 받아서 View 를 리턴한다.

그래서 
`계층 구조를 하나씩 구축하는 것이 아니라, 완성된 구조체를 만들고 그것을 초기화 한다.` 
라는 말이 나오는 것이다.
addSubView 가 필요 없는 이유도 이미 모든 SubView 들을 가진 상태에서 초기화가 일어나기 때문이다.

SwiftUI 를 만들면서 많은 고민을 했고 Scene 에서 rendering 시스템에서 최적화를 해주기 때문에, 성능에 대한 문제는 걱정할 필요가 없다고 한다. 

#### 🤗 modifier 의 이점 🤗
1. 순서를 명시적으로 알 수 있다. 
2. View 간에 공유가 가능하다.

**순서를 명시적으로 알 수 있다.** 

우리는 다양한 feature 들이 적용되는 순서를 정해줄 수 있다.

![](https://i.imgur.com/6UcyUld.png)
위의 이미지는 백그라운드를 먼저 주고 패딩을 준것 이고

![](https://i.imgur.com/M2WjiVQ.png)
위의 이미지는 패팅을 먼저 주고 백그라운드를 준 것 이다.

두개의 결과가 다른것은 modifier가 순서대로 작동하기 때문이다.

이게 만약의 modifier 가 아닌, 텍스트의 속성이라고 한다면 문서를 읽거나 실험을 해보지 않고는 어떤 속성이 먼저 적용되는지 알 수 가 없다. 

modifier 를 순서대로 연결해서 명시적으로 순서를 알려주고 있는 것이다.

**View 간에 공유가 가능하다.**

![](https://i.imgur.com/eDbNyCq.png)
각각의 View 에 opacity modifier 를 설정해 줄 수 있다. 
같은 것은 반복해서 다니 코드가 지저분하다. 

![](https://i.imgur.com/LclbuVl.png)
위와 같이 VStack 에 opacity modifier 를 설정하면 하위 모든 뷰에도 적용이 되어서 위의 코드와 같은 형태를 볼 수 있는데 코드는 더 깔끔해 진다!



![](https://i.imgur.com/xKjkuIP.png)
![](https://i.imgur.com/MXiEkFh.png)

## 🥑 Building custom Views 

![](https://i.imgur.com/8eXmPdy.jpg)
이제 진짜 아보카도 토스트 주문앱을 만들면서 예제를 보자.

![](https://i.imgur.com/AKGQz9h.jpg)

우선 OrderHistory 라는 구조체를 View 를 채택해서 만들었다.
previousOrders 라는 배열도 만들었다.

View 에는 view 를 반환하는 body 라는 연산 프로퍼티가 있다. 
body 는 body 안에 선언된 View 들을 하나의 View 로 묶어서 반환한다.
(지금 이 OrderHistory 에서는 previousOrders 를 List 형태로 만들어서 반환하고 있다.)

### 🍞 SwiftUI 에서 View 란 무엇인가?

SwiftUI 가 어떻게 Custom View 를 만드는지 부터 다시 이야기 해보자.

View 구조체가 View 프로토콜을 채택한다. 

![](https://i.imgur.com/Jaf6VYA.png)
UIKit 이나 AppKit 을 썻었다면, 
View 를 클래스로 만들고, Super Class 를 상속하는 걸로 만드는 것에 익숙할 것이다.

UIView 는 alpha 값을 가지고 있고, 
backgroundColor 도 프로퍼티로 가지고 있다. 

그럼 SwiftUI 는 무엇이 다른가?
![](https://i.imgur.com/h2V4qPi.png)
이런 방식이다. 
개별적인 뷰에 상속되는 대신
modifier 에서 각각 뷰 계층 구조에 걸쳐 분산된다는 것을 알 수 있다. 

각각의 수정자는 고유한 뷰를 만든다. 

이를 통해 뷰의 무게를 줄이고, 고유한 목적에 맞게 스토리지를 최적화 할 수 있다. 

그리고 상속처럼 모든 View 가 공동 탬플릿을 제공할 필요가 없고, View 라는 프로토콜을 채택 하기만 하면 된다는 것이다.

![](https://i.imgur.com/WMmP4om.png)
채택하는 View 프로토콜의 역할은 '나는 UI 요소다' 라고 정의를 하는 것이다.

### 🍞 View 의 재귀?

![](https://i.imgur.com/RdpoLrT.png)

View 프로토콜의 내부 구현을 살펴 보자.

그런데 보다보면 View 프로토콜이 View 를 리턴하고 그러니 재귀가 아닌가? 하는 생각이 든다. 

![](https://i.imgur.com/boaKZ6o.png)
![](https://i.imgur.com/2IFUQgR.png)
이런 식으로 계속 되는데... 

재귀가 계속 일어나지 않도록 View 를 채택하고 있는 타입들의 내부 구현이

```swift
extension Text : View {
    public typealias Body = Never
}
```
이런 식으로 Body 를 Never 로 채택하고 있어서, 계속적인 재귀가 일어나지 않는다. 

![](https://i.imgur.com/jPP6Ylc.png)

이런 기본적인 요소들을 모두 View 를 채택학 있다.

> 이것에 대해서 좀 더 자세한 내용을 보려면 Building swiftUI Custom View WWDC 를 시청하라고 한다.

View 는 모델에 변화가 있을 때 마다 업데이트 되도록 되어 있다.

그리고 뷰가 바뀔 때 마다 적절한 기본 애니메이션 을 넣어주기 까지 한다.

![](https://i.imgur.com/GEJAgzO.jpg)

### 🍞 View 와 modifier 를 적절하게 사용하기

소금과 후추를 추가하는 코드를 작성해보자.
 
먼저 소금부터!
![](https://i.imgur.com/9716zLz.jpg)

if 문 과 같은 제어 흐름을 사용하면 뷰가 스택에 포함되어야 하는 시기을 알 수 있다. 

선언적인 코드에서 if 문을 사용하는건 너무 너무 자연스럽다. 

예시를 하나 보자.
![](https://i.imgur.com/09OHPHq.jpg)
flipped 라는 속성을 가진 뷰가 있고
그 속성에 따라 앱 아아콘이 어떻게 될지 넣어 주었다. 

그런데 이런 방식은 좋지 않다. 

이렇게 코드를 작성하면 자연스럽게 돌아가는 애니메이션이 아니라, 이상한 cross fade 애니메이션이 나타난다.

다른 두 종류의 뷰를 전환시킨다고 인식하기 때문이다.

![](https://i.imgur.com/Kx7K0l0.jpg)

이렇게 쓰는게 적절하다.
한개의 뷰에서 modifier 를 바꿔주는 형태로 말이다. 

이렇게 하면 하나의 뷰에서 modifer 를 바꾸는 것으로 인식해서 자동으로 회전하는 아주 자연스러운 애니메이션이 들아가게 된다.


다시 만들던 아보카도 앱으로 돌아가 보자.
 
### 🍞 코드 분리

![](https://i.imgur.com/SnTl6E2.jpg)
이제 코드가 조금 길어지고 있다.

분리를 해보자.

![](https://i.imgur.com/5Omc6sF.png)
OrderCell 을 만들고
우리는 이미 이 코드가 존재하니 그냥 코드를 OrderCell 로 옮기기만 하면 된다.

![](https://i.imgur.com/xu6JKnu.png)
분리를 하고 order 프로퍼티를 추가하기만 했다. 

애플은 이제 구성하는 것과 성능 최적화 사이에서 고민할 필요가 없다고 한다.


이제 고추 토핑을 추가해 보자. 
![](https://i.imgur.com/2aitI24.jpg)

우선은 이렇게 할 것을 생각할 것이다. 
그런데... 
![](https://i.imgur.com/j34IAM9.jpg)
위와 같은 방법이 더 좋다. 

List 와 마찬가지로 ForEach 는 ViewBuilider 를 사용한다.

둘이 다른점 이라고 한다면 List 는 자체적인 시각 효과를 추가한다는 것이다.
(Divider 를 사이에 추가한다거나 하는 것 같이!) 

이렇게 코드를 바꾸면 뭐가 좋냐? 
만약에 새로운 토핑을 추가하게 된다고 하더라도, 더 추가할 코드가 없다.

![](https://i.imgur.com/3Nr3SvD.jpg)
이렇게 계란을 세번째 아이콘으로 추가 했는데...
어떠한 코드도 더 작성하지 않아도 된다👍

---
## 🥑 Composing Controls

### 🍞 VStack -> Form 
![](https://i.imgur.com/5Q8emCu.jpg)
왼쪽의 뷰를 보면 나쁘지는 않은데 뭔가 어색하다. 
오른쪽 뷰가 여태껏 봐왔던 스타일이다.  

![](https://i.imgur.com/dfxbw4b.jpg)

SiwftUI 에서는 오른쪽 같은 경우를 Form 으로 만들 수 있다. 
표준적인 양식을 제공하는 셈이다. 

오른쪽에 코드를 왼쪽에 처럼 변경하기 위해서 해야 할 것은 

![](https://i.imgur.com/7A5lIpd.jpg)

![](https://i.imgur.com/bBMs3rl.png)

그저 VStack 을 Form 으로 변경하는 것 뿐!
easy!!

### 🍞 Button 의 label

버튼 부분을 조금 자세히 보자.
![](https://i.imgur.com/ltLWciB.png)
버튼에는 label 을 넣을 수 있는데, label 은 View 를 반환하는 형태로 되어 있다.
그러니 View 를 반환하는 그 어떤 것도 올 수 있다!

![](https://i.imgur.com/cagkw3D.png)
Text 든 
![](https://i.imgur.com/1EmyXun.png)
Image 든
![](https://i.imgur.com/yO6ymKy.png)
VStack 이든 모두 넣을 수 있다.

![](https://i.imgur.com/oD7Fwr9.jpg)

이렇게 엄청 다양한 종류의 버튼을 모두 `Button` 을 사용해서 만들 수 있다. 

물론 버튼에도 modifier 를 추가해서 더 다양한 수정도 가능하다.

### 🍞 적응 제어, 다양한 플렛폼 활용

![](https://i.imgur.com/KsOCl8D.png)

**적응 제어**
* 비주얼이 아닌 목적을 선언
* 기본적인 기능보다 똑똑하다
* 재사용 가능성이 높다
* 강렬한 커스터 마이즈 기능 

![](https://i.imgur.com/behq0L2.jpg)
우리가 워치에도 살짜쿵 구현을 해보자.  

`Toggle` 은 플렛폼에 관계없이 작동한다. 

버튼과 다른점은 Bool 값을 바인딩 한다는 것이다. 

(대부분의 primitive 타입이 지원하고 잇다.) 
![](https://i.imgur.com/qK1db7s.png)
Toggle은 VoiceOver 기능도 완벽하게 지원하고 있다.
저런식으로 읽어준다. 
![](https://i.imgur.com/oUnYE9N.png)
물론 우리가 Text 가 아니라 이미지를 넣거나 그랬을 때는 accessibility 를 지정해 줄 수 도 있다. 

> 더 자세한 내용은 Accessibility in SwiftUI WWDC 를 보면 된다고 한다. 

이번엔 macOS 에 Toggle 을 적용해 보자. 

![](https://i.imgur.com/lwLvNcB.jpg)
Toggle 이 아니라 macOS 에 맞게 picker 를 사용할 것이다. 
![](https://i.imgur.com/bsMJF68.jpg)
![](https://i.imgur.com/vsbFwjU.jpg)
Picker 도 List 처럼 바인딩 할 값을 받을 수 있고, 그것에 대한 tag를 설정할 수 있다. 
다양한 picker 스타일이 있는데, 여기서는 radio Group 을 선택했다.
![](https://i.imgur.com/2QjEW7P.png)
![](https://i.imgur.com/DI34ffX.jpg)
이런식으로 많이 만드는 것도 가능은 하다. 추천하지 않지만
![](https://i.imgur.com/lXVLBOj.jpg)
ForEach 를 활용하면 이렇게 더 간단하게 만들 수 있다. 

SwiftUI 다양한 플렛폼에서 지원되는 frame work 다!
![](https://i.imgur.com/BZ6S9ID.png)

mac 에서 우클릭하면 나오는 contextMenu 부분을 보자.
잘보니 Toggle 같기도 하고 Button 같기도 한게 익숙하다. 
그래서 실제로도 Toggle 과 Button 을 사용해서 구현할 수 있다.

Form 을 다시한번 보자. 

![](https://i.imgur.com/YJ558oz.jpg)
전체적으로 이렇게 색깔을 지정할 수도 있다.
![](https://i.imgur.com/ff0V70F.jpg)
특정 조건에서 버튼의 작동을 막을 수도 있고

![](https://i.imgur.com/KnMEv5b.jpg)
네트워크에 연결이 안되어 있으면 이 모든 것을 못하게 할 수도 있다.  

![](https://i.imgur.com/dw7JPfJ.jpg)
물론 이렇게 더 깔끔하게 짜는 것을 더 선호한다. 

### 🍞 Environment
![](https://i.imgur.com/WtovFTd.png)

![](https://i.imgur.com/Yw0K848.png)
Environment 에는 많은 사항이 포함되어 있다. 

![](https://i.imgur.com/cGh7SLS.png)
모든 뷰는 상위 뷰로 부터 enviornment 를 받는다. 

![](https://i.imgur.com/SDJpdxT.png)
여기 레이아웃 디렉션을 상위 뷰로 부터 받는 것이 environment 를 받고 있다고 표현할 수 있다. 

![](https://i.imgur.com/0KaGH3r.jpg)
environment 는 우리가 다양한 환경을 만드는 것을 아주 잘 도와준다. 

일단 우리 앱의 으로 아보카도 토스트에 정확하 계란의 위치를 정하는(ㅋㅋㅋ) 뷰를 만들어 보자. 
![](https://i.imgur.com/C8SdYKv.jpg)
일단 Egg 이미지를 드레그 할 수 있게 gesture 화 포시션을 만들었다.

![](https://i.imgur.com/Bc9h1VV.jpg)
근데 주문을 받다보면 egg 가 부족하거나 그런 이유 때문에 disable 해야 할 때가 있다.
우리가 gesture 를 사용하고 있기 때문에 이건 전역적으로 잘 된다. 

![](https://i.imgur.com/aCRC2ex.jpg)

그런데 계란이 떨어졌다는 것이 전역적으로 알려야 하는 정보라면 environment 에서 관리 할 수 도 있다. 
disable 됐을 때는 이미지의 채도 자체를 줄여 주어서 지금 디스에이블 상태라는 것을 알게 하기 까지 하는 덤이다. 

값이 업데이트 되면 뷰가 다시 그려지기 때문에, 업데이트의 문제는 걱정하지 않아도 된다. 

## Navigation your app

![](https://i.imgur.com/sdgnDLZ.jpg)
이 앱을 잘 만들었는데 일반적인 Navigation style 이 아니라는 문제가 있다. 

화면이동을 해보자.

### 🍞 NavigationView

![](https://i.imgur.com/wGIFddW.jpg)

일단 NavigationView 로 OrderForm 을 감쌀 수 있다. 
.navigationBarTitle 을 사용해서 title 을 꾸밀 수도 있다.

EggLocationPicker 라는 항목을 탭하면 화면이 계란 위치 선택하는 뷰로 이동하는 것을 구현 해보자.

![](https://i.imgur.com/cmTnnzu.png)

NavigationButton 을 저렇게 추가하면 된다. 

![](https://i.imgur.com/6PUrisg.jpg)
이동된 뷰에서는 자동으로 backButton 이 생기고
Baritem 을 추가하면 reset을 만들 수도 있다.

### 🍞 TapView
![](https://i.imgur.com/dPnIk7l.jpg)

Order List 는 완전히 다른 개념이니
TapView 로 표현하자.

![](https://i.imgur.com/Q4ysyAM.jpg)

이렇게 넣고 각 뷰에 따라 tabItemLabel 을 설정해 줄 수 있다. 

![](https://i.imgur.com/su9LMNg.jpg)
![](https://i.imgur.com/jIuVU9s.jpg)
이거 아이폰에서는 이런 형태가 좋은데 
아이패드나 맥북에서는 화면이 크니 스플릿뷰로 보여주게 된다.
물론 세부 정보는 조금 설정을 해주어야 한다.  

중요한 포인트는 모두 같은 Navigation 이라는 것을 공유 하고 있다는 것이다.

## 🥑 마무리

이 WWDC 는 정말 SwfitUI 의 넓은 개념을 설명하고 있는 것이고 
자세한 공부를 하고 싶은면 아래 세션들을 하나하나 더 들어야 할 것 같다.

![](https://i.imgur.com/4x7A6DT.png)

## 🥑 정리 

SwiftUI 는 선언적인 방식의 프레임워크다. 
View 를 상속받지 않고 프로토콜을 채택한다. 
init 을 하고 속성을 수정하는 것이 아니라, 수정된 View 을 한번에 init 한다. 
Customizing 에 최적화가 되어 있다. 
다양한 디바이스에서 같은 코드를 사용할 수 있다. 



