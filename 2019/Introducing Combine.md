## Introducing Combine
![](https://velog.velcdn.com/images/marisol/post/25c0feb3-2300-4728-b1f4-00c09e415292/image.png)

Combine 프레임워크를 소개하기 위해 비동기 프로그래밍을 사용하는 앱으로 예시를 들고 있다.
Wizard School Signup 앱은 학생들이 새로운 마법 학교에 등록할 수 있게 해준다.

간단한 요구사항이 있는데, 우선 서버에 네트워크 요청을 하여 확인할 유효한 사용자 이름이 필요하다. 또한 앱에서 로컬로 확인할 수 있는 일치하는 암호도 필요하다.

이런 작업을 수행하는 동안, 메인 스레드를 차단하는 것이 아니라 responsive한 UI를 유지해야 한다.

![](https://velog.velcdn.com/images/marisol/post/7f96a6b7-f597-485e-9baa-a5d5aa41c5d1/image.png)

먼저 사용자 이름을 입력하는데, Target/Action을 사용하여 사용자 입력에 대한 알림을 수신한다.
그리고 사용자가 입력을 잠시 멈출 때까지 기다리는 타이머를 사용해서 네트워크 요청으로 인해 서버를 과부하 시키지 않도록 한다.
마지막으로, KVO를 사용하여 비동기 작업에 대한 진행 상황 업데이트를 듣는다.

URLSession request를 기다려야 하고, 그 결과를 동기식 검사와 병합해야 한다.
모두 완료되면 KVC를 사용하여 결과대로 UI를 다시 업데이트 해야 한다.

![](https://velog.velcdn.com/images/marisol/post/eed8e3fc-10a4-4d36-bd79-91e11c80d749/image.png)

CoCoa SDK 전체에서 많은 비동기 인터페이스를 찾을 수 있다. 
여기서 본 Target/Action 뿐만 아니라 NotificationCenter와 ad-hoc callback 등등..

이들은 클로져 또는 completion block을 취하는 API 들인데, 함께 사용할 때 어려울 수 있다.
===> Combine 등장

# A unified, declarative API for processing Values over time

Combine은 시간에 따른 값을 처리하기 위한 통합적이고 선언적인 API이다.

![](https://velog.velcdn.com/images/marisol/post/64b209eb-1c53-4aba-89bb-97dab0cea33c/image.png)

Combine은 Swift로 작성되어 있는데, 그렇기 때문에 제네릭과 같은 Swift 기능들을 이용할 수 있다.
제네릭을 사용하면 작성해야 하는 boilerplate 코드의 양을 줄일 수 있다.

Combine은 또한 타입 세이프 하기 때문에, 런타임이 아닌 컴파일 타임에 에러를 캐치할 수 있다. 

핵심 개념이 간단하고, 이해하기 쉽지만, 사용하면 큰 효과를 볼 수 있다.

그리고 Combine은 request-driven 하기 때문에 앱의 메모리 사용량과 성능을 보다 신중하게 관리할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/66fcc9a0-c16a-4bf4-b8ad-c469c7583486/image.png)

Combine의 핵심 개념은 3가지이다.

1️⃣ Publisher
2️⃣ Subscriber
3️⃣ Operator

## 1. ✏️ Publisher

![](https://velog.velcdn.com/images/marisol/post/c71b5baa-2125-4766-9ac1-ce566ab75bcd/image.png)

Publisher는 Combine API의 선언적인 부분이다. Publisher는 value와 error가 생성되는 방식을 설명한다. (반드시 values와 error를 Publisher가 생성하는 것은 아님)

또한 값 타입이며, Subscriber의 등록을 허용한다. Subscriber는 시간이 지남에 따라 값을 수신한다.

![](https://velog.velcdn.com/images/marisol/post/537c3a3f-a0e4-45f8-beeb-c521fbd87511/image.png)

Publisher라고 부르는 protocol이 있고, 2가지 associatedtype이 있다.
Output은 생성되는 value의 종류이고, Failure는 생성되는 오류의 종류이다.
Publisher가 에러를 생성할 수 없는 경우, associated type에 never를 사용할 수 있다.

Publisher에는 subscribe라는 메서드가 있다. generic에서 알 수 있듯이 subscribe는 Subscriber의 input이 Publisher의 output과 일치해야 하고, Subscriber의 failure가 Publisher의 failure와 일치해야 한다.

## 2. ✏️ Subscriber

![](https://velog.velcdn.com/images/marisol/post/b2681464-2830-4292-b21f-f56eea044e52/image.png)

다음은 Subscriber이다. Subscriber는 Publisher의 반대 개념이다. Subscriber는 completion을 포함하여 value를 받는다.

Subscriber는 일반적으로 value를 받으면 동작하고, 상태를 변경하기 때문에 swift에서는 참조 유형을 사용하며, 그렇기 때문에 class이다.

![](https://velog.velcdn.com/images/marisol/post/009a4148-95b5-4e42-9539-eb0cdce310cf/image.png)

Subscriber 프로토콜이며, 두 연관타입이 input과 failure로 동일하다. Subscriber가 failure를 수신할 수 없는 경우에는 Never를 사용할 수 있다.

3가지 주요 메서드가 있다. 첫 번째는 subscription을 받을 수 있다. subscription은 Subscriber가 Publisher에서 Subscriber로의 데이터 흐름을 제어하는 방법이다.

또한 input을 수신할 수도 있다. 마지막으로 완료 또는 실패가 될 수 있는 completion을 받을 수 있다.

![](https://velog.velcdn.com/images/marisol/post/74a5885c-f42e-413e-a0ba-dceafedc8034/image.png)

assign은 Subscriber의 한 예이다. 그래서 class이며, 입력을 수신하면 프로퍼티에 기록한다. Swift에서는 프로퍼티 값을 쓸때 오류를 처리할 방법이 없기 때문에 Assign의 Failure 타입을 Never로 설정한다.

![](https://velog.velcdn.com/images/marisol/post/d7a526c3-f8f0-4796-a359-38132d295780/image.png)

Subscriber를 보유하는 컨트롤러 객체나 다른 타입이 있을 수 있으며, 그 객체는 Subscriber를 Publisher로 호출하여 연결하는 역할을 한다.

이 때 Publisher는 Subscriber에게 subscription을 전송하여 Subscriber가 Publisher로부터 value에 대한 요청을 하도록 한다.

이 시점에서 Publisher는 해당 개수 이하의 value를 Subscriber에게 자유롭게 보낼 수 있다. 또한 Publisher가 유한하면 최종적으로 completion 또는 error를 전송한다.

하나의 subscription에 값이 0개 이상이며, completion은 하나이다.

![](https://velog.velcdn.com/images/marisol/post/f90d2c1d-5f7d-4f48-86b9-84e7af378cb6/image.png)

학생들이 졸업하는 것에 대한 알림을 듣고, 그들이 졸업하면 모델 객체의 값을 업데이트 하고 싶을 경우,

Notification Center Publisher로 시작하고, 그런 다음 Assign Subscriber를 생성하여 Merlin의 grade 프로퍼티에 새 grade를 쓰도록 한다.

다음으로 subscribe를 사용하여 둘을 연결할 수 있다. 하지만 컴파일 되지 않는데, type이 매치되지 않기 때문이다.

![](https://velog.velcdn.com/images/marisol/post/9cca9037-5bda-4563-b463-9383d7d14536/image.png)

그래서 Notification과 integer 사이에서 변환할 수 있는 중간에 무언가가 필요한데, 그것이 Operator이다.

## 3. ✏️ Operator

![](https://velog.velcdn.com/images/marisol/post/12973a95-1c01-43b6-a75e-7cfad10024c0/image.png)

Operator는 Publisher 프로토콜을 채택하기 때문에 선언적이고, value타입이다. Operator는 값을 변경하거나, 추가하거나, 제거하는 등의 다양한 동작을 처리한다.

그리고 우리가 업스트림이라고 부르는 다른 Publisher를 구독하고, 그 결과를 우리가 다운스트림이라고 부르는 Subscriber에게 보낸다. 

![](https://velog.velcdn.com/images/marisol/post/4cf0d9b9-33f5-4916-b2b5-b0033ee8a946/image.png)

Operator의 한 예인 Map이다. Map은 업스트림, 그리고 업스트림의 ouput을 자체 ouput으로 변환하는 transform을 갖고 있다.

Map은 자체적으로 Failure를 생성하지 않기 때문에, 업스트림의 Failure 유형을 반영하고 전달한다.

![](https://velog.velcdn.com/images/marisol/post/61b7713e-124a-4645-98ac-f22ad5171c0b/image.png)

그래서 Map을 사용하면 Notification과 integer 사이에서 변환이 가능하다.

![](https://velog.velcdn.com/images/marisol/post/d3884cf4-2474-4f82-881e-f321bf2ef61c/image.png)

동일한 Publisher와 Subscriber를 유지하고, converter를 추가한다.
이 converter는 graduatePublisher에 연결하도록 구성되어 있으며, closure를 갖고 있다. 이 클로저는 Notification을 받고, NewGrade라는 user info key를 찾는다.
NewGrade가 있고, Integer라면 클로저로부터 그 값을 반환한다. 이 값이 없거나 Integer가 아닌 경우, 기본 값으로 0을 사용한다. 
그래서 무슨 일이 있어도 결과는 정수이므로, Subscriber에 연결할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/2e1a1138-ca9d-4bc8-84d6-75dd06696719/image.png)

더 fluent한 syntax를 사용할 수도 있다.
위와 같이 사용하면, 모든 Publisher가 사용할 수 있는 함수가 된다. Publisher의 extension이기 때문에 upstream에 self를 사용하면 된다.

![](https://velog.velcdn.com/images/marisol/post/9dfff821-eda4-4081-b60f-be528b4e733e/image.png)

예제에서 새로운 구문을 사용해보자.
Notification Center Publisher부터 보자면, 알림을 받으면 앞서 본 것과 동일한 클로저를 사용하여 map한 다음, Merlin의 grade 프로퍼티에 assign 한다.

Assign은 cancellable한 항목을 반환한다. Cancelation은 combine에 내장되어 있다. Cancelation을 사용하면 필요한 경우 Publisher 및 Subscriber의 시퀀스 조기에 해제할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/18ca340b-8352-473c-ba60-c00d9ad074bd/image.png)

Publisher에서 일련의 Operator를 거쳐 Subscriber로 끝나는 값을 변환한다.
이러한 Operator를 많이 보유하고 있으며, Declarative Operator API라고 부른다. (map, filter, reduce 등이 포함된다.)
오류를 기본값 또는 대치 값으로 변환하는 것과 같은 오류 처리가 가능하고, 
스레드 또는 Queue 이동 (무거운 처리 작업을 백그라운드 스레드로 이동하거나, UI 작업을 메인 스레드로 이동)할 수 있으며, Dispatch Queue, 타이머, 타임아웃 등의 스케줄링도 제공한다.

# Try composition first

많은 작업을 모두 수행하는 몇 개의 Operator를 제공하기 보다는, 간단한 작업을 수행하는 Operator를 많이 제공해서, 이해하기 쉽게 만들었다. 또한 개발자들이 쉽게 이해할 수 있도록 SwiftCollectionAPI에서 이름을 많이 따왔다고 한다.

![](https://velog.velcdn.com/images/marisol/post/5765505f-c575-41ea-a72e-34998a678609/image.png)

한 쪽에는 동기식 API가 있고, 한 쪽에는 비동기식 API가 있다. Swift에서 integer를 동기적으로 표시해야 한다면, Int를 사용할 수 있다. 많은 정수를 동시에 표시해야 하는 경우, int의 Array를 사용할 수 있다.

Combine에서 이 개념을 비동기 세계에 매핑해보면,
단일 값을 비동기적으로 표시해야 한다면 Future을 사용하고, 많은 값을 비동기적으로 표시해야 하는 경우 Publisher를 사용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/698577ac-bc28-4bfb-b50e-a73a2713c2d3/image.png)

여기서 key가 없거나, 정수가 아닌 경우 기본값 0을 사용하기로 했었는데, 
bad value가 진행되지 않도록 하고, 결국 모델 객체에 기록되지 않도록 하는것이 더 나을 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/763f118d-501f-4b4f-9023-14359045881b/image.png)

그래서 compactMap을 사용하여 클로저가 0을 반환하도록 한 다음, 0 값을 필터링할 수 있다.
이 클로저에서 0을 반환하면, compactMap이 이를 필터링하여 스트림 아래로 더 이상 진행되지 않도록 한다.

![](https://velog.velcdn.com/images/marisol/post/bbad9083-7958-445a-b7b4-67373e757b9b/image.png)

우리 학교에 5학년 이상의 학생들만 입학이 허용된다고 한다면, filter를 사용할 수 있다.
filter는 predicate을 사용하고, 해당 predicate을 통과하는 요소만 진행할 수 있다. 이 동작은 array의 filter와 정확히 동일하다.

Publisher에서 처음 3가지 요소만 수신하려면, prefix(3)을 사용할 수 있다.
3개의 값을 수신한 후, 업스트림을 cancel하고 다운스트림으로 completion을 전송한다.

그래서 Merlin이 졸업하면, new grade 프로퍼티에서 값을 가져오고,
그 값이 5등급 보다 크고, 최대 3번만 발생했는지를 확인 한 후 최종적으로 Merline의 grade 프로퍼티에 할당한다.

![](https://velog.velcdn.com/images/marisol/post/d28196ac-c5a0-4e25-aec1-753d5f66659c/image.png)

앱에서 사용자가 계속 진행할 수 있도록 허용하기 전에, 사용자는 지팡이가 만들어질 때까지 기다려야 한다. 3가지 작업이 모두 완료되면 계속 버튼이 활성화되는데, Zip이 이를 처리할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/c4aa73ff-a30e-430a-a9d1-c798bd5c4956/image.png)

Zip은 여러 개의 업스트림 입력을 단일 튜플로 변환한다.
계속 진행하기 위해서는 모든 업스트림으로부터의 input이 필요하다.

예를 들어 첫번째 Publisher가 A를 생성하고, 두번째 Publisher가 1을 생성하면, 이제 튜플을 생성하여 해당 값을 Subscriber에게 다운스트림으로 전송하기에 충분한 정보를 얻을 수 있다.

![](https://velog.velcdn.com/images/marisol/post/dc6200bf-938b-4edc-9499-dbe5842afd15/image.png)

각각 Bool 결과 값을 주는 3가지 비동기 작업의 결과를 기다리는 Zip을 사용한다.
그래서 튜플을 하나의 bool에 map하고, 버튼의 isEnabled 프로퍼티에 기록한다.

![](https://velog.velcdn.com/images/marisol/post/ecb01aeb-bc64-42f1-86aa-c738385bb97d/image.png)

그리고 Play 버튼을 활성화하기 전에 3개의 스위치를 모두 활성화 해야 하며, 이 중 하나가 비활성화되면 버튼을 비활성화 해야 한다. 이 작업은 Combine Latest가 처리한다.

zip과 마찬가지로 여러 개의 업스트림 입력을 단일 값으로 변환한다. 하지만 하나라도 비활성돠 되면 play 버튼도 비활성화되어야 한다.

이를 지원하기 위해 각 업스트림에서 마지막으로 수신한 값을 저장한다. 또한 클로저를 사용하여 단일 다운스트림 값으로 변환할 수 있다.

예를 들어 첫번째 Publisher가 A를 생성하고, 두번째 Publisher가 A1을 생성할 때, 이를 문자열화하여 다운스트림으로 전송하는 클로저를 실행한다.

나중에 두 번째 Publisher가 새 값을 생성하면, 첫번째 Publisher 이전 값과 결합하여 새 값을 아래로 보낼 수 있다.

즉, 업스트림이 변경되면 새로운 이벤트가 발생하며, 다운스트림은 업스트림의 가장 최신 정보를 얻을 수 있다.

![](https://velog.velcdn.com/images/marisol/post/d12c9f83-234c-49d7-ba75-1faed3b9256a/image.png)

그래서 세 개의 업스트림, 즉 스위치가 변경될 때 세 개 모두의 부울 상태를 사용하는 Combine Latest를 사용한다. 다시 단일 Bool 값으로 변환하고, 재생 버튼의 isEnabled 프로퍼티에 기록한다.

3개 중 하나라도 false라면, 결과는 false라는 것을 의미한다.
3개 모두가 true라면 결과가 true이기 때문에 버튼을 활성화할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/8040101f-d98d-4647-943d-68ed763616af/image.png)

앱에서 점진적으로 Combine을 채택할 수 있도록 설계했는데, 앱에서 Combine을 사용할 수 있는 장소에 대한 제안을 하자면,

Notification Center를 사용하는 경우, 알림을 받은 다음 알림 내부를 보고 작업 여부를 결정할때 filter를 사용할 수 있다.

네트워크 작업에서 zip을 사용할 수 있다.

또한 URLSession을 사용하여 일부 데이터를 수신한 다음, json decoder를 사용하여 해당 데이터를 자신의 객체로 변환할 때 쓰는 Operator로 decode가 있다.

---
출처
- https://developer.apple.com/videos/play/wwdc2019/722/
- https://icksw.tistory.com/268
