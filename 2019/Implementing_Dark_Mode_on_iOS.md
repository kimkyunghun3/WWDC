# Implementing Dark Mode on iOS

[영상 보러가기](https://developer.apple.com/videos/play/wwdc2019/214)

![](https://i.imgur.com/0RQaizW.png)

> dynamic color와 image를 통해 앱을 새로운 모습으로 바꾸는 방법에 대해 알아보자.


다크 모드가 추가된 것은 iOS 13.
앱에서 다크 모드를 구현하는 방법을 알아보고, UIKit이 어떻게 작업을 처리하는지 알아볼 것이다.


## Dark Mode is a new look

![](https://i.imgur.com/D6rlhdX.png)

두 화면은 동일한 앱의 서로 다른 모습.
이 화면을 보는 사용자는 두 화면이 동일한 앱이라고 느껴야 한다.
이를 위해 화면을 디자인할 때 고려한 세 가지.
- **Colors**, **Materials**, **The built-in views and controls**

![](https://i.imgur.com/XknjYc9.png)

첫 번째이자 가장 중요한 속성인 **색상(colors)**.
기존의 iOS의 기본 앱들은 모든 색상을 하드 코딩(각 색상에 대한 모든 RGB값들을 직접 지정)으로 입력되어 있었다.
이에 따라 다크 모드를 지원하려면 거의 모든 색상을 변경해야 하고, 다양한 색상들을 일일이 지정해주어야 하는 문제점이 발생한다.

![](https://i.imgur.com/q0JeB7F.png)

이것을 해결한 방법은 시맨틱 색상(semantic colors)의 사용이다.
시맨틱 색상이란 색상의 의미를 설명하는 이름을 가진 색상을 뜻하며,
예를 들어 `systemGroupedBackground`는 UITableView와 같이 그룹화된 화면에서 배경으로 쓰이는 색상을 의미한다.


![](https://i.imgur.com/LBReLaa.png)

이러한 색상들은 동적이기 때문에, 라이트 모드와 다크 모드에서 서로 다른 값을 갖는다. 
개발자는 현재 Appearance Mode를 사용하고 있는지 생각할 필요가 없으며 모드가 변경될 때도 UIKit이 변환 작업을 수행하므로 추가적인 코드를 작성할 필요가 없다.


![](https://i.imgur.com/eL1C3XM.png)

`systemBackground`는 기본 배경색을 나타내며 라이트 모드에서는 순수한 흰색, 다크 모드에서는 순수한 검정색을 나타낸다.
그 외에도 `secondarySystemBackground`, `tertiarySystemBackground` 색상이 있으며 이 색상들은 단순한 라이트 모드의 반전이 아니라 다크 모드일 때 더욱 대비된다.

![](https://i.imgur.com/BxINtD9.png)

배경 뿐만 아니라 텍스트에도 이런 계층 구조 개념을 적용한다.
네 가지 레벨의 텍스트 색상이 있으며 다른 요소에 비해 중요한 요소를 강조할 때 사용한다.

![](https://i.imgur.com/iUNv9sz.png)

텍스트 색상의 주된 용도는 다음과 같다.
- Primary: 제목에 사용
- Secondary: 자막에 사용
- Tertiary: placeholder에 사용
- Quaternary: 비활성화 표시에 사용

더 자세한 내용은 [human interface guidelines - colors]()에 색상의 정의 및 용도, 새로운 동적 색상을 만드는 방법 등이 설명되어 있다.


![](https://i.imgur.com/7ZPeJU7.png)

재료(Materials)는 단순히 색상을 변경하는 것에서 벗어나 효과에 관한 것이다.
사진 위에 반투명한 흐림 효과가 씌워지고 그 위에는 눈에 띄는 선명한 요소들이 있다.

![](https://i.imgur.com/sU2f7ZE.png)

마지막은 UIKit에서 제공하는 내장 뷰 및 컨트롤(The built-in views and controls)이다. 위에 보이는 두 화면은 같은 색상과 같은 재료로 만들어졌다.

![](https://i.imgur.com/6RzShWW.png)

다크 모드를 디자인하는 방법
- 코드로 작업하기 전에 디자인 시스템에 대한 이해가 우선되어야 한다.
- UIKit에서 제공하는 색상과 재료, 뷰와 컨트롤을 사용한다.
- 필요한 경우에는 직접 커스터마이징한다.


## It's easy to implement(Demo를 통한 실습)

![](https://i.imgur.com/R2fVGDt.png)

기존의 UIColor은 단일 값만 존재했었으나 동적 색상은 두 개의 값을 갖는다.

![](https://i.imgur.com/dmCWlqW.png)

이 예제는 iOS 12 환경에서 만들어진 앱이다. 따라서 당연하게도 다크 모드에 대한 지원이 전혀 없는 상태로 이것을 업데이트하려고 한다.
앱 하단의 tabBar가 변경된 것은 UIKit의 표준 tabBar를 사용하고 있기 때문이다.

![](https://i.imgur.com/WoUONnj.png)

먼저 배경부터 살펴보면, Storyboard로 view를 선택했을 때 배경색이 White Color로 하드 코딩되어 있음을 볼 수 있다. 대신에 동적 색상인 SystemBackground로 변경하면 라이트 모드에서는 기존과 같이 흰색으로 보여지고 다크 모드에서는 검은색으로 보여지게 된다.

![](https://i.imgur.com/yHUWzZ2.png)

다크 모드에서 텍스트가 보이지 않는 문제가 발생했다.
그 이유는 텍스트 색상 또한 Black Color로 하드 코딩되어 있기 때문이다.

![](https://i.imgur.com/ul8tEk9.png)

여기에 적합한 색상은 Label Color이다.
systemBackground와 마찬가지로 라이트 모드에서는 검은색, 다크 모드에서는 흰색으로 보이는 동적 색상이다.

![](https://i.imgur.com/ZlMiSDX.png)

다음은 왼쪽 상단의 별 모양의 이미지를 조정할 차례이다.
이 UIImageView는 tintColor로 렌더링되도록 설정되어 있다.
이곳의 화살표를 클릭하면 사용자 정의 로고 색상을 지정할 수 있다.

![](https://i.imgur.com/n4fbJoC.png)

Appearances 옵션에서 Dark를 추가하고 다크 모드일 때 표시될 색상을 사용자 정의할 수 있다.

![](https://i.imgur.com/7AN36Ds.png)

이미지 또한 동적으로 적용시킬 수 있다.
예를 들어 라이트 모드에서는 열대 해변 사진을 보여주고, 다크 모드에서는 일몰에 어두어진 해변의 사진을 보여주는 것이다.
해당 UIImageView를 선택하고 Image의 화살표를 클릭한다.

![](https://i.imgur.com/ySPmFti.png)

색상과 마찬가지로 Asset 카탈로그로 이동하여 Dark를 추가하고 이미지를 드래그한다. 이렇게 하면 모드에 맞게 이미지까지 변경이 가능하다.

단지 하드 코딩된 색상을 동적 색상으로 변경하고 동적 이미지와 사용자 정의 색상을 활용하여 추가적인 코드 없이 다크 모드를 완벽하게 적용했다는 점이 가장 큰 이점이다.


## Materials

![](https://i.imgur.com/4R54mi4.png)

재료의 종류에는 네 가지가 있다. 각각은 라이트 모드와 다크 모드에서 위와 같이 표현된다.
- Thick, Regular, Thin, Ultra thin

![](https://i.imgur.com/NvkhTBh.png)

앱에서 View가 있고, 그 위에 흐림 효과를 적용하고 싶다고 가정해보자.
가장 먼저 할 일은 UIBlurEffect를 만들고 스타일을 지정하는 것.
예시에서는 style = .systemMaterial로 생성했다.

![](https://i.imgur.com/Pd7NAiv.png)

이것을 사용하여 UIVisualEffectView를 생성한다.
UIVisualEffectView 아래에 있는 모든 것이 흐려지는 것을 볼 수 있다.

![](https://i.imgur.com/DR7vqj4.png)

블러 효과 외에도 생동감 있는(Vibrant) 효과를 추가할 수도 있다. 생동감 있는 효과에는 네 가지 종류가 있고 일부는 배경을 통과한다. 

![](https://i.imgur.com/1ZXr0M4.png)

생동감 있는 효과는 항상 흐림 효과 위에 와야 한다.
UIVibrancyEffect를 생성하고 스타일을 지정한다.

![](https://i.imgur.com/TV4WEPb.png)

생동감 있는 효과를 적용할 또다른 UIVisualEffectView를 생성한다.
이 두 UIVisualEffectView를 subView 관계로 연결하지 않는다. 대신 contentView를 사용한다.
```
예)
    view.addSubview(blurView)
    blurView.contentView.addSubview(vibrancyView)
    vibrancyView.contentView.addSubview(...)
```
contentView 아래에 Vibrant View를 추가하면 다음과 같이 화면에 나타난다. 색상 대신 생동감 효과가 나타난 이유는 UIVibrancyEffect에서는 색상이 무시되고 투명도만 사용되기 때문이다.

![](https://i.imgur.com/damZ55b.png)

다시 기존의 예제 앱으로 돌아와서, 이 화면(LearnMoreView)에 대한 완벽한 다크 모드 지원을 위해 업데이트해보자.

![](https://i.imgur.com/V8DZJT8.png)

아까의 화면과 다른 점은 이 화면이 코드로 구축되었다는 점이다.
다크 모드에서의 화면은 완벽하지 않지만, 기존에 설정했던 동적 이미지와 색상이 적용되었고, UIKit에서 제공하는 내비게이션 바를 사용한 덕분에 자동으로 색상이 변경된 것을 볼 수 있다.

![](https://i.imgur.com/fuVww03.png)

흐림 효과를 동적으로 바꾸어주자. `light`로 되어 있는 효과를 `systemThinMaterial`로 변경한다.
또한 UIVibrancyEffect의 생성자에 style 매개변수를 추가하여 `.secondaryLabel`(vibrancyView 위에 오는 label들의 색상을 secondaryLabel로 지정) 스타일을 지정해 주었다.

![](https://i.imgur.com/vmrOulD.png)

중요한 것은, 일부 코드를 작성했지만 단지 제공되는 동적인 속성을 가진 색상이나 효과를 사용한 것 뿐 **현재 어떤 모드인지 명시적으로 확인하지 않았고**, 라이트 모드에서 다크 모드로의 **외관 변경을 처리하는 코드를 작성하지 않았다는 것**이다.

![](https://i.imgur.com/qHC19II.png)

이 두 화면은 모두 systemBackground를 사용하고 있는데, 오른쪽 화면은 비교적 회색빛을 띄고 있다. 그 이유는 다크 모드 디자인에는 두 종류의 레벨이 있기 때문이다. 
- View가 전체 화면 가장자리에서 채워질 때, 이를 base level이라고 한다.
- 컨텐츠가 배경 위의 별도의 레이어에 나타날 때, 이를 elevated level이라고 한다. 조금 더 밝은 값을 가지고 아래의 검은색 배경과 구별된다.


## Flexible and powerful

![](https://i.imgur.com/88UMFC7.png)

다크 모드로 전환할 때 UIKit에서 어떻게 변경사항을 알고 적용하는지에 대해 살펴본다.

### trait collection

![](https://i.imgur.com/iz97vkP.png)

동적 색상은 밝은 색과 어두운 색을 각각 갖고 설정에 따라 자동으로 적용된다고 했는데, 색상이 밝은지 어두운지 어떻게 알 수 있을까?

![](https://i.imgur.com/7zpXy8u.png)

앱에서 각 View와 View Controller는 trait collection이 있으며 이를 통해 View의 모양을 결정한다. 
trait collection에는 3가지 속성이 있는데, 
- userInterfaceidiom: 앱이 어떤 기기 환경에서 실행되고 있는지
- userInterfaceStyle: 앱이 어떤 스타일에서 실행되고 있는지
- userInterfaceLevel: 앱이 어떤 수준에서 실행되고 있는지

![](https://i.imgur.com/6wu8ubm.png)

따라서 동적 색상은 trait collection의 userInterfaceStyle을 통해 해석된다. 이 과정은 자동으로 진행되지만 사용자 정의 동적 색상을 정의하여 직접 해석해야 하는 경우가 있을 수도 있다.

![](https://i.imgur.com/XZ04lQT.png)

동적 색상으로부터 저장되어 있는 색상을 알아내는 방법이다. 

![](https://i.imgur.com/VLAH9d5.png)

이를 통해 다음과 같이 동적 색상을 정의할 수 있다. 인자는 trait collection이고 style을 확인하여 알맞은 색상을 반환한다.

![](https://i.imgur.com/HhZwZ8c.png)

항상 trait collection을 인자로 넘기지 않고도 일반 색상과 같이 사용할 수 있는 이유는 UITraitCollection.current라는 속성 덕분이다. 항상 현재 trait collection을 통해 색상을 해석하고 동적 색상에 대한 결과를 얻을 수 있다.

![](https://i.imgur.com/V57ye8d.png)

UIView에서 draw 메서드가 호출될 때, UIKit은 현재 trait collection을 자기 자신의 trait collection으로 설정한다. 그리고 동적 색상을 해석할 때 이 trait collection을 사용한다.
따라서 모드가 변경되면 
1. view의 setNeedsDisplay()가 호출되고
2. view의 draw 메서드가 호출되어 
3. trait collection의 속성을 통해 새로운 색상으로 다시 그려지는 것이다.

![](https://i.imgur.com/IdqrceD.png)

이 외에도 다양한 메서드에서 현재 trait collection을 설정하고 사용한다.
단 이러한 메서드 외에는 현재 trait collection이 특정 값을 갖는다고 보장할 수 없으므로 명시된 메서드 외에서 동적 색상을 해석해야 하는 경우 이를 직접 관리해야 한다.

![](https://i.imgur.com/uIouidc.png)

CALayer, CgColor와 같은 하위 수준 클래스는 UIKit의 개념인 동적 색상을 이해하지 못한다. 예를 들어, 위와 같이 테두리 색을 적용시키면 동적으로 적용되지 않는다. 따라서 현재 View의 trait collection에 대한 인스턴스를 생성하고 이를 사용해야 한다.

![](https://i.imgur.com/30is4dv.png)

첫 번째 방법은 resolvedColor를 사용하는 방법이다.
trait collection을 사용하여 색상이 자체적으로 해석되고, 그 색상을 사용한다.

![](https://i.imgur.com/JScpuxw.png)

두 번째 방법은 trait collection의 performAsCurrent 내부에 동작을 호출하는 방법이다.

![](https://i.imgur.com/hQI0fC8.png)

세 번째 방법은 코드가 실행되기 전에 현재 trait collection을 직접 설정하는 방법이다. 이 방법은 부작용이 없고 절대적으로 안전한 방법이다. 다른 곳에 영향을 주고 싶지 않다면 기존 trait collection을 저장했다가 작업을 처리하고 다시 복원시키면 된다.

![](https://i.imgur.com/mEltDPb.png)

trait collection이 변화하면 `traitCollectionDidChange(_)` 메서드가 호출된다. 유의할 점은 모든 특성 변화가 색상에 영향을 미치는 것은 아니라는 것이다. View의 크기나 위치 등이 변경되는 것은 동적 색상에 영향을 미치지 않는다.
따라서 관련 특성이 변경되었는지 확인하는 방법을 사용하는 것이 좋다.

![](https://i.imgur.com/WnjoLt0.png)

마지막으로 UIImage이다.
UIImageView 또한 UIColor와 같이 trait collection의 userIntefaceStyle을 확인하여 알맞은 이미지를 선택한다.

![](https://i.imgur.com/I8nFr8e.png)

동적 이미지를 해석하는 방법.
Asset에 이미지를 등록했다면, 자동으로 올바른 이미지를 표시한다.

## Trait Collections

![](https://i.imgur.com/UNbIOld.png)

우선, **trait collection은 앱에 하나만 존재하지 않는다.** 화면의 가장 아랫단인 UIScreen부터 시작하여 앱의 계층 구조를 따라 계단식으로 배열된다. 이 계층 구조로 인해 특성의 값이 변경되었을 때 모든 객체에 대해 trait collection이 변경되었음을 수신하게 된다. 예를 들면 앱의 설정이 다크 모드로 변경되면 UIScreen부터 가장 바깥의 UIView까지 특성 변경이 전달된다.

![](https://i.imgur.com/uic0aeQ.png)

이 View Controller에 UIView를 추가한다고 해보자.
View를 생성했지만, 아직 View Controller에서 추가되지 않아 아직 이 계층에 포함되지 않는다.
iOS 13에서 UIKit은 해당 View가 종료될 것으로 예상되는 위치를 예측하고, 그 위치를 기반으로 View의 trait collection을 사용한다.
곧이어 View Controller에서 View가 추가되면, 상위 환경에서의 trait collection을 상속하게 된다. 요약하자면 초기화될 때 trait가 예측된다.

![](https://i.imgur.com/ExNSXMj.png)

iOS 13에서 trait collection의 주요 변경점
- 초기화 중에 특성이 예측된다.
- `traitCollectionDidChange(_:)`는 이제 특성의 변경점이 있을 때에만 호출된다.

![](https://i.imgur.com/7PbrcC4.png)

trait는 레이아웃이 발생하기 전에 항상 업데이트된다. 따라서 새 코드를 가장 안전하게 추가하는 방법은 View 또는 View Controller에서 trait collection을 읽고 필요한 작업을 수행할 때까지 기다리는 것이다.

![](https://i.imgur.com/8zbHsmx.png)

만약 이 화면에서, 'Learn More' 화면은 라이트 모드일 때에도 항상 어둡게 보여지길 원한다고 가정해보자. 앞에서 본 계층 구조를 활용하여 이를 해결할 수 있다.

![](https://i.imgur.com/71zy9KY.png)

만약 시스템이 라이트 모드라면 모든 단계에서 light 스타일을 갖게 된다.
하지만 userInterfaceStyle을 재정의하여 항상 어둡게 유지할 수 있다.

![](https://i.imgur.com/jnEjvnL.png)

iOS 13에 추가된 기능으로 바로 `overrideUserInterfaceStyle` 프로퍼티를 사용하여 재정의하는 방법이다.
또한 앱 전체가 항상 라이트 모드 또는 다크 모드를 유지해야 한다면 Info.plist의 UIUserInterfaceStyle을 Light 또는 Dark로 지정해주면 된다.

![](https://i.imgur.com/GMPZ2DA.png)

trait를 재정의하는 API도 존재한다. 그러나 필요한 부분의 특정 trait만 설정해서 사용해야 한다. 설정되지 않은 trait들은 일반적으로 상속된 값을 자동으로 채워져 사용할 것이다.


## Dark Mode API Updates

1. Status Bar

![](https://i.imgur.com/OKNIeuh.png)

![](https://i.imgur.com/Fq14pwJ.png)

iOS 13 이전에는 default와 lightContent 두 종류가 존재했었는데, iOS 13에서는 darkContent가 새로 추가되어 default를 대체하게 되었다. 기존의 default는 이제 모드에 맞게 자동으로 lightContent와 darkContent를 전환한다.

2. UIActivityIndicatorView

![](https://i.imgur.com/Vjwy0m5.png)

기존의 style들은 전부 deprecated되었고 크기에 기반한 새로운 스타일을 도입하였다.

3. Text

![](https://i.imgur.com/eCkEcXs.png)

기본적으로 시스템에서 UILabel, UITextField, UITextView 모두 label 색상을 사용한다.


4. Attributed Text

![](https://i.imgur.com/z7DtMPu.png)

AttributedString을 사용하는 경우 foregroundColor를 지정하지 않으면 검은색 텍스트를 생성하게 된다.
따라서 해당 코드에 다음 코드를 추가하여 foregroundColor에 동적 색상을 지정해야 한다.
```swift
.foregroundColor = UIColor.label
```

## Recap

![](https://i.imgur.com/w0Ho1sQ.png)

- iOS 13으로 빌드하면 앱이 기본적으로 다크 모드를 지원한다.
- 완벽하게 앱이 다크 모드를 지원하기 위해서는 system color와 materials를 사용해야 한다.
- 필요한 경우 사용자 정의 색상과 이미지 등을 만들고 사용할 수 있다.
- UIKit에 내장된 강력하고 유연한 기능들을 활용하여 앱을 꾸밀 수 있다.
