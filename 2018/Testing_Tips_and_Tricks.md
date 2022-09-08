# Testing Tips & Tricks

<img width="1174" alt="스크린샷 2022-09-08 오후 12 00 05" src="https://user-images.githubusercontent.com/63997044/189024232-af55e96a-c7dc-41a7-bdf2-f451a4d62559.png">

### # Introduction
> `테스트`는 코드가 올바르게 작동하는지 지속적으로 확인하는 필수적인 도구이다. 그러나 코드에 제어할 수 없는 종속성이 있는 경우가 있다. `XCTest`를 이용하여 Apple 플랫폼에서 테스하기 어려운 코드를 테스트하는 기술을 알아보자. 또한 빠르게 실행하고 유지 관리가 쉬운 고품질 테스트를 작성하기 위한 다양한 팁을 알아보자.

<img width="1172" alt="스크린샷 2022-09-08 오후 12 00 27" src="https://user-images.githubusercontent.com/63997044/189024268-e101868a-d1c6-4ad6-98f4-5b96730db864.png">

목차
1. Testing network request(앱에서 네트워킹 코드를 테스트)
2. Working with norigications(알림 객체를 다루는 테스트)
3. Mocking with protocols(테스트에서 모의 ​​객체를 만들 때 프로토콜을 활용하는 방법)
4. Test execution speed(테스트를 빠르게 실행하기 위한 몇 가지 기술)

___

## # Test Network Request
<img width="1172" alt="스크린샷 2022-09-08 오후 12 00 51" src="https://user-images.githubusercontent.com/63997044/189024300-d370f012-f31c-47b5-80f7-c98e74819b0c.png">

- Test Suite를 구성하는 방법에 대한 가이드(`WWDC 2017`의 `Engineering for Testability` 세션)을 요약하자면, 
    - 이상적인 Test Suite는 개별 클래스와 메서드를 실행하는 여러 단위 테스트(unit test)로 구성되어야 한다.
    - 단위 테스트들은 중간 규모의 통합 테스트(midlevel integration test)를, 최종적으로는 사용자가 기기에서 수행하는 행동과 유사한 방식으로 앱을 실행하는 UI 테스트 형태의 시스템 테스트를 구성한다.

<img width="1176" alt="스크린샷 2022-09-08 오후 12 01 12" src="https://user-images.githubusercontent.com/63997044/189024341-c2d204c3-cb95-4217-935f-7190de8b84d7.png">

앱에서 네트워크 요청을 하고 데이터를 UI에 공급하는 데이터 흐름은 다음과 같다.

<img width="1179" alt="스크린샷 2022-09-08 오후 12 01 41" src="https://user-images.githubusercontent.com/63997044/189024410-e8430d0d-b8f0-4f24-af33-447ef85b3ddd.png">

(예제 코드 설명)
이 메서드(loadData)는 사용자의 위치가 포함된 매개변수를 사용하고 이를 쿼리 매개변수로 사용하여 API endpoint에 대한 URL을 구성한다. 그런다음 URLSession API를 사용하여 get 요청에 대한 dataTask를 생성한다.
그런 다음 서버에서 응답하면, 데이터를 언래핑하고 PointOfInterest 타입으로 디코딩하고 테이블 뷰를 업데이트하여 화면에 표시한다. 

<img width="1174" alt="스크린샷 2022-09-08 오후 12 02 04" src="https://user-images.githubusercontent.com/63997044/189024465-699e6873-99b6-4f19-99fe-723330fdb848.png">

Prepare URLRequest와 Parse Response 단계에 대한 단위 테스트를 작성해보자.

<img width="1178" alt="스크린샷 2022-09-08 오후 12 03 05" src="https://user-images.githubusercontent.com/63997044/189024609-36dec76d-456c-4766-9c1f-bbbb16b3522d.png">

이 코드를 더 테스트하기 쉽게 만들기 위해 View Controller에서 꺼내서 PointsOfInterestRequest 타입의 구조체에 속해 있는 두 개의 메서드(makeRequest, parseResponse)를 만들었다.

<img width="1174" alt="스크린샷 2022-09-08 오후 12 04 12" src="https://user-images.githubusercontent.com/63997044/189024756-eb62f66a-8194-4e48-ac7e-d69e0014a8e4.png">

