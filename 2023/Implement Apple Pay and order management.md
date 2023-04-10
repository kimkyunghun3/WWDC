# Implement Apple Pay and order management

필요한 기본 사항

구현

빌드방법

주문 업데이트 방법

Apple Pay는 간편, 안전, 안심되는 결제 방법이다

두 가지 필수 사항이 필요하다.

1. 결제를 승인할 수 있는 가맹점임을 고유하게 식별하는 식별자 -> App Developer에서 Identifier 섹션으로 이동해야한다

![](https://i.imgur.com/UyGjt8r.png)

merchant로 시작해서 식별자를 만든다.

애플페이는 가맹점 식별자 + 공개 인증서로 페이로드를 암호화해서 만든다.

그러면 이것을 개발자 측에서 받아서 해독한 후 결제 성공 or 실패를 반환한다.

![](https://i.imgur.com/ctR6oIB.jpg)

앱에서 설정하는 방법

![](https://i.imgur.com/5tOsM8o.png)

Xcode의 Signing Capabilities 탭에서 Apple Pay 안에 설정해준다.

Web 에서도 설정하려면

![](https://i.imgur.com/A6iz0tG.png)

이 두가지가 필요하다!

### 애플페이 구현하는 방법

애플페이 구현하기 전에 결제할 수 있는지 여부를 파악해야한다.

![](https://i.imgur.com/RwgPhdk.png)

capabilities는 신용 또는 체크카드 등 시트에서 현재 가능한 결제 유형을 나타낸다.
가능한 것을 여기에서 지정해줄 수 있다.

만약 유저가 더 추가하고 싶으면 추가해줄 수 있다.

![](https://i.imgur.com/9DgH58O.jpg)

애플 페이 버튼은 여러 종류가 있으므로 사용자가 필요한 것에 따라 사용하면 된다!

UIKit에서 애플페이 버튼 보여주는 방법

![](https://i.imgur.com/m2lNPtp.png)

Webd에서 보여주는 방법

콘텐츠 전송 네트워크를 웹 페이지에 버튼을 올린다.

![](https://i.imgur.com/9XOeP5Y.png)

요청에 대해서 원하는 조건으로 결제가 되도록 만들어 줄 수 있다.

![](https://i.imgur.com/kdYtLyo.jpg)

요청할때 필수 조건이 있다
- 식별자
- 결제 유형
- 국가 코드
- 화폐 코드
- 결제를 승인하는 결제 네트워크 지정

온라인 애완동물 앱에서 결제 시스템으로 애플 페이를 제공하려고 한다

![](https://i.imgur.com/pXtKxvQ.jpg)

PKPaymentAuthorizationViewController에서 조금전에 설정한 것들에 대한 요청을 보낸다.

![](https://i.imgur.com/LGUDown.jpg)

여기에 있는 delegate는 새 이메일 주소나 유저가 시트에서 변경 사항을 전달하는 방법이다.

결제 시트를 닫는 것은 개발자 책임이므로 이를 구현해주어야 한다.

![](https://i.imgur.com/dfIYib8.jpg)

에러가 났을 때에 대한 처리, shipping이 제대로 안되었을 때 처리, 인증관련 처리도 개발자가 이후 동작을 결제 프로세스에 전달하여 처리해야 한다.

SwiftUI로 Apply Pay 결제 구현

![](https://i.imgur.com/Yja11Xe.jpg)

이렇게 해두면 사용자에 결제 시트가 자동으로 표시된다.

내부에서 process를 통해 반환된 것을 성공이나 오류면 오류에 대한 처리를 해주면 된다.

결과에 대한 행동

![](https://i.imgur.com/Eljg4Mp.png)

문제가 생기면 우선순위에 따라 높은 것을 내보낸다.

![](https://i.imgur.com/uHQ99eD.jpg)

### 실제로 주문을 빌드

iOS 16에서는 주문 추적을 확인할 수 있다

주문 상세 정보와 언제 주문 픽업할 수 있는지를 추적가능하다.

이제 주문 추적을 추가하는 방법

주문하는 방법은 애플 페이 설정하는 것과 비슷하다

Apple Developer에서 identifier 설정하는 곳에 가야한다.

identifier를 설정하고 주문 유형 ID 인증서를 생성해야 한다.
그리고 도메인명에 order를 앞에 하는 것을 추천한다.

![](https://i.imgur.com/JAZI5IL.png)

인증서를 사용하여 주문 패키지를 빌드하고 주문을 업데이트하고 알림을 보낸다.

![](https://i.imgur.com/zkTEnFz.jpg)

요렇게 구입 가능! 지갑에 주문 추가 가능하다.

이제 아까봤던 결제 흐름에 대해 다시 살펴본다.

![](https://i.imgur.com/D75c9KX.png)

결제가 성공하면 서버에서 주문을 생성해야한다.

![](https://i.imgur.com/ossGVIt.jpg)

반환하는 결과에서 주문 추적하려면 생성한 주문에 대한 일부 세부 정보도 포함되어야한다!

이런 방법을 통해 주문을 비동기로 요청할 수 있다.

이제 서버에서 주문 패키지를 기기로 반환하면 지갑에 표시된다

다음으로 반환해야하는 세부 정보를 살펴본다

주문 세부 정보는 4개의 필드로 구성

![](https://i.imgur.com/kYcJSzP.png)

마지막 토큰은 유저와 서버간의 연결하는 비밀 토큰이다.

아까 그곳에서 주문 세부정보를 넣어본다!!

![](https://i.imgur.com/yA1N6Rm.jpg)

Web

![](https://i.imgur.com/QAWl4f3.jpg)

세부 정보를 반환하면 각 사용자의 기기에서 동기화된다.

![](https://i.imgur.com/oxGOAkC.png)

order package의 내부

주문은 주문 패키지를 반환한다.

주문 패키지에는 주문을 사용자에게 표시하고 가맹점의 주문에 필요한 정보를 모두 가지고 있다.

![](https://i.imgur.com/tzuhtsq.png)

JSON, 이미지, 파일 등이 있고 이를 하나의 manifest 라는 또다른 JSON 사전에 설명되어 있다.

주문 패키지의 모든 파일에 대한 참조가 포함되어있다.

항목의 키는 값으로 파일의 SHA256 체크섬이다.

다음으로는 진위를 증명하는 부분

![](https://i.imgur.com/XqsjN4k.png)

ID + Apple에서 제공하는 인증서를 통해 하나의 패키지로 만든다.

이것의 파일 확장자를 order로 변경하기만 하면 된다!

### 주문 업데이트

![](https://i.imgur.com/fj51R1O.jpg)

주문 -> 등록.. order 업데이트되면 noti -> order 요청 -> order package 보낸다.

내부를 파헤쳐보면!

먼저 주문에 대한 업데이틀 한다고 표시한다.
이를 지갑에 알려주어야 한다.

주문 패키지에 2가지 정보만 포함하면 된다.

![](https://i.imgur.com/EZVe9wJ.png)

진위 증명하는데 사용한다.

이제 요청하는 시기를 관리해야한다.

![](https://i.imgur.com/dvECIKE.png)

어떤 기기에서 요청한 것인지 찾을 수 있어야 한다.

또 반대로 기기에서 등록한 주문을 찾을 수 있어야한다.

![](https://i.imgur.com/jI5PJA3.png)

다음으로는 업데이트가 되면 기기에 noti해준다!

![](https://i.imgur.com/WyjkMGZ.png)

주문유형 ID 주소를 통해 APNs와 통신할 수 있다.


다음으로는 새 주문 패키지를 요청한다

![](https://i.imgur.com/6hSRkjQ.jpg)

기기에서 푸시 알림을 수신하면 푸시 알림이 비어있어서 어떤 주문이 변경되었는지 모른다.

요청한 이후 변경된 주문을 파악한다.

관련 주문을 찾은 다음 해당 ID를 반환한다. 

업데이트 시간을 추적하고 응답에 수정 태그를 포함하여 향후 요청되는 ID의 수를 제한한다.

기기에서 다음 변경 요청 때 수정 태그를 제공한다.

애플 페이 Best practices

![](https://i.imgur.com/6SqpdYF.png)
