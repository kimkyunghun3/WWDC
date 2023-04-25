# Enhance your Sign in with Apple experience

### Prevent duplicate accounts

iOS 13 이후로 애플 로그인은 빠른 원터치로 비밀번호 없이 로그인 가능하다.
보안이 잘되어있다.
이메일로 verify한다.
사기를 방지할 수 있다.

비밀번호를 찾을 수 있는 계정이 이미 있으면 동일한 두번째 계정을 만들지 않아야 한다.

![](https://i.imgur.com/f18QybF.png)

기존 이메일이나 애플 로그인으로 로그인할 수 있다.

만약 기존의 로그인 증명된 것이 있으면 키보드 위에 표시해준다.

![](https://i.imgur.com/W11U6iv.png)

그러면 탭 한번으로 자격증명을 할 수 있다.

![](https://i.imgur.com/zFinn7M.jpg)

비밀번호를 기억할 필요 없이 쉽게 로그인할 수 있다.

비밀번호 자동완성 이외에도 기존 자격 증명을 제시한다.

올바른 계정으로 로그인하도록 유도한다.

![](https://i.imgur.com/OopJzFG.png)

Authentication Service API가 이부분에서 유연하다.

도입 방법은 쉽다!

![](https://i.imgur.com/m5bDvnB.jpg)

요청에 필요한 3개를 넣고 delegate와 개체를 선언해준 다음

요청을 하면된다!

iOS 16.0에는 옵션에 들어가는게 생기는데 이 뜻은 장치에서 즉시 사용할 수 있는 자격 증명만 원한다고 시스템에 알리는 기능으로 앱 실행시 알려준다.

이 코드를 실행하면 이처럼 기존 자격 증명 페이지를 띄어준다.

![](https://i.imgur.com/27dsSdr.png)

이제 유저가 자격 증명을 선택하면 authorizationController에서 didCompleteWithAuthrization을 호출한다.

만약 애플 로그인으로 진행하면 appleIDCredential으로 진행하게 된다.

만약 사용자가 암호 기반 계정을 선택하면 passwordCredential로 진행한다.

자격증명이 만약 없다면 마지막 메서드를 통해 error 호출한다.

이 자격증명에서 또한 Passkey credential로도 실행된다.

![](https://i.imgur.com/UJhpZxS.png)

이는 비밀번호를 대체하는 차세대 인증 기술인 패스키로

WWDC: Meet passkeys를 보면 된다!

이렇게 해서 중복계정을 생성하는 것을 방지할 수 있다.

### Demystify Apple ID credential

Apple ID 자격 증명에 대한 설명

애플 로그인을 하게 되면 받게 되는 것들

![](https://i.imgur.com/bbsi7ky.png)

User는 고유하고 안정적인 식별자다.
이걸로 시스템에서 사용자를 고유하게 식별할 수 있다.

필요한 경우에만 fullName을 요청해야 한다.

사용자와 소통하고 싶다면 email을 요청해야한다.
공유하는 방법은 2가지가 있다.
- Apple ID와 연결된 이메일을 공유하는 것
- hide my email 기능을 사용하는 것 -> 숨겨진 이메일로 만들어 준다.

어떤 옵션이든 이메일을 이전에 Apple에서 확인한것이므로 사용할 수 있다.

realUserStatus는 사용자가 실제일 가능성에 대해 신뢰도가 높은 지표다.
계정 증명, 하드웨어 수치 계산 등을 한다.

- Likely real -> 사용자가 실제로 보인다는 뜻
- Unknown -> 실제 사람인지 확인하지 못한 경우
- unsupporeted -> 시스템이 결정할 수 없다

이 3가지는 계정이 처음 생설될 때만 반환된다.

![](https://i.imgur.com/fwAptVH.png)

이후 로그인 시에는 반환되지 않는다.

그래서 시스템에서 유저 정보를 확인할 때까지 fullName, email 같은 속성을 캐시에 저장해두어야 한다.

identityToken
JSON 웹 토큰으로 사용자 정보를 포함하여 웹서버에 필요한 데이터 대부분을 포함한다.

3가지 요소로 존재한다

![](https://i.imgur.com/n2YhdV1.png)

- Header
- Payload
- Apple 서명(Signature)

변조 되지 않았다는 것을 확인하기 위해서는 Apple의 공개 키로 서명을 확인해야 한다.
이 키는 Apple ID 서버에서 가져온다.

그리고 identity가 맞는지 확인해야한다.

![](https://i.imgur.com/440g8w1.png)

iss -> 애플 사이트가 맞는지 확인
aud -> 번들 아이디 확인
exp -> 유효기간 체크
sub -> 사용자 식별자
email -> 유ㅓ저 이메일
realUserStatus -> 0는 unSupported, 1는 unknown, 2는 likely real 입니다.
nonce -> 생성하기 전에 생성된 것인지 확인하는 부분

AuthorizationCode는 수명이 짧은 일회용 토큰이다!

![](https://i.imgur.com/iAOB6IU.png)

refresh 토큰 대신 사용할 수 있다.

refresh 토큰  인증/토큰 앤드포인트에 post 요청을 보내야한다.

![](https://i.imgur.com/za1nhzN.jpg)

응답에서 refresh 토큰,access 토큰을 유사한 새 토큰으로 받을 수 있다.
만료된 access token 있으면 refresh 토큰을 통해 요청하여 얻을 수 있다.

만약 유저 관련 정보가 바뀌게 되면 토큰이 무효화될 수 있다.

### Handle session changes

자격 증명 상태를 처리하는 것을 다룬다.

![](https://i.imgur.com/QWwmAXM.jpg)

우리는 앱에서 Apple ID 사용을 멈추거나 기기에서 로그아웃 할 수 있다.

![](https://i.imgur.com/X50uauZ.jpg)

그래서 유저의 상태를 표현하는 코드가 존재한다.

이 코드를 앱실행시 하거나 상태를 확인하려는 시점에 호출하면 좋다.

만약 상태가 변하면!

![](https://i.imgur.com/RwqTJHm.png)

다른 유저가 접속한 것으로 보고 현재 사용자를 앱에서 로그아웃해야한다.

앱 서버가 서버가 있으면 서버간 알림을 통해 업데이트 여부를 받아야 한다.

받아야할 알림리스트가 있다.

![](https://i.imgur.com/FNRm93e.png)

알림 수신 시작하려면 Apple Developer에서 endpoint URL를 등록해야한다.

![](https://i.imgur.com/59ok5Zs.png)

모든 이벤트는 동일한 엔드포인트 URL에 도착한다.

이벤트는 Apple에서 서명한 JSON 웹 토큰으로 전송된다.

![](https://i.imgur.com/OeZcLyt.png)

만약 메일 전송이 비활성화 되면 type에서 disabled 이벤트를 받게 된다.

만약 유저가 앱에서 사용을 중지하면

consent-revoked 이벤트를 받게 된다

![](https://i.imgur.com/pXbRO9q.png)

이렇게 하면 모든 세션의 이벤트를 무효화해야한다!

유저가 Apple ID를 삭제하면 이 이벤트를 받게 된다

![](https://i.imgur.com/qOddkwY.png)

여기에서도 모든 세션의 이벤트 무효화 + 프로필 상태 업데이트를 바꾸어야한다.

계정 삭제

![](https://i.imgur.com/J6nHzER.png)

App Server로 보내고 해결하는 것은 개발자의 책임이 들어간다.

이것을 REST 요청을 통해 AppleID Server에 전송하고 리스폰스를 통해 삭제할 수 있다.

계정을 삭제하기 위해서는 refresh 토큰이나 access 토큰이 있어야 한다.

토큰이 없다면! 엔드포인트를 사용하여 생성할 수 있다.

![](https://i.imgur.com/JoAqjxd.png)

토큰 중 하나가 있으면 인증/취소 엔드포인트를 사용해서 할 수 있다.

token 삭제하고 싶은 것을 바디에서 입력해서 설정한 다음 삭제요청을 하면 된다.

요청하면 즉시 바로 무효화된다.

### Implement across platforms

웹에서도 애플 로그인을 사용할 수 있다.

![](https://i.imgur.com/2JP01KG.png)

사용성 개선을 위해 관련 앱을 그룹화하는 것이 좋다

그룹화하면 정보 동의를 한번만 하면 되서 편리하다!

웹에서 Apple Login관련 Services ID를 구성하는 방법!

![](https://i.imgur.com/M3Ly4aQ.png)

그 다음 Service에 대한 설명을 입력한다

![](https://i.imgur.com/0heYFtU.png)

그 옆에는 Service의 identifier를 입력해야한다.

아래에는 Apple로그인 으로 한다고 Configure한다

![](https://i.imgur.com/zEudKqy.png)

다음으로는 Primary 
App ID를 선택한다

![](https://i.imgur.com/3umlzoH.png)

그리고 이를 사용할 도메인을 입력한다

![](https://i.imgur.com/m5ckGkL.png)

마지막으로 redirection URL를 입력한다

이러면 웹사이트에서 지원하는 Service ID 구성완료!

JS 프레임워크 사용해서 구성!

![](https://i.imgur.com/cY6mqTX.jpg)

버튼 만들기!!

![](https://i.imgur.com/q0o4ikQ.png)

칼라를 바꿔서 배경색을 변경하면 그에 맞게 내부도 변한다.

data type에서 continue를 입력하면 내부에 글씨도 바꿀 수 있다.

![](https://i.imgur.com/VuEpEvd.png)

로고만 보이게 하는 법

![](https://i.imgur.com/Mvxl4AR.png)

REST API로 Apple login 만들면

![](https://i.imgur.com/gsTKb3d.jpg)

이렇게 만들 수 있다!!

이제 사용자 인증 차례!

![](https://i.imgur.com/lfRd3s2.png)

승인 요청을 해야한다.

아까 보였던 부분에 채워야할 것들

![](https://i.imgur.com/JhtQNPD.png)

usePopup 팝업창에서 사용하려고 할 때 사용!

![](https://i.imgur.com/OYtdk76.png)

사파리에서 이렇게 사용할때!

요청하고 나면 이제 승인 response 받는다

![](https://i.imgur.com/QdfkyoA.png)

DOM 이벤트를 받게 된다!

![](https://i.imgur.com/3jzQ5Be.png)

성공과 실패에 대해 대응해줄 수 있다.

승인 성공 시 받을 Response

![](https://i.imgur.com/duBq3QH.png)

REST API로 요청해서 받으려면..

![](https://i.imgur.com/0ZLGAiF.png)

### Wrap-up

![](https://i.imgur.com/aYm3THE.jpg)