이렇게 분리를 하면 코드에 대한 단위 테스트를 간단하게 할 수 있다. 샘플 데이터를 만들어 메서드에 전달하고 반환 값에 대한 assertion을 통해 테스트할 수 있다. 

<img width="1180" alt="스크린샷 2022-09-08 오후 12 04 27" src="https://user-images.githubusercontent.com/63997044/189024786-3bde3452-3a3a-4a2b-9c3b-b779f30fe3d2.png">

마찬가지로 mock JSON을 전달하여 메서드의 반환 값에 대한 assertion으로 테스트할 수 있다. 여기서 주목해야 할 점은 throws 표시가 있다는 점이다. 주변에 명시적인 do ~ catch블록이 없어도 테스트 코드에서 try를 사용할 수 있다.

<img width="1173" alt="스크린샷 2022-09-08 오후 12 04 56" src="https://user-images.githubusercontent.com/63997044/189024840-a1d8aca4-a0b3-40e2-9267-43c292672e55.png">

<img width="1175" alt="스크린샷 2022-09-08 오후 12 05 19" src="https://user-images.githubusercontent.com/63997044/189024879-b1981757-f6f3-4ece-8485-60a2d97c1c6b.png">

<img width="1177" alt="스크린샷 2022-09-08 오후 12 06 22" src="https://user-images.githubusercontent.com/63997044/189025013-2f12ec3b-2207-4170-9197-c350e563eb63.png">

(예제 코드 설명)
다음은 URL 세션과 상호작용하는 코드이다.
앞에서 봤던 메서드에 대응되도록 APIRequest 프로토콜을 만들고 APIRequestLoader 클래스에서 이를 사용하는 구조이다. apiRequest 타입과 urlSession으로 초기화된 후 loadAPIRequest 메서드에서 이들을 사용하여 URL 요청을 생성하고, 응답이 오면 이를 디코딩한다. 

<img width="1174" alt="스크린샷 2022-09-08 오후 12 06 35" src="https://user-images.githubusercontent.com/63997044/189025035-c4c99612-0d05-4c1b-a148-e85dc81ba6dc.png">

이 메서드의 내부 기능들에 대한 단위 테스트를 만들 수도 있겠지만 지금은 피라미드의 위로 올라가 데이터 흐름의 여러 부분을 다루는 중간 규모의 통합 테스트(midlevel integration test)를 살펴볼 것이다. 이 테스트의 목적은 URLSession API와의 상호 작용에 대한 정확성이다.

<img width="1176" alt="스크린샷 2022-09-08 오후 12 06 53" src="https://user-images.githubusercontent.com/63997044/189025073-f55ea4cf-5c48-4d55-a961-e5d1fb26f2ef.png">

`URLSession`은 앱이 네트워크 요청을 수행하는데 필요한 high-level API를 제공하지만, 그 이면에는 네트워크 연결을 열고, 요청을 write하고, 응답을 read하는 것과 같은 기본 작업들을 수행하는 low-level API인 `URLProtocol`이 존재한다. URLProtocol은 URL 로딩 시스템에 대한 확장성을 제공하기 위해 서브클래싱되도록 설계되었는데, Foundation은 HTTPS와 같은 일반적인 프로토콜을 위한 내장 서브클래스를 제공하지만 우리는 **테스트를 위해 mock 응답을 제공하도록 재정의를 할 것이다.**

<img width="1179" alt="스크린샷 2022-09-08 오후 12 07 25" src="https://user-images.githubusercontent.com/63997044/189025128-5689cb2b-58df-4e9d-9b8e-026bedacd325.png">

테스트 번들에서 MockURLProtocol 클래스를 만들고, canInit 메서드와 canonicalRequest 메서드를 재정의한다.

<img width="1176" alt="스크린샷 2022-09-08 오후 12 07 52" src="https://user-images.githubusercontent.com/63997044/189025194-7b81f495-6603-4edb-8355-8a7e1d4269f8.png">

