# Discover concurrency in SwiftUI

1️⃣ Concurrent data models
2️⃣ SwiftUI and the main actor
3️⃣ New concurrency tools

아래와 같이 RestAPI로 웹서비스와 통신하여 이미지를 fetch해서 우주 사진을 랜덤으로 보여주고, 마음에 드는 우주 관련된 이미지를 다운받는 앱을 만드는 과정을 통해 concurrency를 설명한다.

![](https://velog.velcdn.com/images/marisol/post/31296bec-3750-4537-a12b-b8b629a50cc8/image.png)

---

## 1️⃣ Concurrent data models

단일 이미지의 정보를 담고 있는 **SpacePhoto** 모델을 생성한다.

![](https://velog.velcdn.com/images/marisol/post/2bd7c183-d575-4a6d-ad73-d1b06b1fa0cb/image.png)

- title: 사진 제목
- description: 사진 내용
- date: 이미지가 게시된 날짜
- url: 실제 이미지를 가리키는 URL

```Codable```: 서버 응답에서 인스턴스를 쉽게 인스턴스화 하거나, 디스크에 저장할 수 있도록 채택
```Identifiable```: ForEach에 사용할 수 있도록 채택

각각의 이미지 정보를 담고 있는 모델은 ```SpacePhoto```이고,
이 SpacePhoto의 collection을 가져와 보관할 model이 필요하다.

![](https://velog.velcdn.com/images/marisol/post/d0f7436f-0147-40f3-9c97-c16247bdbb9b/image.png)

단일 ```SpacePhoto```들의 목록을 담기 위해, ```Photos``` 모델 객체를 생성한다.
데이터가 업데이트될 때마다 UI를 자동으로 갱신하기 위해 ```ObservableObject```프로토콜을 채택한다.
REST endpoint에서 업데이트된 항목을 가져오기 위해 ```updateItems()``` 메서드 활용할 예정.

---

## 2️⃣ SwiftUI and the main actor

오늘 예시로 든 앱은 총 2개의 뷰가 있다.

1) PhotoView
2) CatalogView

### 1) PhotoView
![](https://velog.velcdn.com/images/marisol/post/920cf460-6045-4cbb-ae4c-64b996d9c940/image.png)

```PhotoView```는 개별 사진(SpacePhoto)을 프로퍼티로 갖고 있고, 간단히 제목을 표시한다.

### 2) CatalogView
CatalogView는 SpacePhoto의 목록을 보여주기 위해,
@StateObject를 추가하고, Photos를 ObservableObject로 초기화 한다.

![](https://velog.velcdn.com/images/marisol/post/4dbb9049-2ee8-4ac1-9f6e-573342cd15d3/image.png)

뷰의 body에 NavigationView를 추가해서 제목을 표시할 수 있도록 하고,
NavigationView 안에 List를 추가한다.
List안에서는 photos를 ForEach를 돌려서 PhotoView에 매핑한다.

여기까지하면 샘플데이터를 문제없이 확인할 수 있지만, 추가로 뷰를 더 꾸며보자면

#### ✏️ Navigation Title 지정

![](https://velog.velcdn.com/images/marisol/post/a15bcf32-2722-470d-a683-e75b8463e1c6/image.png)

NavigationView의 modifier로 네비게이션 타이틀 지정

#### ✏️ List Style 변경

![](https://velog.velcdn.com/images/marisol/post/f5c88c18-6d3f-4cb9-a3c5-a15b45499938/image.png)

List Style도 modifier로 변경 가능

#### ✏️ List Row Separator 설정

![](https://velog.velcdn.com/images/marisol/post/1c6a9293-3f2a-4b55-9af3-9bc0ea7c93e6/image.png)

ForEach안의 항목에서 listRowSeparator를 hidden으로 설정 가능

### 🧐 그런데 SwiftUI는 어떻게 ObservableObject와 상호작용할까?

저번 WWDC 포스팅에서 다뤘던 SwiftUI update life cycle을 구동하는 코드가 "Run Loop"인데,

![](https://velog.velcdn.com/images/marisol/post/ee4df2f4-7860-42c2-95ed-ab98f8111a9e/image.png)

Swift5.5에서 이 Run Loop은 ```MainActor```에서 실행된다.

```MainActor```
: main thread에서의 동작을 보장하는 Actor
(A singleton actor whose executor is equivalent to the main dispatch queue)

- 코드 생성 단계 - actor 내에 선언된 함수에 async를 자동으로 적용
- 컴파일 단계 - concurrency 문제가 발생할 수 있는 코드를 작성하면, 컴파일러가 오류를 일으켜서 수정하도록 해줌
- 실행 단계 - ⭐️ 각 코드가 실제로 순서대로 실행될 수 있게 해줌 ⭐️

SwiftUI Run Loop은 
1) 유저로부터 이벤트를 수신하고
2) 모델을 업데이트하고
3) SwiftUI 뷰를 화면에 렌더링한다

이 세션에서는 이러한 업데이트를 "Run Loop 체크 (Ticks of the run loop)"이라고 표현한다.

이 Run Loop를 길게 펴보면 아래와 같다.

![](https://velog.velcdn.com/images/marisol/post/84347268-af6c-459a-85da-50777a18fe9f/image.png)

SwiftUI에서 ObservableObject와 SwiftUI Run Loop은 흥미로운 방식으로 상호작용한다.

```Photos```에서 아까 넘어갔던 ```updateItems()``` 메서드를 보면,
```updateItems()``` 메서드는 main actor에서 실행된다.
파란색 사각형이 ```updateItems()```이 실행되는 동안의 시간을 나타낸다.

```items = fetched```라는 코드에 집중해보면,
items는 ```@Published``` 프로퍼티이기 때문에 위와 같이 할당하는 것은

1) ```objectWillChange``` 이벤트를 트리거하고
2) 가져온 사진을 items의 저장소에 쓰게(write) 된다 (storage update)
3) 그러면 SwiftUI는 ```objectWillChange```를 보고 내 items에 snapshot을 만든다
4) snapshot 이후의 run loop tick에서 SwiftUI는 현재의 값과 snapshot을 비교한 뒤, 값이 다르면 Photos에 의존하는 View를 업데이트한다

여기서 objectWillChange와 storage update, run loop tick는 모두 main actor에서 발생하며, 순서대로 발생한다.

만약 모델 코드가 main actor에서 너무 많은 작업을 수행하는 경우, 업데이트 속도가 느려질 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/982d2e1e-10e9-4bff-87e8-c4dfe7f122b0/image.png)

