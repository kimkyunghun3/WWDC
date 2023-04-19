# The Push Notifications primer

![](https://velog.velcdn.com/images/marisol/post/3cad25a7-e74e-4503-9f7f-752261ef7ebf/image.png)

![](https://velog.velcdn.com/images/marisol/post/86576e37-0fb6-47d4-90ae-192b6c50ceb0/image.png)

- Push Notification의 개요 (기능, 사용 이유)
- Alert Push 실행
- Background Push 실행

## 1️⃣ Introducing Push Notifications

![](https://velog.velcdn.com/images/marisol/post/ce93e99b-e536-40ac-adf3-3cf5821c7bf3/image.png)

```Push Notification```을 사용하면, 아래와 같은 기능을 할 수 있다.

#### **1) 사용자가 앱과 상호작용하도록 유도**
- 넷플릭스에서 내가 기다리던 컨텐츠가 추가되었다거나, 무신사에서 할인 쿠폰을 준다든가 하는 알림을 보내서 사용자가 앱을 열어 참여하도록 유도할 수 있다.

#### **2) 업데이트 제공**
- 새로운 정보나 업데이트된 컨텐츠를 제공할 수 있다.

#### **3) 동적 경험**
- 앱이 Background에 있는 상태에서 새로운 메세지가 도착하면 미리 메세지를 다운로드하고, 사용자가 앱을 열면 새로운 메세지를 쉽게 확인할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/aee9f42d-17f8-4c11-a9e6-d52b011ec592/image.png)

푸시 알림은 앱 상태에 상관없이 (포그라운드 / 백그라운드) 앱에 전달되며, 
전력 효율적이고
유저와 소통할 수 있는 좋은 방법이다.

![](https://velog.velcdn.com/images/marisol/post/f2648778-b3ef-43cf-8e67-3cb506e11f06/image.png)

푸시에는 2가지 유형이 있다.

1) Alert notification
- 앱이 유저와 상호 작용할 수 있는 가시적인 알림을 제공할 수 있다.

2) Background Notification
- 앱이 백그라운드 상태일 때도 계속해서 실행되며, 앱의 콘텐츠를 업데이트하거나 새로운 콘텐츠를 다운로드하는 등의 작업을 수행할 수 있다.

먼저 Alert Notification 부터 ㄱㄱ

![](https://velog.velcdn.com/images/marisol/post/809b73a2-8322-4c9c-a587-b2bd28ee436d/image.png)

Alert Push를 사용하면 가시적인 알림을 전달해서 앱에 대한 업데이트를 제공할 수 있다.
이 push는 상호작용할 수 있는 새 정보를 알려주는데 사용한다.
앱의 상태에 관계없이 표시된다.
그리고 전적으로 커스텀이 가능하다. 알림의 표시 방식은 개발자에게 달려있다.

![](https://velog.velcdn.com/images/marisol/post/4377f5d5-d889-4f75-bf41-a3697f020e96/image.png)

Alert Push를 위해 앱 설정을 어떻게 해야하는지 방법에 대해 알아보자.

💡 가장 먼저 해야 할 일 == 원격 알림에 등록하는 것!
그러면 디바이스가 ```APNs(Apple Push Notification system)```에 등록되고,
디바이스 토큰이 앱에 리턴된다. (이 토큰은 디바이스를 식별하는데 사용되며, 이 토큰을 통해 알림 대상으로 지정할 수 있다.)

![](https://velog.velcdn.com/images/marisol/post/3789995a-dd7a-4772-9716-94dcd2e18e03/image.png)

그리고 AppDelegate을 ```UNUserNotificationCenterDelegate```로 선언하고,

![](https://velog.velcdn.com/images/marisol/post/8fce6340-2746-4bc6-919c-a9c84330c838/image.png)

AppDelegate을 NotificationCenter의 Delegate으로 할당해야 한다. 
이렇게 하면 Notification이 열릴 때 앱에 알려준다.

![](https://velog.velcdn.com/images/marisol/post/186b57b0-51b8-4b13-9230-fe684e11dea1/image.png)

맨 처음에 registerForRemoteNotifications 메서드로 원격 알림에 등록을 했는데,
이 메서드를 호출하면 성공/실패 2가지 메서드 중 하나에 대한 콜백이 수신된다.

- didFailRegisterForRemoteNotificationsWithError (실패)
- didRegisterForRemoteNotificationsWithDeviceToken (성공)

여러 이유로 디바이스에 대한 토큰을 가져오지 못한 경우, 등록에 실패한 이유를 설명하는 didFailRegisterForRemoteNotificationsWithError 메서드가 호출된다.

![](https://velog.velcdn.com/images/marisol/post/542bbecd-c1cc-47a7-a44f-11fb2274bcf3/image.png)

토큰을 가져오는데 성공한 경우, 해당 토큰을 백엔드 푸시 서버로 보내야 이 디바이스에 알림을 전달할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/f02c130c-bc8e-4551-b8f0-ce70254cf1a2/image.png)

디바이스 토큰은 앱에 Data 형태로 전달되는데, 서버로 전송하려면 String으로 변환해야 한다.
Data를 16진수 문자열로 변환한 다음, 다시 하나의 string으로 결합한다.

![](https://velog.velcdn.com/images/marisol/post/41caaeb5-c6cd-485d-abc4-97e1ff223e0e/image.png)

그런 다음 이 String을 URLQuery에 추가하여 전체 endpoint를 구성한다.
그리고 URLSession을 통해 토큰을 서버로 전송하여 데이터베이스에 등록한다.

![](https://velog.velcdn.com/images/marisol/post/05781028-d671-4ae8-81cd-4bbb1755f239/image.png)

마지막으로 디바이스가 알림을 수신하려면, 앱에 알림을 표시할 수 있는 권한을 요청해야 한다.
알림 "요청" 권한을 부여하면, 앱이 알림을 표시할 수 있는지를 묻는 메세지가 표시된다.
알림 권한 요청에 대한 결과는 completion Handler에 전달되며, 디바이스 설정에 표시된다.
여기서는 알림을 표시하고(alert), 소리를 재생하며 (sound), 아이콘에 뱃지를 표시할 수 있는 권한 (badge)를 요청하고 있다.

컨텍스트가 있을 때만 액션에 대한 응답으로 권한 부여를 요청한다. (예를 들어서 뜬금없이 갑자기 그냥 알림 보내고 싶으니까 허용해주세요!보다는, 앱을 처음 깔았을 때 할인 같은 이벤트 소식을 보내려고 하니까 허용해주세요!라는 context가 있어야 한다는 뜻)

이러한 context를 제공하면 사용자가 권한 요청에 더욱 긍정적으로 반응할 가능성이 높아진다.

![](https://velog.velcdn.com/images/marisol/post/6f751d50-43b7-475c-84cf-528a2936b42d/image.png)

이제 알림 페이로드를 처리하는 방법에 대해 설명하기 전에, 페이로드의 예를 살펴보려고 한다.
예를 들어, 새로운 이벤트가 있을 때마다 사용자에게 푸시를 보내는 레스토랑 앱이 있다고 해보자.
이 얼럿은 아보카도 베이컨 버거가 판매중임을 알려주고 있다.

![](https://velog.velcdn.com/images/marisol/post/7ba948d2-66b4-44d3-8f1a-4dfb380d4faf/image.png)

이 얼럿에 대한 페이로드를 살펴보면, 
일단 aps dictionary가 있다.
이 aps dictionary는 디바이스에 알림을 렌더링하는 방법을 알려준다. 
aps dictionary 의 alert dictionary에서는 시스템에서 알림에 사용할 텍스트를 알려주고 있다.
title 필드는 얼럿의 목적을 설명하는 문자열인데, 짧고 이해하기 쉬워야 한다.
body 필드는 얼럿 메세지의 전체 설명 텍스트이다.

sound는 선택사항이며, 기본 사운드를 설정할 수도 있지만 앱에 따라 커스텀 사운드를 제공할 수도 있다.
뱃지도 선택사항이다. 얼럿이 열려서 처리할 때 이 값을 0으로 설정한다.

여기서 aps 필드 외부에 있는 special과 price는 알림과 함께 제공할 수 있는 커스텀 데이터이다.
알림의 텍스트로 직접 표시되는 것은 아니지만, 앱에서 푸시 알림을 처리할 때 사용된다.
이 푸시 알림을 받은 사용자는 알림을 클릭하면 해당 앱에서 avocado bacon burger에 대한 자세한 내용을 확인할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/d35282b8-16b4-4beb-b84f-85b2caa313eb/image.png)

이제 페이로드가 어떻게 생겼는지 알았으니 알림 수신을 처리하는 방법에 대해 알아보자.
이 userNotificationCenter 메서드는 알림이 열릴 때마다 호출되는 UNUserNotificationCenterDelegate 메서드이다.
이 메서드에는 메서드가 return 되기 전에 호출되는 completionHandler가 있다.
앱에 전달된 페이로드는 알림 내용의 userInfo 프로퍼티에서 추출할 수 있다.
(아까 special과 price같은 정보들을 userInfo의 key로 지정해서 데이터를 얻을 수 있다)
페이로드가 있으면 json의 dictionary에서 데이터를 파싱해서 사용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/e2f16325-b3a3-4be8-9a78-48cd531fa14c/image.png)

이 메서드에서 앱이 필요로 하는 데이터가 payload에 포함되어 있지 않은 경우에는, completionHandler를 호출하고 메서드를 종료한다.

![](https://velog.velcdn.com/images/marisol/post/db1de720-bcd6-497d-a557-6d789164007f/image.png)

앱에서는 얼럿과 관련된 곳을 열어야 한다. 이 레스토랑 앱의 예에서는, 얼럿을 누르면 해당 항목 (아보카도 베이건 토스트)이 장바구니에 추가되고 장바구니가 표시된다.

그리고 작업이 완료되면 completionHandler를 호출하여 시스템에 완료 사실을 알린다. 그럼 끝!

## 2️⃣ Implementing Alert Pushes & Implementing Background Pushes

![](https://velog.velcdn.com/images/marisol/post/408b3416-0ba1-4019-b71a-5b9de642443d/image.png)

이제 Background push와, 앱에서 Alert push를 사용하는 방법에 대해 알아보자.
Background push는 Alert push와 유사하지만, 기억해야할 몇 가지 차이점이 있다.

![](https://velog.velcdn.com/images/marisol/post/6fa1ccf8-da1b-43e8-b3c5-73fa7d8b48e0/image.png)

먼저, Background Push를 사용하면 푸시 알림을 수신할 때 앱이 백그라운드에서 데이터를 가져올 수 있다.

Background Push는 앱이 실행 중이 아닐 때도 푸시 알림을 받으면 데이터를 가져와 업데이트할 수 있도록 한다. 이렇게 하면 애플리케이션을 실행하지 않고도 항상 최신 상태를 유지할 수 있다. 

시스템은 앱을 실행하고 필요한 런타임을 제공하여 백그라운드 업데이트를 수행한다. 
하지만 몇 가지 제한이 있는데, 시스템은 하루에 애플리케이션의 백그라운드 작업 수를 제한한다. 
그리고 디바이스가 특정 제약 조건을 충족하지 못하면 백그라운드 업데이트를 수행하지 않는다. 예를 들어, 디바이스가 배터리 부족 상태일 때는 백그라운드 업데이트가 수행되지 않는다.

그러면 Background Push을 수신하도록 등록하고 처리할 수 있도록 앱을 설정하려면 어떻게 해야할까?

![](https://velog.velcdn.com/images/marisol/post/828e9416-941a-4d7e-a032-7d5c75762122/image.png)

Alert Push와 마찬가지로, 앱에 대한 디바이스 토큰을 얻으려면 원격 알림을 위해 앱을 등록해야 한다. (registerForRemoteNotifications)

![](https://velog.velcdn.com/images/marisol/post/87e185e0-1f23-4045-be64-3a8d62a2312e/image.png)

하지만 Alert Push와 달리 앱을 UNUserNotificationCenterDelegate로 선언하거나, UNUserNotificationCenter에 할당할 필요는 없다.
왜냐면 UNUserNotificationCenterDelegate는 Alert Push를 처리할 때만 사용되기 때문이다.

registerForRemoteNotifications가 호출되었기 때문에 디바이스 토큰 수신을 처리해야 하며, 
자신의 등록을 위해 해당 디바이스를 푸시 서버로 보내야 한다. (Alert Push에서와 완전 동일함)

![](https://velog.velcdn.com/images/marisol/post/10456043-db3a-40d1-9c6b-e14a8c16bff1/image.png)

Background Push의 페이로드는 Alert Push의 페이로드보다 훨씬 단순하다.
필요한 유일한 필드가 aps dictionary의 content-available 필드이다.
이 필드는 시스템한테 Background Push이며, 업데이트를 수행하려면 앱을 시작해야함을 알려준다.
Alert Push와 마찬가지로, aps dictionary 외부의 custom data를 사용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/5f9715c8-d1ae-45bc-9ed8-fa70a85a957e/image.png)

이제 background push를 어떻게 처리하는지 살펴보자.
디바이스가 원격 알림을 수신하면, didReceiveRemoteNotification 메서드가 호출된다.
이 메서드를 통해 background 알림을 처리하면 된다.

이 메서드에는 completionHandler가 있는데, Alert push를 처리할 때의 completionHandler와는 달리 파라미터가 하나 있다.

이 파라미터는 시스템에게 백그라운드 업데이트가 실패했는지, 받은 데이터가 없는지, 새로운 데이터를 받았는지를 알려주는 enum 타입이다. (UIBackgroundRetchResult)

레스토랑 앱의 경우, background push를 사용하여 메뉴의 내용을 최신으로 유지한다.

![](https://velog.velcdn.com/images/marisol/post/1d20234a-52ae-40c2-b4c5-c113b19da7c5/image.png)

메뉴의 현재 버전에 대한 url을 만들지 못하면, completionHandler를 호출하여 백그라운드 업데이트에 실패했음을 알린다.

![](https://velog.velcdn.com/images/marisol/post/51021ba8-6120-4306-9504-c2c4a5804330/image.png)

URL이 생성되면, URLSession을 통해 오늘 메뉴에 대한 데이터를 가져와야 한다.
데이터가 수신되지 않은 경우(업데이트할 메뉴가 없는 경우 - 최신인 경우), completionHandler를 호출하여 백그라운드 업데이트가 데이터 없이 성공적으로 완료되었음을 알려준다.

![](https://velog.velcdn.com/images/marisol/post/2390efc4-6655-4cf6-a56c-5cc3be9c66ad/image.png)

그렇지 않으면 업데이트된 메뉴를 가져왔으므로 앱은 해당 데이터를 사용하여 내용을 업데이트할 수 있다.
작업이 완료되면 completionHandler를 호출하여 새 데이터를 가져왔고 백그라운드 업데이트가 성공했음을 시스템에게 알릴 수 있다.

![](https://velog.velcdn.com/images/marisol/post/9e975221-5182-465d-b84f-b02c2c505076/image.png)