테스트가 이 MockURLProtocol에 연결할 수 있도록 테스트에 설정해 줄 `requestHandler` 프로퍼티를 생성한다. 
- URLSession task가 시작되면 시스템은 URLProtocol의 서브클래스를 인스턴스화 하고 `startLoading` 메서드를 호출한다. 여기서 requestHandler를 테스트의 일부분으로 가져와 사용하고 매개변수로 `URLrequest`를 사용한다. 
- 요청 취소 테스트를 수행하는 경우 `stopLoading` 메서드를 오버라이딩하여 유사하게 구현한다.

<img width="1174" alt="스크린샷 2022-09-08 오후 12 10 06" src="https://user-images.githubusercontent.com/63997044/189025477-55561fcc-d7de-44a5-a068-bff83e260de5.png">

Stub Protocol을 사용하여 작성한 테스트. APIRequestLoader 인스턴스를 만들어 __요청 타입__ 과 __우리가 재정의한 URLProtocol을 사용하도록 설정된 URLSession__ 을 설정한다. 

<img width="1175" alt="스크린샷 2022-09-08 오후 12 10 45" src="https://user-images.githubusercontent.com/63997044/189025552-648ebb46-1bcf-49e3-a325-713d9cf0b4b4.png">

테스트 본문에서는 MockURLProtocol에 requestHandler를 설정하여 나가는 요청에 대하여 assertion을 만든 다음 stub 응답을 제공한다. 그런 다음 loadAPIRequest를 호출하여 완료 블록이 호출될 때까지 기다리면서 parse된 응답(PointOfInterest 타입으로 디코딩 된 응답 데이터)에 대한 assertion을 수행한다.

<img width="1172" alt="스크린샷 2022-09-08 오후 12 11 18" src="https://user-images.githubusercontent.com/63997044/189025620-3865d965-7a89-4d90-a107-ce2fd7df1003.png">

여기까지 통합 테스트를 진행했다면 코드가 잘 작동하고 시스템과 제대로 통합되었는지 판단할 수 있을 것이다. 마지막으로 시스템 레벨의 E2E 테스트(앱의 처음부터 끝까지를 테스트하는 것으로 UI 테스트 포함). UI 테스트에 관한 내용은 `WWDC 2015`의 `UI Testing in Xcode`에 소개되어 있다. 

<img width="1174" alt="스크린샷 2022-09-08 오후 12 11 43" src="https://user-images.githubusercontent.com/63997044/189025648-dc9e7e2d-b955-4b5e-8ecd-642ed41517ea.png">

E2E 테스트의 문제는 테스트가 실패했을 때 **원인을 찾기 어렵다**는 점이다. 이를 극복하기 위해 우리가 했던 것은 mock 서버의 인스턴스를 설정하고 UI 테스트를 중단하여 실제 서버 대신 mock 서버에 대해 요청을 만드는 것이었다. 이를 통해 앱에 피드백되는 데이터를 제어할 수 있었기 때문에 UI 테스트를 훨씬 더 안정적으로 수행할 수 있었다.(어떤 데이터가 들어올 지 미리 알 수 있기 때문에 UI 테스트가 수월하다는 뜻인 것 같다.) mock 서버를 사용하여 테스트하는 것이 유용하긴 하지만, 결국엔 단위 테스트에서 실제 서버에 대해 요청을 하는 테스트가 필요하다.

<img width="1175" alt="스크린샷 2022-09-08 오후 12 12 11" src="https://user-images.githubusercontent.com/63997044/189025695-e914cc6c-c35e-44c3-873c-c7f9adc4c0ac.png">

지금까지 다음 내용을 살펴보았다.
1. 단위 테스트를 용이하게 하기 위해 코드를 작고 독립적인 조각으로 나누는 예시
2. 네트워크 요청을 mocking하기 위해 URLProtocol을 어떻게 사용할 수 있는지
3. 피라미드 모델을 통해 균형 잡힌 Test Suite는 어떻게 구성되어야 하는지
___
## Working With Notifications

<img width="1171" alt="스크린샷 2022-09-08 오후 12 12 39" src="https://user-images.githubusercontent.com/63997044/189025750-d3f63756-acd9-44d5-b4bd-e27da03ac1bb.png">

