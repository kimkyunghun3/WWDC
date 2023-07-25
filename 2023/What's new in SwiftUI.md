# WWDC23 - What's new in SwiftUI

영상 보러가기: [https://developer.apple.com/videos/play/wwdc2023/10148/?time=1919](https://developer.apple.com/videos/play/wwdc2023/10148/?time=1919)

## Simplified data flow

모델 타입 in SwiftUI
새 observable macro

**@Observable**

![](https://hackmd.io/_uploads/HynarYoFh.png)

타입에 macro를 추가해준다.

ObservableObject와 다르게 프로퍼티에 Published를 쓸 필요가 없다.

Observable 모델은 쉽게 SwiftUI에 존재하는 매커니즘에 통합한다.

예시로 DogCardView를 사용해본다.

View에서 Observable를 사용하면 SwiftUI는 자동적으로 내가 읽는 프로퍼티를 만들어준다.
이 코드를 사용할 때 property wrapper 를 사용할 필요도 없다.

프로퍼티를 읽을 때만 invalidation이 일어나고 그냥 데이터를 전달할 때에는 어떠한 불필요한 업데이트를 할 필요없다.

SwiftUI에서는 상태를 정의하는 여러 툴이 있다.

![](https://hackmd.io/_uploads/BygmgItot3.png)

대부분 ObservableObject를 사용하고 있는데 Observable를 사용하면 더 간편하게 만들어준다.

![](https://hackmd.io/_uploads/rkd-8Kjt2.png)

다이나믹 프로퍼티로 바인딩을 전달해준다.

![](https://hackmd.io/_uploads/rypfIKsKh.png)

environment로 rootView에 이것을 넣고 여러곳에서 사용할 수 있는 공유로 만든다.

![](https://hackmd.io/_uploads/ry2QIFjKh.png)

다른뷰에서! 이걸 읽을 수 있다.

SwiftData는 데이터 모델을 관리하는 데 사용한다!

![](https://hackmd.io/_uploads/ByeVUFsK3.png)

빠르고 규모있게 SwiftUI에서 사용할 수 있다.

예시 

![](https://hackmd.io/_uploads/S1r4Ltstn.png)

모델은 Observable를 사용하는 모든 혜택들을 받을 수 있다.

아까 했던 DogView에서도 바꾸어준다

![](https://hackmd.io/_uploads/BkY4IFoK2.png)

모델 타입을 Container에 넣어준다

그리고 dogs를 Query를 사용해서 다이나믹 프로퍼티로 사용한다.

![](https://hackmd.io/_uploads/BJaVLYjt2.png)

Query를 사용해서 SwiftData에 모델 값들을 가져오도록 한다.

![](https://hackmd.io/_uploads/rklrLKjF2.png)

서버의 Query처럼 정렬같은것도 해줄 수 있다.

Document 스타일 뷰도 만들어 줄수 있다.

![](https://hackmd.io/_uploads/HyNSUKjY2.png)

Inspector도 커스텀 세팅 해줄 수 있다.

![](https://hackmd.io/_uploads/SJwSLYsY2.png)

![](https://hackmd.io/_uploads/HJ5rItiK3.png)

파일을 export 하는 것도 코드로 구현가능


![](https://hackmd.io/_uploads/SJDUUKjY2.png)

![](https://hackmd.io/_uploads/HykPLtjF3.png)

테이블 커스텀 세팅도 가능!

![](https://hackmd.io/_uploads/SJ3wUKjKh.png)
![](https://hackmd.io/_uploads/H1XOItoFn.png)

backgroundProminence를 활용해서 스타일 변경도 가능하게 해준다

![](https://hackmd.io/_uploads/H1QK8YjF2.png)

![](https://hackmd.io/_uploads/r19cIKiFn.png)


![](https://velog.velcdn.com/images/marisol/post/a3562433-2ed6-4ba8-a515-0df4f24982ad/image.png)

헤드셋부터 WatchOS10, 위젯 등을 구성할 때 SwiftUI를 사용할 수 있음!
특히 RealityKit과 통합되어 새로운 3D 기능을 사용할 수 있게됨.

## 🌎 Spatial Computing

![](https://velog.velcdn.com/images/marisol/post/65f3803c-b6e9-459a-bb4b-53228b5b13f9/image.png)

Spatial computing ```공간 컴퓨팅```이라는 플랫폼에서 이미 SwiftUI에서 많이 사용해온 WindowGroup을 통해 Window를 구성할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/6fcb746f-837f-4e50-9bd1-d3f7496626f0/image.png)

![](https://velog.velcdn.com/images/marisol/post/c1e9db74-91f6-4238-973d-76022f73fd41/image.png)

이런식으로 SwiftUI에서 많이 사용되는 NavigationSplitView와 TabView를 이용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/2922a9c8-71e9-4079-9b32-ecfef24470c3/image.png)

간단하게 깊이?volume을 효과를 주고 싶으면 windowStyle을 지정해주면 된다.

![](https://velog.velcdn.com/images/marisol/post/dfeee0b7-6f3f-488f-9503-e3eb92cc4d62/image.png)

![](https://velog.velcdn.com/images/marisol/post/e245ba4e-43cd-4d65-be7a-30b3b508fdac/image.png)

RealityKit을 이용해서 볼륨 효과를 줄 수 있고, addImageBasedLighting을 통해 빛 효과도 줄 수 있다.

![](https://velog.velcdn.com/images/marisol/post/d9ba5856-e94f-4fe4-b824-6d29c4a33d45/image.png)

그리고 ImmersiveSpace를 사용하면 공간을 완전히 채울 수 있다.

![](https://velog.velcdn.com/images/marisol/post/5dac07a0-0d67-44b8-a4b3-a2ecdb406ebd/image.png)

## ⌚️ watchOS 10

![](https://velog.velcdn.com/images/marisol/post/dc9b8909-37a2-47b9-ba69-89b7aae4413f/image.png)

![](https://velog.velcdn.com/images/marisol/post/c60de604-f37f-43c0-91cb-9e89411a3cb3/image.png)

watchOS에서는 적용할 수 있는 디자인이 추가되었는데, containerBackground를 사용해서 세밀한 배경 변화를 줄 수 있다.

![](https://velog.velcdn.com/images/marisol/post/23534ba5-61b2-48cc-8b91-41ef170a512c/image.png)

그리고 기존 Toolbar에서 아이템의 위치를 세밀하게 배치할 수 있게 되었다.

![](https://velog.velcdn.com/images/marisol/post/e467df55-8ac1-42e3-bb88-d8f17fc930f2/image.png)

![](https://velog.velcdn.com/images/marisol/post/c4235128-624d-4254-bdcf-62c11ef7d0ce/image.png)

그리고 DatePiakcer, List 등의 기존 SwiftUI에서 쓰이던 API를 watchOS에서도 사용할 수 있도록 개선되었다.

![](https://velog.velcdn.com/images/marisol/post/701bca49-a24a-47a8-aa0d-699c29a5d7ee/image.png)

그리고 Swift 5.9의 매크로 기능을 이용해 프리뷰가 더 편리해졌다.
systemSmall 아마 위젯의 크기 나타내는듯?

![](https://velog.velcdn.com/images/marisol/post/dac08ef0-576b-49ea-802d-7d00bcde6ba0/image.png)

위젯 상태, 애니메이션 등의 변화를 보기위해 타임라인을 제공해서 시간 흐름에 따른 변화를 순차적으로 확인할 수 있게 되었다.

![](https://velog.velcdn.com/images/marisol/post/3eeb3b44-b856-4b24-9523-aa6ddb59b093/image.png)

그리고 mac 앱의 미리보기도 가능해짐!

![](https://velog.velcdn.com/images/marisol/post/9f2693c7-47bc-4962-b23a-1316eedc556b/image.png)

세션 진행자가 뽑은 SwiftUI의 주목할만한 변경점 중 다음은 MapKit이다.

## 📍 MapKit

![](https://velog.velcdn.com/images/marisol/post/72e04208-fb9b-40b5-9e79-41788a640e9c/image.png)

MapKit에 대규모 업데이트가 이뤄졌는데, SwiftUI랑 MapKit만 import 하면 간단히 사용할 수 있다.

오른쪽 그림처럼 Marker로 목적지 찍고, 경로에 line을 그리고 사용자 위치를 표시하는게 간단해진 것 같다.

## 📊 Chart

![](https://velog.velcdn.com/images/marisol/post/d2985020-c9bd-4f6a-b928-236f0b3f6afd/image.png)

.chartScrollableAxes 모디파이어를 사용해서 .horizontal로 설정하면 가로로 스크롤이 가능해졌다.

![](https://velog.velcdn.com/images/marisol/post/02b3426d-c709-4a60-bbb3-5137f55392ca/image.png)

요렇게 구역별로 나누는 SectorMark도 생겼고,

## 💰 StoreKit

![](https://velog.velcdn.com/images/marisol/post/37931679-5bae-4962-9f41-7e2239900c09/image.png)

그리고 앱 내 구입과 구독도 간편해졌다. 맞춤형 마케팅 컨텐츠와 함께 구독 뷰를 제공한다. 앱에 맞게 전체 배경을 구성하고, 다양한 제어 옵션을 선택할 수 있다.


## 🪄 Extraordinary animations

**Keyframe Animator** 
![](https://hackmd.io/_uploads/BkmTk-Vqh.png)

- 여러 속성을 병렬로 애니메이션 실행
- `First Closure` - 애니메이션으로 인해 변경되는 뷰를 생성
- `Second Closure` - 애니메이션 코드

**Phase Animator**

![](https://hackmd.io/_uploads/HJexe-E5h.png)

- 순차적인 애니메이션 처리
- `Parameter` - 애니메이션 시퀀스, 트리거
- `First Closure` - 회전과 배율 설정
- `Second Closure` - 각 단계(Phase)에 어떤 애니메이션을 적용할 지 전달

**Haptic - Sensory Feedback**

![](https://hackmd.io/_uploads/BJgyvgW453.png)

- `.sensoryFeedback(SensoryFeedback, trigger: _)`
- 햅틱 피드백을 손쉽게 생성하는 방법
- 햅틱을 지원하는 모든 플랫폼에서 사용 가능
- 플랫폼 별로 사용하는 방식에 차이가 있으므로 꼭 H.I.G를 숙지

**Visual Effects**

![](https://hackmd.io/_uploads/SJTFgWV53.png)

- 초점 이동에 따라 업데이트 되는 애니메이션을 만드는 방법
- GeometryProxy를 통해 좌표를 얻고 이것을 `.visualEffect` 에서 활용
    - 전체 Grid View에서 하나의 DogCircle 프레임의 위치를 가져오고 이곳이 현재 초점으로부터 얼마나 떨어져있는지 계산하여 애니메이션 효과를 사용한다.
- 더 이상 GeometryReader를 사용할 필요가 없어진다.

**ShaderLibrary - Metal Shader**

![](https://hackmd.io/_uploads/Sy46eb4ch.png)

- 텍스트에 스타일을 입히는 방법
- .metal 파일에 `angledFill`을 정의

**Symbol Effects**

![](https://hackmd.io/_uploads/SJRgZ-Eqn.png)

- SF Symbols에 대한 애니메이션 발생
- `.symbolEffect`

**Text Scale**

![](https://hackmd.io/_uploads/BJe1Zb45n.png)

- 단위를 나타내는 문자를 입력할 때 textScale 수정자를 통해 자유롭게 입력할 수 있게 됨.

**Typesetting Language**

![](https://hackmd.io/_uploads/r1QGWbVc3.png)

- 각 언어마다 사용하는 활자의 크기가 다르기 때문에 여러 언어를 복합적으로 사용하는 경우 서로 겹치는 불편함이 있었음
- 이러한 문제를 해결해주는 도구가 바로 `typesettingLanguage`

## 📲 Enhanced Interactions

**ScrollView Transition**

![](https://hackmd.io/_uploads/rJKE-W4cn.png)

- 스크롤로 인해 View가 **visible area** 안팎으로 전환할 때 시각적 효과 추가
- `scrollTransition` - content에 효과 추가
- `containerRelativeFrame` - 기존에 배치된 개 사진과 별개로 공원 스택 배치
    - scrollTargetLayout(), scrollTargetBehavior(): 레이아웃 및 페이징

**Accessibility Support**

![](https://hackmd.io/_uploads/S1VdZWVq2.png)

- `.accessibilityZoomAction` VoiceOver를 통해 새롭게 제어하는 방법 추가

**Menu Enhancements**

![](https://hackmd.io/_uploads/r15F-Z452.png)

- `.paletteSelectionEffect`
- `.controlGroupStyle`
- `.pickerStyle`

**HoverEffect**

![](https://hackmd.io/_uploads/H1M6b-Eqn.png)

- 버튼에 Hover Effect를 입힐 수 있는 수정자
- `.hoverEffect`

**Hardware Keyboard**

![](https://hackmd.io/_uploads/Bk7CW-N92.png)

- `.onKeyPress` - 하드웨어 키보드가 존재하는 기기라면 모든 키보드 입력에 직접 반응할 수 있음.
