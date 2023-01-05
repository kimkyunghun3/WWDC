# [WWDC19] Combine in Practice

Combine의 간략한 개요

value나 event를 갖고 있는 Publisher가 있고,
Subscriber는 그 값을 Publisher로부터 받고 싶어한다.
그리고 Operator가 Subscriber와 Publisher를 연결한다.

Subscriber는 그 Publisher에게 값을 받고 싶다고 선언하고,
그 후에는 Publisher가 값을 다운스트림으로 보내기 시작할 수 있다.
이 작업은 Publisher가 값 전송을 중지하기로 결정하거나, 값 전송이 완료되었거나, 오류가 발생할 때까지 계속된다.

![](https://velog.velcdn.com/images/marisol/post/b63bd97c-144a-43ab-9774-34a429a473fd/image.png)

이러한 일반적인 형태의 커뮤니케이션은 콜백, 클로저 등 비동기적인 커뮤니케이션이 존재하는 소프트웨어 전반에 걸쳐 나타난다.
그리거 이것이 Combine이 추구하는 패턴이다.

## ✏️ Publisher

![](https://velog.velcdn.com/images/marisol/post/8dbb53ef-5650-4a84-be51-3899233d39d6/image.png)

저번 Introducing Combine에서 다뤘던 내용이라 간단하게만 짚고 넘어가면, Combine의 Publisher는 Publisher 프로토콜을 채택한다.
2가지 연관 타입, Publish하는 값의 타입과 실패 타입을 가진다.

또한 연관 타입이 일치해야 하는 제약 조건을 사용하여 Subscriber를 자신에게 연결하는 메서드를 갖고 있다.

![](https://velog.velcdn.com/images/marisol/post/7917066b-5d8a-490f-b35d-c6638cc260a9/image.png)

이제 Combine 실습을 해볼 건데, 마법을 다운로드 받을 수 있는 앱을 Combine을 이용해 만들어보려고 한다.

![](https://velog.velcdn.com/images/marisol/post/0406db21-e247-4c1c-84ee-f160bef594e5/image.png)

Notification을 publish할 Publisher를 생성했다.
NotificationCenter Publisher는 notification을 전달하며, fail하지 않는다.

Notification Publisher를 가지고 있지만, 우리가 원하는 것은 다운로드 받는 마법을 설명하는 내부의 데이터이다.

user info dictionary에 데이터가 들어있고, Combine은 내부에 들어가서 우리가 원하는 형태로 notification을 변형해주는  ```map```이라는 기능을 제공한다.

![](https://velog.velcdn.com/images/marisol/post/8f538296-6ee8-4d74-a528-3497e0c9d130/image.png)

Publisher에서 동작하는 map과 같은 메서드를 호출하고, 새로운 Publisher의 Operator를 리턴한다. 그리고 이 데이터는 앱에 미리 정의해둔 타입의 JSON으로 변환될 수 있다.

![](https://velog.velcdn.com/images/marisol/post/821e7426-eafa-44ef-aba8-becd69ecd81d/image.png)

그래서 또다른 Combine Operator를 사용하여 데이터를 디코딩할 수 있고, 이 연산자를 ```tryMap```이라고 부른다. 그것은 스트림에서 던져진 오류를 변환하는 기능을 추가한다는 점을 제외하면 ```map```과 동일하다.

![](https://velog.velcdn.com/images/marisol/post/79512c0a-da06-456f-a78b-1b639cba2cb7/image.png)

데이터에서 Custom Type을 디코딩하는 것이 매우 흔한 작업이기 때문에, 실제로 이를 처리하는 Operator인 ```decode```를 제공한다.👏

![](https://velog.velcdn.com/images/marisol/post/bd57b085-56ce-429f-b1a2-36e177080dc8/image.png)

Combine에서는 실패 가능성에 대해 적절히 대응하는 것이 매우 중요하다.
모든 Publisher와 Subscriber는 자신이 생성하거나 허용하는 failure의 정확한 유형을 기술할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/feb44e31-36a2-4ab9-893a-6acd8799e9c3/image.png)

가장 간단한 방법은 failure가 절대 일어나지 않을 것이라고 해두는 것이다. 그러면 당연히 이 Publisher의 failure type은 "Never"가 될 것이다.

만약 업스트림 Publisher와 다운스트림 Subscriber 사이에 ```assertNoFailure```가 껴있는 상황을 생각해보자.

![](https://velog.velcdn.com/images/marisol/post/089b82e5-c797-4129-9bc0-4f365aa07343/image.png)

값을 잘 받았을 때는 Operator는 값을 잘 전달할 것이다.

![](https://velog.velcdn.com/images/marisol/post/dbfee016-c996-4f98-a914-47ee14f9d390/image.png)

하지만 업스트림으로부터 error가 전달된다면, fatal error가 발생할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/ac5fec4e-d3e9-40fa-872d-2a1285cfaa21/image.png)

다행히도 failure에 관련된 다양한 Operator들이 있다. 업스트림 Publisher에 연결을 재시도 할 수도 있고, error를 다른 타입으로 변환할 수도 있다. 특히 유용한 Operator에는 ```catch```라는 연산자가 있다.

```catch```를 사용하면 원래 업스트림 Publisher에서 오류가 발생한 경우에 사용할 복구 Publisher를 정의하는 클로저를 사용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/4800298f-1c43-487c-afd5-f3b64951de79/image.png)

```assertNoFailure``` 대신 ```catch```를 사용해보자.

이전과 마찬가지로, 값은 다운스트림 Publisher를 따라 전달된다. 그러나 오류가 발생하면 기존 업스트림 연결이 종료된다.

![](https://velog.velcdn.com/images/marisol/post/e3d4d790-5830-4841-bafd-1d8acb630eb7/image.png)

그런 다음 제공된 복구 클로저를 호출하여 새Publisher를 생성하고 이 Publisher를 구독하며, 이후에는 자유롭게 값을 받을 수 있다.

이러한 방식으로 ```catch``` Operator를 사용하면 원래 Publisher를 새 Publisher로 교체하여 오류를 복구할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/fbc6ec11-4a53-4880-a40f-5b5f59d36448/image.png)

```catch```를 사용하는 것은 다른 Operator들과 거의 동일하지만, catch의 클로저는 Publisher를 반환해야한다.

Combine은 이미 게시할 값이 있는 경우에 대한 특수한 Publisher를 정의하며, ```Just```라고 부른다.
Just는 항상 값을 publish하며, fail하지 않는 Publisher이다.

이것으로 여기서 Publisher의 리턴타입은 더 이상 fail할 수 없다.

![](https://velog.velcdn.com/images/marisol/post/fe0724a9-a044-4e9e-beef-8436623dbfa4/image.png)

Notification의 Publisher로 시작해서 map을 이용해 Data로 변환했고, decode Operator를 이용해 데이터를 Custom Type으로 변환했다. 하지만 디코딩은 많은 이유로 실패할 수 있기 때문에, failure가 발생할 경우 업스트림을 placeholder로 대체했다.

하지만 일단 Recovery Publisher로 전환하면, 원래 Publisher의 구독을 종료했기 때문에 다른 알림이 다시 표시되지 않는다.
우리가 진짜로 원하는 것은 디코딩을 시도하다가 실패할 경우, 원래 업스트림에 대한 연결을 유지하면서 placeholder를 사용하는 것이다.

이럴 때 사용하는 Operator가 ```flatMap```이다. 
```flatMap``` 안에서 decode도 하고, catch도 하고, Just도 쓸 예정!

![](https://velog.velcdn.com/images/marisol/post/8e281cb2-80ff-45e4-aaee-4de62566766f/image.png)

```flatMap```은 이름과 같이 ```map```과 매우 유사하게 작동한다. 업스트림 Publisher로부터 값이 제공되지만, 업스트림 Publisher와는 독립적으로 해당 값과 동일한 값을 새 Publisher(Just Publisher)로 publish한다.

![](https://velog.velcdn.com/images/marisol/post/47106b53-de67-4f72-b0ea-91b4aa835ef7/image.png)

이전과 마찬가지로, 값은 업스트림에서 ```flatMap``` Operator로 전달된다. ```flatMap```은 그 값을 새로운 Publisher로 변환하기 위해 클로저를 호출할 것이고, ```decode```와 ```catch```가 실행된다.

그런 다음 ```flatMap```은 이 새로운 Publisher를 구독하여 다운스트림에게 결과 값을 제공한다.

![](https://velog.velcdn.com/images/marisol/post/300c2c4d-4817-498c-82f6-563d32180196/image.png)

만약 decode가 작업 중에 오류를 발생시켰다고 상상해보자.

![](https://velog.velcdn.com/images/marisol/post/0b866703-c6cf-4609-afb2-86f0bc4b0da6/image.png)

failure가 ```catch```에 도달하면 recovery Publisher로 대체된다.(Just와의 연결은 끊어짐) 그리고 이것은 ```flatMap```으로 리턴되는 Publisher가 될 것이며, 따라서 해당 작업이 절대 fail하지 않도록 보장한다.

![](https://velog.velcdn.com/images/marisol/post/f34a541e-b25b-4064-8e27-88b0c4cd7b5b/image.png)

이제 스트림의 첫번째 오류 처리에서 중단했던 곳에서 다시 시작해보면,

![](https://velog.velcdn.com/images/marisol/post/1b67c87f-d4be-4005-8b8b-84bc027b5fbb/image.png)

```catch```와 마찬가지로, 우리가 받은 데이터로부터 새로운 Publisher인 ```Just```를 사용하려고 한다.
방금 ```map``` Operator로부터 디코딩한 데이터를 ```flatMap``` Operator를 사용하여 Custom Type으로 디코딩하고, fail하면 placeholder를 전달하고, fail하지 않으면 Subscriber에게 값을 전달한다.

그러면 이 결과로 나오는 Publisher는 절대 실패하지 않는 (never fail) Magic Trick Publisher가 된다.

여기까지가 업스트림의 failure를 처리하는 방법이고, 원래 하고 싶었던 일 (magic trick의 이름을 Publish하는 것)을 해보려고 한다.

![](https://velog.velcdn.com/images/marisol/post/67f36159-14cb-4b0b-bee9-eb71f199a9cb/image.png)

```publisher``` Operator를 사용하여 간단하게 처리할 수 있다. 그리고 type safe key path를 통해 Produce Magic Trick 내부로 가서 새로운 Publisher (이 경우에는 String Publisher)를 생성한다.

![](https://velog.velcdn.com/images/marisol/post/63449f1c-184e-49d5-9402-800a581f8c59/image.png)

여기서 강력한 기능을 제공하는 Operator를 소개하는데, ```Scheduled Operator```라고 부른다. 실생활에서 스케줄을 잡는 것과 마찬가지로, ```Scheduled Operator```는 특정 이벤트가 전달되는 시기와 장소를 설명할 수 있다.

```Scheduled Operator```는 RunLoop 및 DispatchQueue에 의해 기본적으로 지원되며, 이벤트 전달을 특정 미래 시점까지 연기하는 ```delay```와 같은 Operator가 포함된다.

![](https://velog.velcdn.com/images/marisol/post/12c19dda-c61a-4b73-aa43-3af4c8594785/image.png)

이벤트가 지정된 속도보다 빠르게 전달되지 않도록 하는 ```throttle```도 있고,
```receive(:on)```와 같은 Operator를 사용하여 다운스트림 수신 이벤트가 특정 스레드 또는 큐에 전달되도록 할 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/acd2e933-3501-4723-8f3d-27edab182742/image.png)

방금 얘기했던 ```receive(:on)``` Operator를 사용하여 magic trick의 이름이 항상 main queue에 전달되도록 했다.

![](https://velog.velcdn.com/images/marisol/post/e9df499e-217b-4edf-8951-34497fbe8ac5/image.png)

Publisher chain을 리뷰해보자면,
```flatMap```으로 Magic Trick Output을 얻고,
```publisher(for:)```를 사용하여 magic trick 내부로 들어가 magic trick의 이름을 추출했다.
마지막으로 ```receive(:on)``` Operator를 사용하여 작업을 메인 스레드로 이동시켰다.

이제 메인스레드에서 UI를 업데이트하는 경우, publish된 값이 이미 올바른 스레드에 있게 된다.

## ✏️ Subscriber

![](https://velog.velcdn.com/images/marisol/post/10ed77e0-6874-4191-8e08-a811dd3c728e/image.png)

Publisher와 마찬가지로, Combine의 Subscriber에는 두 가지 associatedType (Input과 Failure)이 있다.

또한 subscription, value, completion을 받기 위한 3가지 이벤트 메서드가 있다. 이 메서드들이 호출될 순서는 미리 정의되어 있으며, 3가지 규칙을 따른다.

> 1️⃣ subscribe 호출에 대한 응답으로, Publisher는 ```receive(subscription:)```을 정확히 _**한 번**_ 호출한다.
2️⃣ Publisher는 Subscriber가 요청한 후, Subscriber에게 0개 이상의 값을 다운스트림으로 보낼 수 있다.
3️⃣ Publisher는 _**최대 한 번**_의 completion만 전송할 수 있으며, completion은 Publisher가 완료되었거나, failure가 발생했음을 나타낼 수 있다. 그리고 한 번 completion이 전송되면, 더 이상 값이 publish되지 않는다.

![](https://velog.velcdn.com/images/marisol/post/913069e3-2d98-46e7-a43b-52ab3562c6c4/image.png)

![](https://velog.velcdn.com/images/marisol/post/3ade11e5-5e22-4da2-9de9-a8dc30b10864/image.png)

![](https://velog.velcdn.com/images/marisol/post/18107924-c8ca-4f0d-8ced-a876fa04281e/image.png)

![](https://velog.velcdn.com/images/marisol/post/ca800e85-64e3-4bf1-a631-8d7be7a66809/image.png)

![](https://velog.velcdn.com/images/marisol/post/2b03f1e3-4f3c-4129-88ef-607cc2701c86/image.png)

Combine에서는 다양한 종류의 Subscriber를 지원한다.

![](https://velog.velcdn.com/images/marisol/post/dcca56e2-98e3-4baa-9743-9f5b75dc13b2/image.png)

Publisher의 예제로 돌아가서,

![](https://velog.velcdn.com/images/marisol/post/93b1d50f-5a5b-4f10-86fa-dd6d42153f58/image.png)

Subscriber를 추가해준다.
여기서는 ```assign(to:on:)``` Operator를 사용하여 Combine에서 가장 간단한 구독 형식 중 하나인 key path assignment를 추가했다. 이렇게 하면 업스트림 Publisher가 publish하는 값이 지정된 object의 key path에 할당된다.

이 Operator는 나중에 호출해서 구독을 종료할 수 있는 cancellation token도 생성한다.

![](https://velog.velcdn.com/images/marisol/post/9c5b7a6b-0e43-4ba1-b9fa-0023d01923c6/image.png)

Publisher가 이벤트 전달을 완료하기 전에 구독을 종료하는 것이 종종 필요하기 때문에 Cancellation이 Combine 형태로 구성되어 있다. 특히 해당 구독과 관련된 리소스를 확보하려는 경우에 사용한다.

AnyCancellable은 deinit 시에 자동으로 cancel을 호출한다. 이렇게 하면 명시적으로 cancel을 호출해야 하는 횟수를 크게 줄일 수 있다. 

![](https://velog.velcdn.com/images/marisol/post/cfba9571-ec08-44eb-b3b4-671451522641/image.png)

그리고 두 번째 subscription 형태인 ```sink```이다. 값을 받으면 클로저가 호출되고, 클로저 안에서 원하는 부가적인 작업을 할 수 있다.

```assign```과 마찬가지로 ```sink```는 구독을 종료할 수 있는 cancellable을 리턴한다.

![](https://velog.velcdn.com/images/marisol/post/8f9c732e-3200-463e-8c77-f22cd199eb4f/image.png)

세 번째 subscription인 ```Subject```는 하이브리드 형식이다. 약간은 Publisher처럼 행동하고, 약간은 Subsciber처럼 행동한다. 받은 값을 멀티캐스팅할 수 있으며, 특히 중요한 것은 값을 명령적으로? 전송할 수 있게 해준다. 이것이 기존 코드 베이스로 작업할 때 가장 중요하다.

![](https://velog.velcdn.com/images/marisol/post/6527e11e-e554-43d7-a2ce-f731c3b75c6b/image.png)

Subject를 사용하면 여러 다운스트림 Subscriber에게 브로드캐스트할 수 있을 뿐만 아니라, 값을 즉시 전송할 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/31cf0c2e-45ab-4746-bf02-bea6f2ef2f3c/image.png)

값이 업스트림 Publisher에 의해 생성된 경우에는 Subscriber 역할을 해서 Publisher가 보낸 값을 받고,
Publisher 역할을 해서 Subscriber들에게 값을 보낸다.

![](https://velog.velcdn.com/images/marisol/post/305db090-0a32-4e01-a775-37790cc38a36/image.png)

Combine에서는 값을 저장하지 않는 ```Passthrough```와, 마지막으로 받은 값을 기록하는 ```CurrentValue``` subject를 지원한다.

![](https://velog.velcdn.com/images/marisol/post/08ef1708-fee2-47ec-aa7c-3744d8a6ec41/image.png)

![](https://velog.velcdn.com/images/marisol/post/d54e4cff-1cb9-4cb9-890a-6abe4faa8f24/image.png)

Subject는 업스트림 Publisher를 구독할 수 있다는 점에서 Subscriber처럼 행동한다.
또한 sink와 같이 Operator를 호출해 Subscriber를 형성해서 Publisher처럼 행동하기도 한다.
그리고 magic word와 같이 값을 보낼 수도 있다.

Combine에서 Publisher는 구조체이기 때문에 copy 비용이 높은데, 
share는 Publisher를 reference로 얻어낼 수 있기 때문에 copy 비용을 줄여주는 Operator라고 한다.

![](https://velog.velcdn.com/images/marisol/post/8f5680b5-9d2f-47c9-b4b4-4da0e234de46/image.png)

SwiftUI는 Subscriber를 보유하고 있기 때문에, Publisher만 제공하면 된다.

![](https://velog.velcdn.com/images/marisol/post/c197290d-23f4-42a5-b241-04b2b00b53a4/image.png)

그렇게 하려면 custom type이 BindableObject 프로토콜을 채택하면 된다. (지금 시점에서는 네이밍이 ObservableObject로 변경되었다.)

BindableObject에는 associatedtype이 있는데, 절대 Fail하지 않도록 제한된 Publisher이다.
이것은 UI프레임워크로 작업하기에 아주 적합한데, 언어의 타입 시스템은 Publisher에 도달하기 전에 업스트림 오류를 처리하도록 강제하기 때문이다.

그리고 이 타입이 변경되었을 때 이를 알려주는 ```didChange``` 프로퍼티를 생성한다.

![](https://velog.velcdn.com/images/marisol/post/fa4eaf40-ad4e-4e58-bbd8-2b57fba91a90/image.png)

wizard school 앱의 기존 모델에 BindableObject(= ObservableObject)를 채택한다.

![](https://velog.velcdn.com/images/marisol/post/7a70e2de-6531-4466-8db4-cfcb6db23706/image.png)

그리고 여기서 모델 객체가 언제 바뀌었는지를 알기 위해 Subject를 사용한다.

![](https://velog.velcdn.com/images/marisol/post/d295744f-f6ec-43f8-b538-a9ad09f9b33c/image.png)

뷰 안에서 해당 모델이 SwiftUI가 자동으로 Publisher를 구독하는 object binding 임을 선언한다. (현재 ObjectBinding => ObservedObject로 변경됨)

그리고나서 뷰의 body 안에서 모델의 프로퍼티를 참조하면 끝이다. SwiftUI는 모델이 변경되었다는 신호를 보낼 때마다 자동으로 새 body를 생성한다.

![](https://velog.velcdn.com/images/marisol/post/ba0c301e-94fa-4f35-a865-626f9705a7bb/image.png)

## ✏️ Integrating Combine

이제 마법사들이 마법사 학교에 등록할 수 있도록 앱에서 계정을 생성하려고 한다.

![](https://velog.velcdn.com/images/marisol/post/1350769b-92a5-4e86-9280-dcc577256fd3/image.png)

앱에는 3가지 요구사항이 있다.

1️⃣ 우선 서버에서 사용자 이름이 유효한지 확인해야 한다.
2️⃣ 비밀번호 텍스트필드와 비밀번호 확인 텍스트필드가 일치해야 하며, 8개 이상의 문자를 사용해야 한다.
3️⃣ 위 조건이 모두 충족되면 UI를 활성화하고, 아닐 경우 비활성화 한다.

비동기적 동작과 동기적 동작이 함께 있는데, Combine으로 이를 결합해 해결하려고 한다.

![](https://velog.velcdn.com/images/marisol/post/0fcea33c-4215-4baa-90ba-8b187eb9bf99/image.png)

먼저 Interface Builder를 사용해 비밀번호 텍스트필드 프로퍼티에 대한 타겟 액션을 생성한다.

![](https://velog.velcdn.com/images/marisol/post/73856030-6f22-440a-9f16-dd48d5a3d086/image.png)

그런 다음 코드에서 이를 사용하면 사용자가 텍스트필드에 입력할 때마다 신호가 전송된다. 
그리고 현재 값을 가져와서 변수에 저장한다. 만약 이를 값이 password 값이 바뀔 때마다 실행하고 싶다면?

![](https://velog.velcdn.com/images/marisol/post/597a1373-6c00-4450-9805-77bca9c3ca58/image.png)

개별 프로퍼티에 ```@Published```를 붙이면 각각에 Publisher를 추가하여 값이 변경될 때마다 publish 할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/976e81bc-bf0a-4bdd-9b9c-d7f0f3ba8fee/image.png)

```@Published```는 Swift5.1의 새로운 프로퍼티 래퍼이며, 프로퍼티에 Publisher를 추가해준다.

![](https://velog.velcdn.com/images/marisol/post/2525484c-71a6-45ac-8795-fb8c22cc0e2f/image.png)

코드에서 사용해보면, 현재 currentPassword의 값은 "1234"가 된다.
@Published 프로퍼티 래퍼로 선언된 변수들은 $ 접두사를 사용하면 Publisher로 사용할 수 있기 때문에,
Publisher에서 사용하던 모든 Operator를 사용하거나 구독할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/8fab533e-7941-482a-a3d6-d96a167cce1c/image.png)

여기서는 ```sink```를 사용했다.
그런 다음 해당 프로퍼티를 다른 비밀번호인 "password"로 다시 설정하면, Subscribers는 변경된 값을 얻게 된다.

![](https://velog.velcdn.com/images/marisol/post/a30f771c-8d00-44c8-a54f-b0c663fc4c02/image.png)

아까전에 두 Publisher가 동일한지 동시에 확인해야 한다고 이야기했었다.

![](https://velog.velcdn.com/images/marisol/post/d7218a3c-2e96-432e-a827-6a32a7b0966a/image.png)

여기서 Published 프로퍼티 래퍼를 추가하여 Publisher를 2개 추가했는데,
하나로 합쳐서 유효한 비밀번호인지 확인해야 한다.

![](https://velog.velcdn.com/images/marisol/post/80a9dc21-b639-46e2-96e2-0e9d33f20342/image.png)

이를 위한 Operator를 ```CombineLatest```라고 한다.

CombineLatest를 사용하면 $ 접두사가 붙은 프로퍼티 래퍼를 참조할 수 있으며, 이 중 하나가 변경되면 신호를 받는다.
그리고 유효성을 판단해서 결과를 알려준다.

![](https://velog.velcdn.com/images/marisol/post/cc82d645-51bb-4440-b880-7d58796bf02e/image.png)

예를 들어, 사용자가 이미 password 텍스트 필드에 입력한 후, passwordAgain 텍스트필드에 입력하기 시작하면
passwordAgain 값은 변경되기 시작하고, 첫번째 필드에 입력한 password 값이 오리지널 값이 된다.

그런 다음 클로저를 사용하여 요구사항을 충족시킬 수 있다. 이 경우 두 필드의 텍스트가 일치하고, 8자 이상이면 된다.

두 개의 Published string을 가져왔고, 그들의 최신(latest) 값을 결합(combine)하여  옵셔널 스트링을 리턴했다.

![](https://velog.velcdn.com/images/marisol/post/0d9ba7e7-93d5-4cc9-bd5c-49b29f2777a7/image.png)

여기에 만약 map을 추가하면, 리턴 타입이 변경되는걸 볼 수 있다.

![](https://velog.velcdn.com/images/marisol/post/f6855e0b-62d3-4867-b0aa-6df5b6a3d927/image.png)

여기서 중요한 것은 절대 fail하지 않는 optional string의 Publisher라는 것이다.
이를 위해 ```eraseToAnyPublisher``` Operator를 사용할 수 있다.
리턴 타입은 변경되지 않았지만 필요한 요구사항을 모두 만족하고, 구현 세부 정보를 숨길 수도 있다.
다운스트림의 Subscriber에게 AnyPublisher 타입으로 감싸사 전달해줄 수 있게 된다.

![](https://velog.velcdn.com/images/marisol/post/07546ace-3ac6-400d-a6d8-696e8f85e00e/image.png)

지금까지 한 것을 보면, String인 초기 프로퍼티에 @Published를 사용하여 string Publisher를 추가했다.
그런 다음 CombineLatest를 사용하여 이 두 Publisher의 최신 값을 결합하고, 로직을 추가했다.

![](https://velog.velcdn.com/images/marisol/post/e90a4c89-d3ea-43d5-a13d-a633674112c2/image.png)

그런 다음 잘못된 password를 필터링하기 위해 map을 사용했고,
마지막으로 eraseToAnyPublisher를 사용했다.

![](https://velog.velcdn.com/images/marisol/post/84348fe5-c3da-49c6-8fa5-41325a14e4ea/image.png)

![](https://velog.velcdn.com/images/marisol/post/8dea0eb3-a6d8-49e2-880a-09bb4def9552/image.png)

이제 우리는 사용자가 빠르게 입력할 값이 유효한지 서버에서 확인해야 한다.

![](https://velog.velcdn.com/images/marisol/post/7ee3108d-1a63-4074-a0b8-15beba5baddd/image.png)

이 경우에는 사용자가 텍스트를 입력할 때마다 네트워크 작업이 수행되는 것을 원하지 않는다. 그렇게 하기 위해서 ```debounce```를 사용한다.

![](https://velog.velcdn.com/images/marisol/post/2570921a-c2ba-4227-b5bf-d894f263e97c/image.png)

사용자가 빠르게 값을 입력하면 신호가 빠르게 여러번 가지만, debounce를 통해 일정 시간 동안 값이 바뀌지 않아야 요청을 하도록 할 수 있다.

또 사용자가 입력하고 마지막 값이 항상 동일하다면, 동일한 값이 유효한지 확인하기 위해 서버에 다시 요청할 필요가 없다.
-> 이를 위해 ```removeDuplicates```를 사용할 수 있다. 그러면 동일한 값이 반복적으로 publish되지 않는다.

![](https://velog.velcdn.com/images/marisol/post/ca58084e-e01d-4230-b216-e68acf011972/image.png)

코드에서 확인해보면, 사용자가 타이핑을 멈추고 0.5초 동안은 서버에 값을 요청하지 않는다.
그리고 중복을 제거한다.

![](https://velog.velcdn.com/images/marisol/post/1a6e75b0-928d-4764-865e-6b44cf959a9b/image.png)

아직 서버에 해당 값이 유효한지 체크하지 않았는데, 이를 위해 앱에 미리 정의해둔 ```usernameAvailable```이라는 메서드를 사용한다. 

```usernameAvailable``` 메서드를 호출하고, 해당 메서드가 비동기적으로 완료되고 값을 갖게 되면, 이 경우 promise를 success로 처리하고, 아닐 경우 nil로 처리한다.

그런 다음 비동기 네트워크 호출을 수행하는 기존 API를 wrap하기 위해 Future를 사용했다.
Future의 promise는 클로저의 success/failure를 결과로 갖는다.

![](https://velog.velcdn.com/images/marisol/post/ace79b58-f0af-4f3d-9544-0e93dcd95423/image.png)

![](https://velog.velcdn.com/images/marisol/post/69982360-f1fb-4b7d-be1e-93be157ed6a6/image.png)

![](https://velog.velcdn.com/images/marisol/post/5bf88a13-9da0-48d8-a672-af033c8de88e/image.png)

이제 validatedPassword와 validatedUsername이라는 2개의 커스텀 Publisher가 있고, 이들을 결합할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/746c21bf-94d8-40c4-8a8c-ab835d0b0561/image.png)

이제 두 개의 시그널을 사용하여 UI를 활성화하거나 비활성화 한다.

![](https://velog.velcdn.com/images/marisol/post/ab227a3e-5ab6-47ed-9504-c8ae7dac8cc3/image.png)

이때도 CombineLatest를 사용할 수 있다. 
유효한지 확인하고, 튜플을 리턴하거나 nil을 반환한다.

![](https://velog.velcdn.com/images/marisol/post/fe1c53d3-da64-4abb-8013-128d3f2a3ced/image.png)

실제로 UI에 연결하는 것은 매우 간단하다. 구독을 저장할 변수를 만들어 ViewController의 라이프타임동안 구독을 유지한다. 
버튼의 isEnabled 프로퍼티에 할당하기 위해 이 값을 bool에 map한다.

마지막으로, receive(on:)을 사용하여 main runloop으로 전환한다. 그런 다음 assign Operator를 사용하여 지정된 키 경로에 할당한다.

![](https://velog.velcdn.com/images/marisol/post/3976f08e-636f-4ef2-a835-1630419c1925/image.png)

![](https://velog.velcdn.com/images/marisol/post/48b03d03-a9f3-4aff-ae41-77358b3a4bc5/image.png)

처음부터 살펴보면, 우리는 String을 publish하는 3개의 간단한 Publisher로 시작했다.

![](https://velog.velcdn.com/images/marisol/post/a959c70e-521e-4799-b52a-908079417d8d/image.png)

그리고 Composition을 사용하여 작은 단계에서부터 chain을 만들고, 버튼에 할당했다. 컴바인 끝!

![](https://velog.velcdn.com/images/marisol/post/c3693abb-f599-4639-aee7-62ef5e8a0f11/image.png)

---
출처
- https://developer.apple.com/videos/play/wwdc2019/721/