- 여기서 말하는 Notification이란 foundation-level의 Notification(NSNotification)을 의미 
- 따라서 subject가 알림을 **관찰(observe)**하는지, 알림을 **게시(post)**하는지 두 가지를 경우에 따라 테스트 
- 알림은 일대다 통신 매커니즘이므로 하나의 알림이 게시되면 앱 전체에서 여러 수신자에게 알림이 전송되고 의도하지 않은 부작용을 발생시킬 수 있다. 그러므로 격리된 방식의 테스트가 필요

<img width="1180" alt="스크린샷 2022-09-08 오후 12 13 05" src="https://user-images.githubusercontent.com/63997044/189025810-84253b31-c325-4a3a-886f-cfaf8d04cbcb.png">

(코드 예시) 알림을 통해 플래그를 변경하는 코드

<img width="1174" alt="스크린샷 2022-09-08 오후 12 13 34" src="https://user-images.githubusercontent.com/63997044/189025883-eac49162-1d05-414f-aea3-114b7f4a28ee.png">

이 코드에 대한 단위 테스트 코드를 살펴보자. 이 테스트가 작동하면 코드의 다른 부분에서 알 수 없는 부작용이 발생할 수 있다. (특히 appDidFinishLaunching과 같은 시스템 알림의 경우)
따라서 이것을 테스트하기 위해 코드를 더 분리해야 한다.

<img width="1173" alt="스크린샷 2022-09-08 오후 12 14 13" src="https://user-images.githubusercontent.com/63997044/189025978-50c25092-2454-40c7-9359-39420bb55b85.png">

NotificationCenter에 여러 인스턴스가 존재할 수 있음을 인식해야 한다. 필요할 때 추가적으로 인스턴스를 생성할 수 있으며 이것이 테스트를 격리하는 데 핵심이 될 것이다. 
따라서 새로운 NotificationCenter 인스턴스를 만들고 `.default` 대신 사용해야 한다. 이것을 **의존성 주입**이라고 한다.

<img width="1175" alt="스크린샷 2022-09-08 오후 12 14 49" src="https://user-images.githubusercontent.com/63997044/189026059-1eb3a058-595c-4599-b042-33d840de9298.png">

원본 대신 별도로 생성한 인스턴스를 사용하도록 수정한 코드이다. 새로운 NotificationCenter 프로퍼티와 생성자에 매개변수를 추가했다. 그리고 default에 옵저버를 추가하는 대신 새로운 Notification Center를 사용한다.

<img width="1174" alt="스크린샷 2022-09-08 오후 12 15 10" src="https://user-images.githubusercontent.com/63997044/189026091-2c1f24b0-412e-467e-a808-e199e928f211.png">

다음은 수정된 테스트 코드이다. default 대신 별도의 Notification Center를 사용하도록 수정하였다. 

<img width="1173" alt="스크린샷 2022-09-08 오후 12 17 17" src="https://user-images.githubusercontent.com/63997044/189026353-81e3accc-9b5a-4745-94c3-75c1133254cc.png">

알림을 게시하는지 테스트하는 방법을 알아보자. 위와 같이 별도의 Notification Center를 사용하는 기술을 다시 언급하면서, 알림 관찰자를 추가하기 위한 `expectation API`의 사용도 함께 언급할 것이다.

<img width="1176" alt="스크린샷 2022-09-08 오후 12 17 36" src="https://user-images.githubusercontent.com/63997044/189026392-6af4a6b9-9d77-45aa-a536-ee85203129cc.png">

예제 코드. 마찬가지로 현재 default Notification Center를 사용하고 있다.

<img width="1176" alt="스크린샷 2022-09-08 오후 12 17 54" src="https://user-images.githubusercontent.com/63997044/189026432-b5b3c9cb-c3ce-4ea0-8a24-c936fccc6746.png">

그리고 마찬가지로 잘못된 테스트 코드 예제이다.

<img width="1173" alt="스크린샷 2022-09-08 오후 12 19 06" src="https://user-images.githubusercontent.com/63997044/189026568-fa147d15-7c42-4984-92ba-0545ad62d634.png">

이 테스트 코드를 개선할 수 있는 방법 중 하나는 `XCTNSNotificationExpectation` API를 사용하는 것이다. 이 API로 관찰자 생성을 처리하면 코드를 짧게 줄일 수 있다. 
여기에서 주목해야 할 것은 expectation의 타임아웃이 0이라는 것이다. NotifyAuthChanged 메서드가 반환될 때 알림이 이미 게시되어 있어야 하기 때문이다. 
알림 테스트에서 이 기술들을 활용하여 테스트가 완전히 격리된 상태로 유지할 수 있고, 기본 매개변수를 지정했기 때문에 기존 코드를 수정할 필요 없이 테스트 용이한 코드를 완성했다. 

