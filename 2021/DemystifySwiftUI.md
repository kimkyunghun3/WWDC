# Demystify SwiftUI

![](https://velog.velcdn.com/images/marisol/post/57be43f1-115a-453b-8286-0a8ad95d36ea/image.png)


SwiftUI는 우리의 코드에서 위 3가지 요소를 확인한다

1. Identify - SwiftUI가 어떻게 앱의 여러 요소를 같거나 다르게 인식하는지
2. LifeTime - SwiftUI가 어떻게 뷰와 데이터의 존재를 추적하는지
3. Dependency - SwiftUI가 인터페이스를 업데이트해야하는 시기과 이유를 이해하는지

=> 위 3가지 개념을 통해 SwiftUI는 무엇이, 어떻게, 언제 바뀌어야 하는지 결정하여 UI를 생성한다

## ✏️ 1. Identity

![](https://velog.velcdn.com/images/marisol/post/3e21f92e-a253-4136-87a3-dcf44a1048d8/image.png)

이 두 개의 뷰는 완전히 다른 뷰일까? 같은 뷰일까?

![](https://velog.velcdn.com/images/marisol/post/3a8060d6-e570-40b1-a3a4-3d23cc038fbb/image.png)

만약 다른 뷰라고 한다면, icon이 독립적으로 전환되어야 한다.
별개의 UI 요소를 나타내는 뷰는 항상 다른 Identity를 가질 것이다.
(초록색/빨간색이 각각 다른 Identity를 가짐)

![](https://velog.velcdn.com/images/marisol/post/01a03b00-cf8e-463f-9e68-7a4b85a0ccc3/image.png)

만약 동일한 뷰라고 한다면, 뷰가 한 위치에서 다른 위치로 이동하면 된다.
동일한 Identity를 공유하는 뷰는, 동일한 UI 요소의 다른 상태를 나타낸다.
(초록색/빨간색이 같은 UI 요소이고, 상태에 따라 초록색/빨간색이 될 수 있는 것)

### ⭐️ SwiftUI가 사용하는 2가지 유형의 Identity

![](https://velog.velcdn.com/images/marisol/post/4fb4771d-21c6-425f-84dd-d4b870443880/image.png)

1️⃣ Explicit identity (사용자 지정 식별자 또는 데이터 기반 식별자 사용)
2️⃣ Structural identity (뷰를 타입과 뷰 계층의 위치로 구분)

![](https://velog.velcdn.com/images/marisol/post/fc58c152-2a22-4dfd-ac11-ad82956daefa/image.png)

강아지가 똑같이 생겼을 때, 어떤 추가 정보가 강아지를 식별하는데 도움이 될까?
=> "이름" 물어보기

두 강아지가 똑같이 생겼고, 같은 이름을 가지고 있다? -> 같은 강아지
두 강아지가 똑같이 생겼지만, 다른 이름을 가지고 있다? -> 다른 강아지

이렇게 이름이나 식별자를 할당하는 것이 explicit identity (명시적 정체성)의 한 형태이다.
명시적 정체성은 강력하고 유연하지만, 누군가 어딘가에서 그 모든 이름을 추적해야만 한다.
이미 익숙한 명시적 정체성의 한 형태는, UIKit 및 AppKit 전반에 걸쳐 사용되는 ```pointer identity```이다.

![](https://velog.velcdn.com/images/marisol/post/24262c61-7010-4e50-a4fd-a5dff76dd720/image.png)

UIView와 NSView는 클래스이기 때문에, 각각 메로리 할당에 대한 고유한 포인터가 있다.
포인터를 사용하여 개별 뷰를 참조할 수 있으며, 두 뷰가 동일한 포인터를 공유한다면 실제로 동일한 뷰임을 보장할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/507ae815-08d8-4a11-a503-ac5a3e6243ae/image.png)

하지만 SwiftUI는 포인터를 사용하지 않는다. (SwiftUI의 뷰는 일반적으로 클래스 대신 **구조체** 타입이기 때문)
value type은 참조를 가지고 있지 않지만, 대신 SwiftUI는 다른 형태의 명시적 정체성에 의존한다.

![](https://velog.velcdn.com/images/marisol/post/f9744344-79fa-4a5a-83a2-724a999466d3/image.png)

구조견 목록이 이렇게 있다고 할 때, 
id 매개변수가 명시적 identity의 한 형태이다.
각 구조견의 강아지 태그 id는 List에서 해당 View를 명시적으로 식별하는데에 사용된다.
구조견 배열이 변경되면, SwiftUI는 이 id를 사용하여 정확히 무엇이 변경되었는지 파악하고,
서로 다른 섹션 사이를 이동하는 애니메이션을 생성할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/10528d80-daf3-427d-b8d5-0dc606eba6ff/image.png)

ScrollViewReader를 사용하여 맨 하단의 "Jump to Top" 버튼을 누르면 상단의 HeaderView로 이동시킬 수 있다.
id modifier는 커스텀 identifier를 사용하여 뷰를 명시적으로 식별할 수 있다.
이동해야할 뷰 (HeaderView)에 identifier를 scrollViewProxy의 ```scrollTo(_:)``` 메서드에 전달하여 특정 뷰로 이동하도록 할 수 있다.

우리가 모든 뷰를 명시적으로 식별할 필요는 없다. 위와 같은 헤더와 같이, 코드의 다른 곳에서 참조해야하는 뷰만 명시적으로 식별하면 된다.
ScrollViewReader, ScrollView, Text, Button 등에는 명시적인 식별자가 필요하지 않다.
-> 이 뷰들의 정체성이 명시적이지 않다고 해서, identity가 없다는 것을 의미하지는 않는다. 왜냐면,

![](https://velog.velcdn.com/images/marisol/post/af1d0023-432a-49fd-877f-885b40cc3fd5/image.png)

모든 뷰는 정체성을 가지고 있기 때문이다. (명시적이지 않더라도)

=> Structural identity
SwiftUI는 뷰 계층 구조를 사용하여 뷰에 대한 암시적 ID를 생성한다.

![](https://velog.velcdn.com/images/marisol/post/e4312812-2f0a-4380-a543-af314a4c355f/image.png)

비슷한 강아지 두 마리가 있고, 이름은 모르지만 각각의 강아지를 식별해야 한다고 가정하자.
이 강아지들이 각각 "어디에 앉아 있는지"를 기준으로 그들을 식별할 수 있다.
(왼쪽에 있는 개 - 오른쪽에 있는 개)

서로를 구별하기 위해 상대적인 배열(relative arrangement)을 사용하고 있다.
```이것이 Structural Identity```

SwiftUI는 API 전반에 걸쳐 Structural Identity를 활용하며, 
전형적인 예는 뷰 코드 내에서 if문과 기타 조건부 로직을 사용하는 경우이다.
조건문의 구조는 우리에게 각각의 뷰를 식별할 수 있는 명확한 방법을 제공한다.
 
![](https://velog.velcdn.com/images/marisol/post/189dc74a-6597-401f-aaaf-6c1f61892f5c/image.png)

첫번째 뷰는 조건이 참일 때만 표시되고, 두 번째 보기는 조건이 거짓일 때만 표시된다.

SwiftUI가 뷰를 볼 때, generic types를 본다.
이 경우에는 if문은 ```_ConditionalContent``` 로 변환되며, 이 변환은 result builder인  ```ViewBuilder```에 의해 작동된다.
SwiftUI의 View 프로토콜은 body 프로퍼티를 ViewBuilder로 암시적으로 래핑한다.
ViewBuilder는 logic statement에서 단일 일반 뷰를 구성하지만, 코드를 복잡하게 하지 않기 위해 숨긴다.

![](https://velog.velcdn.com/images/marisol/post/96c3460c-dcfc-4254-b807-b29b26bb1df7/image.png)

아까의 Good Dog, Bad Dog 뷰를 다시 보면, 
상단의 코드와 같이 if문으로 분기처리해서 각각 다른 뷰로 구성할 수도 있다.

하지만 하단의 코드처럼 상태에 따라 레이아웃과 색상을 변경하는 단일 PawView를 사용할 수도 있다.
다른 상태로 전환되면 뷰가 다음 위치로 자연스럽게 이동한다. (하나의 아이덴티티로 단일 뷰를 수정하기 떄문)

두 가지 경우 모두 가능하지만, 일반적으로 SwiftUI는 두 번째 접근 방식을 권장한다.
뷰의 수명과 상태를 보존하는데 도움이 되며, 자연스러운 전환을 할 수 있기 때문.

![](https://velog.velcdn.com/images/marisol/post/c511ac69-5a77-4ba3-9f35-b9e3b5c67e6a/image.png)

![](https://velog.velcdn.com/images/marisol/post/52aa8d2a-2124-4519-89e2-9631ecb98d5d/image.png)

![](https://velog.velcdn.com/images/marisol/post/6285fb76-b997-4f50-8f86-6eb368ced97d/image.png)

![](https://velog.velcdn.com/images/marisol/post/f39a2d69-b33d-4ccb-9a3a-39966bc1df45/image.png)


## ✏️ 2. LifeTime

![](https://velog.velcdn.com/images/marisol/post/0a30dffb-945d-45a7-87a6-78cfcc94235e/image.png)

고양이의 이름을 지으면, 그 고양이가 다른 상태에 있거나, 하루 종일 움직이더라도 항상 같은 이름의 "Theseus" 고양이일 것이다.

![](https://velog.velcdn.com/images/marisol/post/52a781dc-80e8-461f-b26b-d0e9845f3f3f/image.png)

Identity는 시간이 지남에 따라 다른 값에 대한 안정적인 요소를 정의할 수 있게 해준다.
시간이 지남에 따라 서로 다른 값들을 하나의 객체, 즉 하나의 뷰로 연결한다.

![](https://velog.velcdn.com/images/marisol/post/0f642037-341b-4593-baeb-d6e1c6f43c10/image.png)

고양이가 그르릉거리는 소리의 강도를 보여주는 코드인데,
Theseus는 처음에는 소리의 강도가 25이다가, 점점 배가 고파지고 관심을 갖고 싶으면 강도가 50으로 변경된다.
이때 뷰에 대한 새로운 값이 생성되고, 
SwiftUI는 비교를 위해 값의 복사본을 보관하고 뷰가 변경되었는지 여부를 파악한 다음, 이 전 값을 없앤다.

![](https://velog.velcdn.com/images/marisol/post/fb60f41c-92fa-4d36-ab5b-d86b3ae4320c/image.png)

여기서 이해햐아할 중요한 포인트는, 뷰의 값이 뷰의 ID와 다르다는 점이다.

![](https://velog.velcdn.com/images/marisol/post/792e84ad-1de3-41e8-b046-5c5c6f489192/image.png)

뷰가 처음 생성되고 나타나면, SwiftUI는 앞서 말했던 것처럼 identity를 할당한다.
시간이 지남에 따라 업데이트로 인해 뷰에 대한 새로운 값이 생성되지만,
SwiftUI 관점에서 이것들은 동일한 뷰를 나타낸다.
뷰의 ID가 변경되거나 뷰가 제거되면 뷰의 lifetime이 종료된다.

![](https://velog.velcdn.com/images/marisol/post/c1702c0b-f12c-4265-b155-99700c8d6009/image.png)

![](https://velog.velcdn.com/images/marisol/post/e4950e59-4fc4-40e8-9aaf-631723d27bdc/image.png)

여기서 "State"와 "StateObject"는 뷰의 영구 저장소이다.
뷰가 처음 생성되면, SwiftUI는 초기값을 사용하여 State 및 StateObject에 대해 메모리의 스토리지를 할당한다.
뷰의 일생 동안, SwiftUI는 뷰의 body가 변경될 때에도 이 스토리지를 유지한다.

![](https://velog.velcdn.com/images/marisol/post/b06a4662-d1a0-45ff-98f8-b08212af2bb7/image.png)

위의 코드는 CatRecirder라는 뷰가 다른 브랜치에 존재하고 있다. (분기 처리)
처음에 SwiftUI는 초기값을 사용하여 영구 스토리지를 할당한다.
이 뷰의 lifetime 동안, SwiftUI는 다양한 작업에 의해 변경될 때에도 상태를 유지한다.
만약 datyTime의 값이 변경되어 false branch에 들어가게 된다면?
SwiftUI는 새 저장소를 생성하고, true branch 뷰의 스토리지를 할당 해제한다.

![](https://velog.velcdn.com/images/marisol/post/8058c310-ab86-457d-a261-b905d8d15af6/image.png)

만약 다시 true 브랜치로 돌아가게 된다면 false branch 뷰의 storage를 할당 해제하고 다시 새 스토리지를 생성한다.
중요한 점은, identity가 바뀔 때마다 state가 교체된다는 것이다.

![](https://velog.velcdn.com/images/marisol/post/db0ac137-9d3f-4e8a-968a-17c06677177c/image.png)

State의 지속성은 View의 lifetime에 묶여있다.

![](https://velog.velcdn.com/images/marisol/post/2c40e5ff-84a5-4f51-a14a-6e84e4ef4696/image.png)

SwiftUI에는 데이터의 ID를 뷰에 대한 명시적 ID의 형태로 사용하는 데이터 기반 구조가 있다.

![](https://velog.velcdn.com/images/marisol/post/5721aa98-d89f-41f0-a7f4-eb395f7cc5cd/image.png)

![](https://velog.velcdn.com/images/marisol/post/bef0de07-bf6a-4f6f-9c21-fa4aabe6d97d/image.png)

위 이니셜라이저는 식별자 역할을 하는 id를 가져야 하지만, RescueCat이 Identifiable을 채택한다면, 생략 가능하다.

![](https://velog.velcdn.com/images/marisol/post/aed4b2fa-74d4-4901-8692-1e3da7f7ba02/image.png)

![](https://velog.velcdn.com/images/marisol/post/a055c329-dbfb-46ca-8ab5-e886455e8df2/image.png)

![](https://velog.velcdn.com/images/marisol/post/d0b29a16-fc81-4dcc-8775-cab6444a6b1f/image.png)


## ✏️ 3. Dependency

![](https://velog.velcdn.com/images/marisol/post/5b77079f-f47b-4c1e-80f8-a16c9804f0c6/image.png)

2개의 프로퍼티 (강아지, 간식)가 있으며, 이 프로퍼티는 뷰의 종속성이다.
종속성이 변경되면, 뷰에서 body를 새롭게 작성해야 한다.

body - 뷰에 대한 계층을 작성하는 위치

action - 뷰의 종속성에 대한 변경을 트리거 한다

버튼을 누르면 강아지에게 보상이 주어지며, 강아지는 간식을 먹는다.
종속성이 변경되었기 때문에 DobView는 새 body를 생성한다.

![](https://velog.velcdn.com/images/marisol/post/8b851b0a-6c91-44cd-9bdb-fe33f830c3d7/image.png)

동일한 State 또는 데이터에 여러 뷰들이 의존하고 있을 수도 있다.
이 구조를 dependency graph라고 부르는데, SwiftUI가 새 body가 필요한 뷰만을 효율적으로 업데이트한다.

![](https://velog.velcdn.com/images/marisol/post/9b84e0da-3c53-4610-aa09-67489612f21c/image.png)

![](https://velog.velcdn.com/images/marisol/post/93327ed2-afa4-4e2d-b59a-fe5d6e315f32/image.png)

![](https://velog.velcdn.com/images/marisol/post/31bf96c6-be97-42a4-abd1-0a16349b1a73/image.png)

![](https://velog.velcdn.com/images/marisol/post/eb9696b8-b46c-4fe6-952b-201418bd54ec/image.png)

![](https://velog.velcdn.com/images/marisol/post/7bce5f5f-b9a2-4ae8-a20a-f16145d1eb5a/image.png)

![](https://velog.velcdn.com/images/marisol/post/31bec294-ec4f-4733-a94e-797aa3f65774/image.png)

![](https://velog.velcdn.com/images/marisol/post/f4c23413-534b-4bca-9b8b-7af9a544e2f9/image.png)

![](https://velog.velcdn.com/images/marisol/post/0fefdc41-8db8-4970-8e1c-2c323728a98c/image.png)

![](https://velog.velcdn.com/images/marisol/post/1ec6bf48-e34c-4641-98c0-3a7feaacf5f1/image.png)

![](https://velog.velcdn.com/images/marisol/post/82ce843a-3128-4563-ba7c-28bd4b85603a/image.png)

![](https://velog.velcdn.com/images/marisol/post/86e06d96-0c6a-4d80-914f-ab7404bb15f6/image.png)


