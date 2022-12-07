# Meet AsyncSequence

![스크린샷 2022-12-07 오전 1 54 27](https://user-images.githubusercontent.com/63997044/206086572-4921c8a0-1dd6-48fd-b515-b38d1be7c269.png)

> **Overview**

새로운 AsynceSequence 프로토콜을 사용하면 알림부터 스트리밍 중인 바이트까지 반복해서 데이터를 받아오는 코를 간단하게 작성할 수 있다. 
또한 기존 코드를 리팩토링하여 비동기 시퀀스를 제공하는 방법을 소개한다.
- What is AsyncSequence?
- Usage and API
- Adopting AsyncSequence

## What is AsyncSequence

![스크린샷 2022-12-07 오전 1 55 12](https://user-images.githubusercontent.com/63997044/206086942-35f02c55-b0e5-401f-b9bf-b23cf1f425cf.png)

사용 예시 코드이다.
URL의 `lines`프로퍼티를 사용해서 데이터의 각 줄을 모든 항목이 다운로드될 때까지 기다리지 않고 수신되는 항목부터 출력하도록 코드를 구성했다. 
여기서 또 하나 주목할 점은 `dropFirst()` 메서드 사용 부분. 일반 Sequence에서 사용하는 메서드(`map`, `filter`, `reduce` 등)를 그대로 사용할 수 있다는 것을 볼 수 있다.
그렇다면 AsyncSequence의 특징들을 살펴보자.

![스크린샷 2022-12-07 오전 1 56 35](https://user-images.githubusercontent.com/63997044/206086993-e1ea5cc7-dae3-46d2-9152-24cc8eacdae2.png)

### 🧐 Async Function과 AsyncSequence

async 함수를 사용하면 **await** 키워드를 사용하여 콜백 없이 동시성 코드를 작성할 수 있다. 또한 async 함수를 호출하면 결과값이나 에러가 생성될 때마다 suspend되고 다시 resume되는데, 반면에 AsyncSequence는 **각 요소에서 suspend되고 반복자가 값을 생성하거나 혹은 (에러를) 던질 때 다시 resume된다.**

### 🧐 Sequence와 AsyncSequence

이름대로 일반적인 Sequence와 유사하지만 차이점이 있다.
말 그대로 각 요소들이 비동기적으로 전달된다. 비동기 전달은 실패할 가능성이 있기 때문에 throw 하는 함수와 마찬가지로 에러 핸들링을 할 수 있다.
AsyncSequence는 시간이 지남에 따라 값이 생성되기 때문에 반복자에서 next() 호출에 대해 nil을 반환하여 에러 발생 혹은 완료를 나타낸다.


![스크린샷 2022-12-07 오전 1 57 03](https://user-images.githubusercontent.com/63997044/206087036-5903273f-4a74-47e7-aeea-5e0469df04b3.png)

일반적인 for - in 루프이다. 진도가 3 초과인 값에 대해서만 표시하고자 한다.

![스크린샷 2022-12-07 오전 3 41 53](https://user-images.githubusercontent.com/63997044/206087048-d474514c-6683-4e72-890e-5983be3377c1.png)

컴파일러가 루프를 실행하는 작업을 코드로 표현했다. 컴파일러는 for-in 루프를 while loop로 변환하여 처리한다. 

![스크린샷 2022-12-07 오전 3 43 07](https://user-images.githubusercontent.com/63997044/206087059-172288a6-3c35-41a1-b670-85f78bf8fb56.png)

Async/Await 기능으로 동작하게 하려면, `makeIterator()` 대신 `makeAsyncIterator()`, `iterator.next()` 대신 `await iterator.next()`가 되어야 한다.

![스크린샷 2022-12-07 오전 3 49 12](https://user-images.githubusercontent.com/63997044/206087191-3b906bdf-0713-49cf-8ed2-f7060b40d806.png)

따라서 for-in구문을 이와 같이 **for-await-in**으로 변경해준다. 만약 비동기 시퀀스가 에러를 던질 가능성이 있으면 **for-try-await-in** 구문을 사용한다.

![스크린샷 2022-12-07 오전 3 49 37](https://user-images.githubusercontent.com/63997044/206087200-21359a67-6052-45f0-aa15-301f03ec1226.png)

break나 continue문도 여전히 사용할 수 있다. 반복문 내부에 break문을 넣어 location 데이터가 없을 때 break문을 발동하여 반복문을 종료하고 있다.

![스크린샷 2022-12-07 오전 3 49 47](https://user-images.githubusercontent.com/63997044/206087214-5b6de3fb-1333-4c8e-a772-907d04e4faa4.png)

continue문도 마찬가지로 사용 가능하다.

![스크린샷 2022-12-07 오전 3 50 21](https://user-images.githubusercontent.com/63997044/206087221-5b9f0c3d-388b-496a-8e2c-1e95e0fabf2f.png)

for-try-await-in 구문의 사용을 보여준다. AsyncSequence에서 에러를 throw할 수 있다면 이렇게 작성한다. 

![스크린샷 2022-12-07 오전 3 50 30](https://user-images.githubusercontent.com/63997044/206087228-63088d9b-7dbf-4f28-abd4-e23da9fc1e54.png)

반복을 실행하면서 동시에 다른 작업을 실행해야 하는 경우 반복을 캡슐화하는 새로운 비동기 작업 Task를 만들 수 있다. 예시와 같이 두 개의 반복을 각각의 Task로 만들고 제어할 수 있다.


## Usage and API
iOS 15, tvOS 15 및 macOS Monterey에서 추가된 몇 가지 API를 살펴보자.

### - Bytes from a FileHandle

![스크린샷 2022-12-07 오전 3 54 39](https://user-images.githubusercontent.com/63997044/206087384-e4a945f3-34e3-4436-92e0-ed52e2cc3b76.png)

파일 읽기는 비동기로 동작시킬 수 있는 대표적인 작업 중 하나이다.
- `public var bytes: AsyncBytes` 프로퍼티는 바이트의 비동기 시퀀스에 대한 액세스를 제공한다.
- `bytes.lines: AsyncLineSequence<FileHandle.AsyncBytes>` -> 바이트의 비동기적인 시퀀스를 한 줄의 시퀀스로 변환하여 바이트를 한 줄씩 읽을 수 있다.


### - Read lines from a URL

![스크린샷 2022-12-07 오전 3 54 55](https://user-images.githubusercontent.com/63997044/206087396-e52816ff-24a8-41d8-bfdd-bb20ec28c344.png)

- `public var resourceBytes: AsyncBytes`: URL로부터 받아오는 콘텐츠의 바이트로 비동기 시퀀스를 생성
- `public var lines: AsyncLineSequence<AsyncBytes>`: URL로부터 받아오는 콘텐츠의 바이트를 줄 단위로 비동기 시퀀스를 생성

### - Bytes from a URLSession

![스크린샷 2022-12-07 오전 3 55 14](https://user-images.githubusercontent.com/63997044/206087408-1491ad88-b27d-412a-8afa-3e549725616d.png)

URLSession으로부터 받아오는 바이트에 대한 비동기 시퀀스에 대한 액세스를 제공한다. 타입은 AsyncBytes.

### - Notifications

![스크린샷 2022-12-07 오전 3 55 34](https://user-images.githubusercontent.com/63997044/206087416-11a208f6-b713-4e13-a5ed-7ddda2d4e896.png)

Notification 또한 데이터 전달의 한 수단이기 때문에, Notification도 비동기적으로 알림을 기다릴 수 있게 되었다.

AsyncSequence 역시도 Sequence이므로 `first(where:)`과 같은 메서드도 사용 가능하다.

![스크린샷 2022-12-07 오전 3 55 59](https://user-images.githubusercontent.com/63997044/206087422-fd71d649-ca91-4762-a57d-d4994fbc4d6f.png)

이 외에도 값을 추출하거나 변형시키는 여러 메서드를 제공한다. 이는 우리가 일반 Sequence에서 사용하던 것과 동일하다.

## Adopting AsyncSequence

![스크린샷 2022-12-07 오후 1 25 33](https://user-images.githubusercontent.com/63997044/206088150-287b405b-0504-4869-a520-ddd7124b7604.png)

`AsyncSequence`와 잘 맞는 것 몇 가지 디자인 패턴이 있는데 바로 `Callback`과 `Delegation`이다. 응답이 필요하지 않고 발생한 새로운 값을 알려준다는 점에서 AsyncSequence를 만드는 데 적합하다. 그러한 디자인 패턴이 적용된 핸들러 패턴의 예제 코드로 예를 들어 보겠다.

![스크린샷 2022-12-07 오전 4 34 08](https://user-images.githubusercontent.com/63997044/206087435-e2264456-1a52-4c69-b6ef-21f2abe26f3b.png)

핸들러 패턴으로 예를 들어보자.
이렇게 구성된 QuakeMonitor 클래스가 있다.

![스크린샷 2022-12-07 오후 1 23 41](https://user-images.githubusercontent.com/63997044/206087927-3d3728d7-853a-4e3e-b2be-620e07b2d1e9.png)

우리가 알고있는 기존의 사용법은 QuakeMonitor 객체를 생성하고 핸들러에 클로저를 할당한 뒤 작업을 시작하고 종료하는 위와 같은 코드이다.

![스크린샷 2022-12-07 오전 4 38 10](https://user-images.githubusercontent.com/63997044/206087443-166ea0f3-f70c-4e6a-a6c2-428d360f52c7.png)

동일한 인터페이스를 사용하여 새로운 타입인 `AsyncStream`에 대한 사용법을 알아보겠다.
AsyncStream를 구성할 때는 클로저를 사용하는데, 값을 산출하는 continuation이 있으며 여기에 termination을 처리할 수 있다.
우선 구성 클로저 내부에서 monitor 객체가 생성되고 핸들러를 할당한다.
`onTermination`에 취소와 cleanup 처리에 대한 동작을 정의하고 모니터링을 시작한다.

이렇게 코드를 비동기적 스트림 내부로 캡슐화를 하는 것은 모든 사용처에서 동일한 논리의 코드를 복제하지 않아도 되므로 효율적이다.

![스크린샷 2022-12-07 오전 4 38 50](https://user-images.githubusercontent.com/63997044/206087601-e9190b2e-e839-47a9-b409-ba42d3557612.png)

AsyncStream을 생성했다면 이것을 사용하는 방법이다.
`filter`와 같은 Sequence 변환 기능과 함께 `for-await-in` 구문을 사용할 수 있다.


![스크린샷 2022-12-07 오전 4 39 15](https://user-images.githubusercontent.com/63997044/206087609-4d71e851-178d-4229-b0c5-609df27ad13b.png)

AsyncStream
- 기존 코드를 비동기 시퀀스로 만드는 방법
- 안전성, 반복 및 취소, 버퍼링과 같이 Sequence에서 할 수 있는 모든 것을 처리한다.

![스크린샷 2022-12-07 오전 4 39 24](https://user-images.githubusercontent.com/63997044/206087618-5dfeccfb-143a-4b27-9a1f-0899d3a2d5f6.png)

AsyncThrowingStream
- 오류를 처리할 수 있는 AsyncStream
- 반복 중에서 오류가 발생한다면 throw를 통해 실패를 처리할 수 있다.

정리
- AsyncSequence는 둘 이상의 비동기 값을 처리하는데 유용한 도구임
- Sequence의 사용법을 알고 있다면 동일한 방법으로 AsyncSequence를 다룰 수 있음
- AsyncSequence가 무엇인지, 어떻게 사용되는지, AsyncStream이 무엇인지 알아보았음