## Mocking With Protocols

<img width="1167" alt="스크린샷 2022-09-08 오후 12 19 44" src="https://user-images.githubusercontent.com/63997044/189026647-e014ec41-552a-41ae-9f44-a54da5de4a84.png">

앱을 개발하는 동안 여러분의 클래스가 앱의 다른 부분이나 SDK에서 제공하는 다른 클래스와 상호작용하는 상황이 일어났을 것이다. 대부분의 SDK 클래스들은 직접 생성하도록 설계되지 않았기 때문에 테스트를 작성하는 것이 까다로울 것이다. 특히 테스트해야 하는 Delegate 메서드가 있는 경우에는 더욱 어렵다. 
해결법: 프로토콜을 사용하여 외부 클래스와의 상호작용을 mocking(Mock Interface 생성)

<img width="1170" alt="스크린샷 2022-09-08 오후 12 22 37" src="https://user-images.githubusercontent.com/63997044/189026948-7e3d3179-f260-4d8d-8e4b-dbcad4111e27.png">

<img width="1170" alt="스크린샷 2022-09-08 오후 12 21 02" src="https://user-images.githubusercontent.com/63997044/189026791-52043448-29e8-43ea-b90f-65cb5731ee29.png">

(예제 코드) CoreLocation을 사용하는 CurrentLocationProvider 클래스가 있다. CLLocationManager를 만들고 이니셜라이저 내부에서 설정 및 자신을 대리자로 등록한다. 메서드 내에서 대리자 메서드 호출을 통해 현재 위치를 얻어오고 있다.

<img width="1170" alt="스크린샷 2022-09-08 오후 12 21 47" src="https://user-images.githubusercontent.com/63997044/189026867-ca250dde-a86d-43c6-ac09-08ae1f508024.png">

이 클래스에 대한 단위 테스트 코드이다. 이 코드는 두 가지 문제점이 있는데
- 메인 로직이 존재하는 메서드를 확인하고 싶지만 위치를 요청하는 메서드가 호출되는 시점을 알 수 있는 방법이 없다. CLLocationManager의 메서드이기 때문이다.
- CoreLocation에 대한 사용자 인증을 받지 못했다면 권한을 요청하는 대화 상자가 화면에 띄워질 것이므로 테스트가 기기의 상태에 의존적이게 된다. 

<img width="1172" alt="스크린샷 2022-09-08 오후 12 23 12" src="https://user-images.githubusercontent.com/63997044/189027010-df0a4048-7b75-4c99-9d2e-048c34d3b60b.png">

이런 문제점을 해결하기 위해 외부 클래스를 서브클래싱하고 해당 클래스에서 호출하는 모든 메서드를 재정의하는 방법이 있다. 그러나 SDK의 모든 클래스가 서브클래싱을 염두해 두고 설계되어 있는 것은 아니기 때문에 **위험한 방법**이다. 따라서 서브클래싱 대신 **프로토콜**을 사용하도록 하자.

<img width="1170" alt="스크린샷 2022-09-08 오후 12 23 44" src="https://user-images.githubusercontent.com/63997044/189027070-a1904504-7c85-4a2d-8e2a-55d52d34641f.png">

새로운 프로토콜 LocationFetcher을 정의하여 CLLocationManager에서 사용하는 메서드 및 프로퍼티를 포함한다. 클래스에서는 프로토콜을 준수하게 만들고 locationManager를 locationFetcher로 변경하고 기존 코드가 충돌이 일어나지 않도록 기본 매개변수를 추가한다.

<img width="1172" alt="스크린샷 2022-09-08 오후 12 24 28" src="https://user-images.githubusercontent.com/63997044/189027160-b2afe244-f84a-4fa7-be18-4f934646a635.png">

<img width="1173" alt="스크린샷 2022-09-08 오후 12 24 41" src="https://user-images.githubusercontent.com/63997044/189027181-db7beae8-3b31-4aa8-8309-dad2ced04a33.png">

