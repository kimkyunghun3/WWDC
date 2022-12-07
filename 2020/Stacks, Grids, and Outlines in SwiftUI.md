# Stacks, Grids, and Outlines in SwiftUI
![](https://velog.velcdn.com/images/marisol/post/0274c4d9-3669-479e-aa8d-e5e3319d8d47/image.png)

SwiftUI에는 뷰 컬렉션을 수평/수직으로 정렬하기 위한 다양한 레이아웃 기본 요소가 내장되어 있다.
MacOS의 새로운 알림 센터는 SwiftUI로 구현되었음!

간단한 Stack, Grid는 계층(hierarchy), 정렬(alignment), 간격(spacing)을 사용하여 함께 작동하며 많은 정보를 구성한다.

![](https://velog.velcdn.com/images/marisol/post/0212340a-5eb1-476b-a1be-938d48a78771/image.png)

1️⃣ Stacks 
- SwiftUI의 기본 레이아웃 유형

2️⃣ Grids
- 그리드 레이아웃

3️⃣ Lists
- 기존 List 타입의 계층적 데이터를 표시할 수 있는 새로운 기능

4️⃣ Outlines
- UI 제어를 위한 Outlines와 Forms

## ✏️ 1. Stacks

🥪 별점과 함께 기억에 남는 샌드위치를 보여주는 갤러리 뷰 구성하기! 🥪

![](https://velog.velcdn.com/images/marisol/post/5baceea0-f8c6-4046-8697-538f10d8babb/image.png)

샌드위치 데이터 모델은 id, 이름, 별점, 이미지로 간단하다.
갤러리에 개별 샌드위치를 전시하는 뷰도 간단한데,
크기가 조정된 이미지와 함께 샌드위치에 대한 정보를 포함하는 오버레이가 추가되어 있다.

![](https://velog.velcdn.com/images/marisol/post/c690afe4-6ff8-4e3d-a7ba-2608b590f2d2/image.png)

이미지에 오버레이 되는 ```BannerView```는 VStack을 사용하여 샌드위치의 title과 별점을 정렬한다.
RatingView는 별 5개의 이미지가 단순히 HStack으로 정렬되어 있다.

![](https://velog.velcdn.com/images/marisol/post/5821d264-7557-4b50-910a-afec17d6600b/image.png)

샌드위치 갤러리 뷰의 초기 구성은 간단하다. Sandwich의 뷰들을 VStack을 활용하여 나타내면 된다.
만약 샌드위치가 많을 수록 리스트는 점점 길어질 것이기 때문에, ForEach를 활용하여 각각의 샌드위치에 대한 뷰를 만들어야 한다.
그리고 VStack은 자체적으로 스크롤되지 않기 때문에, ScrollView로 감싸야 한다.

현재까지는 괜찮지만, 갤러리에 표시해야 하는 사진이 많을 수록, 화면이 표시될 때 응답하는 시간이 더 오래 걸린다.

처음에는 첫 화면의 이미지만 렌더링하고, 나머지는 사용자가 갤러리를 스크롤할 때 필요에 따라 로드할 수 있도록 하기 위해서, 새로운 Stack 유형을 사용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/5cd18bda-1d20-407f-8c7d-608051c33ebb/image.png)

LazyVStack과 LazyHStack은 콘텐츠가 보임에 따라 점진적으로 렌더링이 된다는 점을 제외하고는 기존 Vstack, HStack과 동일하다.
뷰는 모든 이미지를 로드하고 측정하는 메인 스레드를 블락하지 않으며, 앱의 메모리 공간이 불필요하게 커지지 않는다.

![](https://velog.velcdn.com/images/marisol/post/2436bb56-fa27-478b-a59c-f7ae2be65e2c/image.png)

그런데 HeroView의 VStack이 유일한 스택이 아니다. 
각 HeroView에는 평점을 배치하기 위한 RatingView에 HStack도 있고,
HeroView에 overlay하기 위한 ZStack도 있다.
그래서 외부 스택을 Lazy하게 만들었기 때문에, 내부 스택들도 Lazy하게 만들어야 할까?

이 경우에서 정답은 그렇지 않다.
HeroView 내의 스택들을 Lazy하게 만드는 것은 실제로 어떤 이점도 제공하지 않는다. 
뷰는 화면에 나타나는 즉시 내용이 표시되어야 하며, 컨테이너의 기본 동작에 관계없이 모든 것을 한번에 로드해야 한다.

일반적으로 어떤 유형의 Stack을 사용할지 모르는 경우에는 VStack 또는 HStack을 사용하되,
성능 병목 현상(performance bottlenecks)을 해결하는 방법으로 LazyStack을 채택해야 한다.

## ✏️ 2. Grids

아이폰에서는 현재 보이는 것이 만족스럽지만, 아이패드 같은 더 큰 화면에서는 어떻게 작동할까?🧐

![](https://velog.velcdn.com/images/marisol/post/2b7d2976-40df-4f3d-88ec-30df5d0f60c9/image.png)

![](https://velog.velcdn.com/images/marisol/post/d4511904-08e2-4e44-9910-2cf95bf91bc9/image.png)

아이폰에서와 똑같고 샌드위치의 크기만 커졌는데, 화면에 더 많은 샌드위치를 넣고 싶다!
➡️ 단일 row에서 여러 row가 있는 그리드로 변경할수 있다면, 전체 샌드위치의 밀도를 높일수 있다.

![](https://velog.velcdn.com/images/marisol/post/fba1786c-1441-493c-948d-ab563c2cf825/image.png)

이름에서 알 수 있듯이, 컨텐츠의 레이아웃을 grid로 구축하고, Stack과 사용이 유사하다.
LazyVGrid를 사용하여 multi-column 레이아웃을 쉽게 구현할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/d27a59b9-9edb-4eb0-a7c7-14e3fda062b9/image.png)

샌드위치를 하나가 아니라 세 칸으로 업데이트 할 건데, 이전 코드와 주요 차이점은 레이아웃 컨테이너(```LazyVGrid```)다.
LazyVStack 대신 LazyVGrid를 사용하고 있으며, grid의 column을 알려주기 위해 GridItem의 배열을 만든다.
각 항목은 개별 column의 너비를 계산하는 방법을 지정한다. 여기서는 세 개의 column을 정의하고, GridItem은 디폴트로 flexible하기 때문에 이 배열은 동일한 너비의 column으로 grid를 채운다.

디바이스의 방향을 landscape orientation로 변경하면, column의 수는 같고, 사진이 더 넓어질 뿐이다.

![](https://velog.velcdn.com/images/marisol/post/216cdd4e-09e9-4d8c-8e8e-e6cd2d8eee73/image.png)

Grid 레이아웃은 사용 가능한 공간에 맞춰 다양한 수의 columns를 생성할 수도 있다.
예를 들어, 지정된 최소 열 너비를 유지하면서 동일한 너비의 열을 최대한 많이 생성하는 adaptive Grid Item을 선언했다.
추가 열을 넣을 수 있는 가로 모드에 적합하며, 창 크기를 임의로 조정할 수 있는 macOS에서도 유용하다.

## ✏️ 3. Lists

List는 단순한 기본 레이아웃 그 이상의 기능을 한다. interactive하고, selection 관리와 스크롤을 지원한다.
List의 컨텐츠는 항상 Lazy하게 로드된다.

![](https://velog.velcdn.com/images/marisol/post/b93c1445-9c22-44c0-a06e-f725eeb34c30/image.png)

ShapeEdit은 macOS, iPadOS, iOS에서 실행되는 문서 기반 앱이다.
사이드 바를 볼 수 있는데, 여기서 List를 사용하여 캔버스에 도형을 열거했다.
그룹에는 다른 그룹도 포함될 수 있기 때문에, 이 flat한 list는 트리 구조가 되어야 한다.

![](https://velog.velcdn.com/images/marisol/post/153aef95-d0b6-4c70-ae3c-5fe349d3428f/image.png)

List를 Outline으로 만들려면, list에게 새로운 이니셜라이저를 사용하여 children keyPath를 전달하면 나머지는 SwiftUI가 처리해 준다.
이 한 가지 변경사항을 통해 사이드 바에 전체 shape 계층이 표시된다.

## ✏️ 4. Outlines

OutlineGroup
- disclosure view를 사용하여 계층 구조를 나타낼 때 사용

DisclosureGroup
- disclosure control 상태에 따라 content를 표시하거나 숨길 때 사용

![](https://velog.velcdn.com/images/marisol/post/826f9c3d-eefd-442f-8316-014081fa2695/image.png)

List는 그대로 두고, canvas를 ForEach를 돌려서 반복한다.
각 canvas에 section을 사용하여 canvas의 이름을 나타내는 header를 추가한다.
그리고 section의 content는 OutlineGroup을 사용했다.

OutlineGroup은 flat한 배열에서 반복하는 대신, 크리 구조의 데이터를 통과한다는 점을 제외하면 ForEach와 유사하다.
여기에는 graphic 배열과 children key path가 필요하다.
OutlineGroup은 각 item이 GraphicRow인 아웃라인을 생성한다.

![](https://velog.velcdn.com/images/marisol/post/e5af61c5-c075-439d-9b78-7e83ab660634/image.png)

때때로 앱은 이 inspector 팝업과 같이, 규칙적인 계층 구조를 따르지 않는 컨트롤 또는 기타 정보를 숨기거나 표시해야 한다.
이러한 경우 DisclosureGroups를 사용한다.

DisclosureGroup은 disclosure Indicator, label, content를 제공한다.

![](https://velog.velcdn.com/images/marisol/post/1364bee3-51ad-4fd9-af7a-3f082f6beadc/image.png)

만약 Fill이 자주 사용하는 속성이라 항상 펼쳐두고 싶다면,
@State로 Fill이 Expand되었는지를 나타내는 ```areFillControlsShowing```을 true로 설정해두면, Fill은 디폴트로 항상 Expand되어 있다.

![](https://velog.velcdn.com/images/marisol/post/4c7c414e-f0bc-453f-b18c-d0708db991fc/image.png)

HStack과 VStack은 고정된 항목 배열의 레이아웃을 제어하는 데에 적합하다.
또 새로운 Lazy Stack은 가변적이고, 잠재적으로 큰 항목 배열을 표시하기 위해 ScrollView 내에서 잘 작동한다.
LazyGrid는 배열을 그리드로 표시하는 편리하고 새로운 방법을 제공한다.
List는 선택, 스크롤, lazy한 컨텐츠 로드 및 계층적 데이터 표시를 지원하는 기능을 제공한다.
Inspector 예제에서 본 것처럼, 설정 및 기타 컨트롤 목록에 대해 Form을 사용한다.
마지막으로, 새로운 Outline과 DisclosureGroup을 통해 앱에 적합한 정보의 표시를 커스텀할 수 있다.
