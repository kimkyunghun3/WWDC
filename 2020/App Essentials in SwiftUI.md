# App Essentials in SwiftUI

![](https://velog.velcdn.com/images/marisol/post/3dbad0c7-8fe7-4bf0-926f-58b881d6e376/image.png)

이 세션에서는 SwiftUI로 앱을 구축하는 새로운 API를 소개하고,
```Views```, ```Scenes```, ```Apps``` 가 어떻게 함께 작동하는지 설명하려고 한다.

![](https://velog.velcdn.com/images/marisol/post/5f77ffbc-c5a7-4df7-87c5-00eac78bb1e4/image.png)

그리고 SwiftUI의 scnene 아키텍처에 대해 자세히 다루고, 앱에서 어떻게 scene을 커스터마이즈 할지 알아본다.
마지막으로, 앱을 커스터마이징하는데에 사용할 수 있는 API들에 대해 알아본다.

![](https://velog.velcdn.com/images/marisol/post/5d296ce8-5334-4f0a-b820-c7abe096092f/image.png)

먼저, SwiftUI를 이전에 써보았다면 익숙할 View에 대해 먼저 얘기하려고 한다.
각 View는 UI의 한 부분을 정의하기 때문에 중요하다. 앱을 볼 때, 유저가 보는 모든 것은 ```View```이다.
개별이미지와 텍스트 조각 모두 뷰이며, 그들을 담고 있는 컨테이너 또한 뷰이다.
화면에 보이는 모든 픽셀은 어떤 식으로든 뷰에 의해 정의된다.

하지만 앱이 전체 화면을 완전히 제어하지 못하기 때문에, 모든 뷰가 동일한 앱에 속하는 것은 아니다.
대신, 플랫폼이 앱이 표시되는 방식을 제어하여 서로 다른 구별되는 영역(distinct regions)을 보여준다.

![](https://velog.velcdn.com/images/marisol/post/0cf31d79-9331-4664-8a6a-59c3a4a05fec/image.png)

SwiftUI에서 이러한 구별되는 영역을 ```scene``` 이라고 부른다. 
window는 scene의 컨텐츠가 화면에 표시되는 가장 일반적인 방법이다.

![](https://velog.velcdn.com/images/marisol/post/2235370f-c015-4286-8442-8319e1efe745/image.png)

iPadOS와 같은 일부 플랫폼은 여러 개의 window를 나란히 보여줄 수 있다.

![](https://velog.velcdn.com/images/marisol/post/3622a3ab-2330-420e-9475-eeb2e77ae6c2/image.png)

iOS, watchOS, tvOS와 같은 다른 플랫폼들은 각 앱에 대해 하나의 full-screen window만 보여주는 것을 선호한다.

![](https://velog.velcdn.com/images/marisol/post/a7973b0f-e8c6-4c2a-a7db-5be63079b70f/image.png)

macOS는 scene의 컨텐츠가 어떻게 다른 방식으로 나타날 수 있는지를 보여주는데,
이 경우 관련 window 모음을 볼 수 있으며, 각 window는 다른 scene의 내용을 나타낸다.

![](https://velog.velcdn.com/images/marisol/post/34cfb0c3-c837-47ca-b6ce-b3527b074c55/image.png)

또한 이 shared window는 각 탭과 연결된 하위 scene의 컨테이너 역할을 하는 자체 scene으로 표시될 수 있다.
App, Scene, Views가 통합된 계층을 형성한다.

![](https://velog.velcdn.com/images/marisol/post/bccdcd3c-2fb4-4193-8315-a1dca8890e0d/image.png)

앞서 말했듯이, View는 화면에 표시되는 모든 것을 렌더링하는 기본 구성 요소 역할을 하며,
보다 복잡한 UI를 형성하도록 결합될 수 있다.

View는 Scene의 컨텐츠를 형성하여, 플랫폼에서 독립적으로 표시할 수 있다.

View와 마찬가지로, Scene들도 앞에서 본 탭 window 예제와 같이 더 복잡한 scene들을 형성하기 위해 결합될 수 있다.

그리고 마지막으로, 이 모든 Scene들이 당신의 앱의 컨텐츠를 형성한다.

이제 app, scene, view가 함께 동작하는 방식을 이해했으므로, SwiftUI 코드에서 이 기능이 어떻게 작동하는지 살펴보자.

![](https://velog.velcdn.com/images/marisol/post/54585ba4-54c2-4cab-846f-b4f54b054535/image.png)

독서클럽에서 읽고 있는 책을 추적하는데에 도움이 되는 앱인데,
```ReadingListViewer``` 라는 뷰를 사용하여 앱의 인터페이스를 정의했다.

![](https://velog.velcdn.com/images/marisol/post/9675f85b-feb3-4f3a-b2ee-67e8aa84d8b9/image.png)

```ReadingListViewer```는 읽기 목록을 검색할 수 있도록 만든 커스텀 뷰이다.
```ReadingListViewer```는 ```WindowGroup```이라는 scene 내에 포함되어 있다.

WindowGroup scene은 ```ReadingListViewer```가 렌더링할 window를 관리한다.

또한 이런 기능을 지원하는 플랫폼에 추가 window 또는 동일한 window 내에 새 탭을 만들 수도 있다. (뒤에서 자세히 설명)

![](https://velog.velcdn.com/images/marisol/post/b4d936ee-b470-4923-86dc-0b2838a4e5c0/image.png)

WindowGroup scene은 ```App``` 프로토콜을 준수하는 커스텀 struct로 대표되는 앱 내에 포함되어 있다.
앞에서 살펴봤듯이, App에는 scene이 포함되어 있고, scene에는 view가 포함되어 있다.

![](https://velog.velcdn.com/images/marisol/post/5c9a16fb-4ec5-4000-906e-d5ed245f0f40/image.png)

app을 선언하는 것이 커스텀 view를 선언하는 것과 유사한 것을 볼 수 있는데,
예를 들어 view와 app 모두 데이터 종속성을 선언할 수 있다.
여기서 ```ReadingListViewer```는 ```ReadingListStore``` 객체를 관찰하고 있다.

우리 app은 ```ReadingListStore``` 객체에 의존하지만, 올해 SwiftUI의 새로운 피처인 ```StateObject``` 프로퍼티 래퍼를 사용하여 스스로 해당 객체의 소유자임을 선언하고 있다.

![](https://velog.velcdn.com/images/marisol/post/a6920455-33f1-42fc-ace6-b530defc0b31/image.png)

그리고 View와 app 모두 UI 컨텐츠를 정의하는 "body" 프로퍼티를 선언하고 있다.

View는 다른 뷰들과 결합되기 때문에 body 프로퍼티가 View를 리턴하지만,
App은 Scene을 사용하여 구축되기 때문에 body 프로퍼티가 Scene을 리턴한다.

![](https://velog.velcdn.com/images/marisol/post/d1a0e782-fdb7-463c-accc-f84d40982bdf/image.png)

App 상단의 ```@main``` 속성은 Swift 5.3의 새로운 속성이며, 프로그램 실행의 시작점 역할을 할 수 있도록 해준다.
일반적으로 Swift 프로그램을 실행하려면 main.swift 파일이 필요하다.
```@main``` 속성을 사용하면 해당 책임을 App 구조체에 위임할 수 있다.
App이 화면에 나타나도록, 시작할 때 필요한 모든 설정을 자동으로 수행한다.

이제 앱이 어떻게 작동하는지 정말로 깊게 이해하기 위해서, UI를 관리하는 WindowGroup scene에 대해 더 이야기하려고 한다.

![](https://velog.velcdn.com/images/marisol/post/789f2255-1ff1-445d-85df-93f96e4a0913/image.png)

지금까지 다룬 것을 잠깐 데모로 보자면,
Book Club 앱을 아이패드에서 실행하면 내가 지정한 content를 담고 있는 View를 담고 있는 초기 window와 함께 실행된다.
꽤 많은 책을 읽고 있는 것 같으니, 진행상황을 확인하기 위해 새 window를 열려고 한다.

![](https://velog.velcdn.com/images/marisol/post/9ef94e28-1fc8-4954-925c-f627903c1419/image.png)

App Exposè를 열면, 여기서 쉽게 새 앱을 만들고 다른 책으로 이동할 수 있다.
```WindowGroup```은 iPadOS에서 자동으로 내 앱에 이 기능을 제공한다. 각 window가 인터페이스의 다른 상태를 반영하고 있다는 것을 알 수 있다.

선택한 책은 각각 다르며, 한 window에서 책을 변경해도, 다른 window에는 영향을 미치지 않는다. 이것이 SwiftUI에서의 scene의 핵심적인 특징이다.

각 window에는 앱 이름과 선택한 책의 이름이 표시된다. 이 작업은 iOS에서 네비게이션바와 앱스위처에 제목을 설정할 수 있는 "navigation title"이라는 올해 도입된 새로운 view modifier를 통해 수행되며,
상위 scene의 상태에 영향을 미칠 수 있는 view modifier의 한 예이다.

Mac에서는 앱이 여러 window를 지원하는 것이 매우 일반적이다. 앱에서 ```WindowGroup```을 사용하여 SwiftUI는 상단 "File" 메뉴에 새 scene 인스턴스 생성을 지원하는 메뉴 항목을 제공한다. 이 항목은 Command + N 바로가기 키를 통해서도 호출할 수 있다.

여기서 좋은 제목을 제공하는게 유저에게 중요한데,
사용자가 상호 작용하는 내용에 대해 더 많은 컨텍스트를 제공할 뿐만 아니라,
열려있는 window 목록에서 원하는 window를 선택하는 데에도 도움이 되기 때문이다.

![](https://velog.velcdn.com/images/marisol/post/b0942149-55ff-4630-818f-0769765a1f58/image.png)

![](https://velog.velcdn.com/images/marisol/post/30c1cc19-442a-4aba-afe8-53d5fc793d88/image.png)

![](https://velog.velcdn.com/images/marisol/post/5b4b360f-5e27-493b-b399-6f6c141368ca/image.png)

![](https://velog.velcdn.com/images/marisol/post/a8b59b4f-3a63-4d7c-b3cb-190c293b49a8/image.png)

macOS는 여러 window를 지원하는 것 외에도, window를 그룹화하는 것도 지원한다.
상단 "Window" 메뉴를 통해 열려 있는 window를 탭으로된 단일 인터페이스로 병합할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/379a4e6f-06ea-466c-9c26-c6192384504f/image.png)

각 탭이 별도의 Scene으로 표시된다.

다시 말하지만, 이 기능을 위해 코드를 작성할 필요가 없으며, SwiftUI가 이 기능을 자동으로 제공한다.

![](https://velog.velcdn.com/images/marisol/post/b3640da4-54c4-4453-87c7-8b11385c368e/image.png)

방금 본 내용을 요약하자면, WindowGroup은 앱의 기본 인터페이스를 표현할 수 있는 scene이다.
WindowGroup에 제공하는 뷰는 해당 인터페이스의 정의로 사용된다.

![](https://velog.velcdn.com/images/marisol/post/d2de2142-9aa2-4ebd-8460-6f0f89b0541c/image.png)

그리고 이것은 모든 애플 플랫폼에서 예상되는 방식으로 작동한다.
예를 들어 iOS와 watchOS에서는 기기의 전체 화면을 차지하는 window에 view가 표시된다.
macOS에서 이 window는 view의 정의에 따라 크기가 조정된다.

![](https://velog.velcdn.com/images/marisol/post/7a62ad35-38ca-4b50-8a48-3785c44b499f/image.png)

scene의 생명주기는 scene이 실행중인 플랫폼에 의해 관리된다.
예를 들어, macOS를 사용하여 플랫폼이 당신의 앱을 위한 window를 만들어야할 때,
WindowGroup은 기본적으로 window 안에 컨텐츠를 표시하는 새로운 child scene을 인스턴스화할 것이다.

![](https://velog.velcdn.com/images/marisol/post/0d591a8a-35f4-452d-8353-bab8d7111db7/image.png)

macOS와 iPadOS와 같은 여러 개의 window를 지원하는 플랫폼에서,
WindowGroup은 여러 개의 children을 인스턴스화할 수 있다.

이는 menu 항목을 클릭하거나, 멀티태스킹 제스처를 호출하는 것과 같은 사용자 작업에 응답해서 발생할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/4841b35f-913f-4790-aa7c-e07cc7fc8584/image.png)

각 scene은 UI 정의를 공유하지만,

![](https://velog.velcdn.com/images/marisol/post/b11b14e2-3150-4c0d-854f-56eb1ccb5f3b/image.png)

이 정의를 구성하는 뷰는 모두 고유한 독립 상태를 가진다.
이는 한 window에서 뷰 상태를 변경해도, 다른 window 상태에는 영향을 미치지 않는다는 것을 의미한다.

![](https://velog.velcdn.com/images/marisol/post/6c5aa7f9-2c53-4b28-9974-816f3e767c8e/image.png)

플랫폼이 scene의 라이프 사이클을 담당하기 때문에, 올해 뷰 상태 복원을 관리하는데 도움이 되는 새로운 프로퍼티 래퍼를 도입했다.
```SceneStorage``` 프로퍼티 래퍼를 사용하여 뷰 상태를 유지할 수 있다.

저장할 상태를 식별하는 고유한 Key가 필요하며, SwiftUI가 상태를 자동으로 저장하고 적절한 시간에 복원해준다.

![](https://velog.velcdn.com/images/marisol/post/83f05a1d-1804-4c63-8d3c-cc0ebff7fad7/image.png)

이제 App을 추가로 커스터마이징할 수 있는 방법에 대해 알아보려고 한다.

![](https://velog.velcdn.com/images/marisol/post/9700b4a1-1e53-47f4-9fbe-6ffcb83b9a34/image.png)

앞서 본 BookClub 앱은 공유 데이터 모델이 지원하는 데이터 중심 (data-driven) 앱이었다.
그러나 여기서 볼 ```ShapeEdit```과 같이 문서 기반 (document-based)과 같은 다른 종류의 앱도 있다.

문서 기반의 scene을 열기, 편집하기, 저장하기를 자동으로 관리하는 DocumentGroup scene 타입이 올해 새롭게 추가되었다.

![](https://velog.velcdn.com/images/marisol/post/636137ee-a377-4b7c-94bd-7a465e31a448/image.png)

이제 BookClub 예제로 돌아가면, macOS 앱의 공통 기능은 "preferences window"(기본 설정 창)이다.
올해, 표준 preferences window를 자동으로 설정하는 macOS에서 사용할 수 있는 새로운 "settings" scene type을 공개했다.

![](https://velog.velcdn.com/images/marisol/post/1eb8eb24-9300-4815-a41f-f497a1718a83/image.png)

Setting scene은 앱 메뉴에서 preference 명령을 자동으로 설정하고, window에 올바른 스타일 처리를 제공한다.

![](https://velog.velcdn.com/images/marisol/post/72f201e8-b992-4e95-90b3-182430bf99c2/image.png)

그리고 SwiftUI는 새로운 ```"commands"``` modifier를 통해 scene에 커스텀 명령을 추가할 수 있는 API를 제공한다.
커스텀 타입인 ```BookCommands```를 잠깐 살펴보자면,

![](https://velog.velcdn.com/images/marisol/post/9568edc2-e909-4162-a33b-7a924d1b48b8/image.png)

commands API는 View, Scenes, Apps에서 사용하는 것과 동일한 선언적이고, 상태 기반 (declarative & state-driven) 프로그래밍 모델을 사용하여 강력하고 유연하다.

AppKit 또는 UIKit의 Responder chain과 유사하게, 커스텀으로 명령을 캡슐화하고,
유저 포커스에 따라 target action을 지정할 수 있으며, 뷰를 사용하여 명령을 직접 작성할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/e5948c56-7d08-4598-baf7-f56e015c75b2/image.png)