마지막으로 Delegate 메서드를 변경한다. Delegate 프로퍼티의 이름을 LocationFetcherDelegate로 변경하고, 익스텐션에서 해당 코드를 구현한다.

<img width="1176" alt="스크린샷 2022-09-08 오후 12 25 07" src="https://user-images.githubusercontent.com/63997044/189027247-3827846a-6f25-46e3-a9e6-c33903d8cf65.png">

Delegate를 교체해야 하지만 여전히 이전 CLLocationManagerDelegate 프로토콜을 준수해야 한다. 그러므로 코드를 추가해준다.

<img width="1171" alt="스크린샷 2022-09-08 오후 12 25 51" src="https://user-images.githubusercontent.com/63997044/189027321-ed096036-48b5-4820-ab6d-19fac1c37c1e.png">

단위 테스트에서, locationFetcher 프로토콜을 준수하고 요구 사항을 채우는 mock 객체를 위해 구조체를 정의한다.
requestLocation 메서드를 통해 가짜 위치를 가져온 다음 대리자 메서드를 호출하여 해당 가짜 위치를 전달하게 된다. 이제 준비가 끝났다.

<img width="1174" alt="스크린샷 2022-09-08 오후 12 26 05" src="https://user-images.githubusercontent.com/63997044/189027344-2c826781-b2f7-45d0-9e18-691ad2d7e846.png">

MockLocationFetcher 구조체 인스턴스를 만들고 handleRequestLocation 블록을 구성하여 가짜 위치를 제공한다. 그런 다음 CurrentLocationProvider의 인스턴스를 만들어 MockLocationFetcher에 전달한다. 그리고 마지막으로 완료 블록으로 checkCurrentLocation을 호출한다. 완료 블록 내부에는 위치에 대한 테스트를 수행하는 assertion이 있다. 
지금까지 프로토콜을 사용하여 외부 클래스 및 해당 대리자와의 상호 작용을 모의하는 방법을 보여주었다.

<img width="1168" alt="스크린샷 2022-09-08 오후 12 26 29" src="https://user-images.githubusercontent.com/63997044/189027390-03cc2df9-ddeb-4ff2-8413-36afffc360ee.png">

지금까지의 과정을 요약해보면, 
- 외부 클래스에 대한 인터페이스를 나타내는 새 프로토콜을 정의하였다. 이 프로토콜은 외부 클래스에서 사용하는 모든 메서드와 프로퍼티를 포함해야 한다.
- 프로토콜을 준수하도록 하는 외부 클래스에 대한 확장을 만들었다.
- 외부 클래스의 모든 사용을 새 프로토콜로 교체하였다.
- 테스트에서 이 타입을 설정할 수 있도록 이니셜라이저에 초기화 매개변수를 추가하였다.

<img width="1172" alt="스크린샷 2022-09-08 오후 12 26 37" src="https://user-images.githubusercontent.com/63997044/189027410-4829f3fc-dfb5-477f-a74b-e0be3f99ea1c.png">

- mock delegate 프로토콜을 정의하고 실제 타입을 대체하였다.
이렇게 하면 서브클래싱보다 작성해야 할 코드가 많지만, 시간이 지남에 따라 코드를 확장할 때 더 안정적이다.(수평적 확장의 안정성. 프로토콜의 장점)


## Test Execution Speed

<img width="1168" alt="스크린샷 2022-09-08 오후 12 27 04" src="https://user-images.githubusercontent.com/63997044/189027478-25c66b97-6f5e-4aa7-8275-2b6b4cb24aea.png">

테스트에서 인위적인 지연을 피해야 한다.

<img width="1172" alt="스크린샷 2022-09-08 오후 12 27 48" src="https://user-images.githubusercontent.com/63997044/189027559-3b9bda83-db6e-44d6-ae02-9461e836cb80.png">

(예제 코드) 타이머 API를 사용하여 10초마다 새로운 위치를 표시한다.

<img width="1173" alt="스크린샷 2022-09-08 오후 12 28 07" src="https://user-images.githubusercontent.com/63997044/189027597-53c91069-f67c-4cef-ad4e-aa979c6e4775.png">

