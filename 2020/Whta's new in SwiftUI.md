# What's new in SwiftUI

![](https://velog.velcdn.com/images/marisol/post/63dc9b16-2b4e-444c-a833-1e5e7aff567f/image.png)

작년(2019)에 애플의 모든 플랫폼에서 훌륭한 UI를 구축할 수 있는 ```SwiftUI```를 소개했고,
이번 2번째 릴리즈 때 다양한 새로운 기능에 대해 소개하려고 한다.

엄청 많은 피쳐들이 추가되었기 때문에, 이 세션에서 다루지 못하는 것들은 다른 세션을 통해 다룰 예정임.

![](https://velog.velcdn.com/images/marisol/post/bfef54f5-30b7-4726-8d02-1bf43dbac8cb/image.png)

먼저 앱과 위젯 API에 대해 소개하고,
List와 Collection의 개선사항,
툴바와 컨트롤을 위한 새로운 멀티 플랫폼 API,
앱을 스타일링하기 위한 새로운 종류의 시작적 효과,
마지막으로 SwiftUI 앱에서의 시스템 통합에 대해서 설명한다.

## 1️⃣ Apps and widgets

![](https://velog.velcdn.com/images/marisol/post/0c1218ac-5949-41c2-a7a2-a66dcae9aaa9/image.png)

이제 UIKit/AppKit/WatchKit 앱에 SwiftUI 코드를 임베드하지 않고도, SwiftUI만을 사용하여 전체 앱을 구축할 수 있다. 

![](https://velog.velcdn.com/images/marisol/post/f92797af-c5d4-41cd-822f-ea051259b6fc/image.png)

이것은 실제로 동작하는 앱이며, build & run 할 수 있다.
실제로 앱 전체를 140 character 내로 구성할 수 있을 정도로 간결하다.
SwiftUI는 선언형 앱을 위한 단순하고 유연한 API에 수많은 똑똑하고 자동적이지만 커스텀이 가능한 동작을 포함하고 있다.

![](https://velog.velcdn.com/images/marisol/post/f5783b33-8797-4574-89ef-b685d616297e/image.png)

저번 세션과 동일한 예시인데, 현재 읽고 있는 책들을 추적하고 있는 앱이다.
하단에는 앱의 주요 UI를 나타내는 커스텀 뷰를 작성했다.

![](https://velog.velcdn.com/images/marisol/post/c20739da-a9ba-4ee5-8c91-31616942c420/image.png)

그리고 맨 위에서 그 뷰를 앱의 메인 window의 컨텐츠로 사용하고 있다.

![](https://velog.velcdn.com/images/marisol/post/b096d3ca-6c5f-4ca7-b99c-e3432f7a10a6/image.png)

가장 먼저 주목해야할 것은, 이 두 선언이 얼마나 유사한가 하는 점이다.
우리는 SwiftUI의 새로운 app API를 이미 View 코드를 통해 익숙한 선언적이고, 상태 중심적인 패턴을 따르도록 디자인했다.
(declarative & state-driven patterns)

두 경우(app & view) 모두 프로토콜을 준수하는 struct를 정의한다.

![](https://velog.velcdn.com/images/marisol/post/580b8706-605b-4bae-b047-55535095857a/image.png)

프로퍼티를 사용하여 데이터 종속성을 선언할 수 있으며,
해당 데이터는 app과 view 모두에 대해 선언적인 UI 컨텐츠를 정의하는 body 프로퍼티 내에서 사용된다.

![](https://velog.velcdn.com/images/marisol/post/c0f47ad1-f506-4332-8cce-f1a9d7128a00/image.png)

그러나 한 가지 중요한 차이점은 app의 body 프로퍼티의 반환 유형이다.
앱의 body는 ```scene```을 반환하는데, 플랫폼별로 독립적으로 표시할 수 있는 앱 UI의 일부를 나타낸다.

![](https://velog.velcdn.com/images/marisol/post/167f7ae4-46cf-433d-9c18-cb7a5c1950b4/image.png)

WindowGroup은 SwiftUI가 멀티 플랫폼에서 scene을 표시하는 강력한 기능이다.
iOS앱에서 WindowGroup은 앱을 위한 단일 전체 창을 만들고 관리한다.

![](https://velog.velcdn.com/images/marisol/post/1c628676-3d72-474c-8903-82e5939cf3ca/image.png)

동일한 코드가 WatchOS에서도 실행될 수 있으며, 역시 단일 전체 화면 창을 관리할 수 있다.
물론 WatchOS 앱은 iOS앱과 모양은 다르지만, 핵심 앱 구조는 동일하기 때문에 동일한 app 선언을 공유할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/2905c227-bd47-49f3-b528-c5ca7fea1260/image.png)

이 앱은 tvOS에서도 동작할 수 있고,

![](https://velog.velcdn.com/images/marisol/post/299978b5-2f75-4ef2-9120-8fbb49a1bfae/image.png)

iPad에서도 동작할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/b2207a4b-a981-44b0-88f0-e45397c89dc6/image.png)

그리고 iPadOS는 멀티 윈도우를 지원하기 때문에, 나란히 표시될 수 있는 여러 개의 앱 인스턴스를 만드는 기능도 사용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/8fd33ce3-a625-4dae-ad2b-6a42e7f990b1/image.png)

이것은 여러 창을 지원하는 macOS로도 확장되는데, 

![](https://velog.velcdn.com/images/marisol/post/488bc7e0-9403-4ccc-8c32-3a1355a3101a/image.png)

```command + N```을 통해 새로운 창을 만들 수도 있고,
그것들을 단일 탭 창으로 모을 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/a2a7e48b-5e1c-47e2-923e-130fc64e025e/image.png)

SwiftUI는 자동으로 새 창 메뉴 명령 (commend + N -> new Window)을 메인 메뉴에 추가한다.

이 모든 것은 새로운 ```WindowGroup API```를 사용하여 내 인터페이스르 정의하는 간단한 앱 선언으로 가능하다.

![](https://velog.velcdn.com/images/marisol/post/2b04cd88-e7fc-4ea8-ac52-1b0d4feafadd/image.png)

SwiftUI는 다른 유형의 scene도 지원한다.
예를 들어 Mac 앱에 기본 설정 창 (preferences window)을 추가하기 위해 macOS에서 사용할 수 있는 새로운 Setting scene이 있다. 

Setting scene은 앱 메뉴에서 표준 기본 설정 명령을 자동으로 설정하고, 창에 올바른 스타일 처리를 제공한다.

![](https://velog.velcdn.com/images/marisol/post/1f1beb6a-cbdb-4a4f-be42-2591d9652676/image.png)

SwiftUI의 scene API는 vector shape을 그리기 위해 만든 이 앱과 같은 document-based app도 지원한다.
iOS, iPadOS, macOS에서 지원되는 document-based scene 열기, 편집 및 저장을 자동으로 관리하는 DocumentGroup scene 타입이 올해 새롭게 추가되었다.

![](https://velog.velcdn.com/images/marisol/post/8aca7da3-0703-4aed-aab6-edae84983d32/image.png)

iOS 및 iPadOS에서 DocumentGroup은, 다른 기본 인터페이스가 제공되지 않는 경우,
자동으로 document 브라우저를 표시한다.

![](https://velog.velcdn.com/images/marisol/post/51bb4385-4751-412e-87e4-1e6bc7d42fd9/image.png)

그리고 Mac에서 DoucmentGroup은
각 새 문서에 대해 다른 창을 열고, 일반적인 문서 작업에 대한 명령 (새 문서, 열기, 저장 등..)을 기본 메뉴에 자동으로 추가한다.

![](https://velog.velcdn.com/images/marisol/post/a6349195-bd74-4d19-b5db-45c46a0d8883/image.png)

메뉴 command에 대해 더 얘기하자면, SwiftUI에서 새 command modifier를 사용하면 command를 추가할 수 있다.

예를 들어, 캔버스에 새로운 Shape을 추가하기 위한 커스텀 ```Shape``` 메뉴를 추가했다.
macOS는 자동으로 메인 메뉴의 올바른 섹션에 커스텀 메뉴를 추가하고, 새로운 ```keyboardShortcut``` 모디파이어를 통해 할당한 키보드 단축키를 보여준다.

![](https://velog.velcdn.com/images/marisol/post/798a7455-0eee-47ec-998b-106b19e20c3d/image.png)

![](https://velog.velcdn.com/images/marisol/post/f48a8d89-ea63-49ae-8161-52c4cc76c2a4/image.png)

이런 멀티플랫폼 앱을 구축하기 위해, Xcode의 "new project"에 SwiftUI 전용 새로운 multi-platform 템플릿을 추가했다.

![](https://velog.velcdn.com/images/marisol/post/cfd02d3a-c6b3-4a06-a2a0-68f370c132b7/image.png)

이러한 새로운 템플릿은 멀티 플랫폼 코드에 최적화되어 플랫폼별 components 및 assets뿐만 아니라 공유 코드에 대한 그룹도 자동으로 설정한다.

![](https://velog.velcdn.com/images/marisol/post/2d9b3bfe-311c-4875-ac82-b8278f7a657f/image.png)

또한 올해 새롭게 ```Launch Screen Info.plist key```가 추가되었다.

이를 통해 기본 이미지, 배경색, 상단 및 하단 bar 등 표준 런치 스크린 화면 구성 요소의 다양한 조합을 선택할 수 있다.

이미 잘 작동하고 있는 스토리보드 런치 스크린을 사용하고 있을 수도 있지만,
만약 스토리보드를 사용하지 않는 새로운 SwiftUI 프로젝트라면 Launch Screen configuraion이 간단한 대안이 될 수 있다.

이제 iOS, iPadOS, macOS의 위젯에 대해 소개한다.

![](https://velog.velcdn.com/images/marisol/post/4e257fe2-ceab-41c9-bba5-5855eab355ee/image.png)

Widgets은 SwiftUI로만 구축된다.

새로운 ```Widget``` 프로토콜을 준수하는 커스텀 struct를 사용하여 app이나 view를 만드는 것과 같이 위젯을 만들 수 있다.

![](https://velog.velcdn.com/images/marisol/post/e7252ec6-13a4-4190-9097-243c4381d87e/image.png)

주기적으로 새 앨범을 추천하는 이 위젯과 같이 다양한 타입의 위젯을 만들 수 있다.

![](https://velog.velcdn.com/images/marisol/post/40bc5c5f-77dd-4cc0-bee9-8061d5a278dd/image.png)

또한 SwiftUI를 사용해 Apple Watch를 위한 custom complications를 구축할 수 있다.

이 주간 커피 차트와 같이 full-color complication을 만들 수 있고,

![](https://velog.velcdn.com/images/marisol/post/ea03781a-92a7-4d9f-91a4-8a2238428db5/image.png)

파란색 틴트와 같은 색조의 watch face를 만들 수도 있다.

## 2️⃣ Lists and collections

![](https://velog.velcdn.com/images/marisol/post/e882f154-e13c-482d-a692-77c17c5ea0cb/image.png)

![](https://velog.velcdn.com/images/marisol/post/20bac5f7-5878-492b-a777-468af53b2b44/image.png)

이번 릴리즈에서 List에 새로운 피쳐가 추가되었는데, outline에 대해 얘기해보자면,

![](https://velog.velcdn.com/images/marisol/post/cabe99e4-b569-40d1-8e4d-3cc0cc24adba/image.png)

이니셜라이저에 하위 key path를 제공함으로써 내용의 개요 (outline)를 작성할 수 있다. 기본적으로 macOS와 iOS, iPadOS에서 예상되는 시스템 표준 스타일을 사용하여 표시된다.

outline을 사용하면 불필요한 push-and-pop 네비게이션 패턴을 줄일 수 있다.

![](https://velog.velcdn.com/images/marisol/post/1ae05789-542a-42c9-86a8-f4cdd68dd1ab/image.png)

List와 outline와 마찬가지로
```Grid```도 스크롤 가능한 레이아웃의 콘텐츠 모음을 표시할 수 있다.
올해, SwiftUI에 lazy-loading grid layout이 추가되었으며, 컨텐츠의 원활한 스크롤 그리드를 만들 수 있다.

Grid는 다양한 구성을 지원하는 강력한 레이아웃인데,
예를 들어 사용 가능한 공간에 맞게 columns 수를 조정할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/86de9c4a-b868-4197-831d-ad0df80f70bf/image.png)

또는 가로 또는 세로 모든 방향에서 4개의 columns로 고정되도록, 고정된 수의 열을 강제 적용할 수도 있다.
물론 가로로 스크롤되는 LazyHGrid도 사용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/bf81ea27-3467-43a7-ab86-80b3053c1ec8/image.png)

Lazy하게 로딩되는 Grid 레이아웃은, 비대칭 이미지 갤려이와 같은 커스텀 스크롤 레이아웃을 구축할 때 유용하다.
여기서 모든 갤러리 콘텐츠를 LazyVStack으로 구성하고 있다. 
또한 switch문에 대한 새로운 ViewBuilder를 사용해서 상단의 큰 이미지, 3개로 이미지로 이루어진 비대칭 그룹, 작은 이미지의 행도 자연스럽게 표시할 수 있다. Lazy하게 loading되는 VStack으로 구성된 이 뷰들은 원활하고 매끄러운 갤러리 뷰를 형성한다.

![](https://velog.velcdn.com/images/marisol/post/8e79c1f9-45ea-453c-b598-0d6d7b688717/image.png)

## 3️⃣ Toolbars and Controls

![](https://velog.velcdn.com/images/marisol/post/d43752d5-b61d-459e-97ef-77abb1a98dc6/image.png)

플랫폼 전반에 걸쳐 Toolbar가 새롭게 업데이트 되었으며, 올해 SwiftUI에는 새로운 toolbar modifier를 사용하여 새로운 기능을 사용할 수 있는 API가 추가되었다.

![](https://velog.velcdn.com/images/marisol/post/4b083257-c0d4-4247-87e9-cafa07b34772/image.png)

Toolbar Item은 나머지 뷰에서 사용하는 것과 동일하게 뷰로 구성되며, 이 경우에는 Button으로 구성되었다.

기본적으로는 적절한 위치에 배치되지만, toolbar items를 사용하여 명시적으로 위치를 커스텀할 수도 있다.
이 경우 기본동작은 watchOS의 기본 배치이지만, 다른 배치도 있다.

![](https://velog.velcdn.com/images/marisol/post/a49653a0-33f9-4758-bcb1-3f693192f3c4/image.png)

예를 들어, 확인 및 취소 모달 액션이 있는데, SwiftUI가 자동으로 올바른 위치를 파악한다.

![](https://velog.velcdn.com/images/marisol/post/0275f46c-bc6a-4dff-a547-cf87d77b2fa0/image.png)

또 다른 예는 iPad와 macOS에서 볼 수 있는데, item을 두드러지게 보여주는 principal 배치도 있다.

![](https://velog.velcdn.com/images/marisol/post/a88819eb-8c19-42a2-85e1-7a11f6b6e9c1/image.png)

맨 아래 bottomBar에 toolbarItem이 있는 것도 흔한 배치인데,
placement를 .bottomBar로 명시적으로 지정할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/031d8e16-c329-419c-a1c0-fa06fa4d212e/image.png)

이렇게 toolbar item을 지정할 때 SwiftUI의 새 ```Label``` 뷰를 사용하는 것을 볼 수 있는데,
Label은 UI 요소를 분류하는데 사용하는 ```제목```과 ```아이콘```을 결합한 것이다.

그리고 제목은 accessibility에서도 활용된다.

![](https://velog.velcdn.com/images/marisol/post/e85f0536-1c8a-418c-a5a4-844ef3e40af3/image.png)

![](https://velog.velcdn.com/images/marisol/post/9c10c324-e676-46cc-b59b-f59371bccdbd/image.png)

이 List에는 여러 개의 label rows가 포함되어 있다.
Title은 이미지 크기에 상관없이 완벽하게 정렬되어 있으며, dynamic type size를 사용할 때 label의 장점이 발휘된다!

이 뷰는 기본 large size의 레이아웃을 보여주고 있는데

![](https://velog.velcdn.com/images/marisol/post/3dbf5b9e-5686-4f3e-a31a-fdf2ab756f75/image.png)

extraExtraLarge 사이즈로 늘리면 아이콘과 제목이 모두 자동으로 업데이트 된다.

![](https://velog.velcdn.com/images/marisol/post/7517bd91-5cdf-47f1-8b53-1b1719a8f784/image.png)

접근성 크기에서도 적용된다.

![](https://velog.velcdn.com/images/marisol/post/4d9589dd-c050-4aef-b288-2a2879abeffd/image.png)

toolbar에 아이콘만 표시되는 아이템들이 있기 때문에, 추가 도움말이나 컨텍스트를 제공하는 것이 중요하다.
새로운 help modifier를 사용하면, 툴팁처럼 설명을 표시할 수 있다.

이 modifier는 모든 플랫폼에서 사용할 수 있고,

![](https://velog.velcdn.com/images/marisol/post/0a7137c8-f484-4703-bc45-01c9a2fb45d4/image.png)

접근성에 힌트를 제공해서 앱에 더 나은 음성 경험을 제공하기도 한다.
SwiftUI는 모두의 앱의 경험을 향상시킬 수 있다.✨

![](https://velog.velcdn.com/images/marisol/post/d33ce297-8cb3-4efc-a510-1e32eb992e73/image.png)

![](https://velog.velcdn.com/images/marisol/post/0d8c4659-bb8e-4107-adaa-5f7ca89dcffd/image.png)

keyboard shortcut modifier를 사용하면 유저와 앱의 상호 작용 방식에 더 많은 유연성을 줄 수 있는데,
앞서 본 것 처럼 iPad와 macOS에서 키보드 단축키를 통해 접근하기 때문에 scene 명령에 가장 자주 사용된다.

![](https://velog.velcdn.com/images/marisol/post/739924f3-056b-41ab-92b4-f98c8b6bb67d/image.png)

그리고 앱 전체에서 사용할 수 있는 몇 가지 새로운 control이 있는데,
먼저 Progress view가 있다.

![](https://velog.velcdn.com/images/marisol/post/46b29104-abee-4c20-8353-cbfe20fad0da/image.png)

![](https://velog.velcdn.com/images/marisol/post/6dab41e3-1591-4e1e-a128-76ddf3a82311/image.png)

![](https://velog.velcdn.com/images/marisol/post/45f77930-4997-44af-be89-3c5f55ef2103/image.png)

Progress view는 시간 경과에 따른 진행 상황을 표시하는데 사용한다. 
선형 및 원형 스타일의 뷰가 있다.

![](https://velog.velcdn.com/images/marisol/post/6b352fa3-3d0f-4b0b-87e0-7511fb188464/image.png)

또 다른 Control로는 게이지가 있다. 게이지는 전체 용량에 상대적인 값의 수준을 표시하는데에 사용한다.
예시는 정원 토양의 산성도를 추적하기 위한 원형 watchOS 게이지이다.

![](https://velog.velcdn.com/images/marisol/post/afe5231f-4826-4054-a83d-610b8d39bee7/image.png)

게이지에는 추가적인 옵션 커스텀 기능이 있다. 
정확한 pH를 알아야할 정도로 토마토는 까다로운 작물인데, 현재 값 레이블을 추가하여 표시할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/11f804c0-a381-4a30-8770-bc56ab0016c5/image.png)

또한 게이지에 최소 및 최대 값 레이블도 포함할 수 있다. 경우에 따라 이미지 아이콘일 수도 있지만, 여기서는 pH 수준을 텍스트로 표시했다.

## 4️⃣ New effects and styling

![](https://velog.velcdn.com/images/marisol/post/e4a1dafb-f312-4f40-bdbf-17c34b11b647/image.png)

이제 SwiftUI를 사용해 몰입감있고 재미있는 경험을 만드는 방법을 살펴보려고 한다.

![](https://velog.velcdn.com/images/marisol/post/8c4a2583-19c7-4d43-ba5e-b674a77d5426/image.png)

macOS Big Sur는 모두 SwiftUI를 사용하여 메뉴 바의 새로운 제어 센터와 알림 센터를 리뉴얼했다.
제어 센터는 SwiftUI의 새로운 피처를 사용해서 다양한 모듈 안밖에서 부드러운 애니메이션을 제공하며, 우리 앱에 적용할 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/74b7cf4b-fe5f-4bf9-8629-ca2af3a77266/image.png)

좋아하는 앨범을 모으기 위해 작은 앱을 만들었다. 앨범의 스크롤 grid와 맨 아래 선택한 앨범의 한 행으로 이루어져있다.

![](https://velog.velcdn.com/images/marisol/post/8415691d-03c1-494b-a461-805b2c0f78f5/image.png)

앨범을 선택할 때, 앨범이 맨 아래 행으로 pop 되는 것보다, grid에서 유동적으로 전환되기를 원한다.
그 때 ```matchedGeometryEffect```를 사용할 수 있다.

album의 id를 식별자로 사용하여 grid뷰와 row뷰에 일치하는 modifier를 적용하여 두 뷰를 연결할 수 있다.
이렇게만 하면 앨범이 한 섹션에서 제거되고, 다른 섹션에 삽입되면 SwiftUI는 원활한 애니메이션으로 프레임을 자동으로 업데이트한다.

![](https://velog.velcdn.com/images/marisol/post/bd702242-38ca-4538-9d60-d631558c0493/image.png)

그리고 ContainerRelativeShape이라는 도구가 추가되었는데,
가장 가까운 모양의 유사한 path를 그리는 새 Shape type이다.

![](https://velog.velcdn.com/images/marisol/post/e59c92e2-c999-41f4-b427-009ddf0ca3dd/image.png)

패딩을 적용하면 offset이 외부 컨테이너 모양으로 변경된다.

![](https://velog.velcdn.com/images/marisol/post/5d1a50e6-d36e-49c0-bb65-0bf49b2ba605/image.png)

텍스트 관련 요소의 개선사항도 있다.
커스텀 폰트도 dynamic type 변경에 따라 자동으로 크기가 조절된다.

![](https://velog.velcdn.com/images/marisol/post/74a4f1dc-c039-4bb9-a615-7b308d05eaa7/image.png)

또한 Text에 이미지도 추가할 수 있어서 dynamic type에 함께 대응 가능하다.

![](https://velog.velcdn.com/images/marisol/post/602686bd-6831-4f31-99dc-e04abdca3b95/image.png)

그리고 레이아웃과 같은 커스텀 비텍스트 메트릭의 경우, 현재 dynamic type 크기에 대해 일부 기본값을 자동으로 조절하는 ```@ScaledMetric``` 프로퍼티 래퍼도 추가되었다.

![](https://velog.velcdn.com/images/marisol/post/89023f1a-53ae-4348-9adc-1bed7563bd06/image.png)

이러한 기능들을 통해 접근성 크기에서 잘 반응하는, 응답성이 뛰어난 맞춤형 레이아웃을 쉽게 만들 수 있다.

![](https://velog.velcdn.com/images/marisol/post/2f85f52e-c97d-48ca-83b4-3d1a855a8a34/image.png)

또한 커스텀 액센트 색상을 사용하여 앱을 다른 앱들과 다르게 만들 수 있다.
올해는 macOS에서 액센트 색상을 커스텀할 수 있는 기능과, Xcode12의 asset 카탈로그에서 직접 액센트 색상을 커스텀할 수 있는 기능이 새롭게 지원되었다.

![](https://velog.velcdn.com/images/marisol/post/39e0ca38-b9c2-48aa-9a68-79dd36ccb91d/image.png)

이렇게 하면 앱이 지원하는 모든 플랫폼에 대해 해당 색상을 쉽게 지정할 수 있다.
앱 전체에 광범위한 테마 색상을 적용하는데 유용하지만, 색상을 디테일하게 커스텀하려는 경우도 있을 것이다.

![](https://velog.velcdn.com/images/marisol/post/afb205a9-9630-42f8-9752-63c293a2ebea/image.png)

기본적으로 iPadOS와 macOS의 아이콘은 앱의 액센트 색상을 따른다.

![](https://velog.velcdn.com/images/marisol/post/ad9295e6-3555-44c3-bb94-4e84d2259035/image.png)

새로운 ```listItemTint``` modifier를 사용하면 항목별, 또는 전체 섹션에 대한 아이콘의 색상을 커스텀할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/ba8ca61f-7473-43fb-808a-60cff2c15630/image.png)

macOS 사이드 바에도 동일하게 적용되고,

![](https://velog.velcdn.com/images/marisol/post/dcd06f9c-1a3a-4f32-bffa-6a442ca93c06/image.png)

watchOS에서도 적용된다.

![](https://velog.velcdn.com/images/marisol/post/068f10c6-1b73-40a7-a08c-3e2e4495ccd3/image.png)

또한 버튼, 토글과 같은 컨트롤을 명시적으로 tinting할 수도 있다. 여기서 switch는 기본 녹색이 아닌, 전체 테마 액센트 색상을 따르도록 커스텀 되었다.

## 5️⃣ System integration

![](https://velog.velcdn.com/images/marisol/post/5f4531b8-f574-49f1-956a-ecb0a63d7676/image.png)

마지막으로 앱이 시스템에서 제공하는 기능과 서비스를 통합하고 활용할 수 있는 방법에 대해 소개한다.
올해 SwiftUI는 URL을 열기 위한 first-class API를 도입했으며, 모든 플랫폼에서 사용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/253990ca-a6ee-40a1-a295-200c50a629d3/image.png)

그 중 하나는 URL을 받아 link를 여는 새로운 ```Link``` 뷰이다.
원하는 대로 해당 레이블로 뷰를 만들고, 기본 웹브라우저로 해당 URL을 열 수 있다.

![](https://velog.velcdn.com/images/marisol/post/6aae7dd7-7e00-4b6b-be4a-c3c6fcdb1344/image.png)

혹은 다른 앱으로 직접 링크를 열 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/06c77ed1-6f13-472d-b80d-ca2f90984418/image.png)

Link는 또한 위젯에서도 작동하며, 여기서 기본 앱 내의 콘텐츠로 직접 다시 연결할 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/7ff1f2cd-1f40-481f-bece-3c29f3177243/image.png)

이제 앱의 맥락에서, URL을 프로그래밍 방식으로 열어야 하는 경우도 있다.
이런 경우를 위해 environment에 ```openURL```이라는 액션이 있으며, optional completion handler에서 URL을 열기 위해 호출된다.

![](https://velog.velcdn.com/images/marisol/post/c38d566d-d600-4327-9ca2-2cfda95dc572/image.png)

마지막으로 다른 서비스와 통합할 수 있는 사례는 Sign in with Apple 버튼이다.

모든 플랫폼에서 사용할 수 있는 API이다.
AuthenticationServices를 import하기만 하면 API를 사용할 수 있다. 다른 프레임워크는 필요하지 않다.

![](https://velog.velcdn.com/images/marisol/post/d8501ea4-c6e8-4aad-9f7e-ea60b4035754/image.png)

