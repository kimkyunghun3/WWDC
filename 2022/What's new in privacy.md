# [2022] What's new in privacy

![](https://velog.velcdn.com/images/marisol/post/4845f4ee-41c9-4324-87b2-02c0ab1e3dc6/image.png)

애플은 개인정보를 엄청 중요시함! 훌륭한 기능을 만들어야 사람들의 관심을 끌 수 있지만, 개인 정보 보호 기능이 있어야 신뢰를 얻을 수 있음. 사람들이 어떤 데이터가 수집되고, 어떻게 사용되는지 이해하면 다른 앱 대신 여러분의 앱이나 서비스를 이용할 가능성이 높아질 것이라고 얘기하고 있다.

![](https://velog.velcdn.com/images/marisol/post/f13da6e4-879d-4b0d-ac1c-167b22c31297/image.png)

애플은 실행 가능한 패턴을 모은 privacy pillars를 이용해 개인 정보를 보호하고자 한다.
(privacy pillars는 개인 정보 보호를 구축하기 위한 핵심 구성 요소 정도로 이해하면 될 것 같다.)

1️⃣ 데이터 최소화
- 기능을 구축하는 데 필요한 데이터만 사용한다.

2️⃣ On-device processing
- 온디바이스 프로세싱(on-device processing)은 기기 자체에서 데이터를 처리하고 분석하는 것을 의미함. 이 방식은 사용자의 데이터를 외부로 전송하지 않기 때문에 개인 정보 보호 측면에서 매우 안전하다.

예를 들어, Siri를 사용하는 경우, 사용자의 음성 데이터는 기기 내에서 처리되고, 이후에 서버로 전송되는 것이 아니라 결과만 서버로 전송된다. 또한 Face ID는 디바이스 자체에서 얼굴 인식을 처리하고 사용자의 얼굴 데이터를 저장한다.

3️⃣ 투명성과 통제
- 민감한 정보가 기기에서 전송되면, 사용자에게 어떤 정보가 보내졌고 어떻게 사용되는지 알려주고, 그에 대한 통제 권한을 준다.

4️⃣ 보안 및 보호
- 디바이스가 켜져있든 꺼져있든 전송중이거나 보관중인 민감한 정보를 보호한다.

## 1. Platform updates

![](https://velog.velcdn.com/images/marisol/post/073856dd-4dbf-43e9-89a1-e389392f041a/image.png)

이 세션에서는
1️⃣ 개인정보 보호와 관련된 플랫폼 변화와,
2️⃣ 적용해야할 새로 강화된 보안기능, 
3️⃣ 그리고 개인 정보 보호에 영향을 미치는 중요한 새 기능에 대해 소개한다.

### 1) Device name entitlement

![](https://velog.velcdn.com/images/marisol/post/dce1e2f9-c053-47d0-ab6d-a1181ca74f35/image.png)

우선, 앱에 영향을 끼칠 플랫폼의 새로운 변화에 대해 말하자면, iOS 16과 macOS Ventura에는 몇가지 중요한 변화가 있었다. 새로운 Device name entitlement로 기기 이름에 대한 접근을 제한하는 것이다. 그리고 위치 정보를 사용하고 있음을 나타내는 location indicator를 제어 센터에서 확인할 수 있다. 
Gatekeeper의 개선으로 더 많은 곳에서 공증된 앱을 확인할 수 있다.
또한 로그인시 Mac 앱을 실행하면 추가 정보가 사용자에게 통보된다.
마지막으로, Pasteboard 접근에 권한이 필요하도록 변경되었다.

![](https://velog.velcdn.com/images/marisol/post/8e91d3bd-4955-4f16-8eeb-06687de135ea/image.png)

먼저 디바이스 이름 접근의 변경사항부터 살펴보면,
iOS 16 이전에는 UIDevice API가 앱에서 사용자가 지정한 디바이스 이름에 접근하는 걸 허용했었지만, 

![](https://velog.velcdn.com/images/marisol/post/52ba0454-7910-4b2f-a5cb-7970ed638729/image.png)

이제는 UI Device Name API를 통해 디바이스의 모델명에만 접근할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/60bc10a1-03bb-45c4-a2dd-832f168928bb/image.png)

만약 다중 디바이스를 지원하는 앱이 있다면, (회사용 iPhone, 가정용 iPhone 이렇게)
디바이스 이름을 통해 각각의 디바이스를 식별해야 하기 때문에 디바이스 이름에 대한 접근 권한을 요청할 수 있다. 이 요청에 사용자가 접근 권한을 허용한다면, "회사용 iPhone"과 "가정용 iPhone" 이라는 이름을 통해 각각의 디바이스를 구분할 수 있게 됨. (하지만 이 접근권한은 클라우드 호스팅 서비스 제공 업체를 제외한 제 3자와 공유할 수 없다고 한다.)

### 2) Location attribution

![](https://velog.velcdn.com/images/marisol/post/6c7a6857-7629-4c91-bcc3-88b4c26280d6/image.png)

![](https://velog.velcdn.com/images/marisol/post/69e075d5-03bc-4440-84d4-36bac5882f7a/image.png)

앱에서 위치 기능을 켜면 iOS는 상태 표시줄에 화살표로 표시한다.

![](https://velog.velcdn.com/images/marisol/post/d2a5055f-84d9-4fd4-9432-711863d76573/image.png)

iOS 16에서는 쓸어내려서 제어 센터를 열면 어떤 앱이 위치를 사용 중인지를 나타낸다. 필요할 때만 위치를 사용해서 사용자들이 놀라는 일이 없도록 해야 한다.

### 3) Gatekeeper improvements

![](https://velog.velcdn.com/images/marisol/post/e5bb8bdd-3639-49cc-8315-2ec057c1c4a5/image.png)

Gatekeeper는 새로 다운받은 앱에 결점이 있는지 확인한다. macOS Ventura에서는 Gatekeeper가 공증되지 않은 앱은 물론이고, 공증된 앱까지 모두 검사한다.

(```Gatekeeper```는 Mac 운영체제에서 실행 가능한 애플리케이션을 검증하고 보안상 문제가 있을 가능성이 있는 애플리케이션을 차단하는 보안 프로그램)

![](https://velog.velcdn.com/images/marisol/post/7c6ebb9c-a864-4928-807a-53a4d096cea4/image.png)

우선, 앱에는 제대로된 서명이 있어야 한다.
만약 공증된 앱의 서명이 더이상 유효하지 않다면, 첫 launch에서 Gatekeeper에 의해 차단될 것이다.
모든 실행 파일과 번들에 서명해야 하며, 앱을 변경할 때 해당 서명이 유효한지 확인해야 한다.
Gatekeeper는 결점 검사와 더불어, 앱이 특정 방식으로 수정되는 걸 방지한다.

![](https://velog.velcdn.com/images/marisol/post/dcef18a1-e81a-45f0-b740-ee161ae9981a/image.png)

일반적으로 앱을 수정하는 건, 업데이트를 할 때인데, 같은 개발자 계정이나 팀에서 유효한 서명을 했다면, 서로 계속해서 업데이트를 할 수 있다. (바로 작업 가능!)

![](https://velog.velcdn.com/images/marisol/post/2c9b466a-0bcd-44f9-8620-42ebc592201d/image.png)

다른 개발팀이 내 앱을 업데이트 하는 걸 허용하거나, 혹은 나만 업데이트하도록 제한하고 싶다면 Info.plist를 업데이트하면 된다. 여기서는 Unrelated App은 info.plist의 변경만으로 Pal About의 업데이트를 허용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/cff579d2-6a0d-406a-a9c9-fc71e4541917/image.png)

NSUpdateSecurityPolicy만 추가하면 된다. NSUpdateSecurityPolicy에서는 AllowProcesses를 추가하고, 딕셔너리로 팀 식별자를 서명 식별자 배열에 매핑한다.

여기서는 Pal About의 팀 식별자가 서명한 com.example.pal.about으로 된 모든 프로세스를 허용해서 내 앱을 업데이트할 수 있도록 한다.

![](https://velog.velcdn.com/images/marisol/post/acafe8f1-a3cb-4438-8658-8d61820f9b1f/image.png)

같은 개발팀에서 서명하지 않은 앱에 의해 앱이 수정되거나, NSUpdateSecurityPolicy에 허용되지 않은 경우, macOS가 수정을 차단하고, 다른 앱을 수정하려고 하는 것을 사용자에게 알린다. 

![](https://velog.velcdn.com/images/marisol/post/b3ed60d0-ada4-443e-96a9-bd532cc20105/image.png)

알림을 누르면 시스템 설정으로 연결돼서 앱이 다른 앱을 업데이트하고 수정할 수 있도록 설정할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/a840a020-50eb-4414-ad32-6c37ceb574c9/image.png)

앱의 실행 파일과 번들에 서명해야 하며, 업데이트 후에도 그 서명이 유효하도록 해야 한다.
NSUpdateSecurityPolicy를 적용하고,
앱이 정책 범위 밖에서 다른 앱을 수정하려고 할 경우, 사용자가 이를 허용해야 한다.

### 4) Launching Mac apps at login

![](https://velog.velcdn.com/images/marisol/post/81d0f3cd-0460-4f45-87c0-6839bd26a790/image.png)

다음으로는 Mac 로그인시 앱을 실행하는 것에 관련된 내용이다.

![](https://velog.velcdn.com/images/marisol/post/99eb2350-7eac-46be-95f1-ad093e738ec7/image.png)

macOS Monterey와 이전 버전에서는 누군가 맥에 로그인할 때, 앱은 launch agent나 daemon을 사용하여 로그인 시 자동으로 실행될 수 있었다. 
(Launch agent / daemon : 시스템의 백그라운드에서 실행되며, 특정 이벤트나 조건에 의해 앱을 자동으로 실행하도록 하는 프로그램)
이 기능을 사용하면 맥 사용자가 로그인할 때 필요한 앱이 자동으로 실행되도록 설정할 수 있었다. 예를 들어, 메시지 앱이 로그인시 자동으로 실행되도록 설정해 두면, 매번 로그인 할 때마다 메시지 앱을 수동으로 실행하지 않아도 되는 것.

하지만 가끔 사용자가 Mac에 로그인할 때, 관련되지 않은 앱이 열려 방해가 될 수도 있고,
앱이나 다른 소프트웨어는 센서나 위치 데이터에 접근할 수도 있는데 사람들은 그 사실을 잘 모를 수 있다. (실행 중이어도 보이지 않을 수 있기 때문)

![](https://velog.velcdn.com/images/marisol/post/f440a961-7105-425c-83ed-450c38b392ca/image.png)
macOS Ventura에서는 단일 API를 사용해 앱을 실행하고 로그인시 agent나 daemon을 실행할 수 있다. 기본적으로 로그인했을 때 실행되고, 사용자들에게 알림이 표시될 것이다. 앱에 높은 권한의 daemon이 필요한 경우, 실행을 위해서는 관리자의 승인이 필요하다.

![](https://velog.velcdn.com/images/marisol/post/d4572b04-d1f4-412d-a87c-1cb408a5bec0/image.png)

알림을 클릭하면 시스템 설정의 Login 설정 창이 열린다. 사용자가 시스템에서 실행되는 앱을 관리할 수 있다.

> _**근데 Add Login Items랑 Login Items Added by Apps가 뭐가 다를까?**_
"Add Login Items"는 사용자가 수동으로 로그인 시 자동으로 실행되는 앱을 추가하는 기능이다. 사용자가 직접 로그인 항목을 추가하거나 삭제할 수 있다.
반면에 "Login Items added by apps"는 앱이 자동으로 사용자의 로그인 항목에 앱 자체를 추가할 수 있도록 허용하는 기능이다. 예를 들어, Dropbox 앱이 설치되어 있으면 Dropbox은 자동으로 사용자의 로그인 항목에 추가되어 Dropbox이 로그인 시 자동으로 실행된다. 이렇게 앱이 로그인 항목에 자동으로 추가될 수 있도록 허용하는 것은 일부 사용자가 편리하게 사용할 수 있지만, 다른 사용자들은 앱이 자동으로 실행되지 않도록 설정하려고 할 수도 있다. - by ChatGPT

![](https://velog.velcdn.com/images/marisol/post/1a35b1f4-2db6-48ce-987e-15cc0942900d/image.png)

이 새로운 API를 사용하려면, Service Management Framework를 사용할 수 있다.
이 프레임워크는 앱 번들에 이미 존재하는 에이전트와 데몬을 사용하여 앱이 자동으로 실행되도록 구성할 수 있다.

### 5) Pasteboard access

![](https://velog.velcdn.com/images/marisol/post/cdf2e9ce-8083-440e-8e05-2e2742804484/image.png)

다음은 pasteboard 접근이다.

![](https://velog.velcdn.com/images/marisol/post/ff657494-242c-4b74-88b7-bf442371feed/image.png)

이전에는 사용자가 편집 옵션에서 붙여 넣기를 누르지 않았는데 pasteboard에 접근하는 경우, 투명한 알림창이 떴었다.

![](https://velog.velcdn.com/images/marisol/post/b55550c0-e93e-45d0-8ff4-168bb45b82a4/image.png)

iOS 16에서 시스템은 다른 앱이 작성한 pasteboard 항목에 대한 모든 접근 의도를 확인한다.
앱이 pasteboard 항목 값에 계속 접근하려는 경우에 UIPasteboard API를 사용하면 시스템은 모달 프롬프트로 나타난다.

근데 약간 귀찮을 수도? 그래서 이 프롬프트가 나타나지 않게 하는 방법이 3가지가 있다.

![](https://velog.velcdn.com/images/marisol/post/0188c858-c52d-464d-bb99-44c61cc085f2/image.png)

첫번째는 편집 메뉴를 이용하는 것이고,
(앱 내에서 copy나 paste 등의 작업을 할 때 편집 메뉴를 이용하면 프롬프트가 나타나지 않도록 할 수 있음)
두번째는 단축키를 이용하는 것이다.
(단축키를 지정하고, 해당 단축키를 사용자가 누를 때 UIPasteboard API를 호출하는 방식으로 구현)
세번째는 Features to adopt 부분에서 다룰 예정이다. (앱에서 pasteboard 항목에 대한 접근 권한을 사용자가 직접 허용하도록 하는 방법)

## 2. Features to adopt

![](https://velog.velcdn.com/images/marisol/post/8fd17626-a378-4a4f-8951-995bdbf46b24/image.png)

두번째 파트는 개인정보 보호를 강화하는 새로운 기술들이며, 앱의 개인 정보 보호 시스템을 쉽게 구축하는데 도움이 된다.

### 1) UIKit paste controls

![](https://velog.velcdn.com/images/marisol/post/9bd6002e-1f1e-469d-bfd0-f7900a146654/image.png)

제일 먼저 방금 얘기가 나왔던 UIKit Paste Controls이다.

![](https://velog.velcdn.com/images/marisol/post/96da3558-e9d5-475a-8cbd-db1e22499f37/image.png)

UIKit Paste Controls를 앱에 추가하면, 사람들이 직관적인 버튼을 눌러서 pasteboard에 접근할 수 있다.
UIKit Paste Controls는 편집 메뉴는 물론이고, 단축키나 시스템 프롬프트 없이도 붙여 넣을 수 있다. 

![](https://velog.velcdn.com/images/marisol/post/45277478-9bb0-4497-baa7-be0c460b1182/image.png)

이 버튼을 앱의 UI에 맞게 변경할 수도 있다. corner radius를 주던지, 글자 색이나 배경색을 변경할 수도 있다.

### 2) Media device discovery

![](https://velog.velcdn.com/images/marisol/post/60bebc49-d8b0-40ae-ace2-0b431a3e0975/image.png)

데이터 접근을 최소화하면서 원활한 이용을 위해 만든 툴 중 하나가 Media device discovery이다.

![](https://velog.velcdn.com/images/marisol/post/8fb73a4c-d333-4b5d-8470-ade0aaeec624/image.png)

요즘 앱은 사용자들의 다양한 통신 장비를 통해 광범위한 스트리밍 프로토콜을 사용하고 있다.
(스트리밍 프로토콜: 오디오, 비디오, 게임 등 다양한 미디어 콘텐츠를 인터넷을 통해 전송하는 방식) 

![](https://velog.velcdn.com/images/marisol/post/72ae04ef-22da-4944-916e-8664e1018ebc/image.png)

전에는 이런 프로토콜을 사용해 미디어를 스트리밍 하려면, 앱이 로컬 네트워크에 접근 권한이 필요했고, 종종 Bluetooth도 필요했다.

![](https://velog.velcdn.com/images/marisol/post/c3abbde3-71ca-4743-b5b0-996c43221330/image.png)

선택된 기기들을 관리하려면, 모든 기기에 대한 지식이 필요해 로컬 네트워크 접근 권한 등이 필요했던 것이다. 하지만 이것은 필요한 것보다 더 많은 정보에 접근 권한을 줘서 흔적을 남길 위험성이 있다.

![](https://velog.velcdn.com/images/marisol/post/bb27032c-18c1-4d01-affd-3810ac9d2be8/image.png)

Media device discovery는 기기의 앱에서 선택한 미디어를 네트워크나 Bluetooth가 연결됐다는 프롬프트 없이 재생할 수 있다. 스트리밍 기기는 AirPlay로 같은 디바이스에 표시되고, 

![](https://velog.velcdn.com/images/marisol/post/e9174570-48af-4a66-b245-642ea197f7fd/image.png)

앱에서는 스트리밍하는 디바이스를 볼 수 있다. 이 기능은 Device Discovery 확장 프로그램 덕분에 가능함.

![](https://velog.velcdn.com/images/marisol/post/44353865-83d4-480c-9c2e-f314919b77d0/image.png)

이 확장 프로그램에서는 로컬 네트워크와 Bluetooth 기기를 검색할 수 있고, 앱과 별도로 샌드박스에서 실행되므로 검색 결과를 다시 전송할 수 없다. 앱이 로컬 네트워크나 Bluetooth에 접근하는데에 광범위한 권한이 필요하지 않다는 것. (앱이 전체 네트워크는 볼 수 없기 때문)

![](https://velog.velcdn.com/images/marisol/post/879aeb56-0a2e-4088-b2bd-040cea60ae01/image.png)

그대신 확장 프로그램은, 입력된 정보를 Device Discovery Extension Framework로만 보낼 수 있다. Device Discovery Extension Framework는 발견된 기기 목록을 보여주고, 

![](https://velog.velcdn.com/images/marisol/post/5dccc871-caa7-41bb-b9f6-4aa72e0edac6/image.png)

사용자가 선택을 한 후에는 시스템에서 선택된 기기와 통신을 가능하게 한다. 다른 권한은 필요하지 않다.

![](https://velog.velcdn.com/images/marisol/post/1b725003-2d1e-43f9-b54f-69f440634957/image.png)

프로토콜 공급자(Youtube, Netflix, Apple Music, Spotify..)는 Device Discovery Extension으로 앱 확장 프로그램을 생성해야 한다.
이로써 앱은 스트리밍에 필요한 데이터에 정확히 접근할 수 있고, 검색은 간단하게 프롬프트 없이 이루어지며, 사람들은 훌륭한 개인 정보 보호망을 얻게 된다.

### 3) PHPicker expansion

![](https://velog.velcdn.com/images/marisol/post/dfe65ae4-12e5-497c-a4a8-79185797ff79/image.png)

Media device discovery에서 프롬프트 없이 필요한 장치에만 접근할 수 있는 권한을 제공하듯이,
PHPicker API가 권한을 제공해서 프롬프트 없이 필요한 사진에만 접근할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/90e6b776-4006-4ef9-878c-5a2c76c752a3/image.png)

앱에서는 더이상 모든 사진에 대한 액세스 권한을 요청하지 않아도 된다. 이는 사용자 경험을 향상시키고, 개인정보 보호를 더욱 강화할 수 있게 해준다.

### 4) Private Access Tokens

![](https://velog.velcdn.com/images/marisol/post/977b5de3-7d84-4a65-b48b-66f587e947d3/image.png)

Private Access Token은 웹사이트 또는 API 개발자가 합법적인 기기를 인식할 수 있게끔 도와준다.
개발자는 웹사이트 또는 API 사용자에게 숨겨진 토큰(Private Acceess Tokens)을 제공하는데, 이 토큰은 웹사이트 또는 API가 해당 사용자를 추적할 수 없게끔 보호합니다.

![](https://velog.velcdn.com/images/marisol/post/746f018f-937f-41e7-ad02-2ba7b00c81c4/image.png)

이 토큰을 사용하여 Apple은 사용자의 기기를 추적하지 않고, 토큰을 보내는 서버에서도 사용자의 개인정보를 남기지 않는다.

### 5) Passkeys

![](https://velog.velcdn.com/images/marisol/post/31bf1546-ea0e-4ca7-a21b-bde97a4e3451/image.png)

암호는 계정 인증에서 가장 중요하다. 개인 데이터를 안전하게 보관하는 데도 중요하지만,
기억하는게 어렵기 때문에 사람들이 암호를 단순하게 만들거나, 여러 사이트에서 똑같은 걸 사용하기도 한다. 그럼 해킹당하기 쉬움.

![](https://velog.velcdn.com/images/marisol/post/e34ceee0-e331-4d40-b011-0ebf27ae9ee6/image.png)

Passkey는 보다 강력한 인증 방법을 구현해서 금방 익숙해질 수 있는 UI 스타일과 암호를 자동으로 입력할 수 있는 생체 인식 방법을 사용한다.

![](https://velog.velcdn.com/images/marisol/post/7c0818ba-033e-4c4a-ba8f-a968224d6e46/image.png)

Passkey는 공개 키 암호 방식을 기반으로 한다. 완전히 공개되어 있는 서버이기 때문에 서버가 유출된다고 해도 상관없다. Passkey는 해당 웹사이트와 연결되어 있기 때문에 해킹될 일이 없다.

## 3. Safety Check

![](https://velog.velcdn.com/images/marisol/post/049ec4e5-483e-4a34-9420-9adc15780aab/image.png)

Safety Check는 iOS 16의 새로운 개인 정보 보호 도구이다. 
가정 내에서나 가까운 사람에게 폭력을 당하는 사람들이 다른 사람에게 허락했을지도 모를 접근 권한 검토 및 재설정을 위해 고안되었다.

![](https://velog.velcdn.com/images/marisol/post/416fb192-653d-44a9-8cc4-67f9730669ca/image.png)

다음과 같은 면에서 도움이 되는데,
FindMy에서 위치 공유를 해제해 사람들과 데이터 공유를 중지하고, 사진이나 메모, 캘린더 공유를 막는 것이다.

앱과 데이터 공유를 중지하기 위해 시스템 개인 정보 보호 권한을 재설정하는데, 이는 모든 앱에 적용된다.
다른 iCloud 기기의 FaceTime과 iMessage에서 로그아웃 되도록 도와주고, 메시지와 전화가 당사자가 가진 기기로만 전송되도록 한다. 

디바이스와 iCloud 계정의 암호도 변경할 수 있다. 신변에 위협을 받는 사람들을 도와 그들의 데이터에 대한 접근을 제어한다.

![](https://velog.velcdn.com/images/marisol/post/f4951c47-5ac2-4a3a-8a28-7c0450f6f87b/image.png)

Safety Check에는 두 가지 사용법이 있다. 
첫 번째는 위험한 상황에 대비한 Emergency Reset이다. 사용자나 앱에 대한 접근 권한을 바로 재설정할 때 필요하다.

![](https://velog.velcdn.com/images/marisol/post/bb17e82b-cf9b-464c-a52c-331e079166cc/image.png)

두 번째는 Manage Sharing and Access이다. Safety Check의 각 기능을 보다 세밀하게 설정할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/4bdd4249-d87b-4783-8a66-60e5f02d7d12/image.png)

Emergency Reset은 모든 사용자와 앱에 대한 신속한 조치를 취할 수 있고, 다른 기기에서 FaceTime과 iMessage에 대한 접근을 비활성화 한다. 그 다음 기기의 iCloud 계정을 당사자 기기에 안전하게 보호하고, 신뢰할 수 있는 기기와 비상 연락처를 검토한다.

![](https://velog.velcdn.com/images/marisol/post/7dc7732e-bf00-41c3-bc7c-bcdc3420faab/image.png)

Manage Sharing and Access는 공유 중인 정보의 이름이나 형식에 따라 구분할 수 있다. 특정 사람과 공유 중인 내용을 확인하고 제어할 수 있다. 민감한 권한을 가진 앱을 찾을 수도 있다. (누군가가 디바이스에 접근해 설치해 놓은 앱 같은 것들)

![](https://velog.velcdn.com/images/marisol/post/980cec43-9d25-4872-a739-4b55fc5c0d19/image.png)

그리고 Safety Check에서는 빠른 종료(Quick Exit)가 가능하다. 그들이 뭘하고 있는지 누군가 볼까봐 걱정된다면 사용할 수 있다. Quick Exit을 누르면 홈 화면으로 돌아가고, 다음에 다시 설정에 들어가면 Safety Check 화면이 아니라 기본 설정 페이지가 나타난다.

![](https://velog.velcdn.com/images/marisol/post/45f77569-927d-438e-90fb-bffbc203993b/image.png)

개인 정보 보호는 단지 데이터를 공유할 때 결정하는게 아니라,
언제든지 그 결정을 이해하고 변경할 수 있게 하는 것.