이 작업을 위해 만든 단위 테스트 코드를 살펴보자. 
유예 기간으로 1초를 추가하여 11초 동안 런 루프를 실행한다. 그러나 실행하는 데 오랜 시간이 걸린다.(10초) 

<img width="1175" alt="스크린샷 2022-09-08 오후 12 28 44" src="https://user-images.githubusercontent.com/63997044/189027675-fbfb8281-7c5a-4e8e-95de-685136d2280f.png">

해당 시간 제한을 더 짧은 시간으로 지정할 수 있지만 단지 더 주기가 짧을 뿐 여전히 지연이 존재한다. 그리고 테스트가 시간에 종속적이라는 문제점이 존재한다.

![스크린샷 2022-09-08 오후 1 47 24](https://user-images.githubusercontent.com/63997044/189036590-3fa131ef-40a5-4343-bb09-7d16c857f16f.png)

이를 해결하기 위한 더 나은 접근방식은 무엇일까. 
지연 매커니즘을 식별하고, 이것을 Mock해서 테스트에 이용하는 방법이다.
예시를 통해 알아보자.

![스크린샷 2022-09-08 오후 1 48 59](https://user-images.githubusercontent.com/63997044/189036773-fcf028a1-beb3-4c01-8002-afa059d259be.png)

`scheduledTimer` 메서드는 실제로 두 가지 작업을 수행하고 있다. 타이머를 생성한 다음 해당 타이머를 RunLoop에 추가하는 것이다. 이 코드를 위처럼 분리함으로써 Testable한 코드로 바꿀 수 있다.
이렇게 분리하고나면, RunLoop가 이 클래스와 상호작용하는 또다른 외부 클래스임을 알게 된다.

![스크린샷 2022-09-08 오후 1 54 08](https://user-images.githubusercontent.com/63997044/189037433-6fe72972-1c67-40fc-a56b-0263fe4ab627.png)

`Mocking with Protocols`에서 했던 방법처럼 프로토콜을 만들고

![스크린샷 2022-09-08 오후 1 54 20](https://user-images.githubusercontent.com/63997044/189037439-8c53374a-3900-4b4e-b115-784e6bf4862e.png)

RunLoop를 새로 만들 프로토콜로 교체한다. 

![스크린샷 2022-09-08 오후 1 54 53](https://user-images.githubusercontent.com/63997044/189037522-3290eb89-3a59-46c9-988f-fca66e241eb6.png)

그리고 테스트 코드에서는 실제 RunLoop를 사용하지 않고 TimeScheduler로 대체할 것이므로 중첩된 새 구조체를 생성한다.

![스크린샷 2022-09-08 오후 1 54 41](https://user-images.githubusercontent.com/63997044/189037490-d7165ce1-f5e8-4b00-93e0-cdd5df8b630f.png)

최종적으로 수정한 테스트 본문 코드. 더 이상 지연되지 않고 타이머에 의존하지 않는다.

![스크린샷 2022-09-08 오후 1 56 26](https://user-images.githubusercontent.com/63997044/189037703-4643cfa1-f63a-4ce9-9784-7740e3d83c1b.png)

따라서, 이런 기술을 사용하면 지연시키는 코드를 완전히 제거할 수 있다.

![스크린샷 2022-09-08 오후 1 55 26](https://user-images.githubusercontent.com/63997044/189037577-44c83f95-9708-45b2-9745-5bef4690d15a.png)

테스트 실행 속도에 대한 추가적인 팁. 
- `NSPredicateExpectation`의 사용은 다른 expectation 클래스보다 성능이 좋지 않다. 그리고 UI 테스트에 적합하다. 콜백 매커니즘이 아니라 polar에 의존한다고 함. 
- 따라서 단위 테스트에서는 XCTestExpectation, XCTNSNotificationExpectation, XCTKVOExpectation과 같이 빠르고 콜백 기반의 매커니즘을 권장한다.

<img width="1163" alt="스크린샷 2022-09-08 오후 12 43 06" src="https://user-images.githubusercontent.com/63997044/189029339-a87bc489-cf12-43fb-a429-10f0eeb76fbe.png">

또 다른 팁. 
- 최대한 빠르게 실행하도록 만들어라. 테스터 러너로 실행될 때 많은 작업이 불필요하게 실행될 수 있다.