예를 들어 다운로드가 완료되기를 기다리는 동안, ```fetchPhotos()``` 메서드가 블락하고 있고, 연결 속도가 느리다고 가정하면, 
main actor를 블락하고 있기 때문에 run loop tick을 놓치게 된다.
-> ⚠️ 사용자들에게 UI상에 문제로 보임

![](https://velog.velcdn.com/images/marisol/post/1f94843e-d507-4d4d-8668-6ec5a20d2b88/image.png)

그래서 이전에는 작업을 수행하기 위해 다른 queue로 전송해서 처리를 할수도 있었는데,
요렇게 하면 괜찮아보이지만 tricky한 issue가 있음

![](https://velog.velcdn.com/images/marisol/post/75a6379e-355b-48e7-8077-aed300f85a0f/image.png)

main actor가 아닌 다른 queue에서 작업을 하면, 다음 run loop tick에서 비교할 때 업데이트되지 않은 item의 snapshot과 비교하여 SwiftUI가 뷰를 업데이트하지 못하는 상황이 생길 수도 있다 (objectWillChange 스냅샷이 run loop tick 직전에 발생한 경우)

![](https://velog.velcdn.com/images/marisol/post/5b5e6b2a-19b5-4c37-a4a0-4d8c83e32123/image.png)

그래서 꼭 위의 순서를 지켜야함!

Swift 5.5 이전에는 상태를 업데이트하기 위해 main queue로 다시 dispatch했을 수도 있지만, 지금은 훨씬 쉬워졌음. 

![](https://velog.velcdn.com/images/marisol/post/8a3f6a83-4a73-45c6-a44b-a67000dbe7c9/image.png)

```await```를 사용하여 main actor로부터 비동기 호출을 함으로써, 비동기 작업이 발생하는 동안 main actor는 다른 작업을 계속 할 수 있게 한다.

![](https://velog.velcdn.com/images/marisol/post/2fb527ef-51d9-4bda-a7bf-c2e3fcbb2e68/image.png)

```updateItems()```에서 긴 I/O를 실행하는 동안 run loop ticking을 계속 작동시키고, UI 문제를 방지할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/df5ecaa4-f996-443b-b842-cc3d7d2a3f75/image.png)

```updateItems()``` 작업이 완료되면, 상태를 업데이트할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/b54881b8-cca1-4c69-942f-4e534bedeb04/image.png)

다른 queue로 dispatch하는 대신, 긴 running operation을 기다리는 것이다. ```await```을 쓰면 메서드는 run loop이 계속될 수 있도록 main actor를 제어한다. 
그리고 대기중인 fetch가 완료되면, main actor가 메서드를 re-enter하여 @Published 프로퍼티를 안전하게 업데이트하고, objectWillChange를 트리거하고, SwiftUI가 새로운 값을 만들 수 있도록 해준다.

![](https://velog.velcdn.com/images/marisol/post/97c7f21b-5651-4fe3-bd95-f428b3798c1f/image.png)

이제 Photos가 main actor에서만 실행된다면, async-await을 사용하기 때문에 아까 말한 tricky한 버그가 발생하지 않을 것 같은데, main actor에서만 실행된다는 것을 어떻게 확실히 보장할 수 있을까?

![](https://velog.velcdn.com/images/marisol/post/aaf4add9-5303-4d50-98f2-1ad5c27db954/image.png)

Photos 클래스에 ```@MainActor```라는 주석을 추가하면, 컴파일러는 Photos의 프로퍼티와 메서드가 Main Actor에서만 액세스할 수 있도록 보장한다.

---

## 3️⃣ New concurrency tools

### ✏️ task
CatalogView가 나타날 때마다 ```updateItems()``` 메서드를 호출하고 싶다면?

예전에는 ```.onAppear```를 사용했지만, 이제 ```.task```를 사용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/fe3ff08e-1b23-4887-8462-1fc47777a938/image.png)

```task```를 사용해서 비동기 태스크를 뷰와 연결할 수 있다. task는 View의 라이프타임이 시작할 때 함께 시작된다. 
task는 기본적으로 비동기이기 때문에, closure안에서 ```updateItems()```를 호출하고, 결과를 기다릴 수 있다.

또한 task의 lifetime은 View의 lifetime과 연결되기 때문에, view의 수명이 끝나면 task가 자동으로 취소된다.


### ✏️ AsyncImage

새로운 ```AsyncImage```API를 통해 서버에서 이미지를 로드하는 것이 매우 쉬워졌다.

서버에서 가져오고 싶은 이미지 URL을 가져와서 AsyncImage에 전달하기만 하면 끝난다.

![](https://velog.velcdn.com/images/marisol/post/7971451f-2a1c-4c62-99a8-8b74b127cc3e/image.png)

+이미지를 불러오는 동안 ProgressView를 보여줄 수도 있고
+이미지 사이즈를 조절할 수도 있다
+이미지 사이즈를 flexible하게 조정하기 위해, minWidth와 minHeight를 설정할 수도 있다

AsyncImage는 오류가 발생하더라도 placeholder를 계속 표시해준다

### ✏️ async

사용자가 좋아하는 이미지를 저장할 수 있도록 하기 위해 저장 버튼을 만들어보자.

![](https://velog.velcdn.com/images/marisol/post/824b7f63-72ec-451e-a65e-e5d3728370cb/image.png)

이 버튼은 이미지를 디스크에 저장하는 비동기 작업을 트리거 한다.
저장이 진행되는 동안 ProgressView를 보여주기 위해
@State로 ```isSaving``` 프로퍼티를 추가한다. (저장 중인지를 나타냄)
isSaving 프로퍼티의 상태를 이용해 "save" 텍스트를 가릴 수도 있고, 
버튼을 비활성화 시킬수도 있다.

### ✏️ refreshable

데이터를 새로고침하는 기능을 제공하는데 사용할 수 있는 ```refreshable``` modifier를 출시했다.

![](https://velog.velcdn.com/images/marisol/post/2816aa64-18f3-45f6-b268-4e114efb0976/image.png)

List에 ```refreshable```을 추가함으로써, SwiftUI에게 이 컨텐츠는 refereshable 임을 알려준다.

```refreshable```의 async closure에서 ```updateItems()```를 호출할 수 있다.

비동기 작업이 완료되면 referesing indicator가 자동으로 해제된다.

이제 pull down 하여 이미지를 새로고침하고,
저장을 눌러 원하는 이미지를 저장하고,
저장된 이미지를 보려면 저장 탭으로 전환할 수 있다.


![](https://velog.velcdn.com/images/marisol/post/f7e8d048-1ef0-4b61-b2a1-3123321f605c/image.png)

