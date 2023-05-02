## Cryptography And Your App
![](https://velog.velcdn.com/images/marisol/post/4a6840b6-6c93-419f-86d4-52bcb344feae/image.png)

개발자 & 유저로서, 우리는 사용자의 정보를 보호하는것이 매우 중요하다는 것을 알고 있다.

![](https://velog.velcdn.com/images/marisol/post/18e6fec2-7a67-490a-beb8-7358a812026d/image.png)

다양한 종류의 정보가 있지만, 모두 중요하고 보호해야 한다.
전화번호나 이메일 주소 같은 프로필 정보도 있을 수 있고, 비즈니스 정보도 있을 수 있다.

이러한 모든 정보에 대해 보안을 유지하는 것이 매우 중요하며, 이를 위한 좋은 도구가 필요하다.
Apple에서는 보안과 개인 정보 보호가 핵심 가치이며, 모든 기능에 개인 정보 보호를 포함하고 있다.
따라서 암호화가 매우 중요하며, 디바이스 무결성(device integrity)을 통해 플랫폼에서 실행되는 소프트웨어의 무결성을 검증하여 소프트웨어가 손상되지 않았는지, 보안 속성이 변경되지 않았는지를 확인한다.

![](https://velog.velcdn.com/images/marisol/post/1b1d14b4-4136-4d17-abef-b20107360fd9/image.png)

그리고 데이터의 안전한 보호를 위해서도 암호화를 사용한다. 
Apple의 Messages, Safari, iCloud 등 다양한 애플리케이션에서 매우 개인적인 순간을 기록하고 공유하는데, 이를 보호하기 위해 암호화를 사용한다. 즉, 사용자의 개인정보 보호를 위해서도 암호화가 매우 중요한 역할을 한다.

Apple은 10억 대가 넘는 기기에서 암호화를 수행하고 있으며, 이는 매우 광범위한 규모로 암호화를 수행하고 있음을 의미한다.

그리고 그에 따른 위험도도 매우 높기 때문에, 매우 높은 안정성이 요구된다. 좋은 사용자 경험을 위해서는 높은 성능이 필수이며, 배터리 수명도 중요하고, 보안은 여기에서 중요한 기반 요소이다.

![](https://velog.velcdn.com/images/marisol/post/339abe9d-e460-401f-8ee1-0fbaa5903630/image.png)

암호화는 기본적으로 몇 가지 속성을 제공하는 도구이다. 

예를 들어 설명하자면, 
발신자를 알 수 있는 인증, 
메시지를 복호화할 수 있는 권한이 있는 사람이 단 한 명뿐이라는 암호화, 
그리고 메시지가 전송되기 전에 누구도 내용을 수정하지 않았다는 것을 보장하는 무결성 등이 있다.

더 넓은 보안 문제를 해결하기 위해, 우리는 이러한 암호화 기능을 ```암호화 프로토콜```로 결합해야한다. 

"암호화를 직접 구현하지 말라"는 말이 있는데, 암호화에서 많은 것들이 잘못될 수 있기 때문이다.

![](https://velog.velcdn.com/images/marisol/post/88daa3d7-7382-4507-a3d4-32979ec10a30/image.png)

제대로된 암호화 도구를 선택하지 않으면 보안을 달성할 수 없거나, 
제대로된 도구를 선택했어도 제대로 사용하지 않으면 보안이 취약해질 수 있다. 
또한, 구현 단계에서 비밀번호나 키 같은 중요 정보가 노출될 수 있다. 
이는 시간을 측정하는 공격이나 패딩 오라클 공격과 같은 공격으로 이어질 수 있다.

그래서 암호화는 굉장히 어려운 분야이다. 많은 사례에서 암호화 프로토콜이 실패한 경우가 있는데, 
이러한 취약점을 발견하는 데는 시간이 꽤 걸릴 수도 있고, 몇 년이 지나기 전까지는 문제를 발견하지 못할 수도 있다.

그래서 보안 리스크가 매우 높기 때문에, 암호화를 올바르게 구현하려면 많은 노력이 필요하다.

![](https://velog.velcdn.com/images/marisol/post/41ac315e-bc24-46aa-8bda-0ac3f48e74ae/image.png)

강력한 프로토콜을 설계하려면 암호화 기술자가 되기 위해 많은 시간, 기술 및 전문 지식이 필요하다.

그리고 새로운 공격을 모니터링하고 자신의 코드에 적용할 수 있는 것을 찾아 대응해야하며, 종속성 문제와 해석 가능성 시나리오를 관리해야 한다.

> 해석 가능성 시나리오(interoperability scenarios)는 서로 다른 플랫폼, 시스템, 앱 등에서 암호화 프로토콜을 상호 운용(interoperate)할 수 있는지에 대한 시나리오를 말합니다. 예를 들어, 애플 디바이스에서 생성된 암호화된 데이터가 안드로이드 디바이스에서도 정상적으로 해독될 수 있는지, 또는 서로 다른 클라우드 서비스에서 암호화된 데이터를 안전하게 공유할 수 있는지 등이 해당됩니다. 이러한 상호 운용성은 다양한 디바이스 및 애플리케이션 간에 안전하고 원활한 데이터 교환을 가능하게 해줍니다. - by chatGPT

![](https://velog.velcdn.com/images/marisol/post/a73b2f53-4d2c-47e7-8752-786d2b57f97a/image.png)

이 모든 것은 많은 작업을 필요로 하며, 시간과 에너지를 빼앗아 다음 멋진 아이디어나 새로운 기능을 생각해내는 데 지장을 줄 수 있다. 
따라서, 네이티브 기능을 활용하면 이러한 어려움을 해결할 수 있으며, 플랫폼에서 지원하는 하드웨어 기능을 사용하면 더 나은 보안 수준을 달성할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/90d483a5-5a04-4413-974d-c6d4529591d1/image.png)

이제 많은 사람들에게 익숙할 매우 일반적인 암호화 시나리오들을 살펴보려고 한다.

1) 먼저 디바이스의 데이터를 보호하는 방법부터 알아보고, (보안 스토리지와 암호화 기술)
2) credential (자격증명)과 key 보호를 살펴보고, (키 체인 또는 기타 안전한 보관 장치)
3) 디바이스와 사용자 간의 데이터를 공유하는 것을 알아보고, (안전한 통신 프로토콜과 다중 인증 기능)
4) 네트워크 연결을 보호하고, (전송 계층 보안(TLS) 및 안전한 소켓)
5) 마지막으로 인증서를 사용하여 외부 서비스나 기기와의 통신의 진실성과 무결성을 보장하는 것을 알아보려고 한다.

![](https://velog.velcdn.com/images/marisol/post/72b44716-8cd7-4ec3-8990-ef3ac0f32cbb/image.png)

첫번째 시나리오인 디바이스 내 데이터 보호 방법에 대해 더 자세히 설명하고 있다.

먼저, 발표자는 다른 여러 웹사이트에서 코드를 긁어오는 것이 아니라, 데이터 보호를 사용하는 것이 가장 좋은 방법이라고 말하고 있다. (당연한 얘기..)

이는 기기 내의 데이터를 보호하기 위해 패스코드의 강력한 보안 기술과 Secure Enclave의 하드웨어 기술을 결합하여 키를 안전하게 보호하고, 암호화된 데이터가 패스코드를 무차별 대입 공격(brute-force)으로 해독할 수 없도록 해준다고 한다.

또한, 데이터 보호는 포스트 컴프로마이즈 복구(post compromise recovery)를 제공한다. 
이는 악의적인 공격자가 디바이스의 키를 획득하더라도 사용자가 패스코드를 변경하면 그 이후에 암호화된 모든 데이터는 이전 키에서 복구될 수 없도록 보장한다.

그리고 앱에서 데이터 보호 속성을 설정할 수 있다. 기기 부팅 이후 처음으로 사용자가 폰을 잠금 해제할 때까지 데이터를 보호할 수 있도록 설정하는 것으로부터 시작하여, 다양한 수준의 보안 속성을 선택할 수 있다.

예를 들어, 완전한 파일 보호를 할 수 있는데(complete file protection),
사용자가 폰의 잠금을 해제한 후에만 데이터를 사용할 수 있음을 의미한다. 사용자가 폰을 다시 잠그고 주머니나 가방에 다시 넣으면 데이터가 다시 보안 상태가 된다. 

![](https://velog.velcdn.com/images/marisol/post/c53ef393-4e64-4062-b974-763126260400/image.png)

사용하기도 매우 쉬운데, option에 .completeFileProtection을 설정하면 된다.

위 코드는 파일을 완전한 파일 보호 옵션으로 암호화하는 방법을 보여준다.

- ```fileURL```: 암호화하려는 파일의 URL
- ```data```: 암호화하려는 데이터를 포함하는 Data 객체
- ```write(to:options:)```: Data 객체를 파일에 쓰는 데 사용됨 
- ```.completeFileProtection``` 옵션: 데이터 보호를 위해 전체 파일 보호 기능을 활성화
(완전한 파일 보호 옵션 설정하면 -> 액세스 권한을 가진 앱이나 사용자에게만 복호화 및 액세스 권한이 부여되고, 해당 파일의 백업 및 복원 시에도 추가적인 보안이 제공된다고 함)

![](https://velog.velcdn.com/images/marisol/post/08102fae-bc80-495e-a90f-428061344b24/image.png)

파일 외에도 보호해야하는 중요한 정보가 당연히 있을 건데,
서비스 또는 암호화 키에서 인증하는 데 사용되는 ```인증 토큰```과 같은 자격 증명이 필요할 수도 있다.

이런 경우에, UserDefaults를 사용하면 안된다. 대신 ```Keychain``` 사용해야함.

SecItem API의 Keychain을 사용하면 로컬 Keychain에서와 마찬가지로 항목을 작성할 수 있다.
즉, 해당 항목은 해당 디바이스 또는 iCloud Keychain에 유지되며, 사용자 계정에서 모든 디바이스에서 동기화된다.

이를 위해서는 이중 인증이 활성화된 사용자 계정이 필요하다. 
Keychain에는 앞서 말한 파일과 매우 유사한 보호 기능이 있으며, 추가적인 기능도 제공된다.

(이중인증 : 암호 외에도 추가 인증 단계를 요구하여 보안을 높이는 방식. 예를 들어, 암호 입력 후에 사용자에게 문자 메시지나 인증 앱을 통한 코드 입력을 요청하는 것.)

암호화 키가 사용 가능한 경우 뿐만 아니라, 사용자가 언제 인증했는지와 어떻게 인증했는지 알아야 할 때가 있는데, 
이를 위해 LocalAuthentication이 제공된다.

LocalAuthentication을 사용하면, 작업을 실행할 때 조건을 설정할 수 있도록 해준다.
예를 들어 사용자가 FaceID와 같은 생체 인식으로 인증해야 하는 것을 요구할 수 있다. 엄청 편리함!

![](https://velog.velcdn.com/images/marisol/post/5d879ad8-a1a8-4d11-82db-4377008a8104/image.png)

macOS에서는 디바이스 인증 속성을 사용하여 사용자가 비밀번호 또는 Touch ID를 지원하는 생체 인식으로 인증하는 2가지 방법을 제공한다.
올해는 앱에서 어떠한 변경 없이 Apple Watch와 더블 클릭으로 인증할 수 있도록 지원한다. 이렇게 하면 비밀번호 사용을 줄이고, 사용자 경험을 개선할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/fcf2dd9d-87aa-4597-aeea-5e17aa9ea034/image.png)

이런 새로운 기능을 추가함으로써, 2개의 새로운 인증 정책을 추가했다.
1) 생체 인식 및 Apple Watch로 인증을 줄이는 것
2) Watch로만 인증하는 것
(Watch만 사용해서 인증하는 경우, 맥북을 사용하던 중 맥북에서 로그인을 시도하면 인증 요청이 바로 Apple Watch로 전달되어 Apple Watch에서 더블 클릭만 하면 맥북에 로그인할 수 있음. 이 경우, Apple Watch가 근처에 있어야하며, Apple Watch와 맥북이 서로 페어링되어 있어야 한다. 이를 통해, 비밀번호 없이 Apple Watch만으로 맥북 로그인이 가능 -> 사용자 경험 향상)

![](https://velog.velcdn.com/images/marisol/post/602eacc3-732e-48af-a0de-dfd5195c09d4/image.png)

지금까지 Keychain에 대해 언급했는데,
데이터 동기화에 대해서도 훌륭한 해결책이 있다. -> ⭐️ CloudKit

CloudKit은 개인 CloudKit 데이터베이스에서 asset을 암호화하여 사용자가 앱에 로그인하지 않아도 모든 디바이스에서 데이터를 사용할 수 있다. 
그리고 모든 사용자에게 공유할 때도 사용할 수 있다.

(예를 들어, iPhone에서 앱에 로그인하고 일기를 작성하면 CloudKit은 iPhone의 개인 CloudKit 데이터베이스에서 해당 자산(일기)을 암호화하여 저장한다. 그런 다음, 사용자가 iPad에서 앱에 로그인하면 CloudKit은 해당 사용자의 iCloud 계정에 암호화된 자산(일기)이 저장된 것을 확인하고 해당 자산(asset)을 iPad의 개인 CloudKit 데이터베이스로 동기화한다. 그 결과, 사용자는 iPad에서도 앱에 로그인하지 않아도 저장한 일기를 볼 수 있다.)

![](https://velog.velcdn.com/images/marisol/post/4ab94b46-d98e-43a9-952c-32254e932344/image.png)

작동 방식은 Apple이 iCloud ID와 데이터 제어를 관리하는 것이다. 
위 예제에서는 파일에서 asset을 만든다.
이 asset으로 record라는 기록을 만들었다.
그런 다음 이 record를 Private CloudKit 데이터베이스에 업로드한다. 그럼 끝!

![](https://velog.velcdn.com/images/marisol/post/55722459-d2e6-465e-b95f-525a004a7b22/image.png)

이제 보안 네트워크 연결을 살펴볼건데,
보안 연결은 사용자가 앱을 통해 액세스하는 모든 정보의 기밀성을 보장할 뿐만 아니라
정보의 신뢰성을 보장하고, 정보가 손상되지 않도록 하기 때문에 매우 매우 중요하다!

❌ 여기서 하지 말아야 할 첫 번째는 Custom Protocol을 사용하는 것이다.
표준 솔루션을 사용해야 한다. TLS (전송 계층 보안)이다.

Network Framework나 URLSession에서 쉽게 사용할 수 있어서 예시를 볼텐데,
그 전에 먼저 Secure Transport를 더이상 사용하지 말라고 얘기하고 있다.
이미 Secure Transport를 사용하고 있다면, Network framework나 URLSession으로 마이그레이션 할 것을 권장하고 있음.

(Secure Transport은 Apple의 예전 버전에서 사용되었던 네트워크 보안 프레임워크. 더 이상 새로운 기능이 추가되지 않음.)

![](https://velog.velcdn.com/images/marisol/post/ac4bd0ad-9f3e-44bb-a451-9d6a3219f0a5/image.png)

Network Framework에서는 연결을 설정할 때 ```.tls``` 만 사용하면 되고,
URLSession의 경우, ```https``` endpoint를 사용하면 된다.

![](https://velog.velcdn.com/images/marisol/post/dcb57c5e-2df9-44e4-ad14-22140973169d/image.png)

경우에 따라 TLS를 사용하지 못할 수도 있는데, CDN (Contents Delivery Network)를 통해 데이터를 보호해야하는 경우가 그런데,
이 경우 인증서를 관리해야 하는데, 인증서를 관리할 때 Parser를 사용하면 보안에 취약해질 수 있다고 한다.

그럴 때 인증서 검증을 위해 ```SecTrust```를 사용할 수 있다.

SecTrust는 인증서 유효성 검증을 쉽게 제공한다. 
인증서 유효성 검증에는 만료, 폐기 등과 같은 정보가 포함될 수 있으며, 인증서가 유효하다는 것을 확인한 후에는 키를 사용할 수 있다.
(TLS 사용하는 경우에는 인증서 걱정할 필요 없음)

![](https://velog.velcdn.com/images/marisol/post/1d9cf327-ec95-4419-8737-87f58fbb1403/image.png)

이번 릴리스에서는 SecTrust에 새로운 함수가 추가되었는데, 2가지를 결합하여 수행한다.
1) 인증서 유효성 검증을 비동기적으로 처리할 수 있도록 하며, (인증서가 검증되는 동안 앱이 다른 작업 수행 가능)
2) 실패 시 에러 핸들링 및 디버깅이 가능하도록 해준다.

![](https://velog.velcdn.com/images/marisol/post/88dfb629-3929-4bd0-b65b-4b294511e718/image.png)

지금까지 5가지 시나리오를 살펴봤는데,

어떤 시나리오에서든 시스템 프레임워크를 사용하고, 이미 사용 중이면 최상의 속성을 사용하여 사용자 및 비즈니스 자산을 최대한 보호하도록 노력해야 한다.

![](https://velog.velcdn.com/images/marisol/post/621203fd-58ca-45af-b6a9-ef7a02a0fee1/image.png)

![](https://velog.velcdn.com/images/marisol/post/2d20d27f-0cb4-4cb4-b9ec-ec62b819c90d/image.png)

어떤 상황에서는 Apple 시스템 기능을 사용하는 것이 현실적이지 않을 수 있다. 
예를 들어, Apple 생태계 외부에서 상호 운용성이 필요할 수 있다. 서비스 인증이 필요할 수도 있고 구현해야 할 명세가 주어질 수도 있다. 
이 경우를 위한 훌륭한 솔루션이 바로 Apple CryptoKit이다.

Apple CryptoKit은 Swift 암호화 API로, 이전에 설명한 기능들과 같은 철학을 따르도록 설계되어 
암호화 함수 및 잘못 사용하기 어려운 함수에 쉽게 액세스할 수 있도록 한다.

![](https://velog.velcdn.com/images/marisol/post/9c5ea883-9eda-471b-a6d6-a4660bd78635/image.png)

![](https://velog.velcdn.com/images/marisol/post/f7b29413-974b-4551-adf5-9440c76a8832/image.png)

Apple CryptoKit의 4가지 측면에 대해 얘기하려고 한다.

1. CryptoKit과 Swift
2. CryptoKit에서 사용할 수 있는 알고리즘
3. Secure Enclave 및 생체 인증
4. 성능

![](https://velog.velcdn.com/images/marisol/post/f3da2cb8-3be2-4d00-a904-8064406d294b/image.png)

예전에는 Swift에서 C Crypto API를 호출할 때 이렇게 생긴 코드를 작성해야 했다고 한다..
버퍼를 할당하고, 각 버퍼의 크기를 파악하고 어쩌고 저쩌고....

하지만 이제 Apple CryptoKit에서는 코드 한 줄로 동일한 작업을 수행할 수 있다.

- dataToEncrypt: 암호화할 데이터
- symmetricKey: 대칭키. 암호화에 사용됨
- AES.GCM.seal: dataToEncrypt와 symmetricKey를 파라미터로 사용하고, 이를 기반으로 암호문을 생성함. 생성된 암호문은 sealed 변수에 할당된다.

![](https://velog.velcdn.com/images/marisol/post/cca190aa-ee3d-4887-853d-d50bc3e72b07/image.png)

C Crypto API를 호출할 때, 자신만의 암호화 키를 생성해야 했다.
그리고 암호화 키는 일반적으로 비트로 표현되기 때문에 먼저 수많은 비트와 바이트를 변환해야 했다.
그런 다음 그 바이트에 대해 버퍼를 할당하고, 암호화된 보안 난수로 버퍼를 채워야 했다.
그렇게 키를 사용할 준비가 되었고, 키를 다 사용한 후에는 메모리에서 키를 제거하기 위해 키를 0으로 설정해야 했다.

CryptoKit에서 키를 생성하는 것은 코드 한 줄로 끝난다.
SymmetricKey initializer를 호출하고, 키를 유지할 비트 수를 전달한다.

할당이 해제될 때마다 이 값의 ARC에 대해 알고 있기 때문에 이 키를 릴리즈할 때 0으로 설정할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/65b344af-2ea6-418f-9a42-9a881925b144/image.png)

CryptoKit은 MAC (Message Authentication Code)와 같은 값을 검증할 때 등가 준수 (equitable conformance)를 수행하기 때문에, 두 개의 MAC 값을 == 연산자로 검증할 수 있다.

그리고 CryptoKit은 해시 함수 H를 기반으로 하는 제네릭 코드를 작성할 수 있도록 프로토콜을 정의한다. 코드 베이스에서 다른 해시 함수를 전달해야 하는 여러 위치에서 동일한 코드를 한 번 작성하고 함수 호출을 다른 해시 함수로 매개변수화 하고 있다.

(-> 예를 들어서, SHA-256을 사용하여 데이터를 해시하는 함수를 작성하고, 이 함수를 여러 곳에서 사용해야 한다고 치자. 나중에 알고리즘을 업그레이드하여 SHA-384를 사용해야 한다고 하면 모든 함수 호출을 찾아서 새로운 함수로 바꿔야 한다.
하지만 Cryptokit은 제네릭 프로토콜을 정의하여 이 문제를 해결할 수 있다. 해시함수 H를 기반으로 하는 제네릭 함수를 작성하고, 이 함수를 호출 할 때 다른 해시 함수를 매개변수화해서 전달할 수 있다. 그래서 이 함수를 한 번만 작성하면, 언제든지 다른 해시 함수를 사용해서 호출할 수 있다.)

```
import CryptoKit

func hashData<H: HashFunction>(_ data: Data, using hashFunction: H.Type) -> H.Digest {
    return H.hash(data: data)
}

let data = "Hello, World!".data(using: .utf8)!
let sha256Digest = hashData(data, using: SHA256.self)
let sha512Digest = hashData(data, using: SHA512.self)
```

![](https://velog.velcdn.com/images/marisol/post/27ac4e67-322f-4a86-8c96-1bbafa21d036/image.png)

이제 CryptoKit에서 지원되는 일련의 알고리즘에 대해 알아보려고 한다.

![](https://velog.velcdn.com/images/marisol/post/4181a4ee-557d-4951-8c66-97ab663edb48/image.png)

CryptoKit은 해시 함수, 메시지 인증 코드, 인증된 암호화, 키 계약 및 서명을 지원한다.

이러한 알고리즘 중 일부는 Common Crypto 또는 SecKey와 같은 프레임워크에서 이전에 지원했던 알고리즘이지만, 처음 지원하는 알고리즘도 있다. 
(🟢 초록색으로 표시된 알고리즘)

Insecure Module도 있는데, 이 모듈은 MD5 및 SHA1과 같은 알고리즘을 제공하기 때문에, 보안 기준을 충족시키지는 못하지만 이전에 사용하던 코드와의 호환성을 유지할 필요가 있는 경우에 사용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/83c4c2a1-dd09-4b0d-a8a3-6e0590650ef2/image.png)

해시 함수부터 살펴보면,
해시 함수는 ```digest``` 라고 하는 고정 크기의 output을 생성하며, 암호화 속성을 제공한다.

SHA-256은 Secure Hash Algorithm의 일종으로, 입력 데이터를 고정된 크기의 해시 값으로 변환하는 알고리즘이다. 
이 해시 함수는 256비트 크기의 출력 값을 생성하며, 임의의 길이의 입력 데이터를 받아들인다.

SHA-256은 주로 데이터 무결성을 검사하는 용도로 사용되는데, 
예를 들어 파일을 전송하거나 저장하기 전에 해당 파일의 SHA-256 해시 값을 계산하여 나중에 파일이 변경되었는지 여부를 확인할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/4435f74d-f5e6-4478-a09f-004c6a13cdcd/image.png)

여기 해시하고 싶은 오디오 데이터가 있다. 그리고 SHA256 해시 함수를 사용하여 다이제스트를 계산한다.

![](https://velog.velcdn.com/images/marisol/post/5b3be91d-12c6-431e-a6f0-91487da38442/image.png)

파일을 스트리밍하는 경우, 입력 스트림에서 파일을 읽을 수 있다.

이 경우, 데이터를 받을 때마다 일부분씩 처리하여 해시 값을 계속해서 업데이트하면서, 모든 데이터를 받았을 때 최종 결과를 얻게 된다. ```(incremental hashing / streaming hashing)```

이를 위해 CryptoKit에서는 해시 함수를 초기화하는 메서드와 해시 값을 업데이트하는 메서드를 제공한다. 이를 통해 대용량의 데이터를 효율적으로 처리하고, 보안적으로 안전한 방식으로 해시 값을 계산할 수 있다.

해시 함수의 ```MT 이니셜라이저```를 호출하여 해시를 초기화하는 것으로 시작한다. 그런 다음 해시할 데이터를 전달하고 업데이트 메서드를 한번 이상 호출하여 해실 수 있다. 그리고 다이제스트를 계산하려면 해시에 대해 finalize 메서드를 호출하면 다이제스트가 반환된다.
(근데 위 예시 코드에서는 MT 이니셜라이저 호출하지 않음..)

> MT 이니셜라이저
: CryptoKit에서 사용되는 Message Authentication Code(MAC)을 계산하기 위한 Hasher를 초기화하는데 사용되는 메서드. SHA-256을 사용하여 MAC을 계산하려면, SHA256.MAC대신 SHA256.MT를 사용하여 Hasher를 초기화 한다.

![](https://velog.velcdn.com/images/marisol/post/065e23ae-74a4-43c6-a197-761ef8db6549/image.png)

Authenticated Encryption은 이름에서 알 수 있듯이 ```인증```과 ```암호화```를 모두 제공한다.

암호화 프로토콜에서 Authentication이 취약하면 다양한 공격이 발생할 수 있으며, 수동으로 인증과 암호화를 결합하면 Oracle 공격을 패딩할 수 있다.
따라서 단일 API 호출로 제공하니까 이걸 쓰라고 한다.

![](https://velog.velcdn.com/images/marisol/post/eec62713-4983-4491-8c2c-554f8fc7e2b2/image.png)

하이킹 앱 프로젝트를 진행중이라고 해보자. 앱의 대부분의 컨텐츠는 무료이지만, 앱 내 구매를 통해 추가 컨텐츠를 구매할 수 있다. 컨텐츠를 구매한 후에는 해당 컨텐츠를 검색할 수 있다.

사용자 수가 증가함에 따라 다운로드 속도를 유지하기 위해 컨텐츠 전달 네트워크 (CDN, Content Delivery Network)를 사용하고 있다.

![](https://velog.velcdn.com/images/marisol/post/645d48aa-d286-4fee-b396-423192f5d83d/image.png)

컨텐츠를 검색하려면 먼저 서버에서 앱 내 구매가 유효한지 확인한다. 그런 다음 서버에서 키를 검색하여 디바이스로 전달한다.

![](https://velog.velcdn.com/images/marisol/post/dd2da740-145d-4da5-91a7-aabe5bee5295/image.png)

CDN에서 암호화된 데이터를 다운로드하고 키를 사용하여 암호화된 컨텐츠를 복호화한다. 키가 맞고, CDN에서 데이터가 변조되지 않았다면, map 데이터를 검색할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/b3596fb4-20d7-424c-afeb-e4585129c36e/image.png)

이제 코드로 살펴보자면
- 암호화된 데이터를 다운로드 받고,
- 이 데이터를 복호화하여
- 지도 데이터를 가져오는 작업을 수행하고 있다.

우선 서버에서 검색한 키 데이터로 SymmetricKey를 초기화 한다. 여기에는 다운로드된 암호화된 데이터를 복호화하는데 필요한 비밀 키 데이터가 저장된다.

그런 다음 sealedBox를 초기화 한다. 이 객체는 ChaCha20-Poly1305 암호화방식으로 암호화된 데이터와 Nonce 및 Tag를 모두 포함한다.

그리고 첫번째 줄에서 생성한 비밀키인 key를 사용하여 SealedBox 객체를 열어서 암호화된 데이터를 평문으로 복호화한다. 이렇게 해서 지도 데이터를 복호화하고 사용자가 이 지도를 활용할 수 있게 한다.

> SymmetricKey: 암호화와 복호화에 동일한 비밀키를 사용하는 대칭키 암호화 알고리즘에서 사용되는 키

> SealedBox: 공개화 암호화 방식 중 하나인 Asymmetric-Key 암호화 방식에서 사용되는 암호화 객체. 평문과 비밀키를 사용하여 생성되는 암호화된 메세지이다. 암호화된 메세지와 함께 Nonce와 함께 사용된다. SealedBox 객체는 평문과 Nonce, Tag로 이루어져 있으며, 이 세가지 값으로부터 암호화된 데이터를 복원할 수 있다. SealedBox는 암호화된 데이터를 안전하게 저장하고 전송할 때 사용된다.

> Nonce: 한 번만 사용되는 임의의 값을 의미하며, 암호화를 위해 사용된다.

> ChaCha20-Poly1305: 비밀키 대칭 암호화 및 인증 알고리즘. ChaCha20은 스트림 암호화 방식이고, Poly1305는 메시지 인증 코드(MAC) 방식이다. 이 둘을 결합하여 사용하여, 메시지를 동시에 인증하고 암호화하느 방식으로 보안성을 높일 수 있다.

![](https://velog.velcdn.com/images/marisol/post/c6a9b66e-5b1d-45fc-98b0-c884a2f985b3/image.png)

다음은 서명(Signature)이다. 
서명은 개인 키를 사용하여 데이터를 인증하는 데 사용되며, 해당 서명을 사용하여 연결된 공개 키를 사용하여 데이터를 확인할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/0ab90e1c-4440-44ce-9376-a03dce73e981/image.png)

예를 들어, 서명을 사용하여 로그인 또는 돈 송금과 같은 민감한 트랜잭션을 인증하는 방법에 대해 살펴보자.

먼저 디바이스에 개인 키를 생성한다.

![](https://velog.velcdn.com/images/marisol/post/736c7e37-387f-4397-b821-bf3ce52dc45c/image.png)

그런 다음 연결된 공용 키를 검색한다. 연결된 공용 키가 서비스에 등록되어 있으면,

![](https://velog.velcdn.com/images/marisol/post/52dc5a96-ca5b-4a6b-9fe1-73594f5f5bb0/image.png)

개인 키를 사용하여 트랜잭션 데이터에 서명을 생성한 다음,

![](https://velog.velcdn.com/images/marisol/post/f4081344-a285-49f9-a46b-1234d6e8c2fc/image.png)

트랜잭션 데이터와 서명을 서버로 전송한다.

![](https://velog.velcdn.com/images/marisol/post/7717aaf5-50df-4ceb-a44e-27774ef955bc/image.png)

서버는 서명이 올바른지 확인하고, 서명이 올바른 경우 작업을 계속 진행한다.
코드로 어떻게 생겼는지 보자.

![](https://velog.velcdn.com/images/marisol/post/5d7ef62d-e5a4-49e6-ab8b-20b69b2e5b59/image.png)

암호화된 강력한 개인 키를 생성하기 위해 ```PrivateKey``` 이니셜라이저를 호출한다.

그리고 서버에 공개 키 데이터를 등록해야 한다. 개인 키를 호출하여 공개 키를 검색한다. 그리고 공개키에서 어떤 표현 방식을 원하는지 지정한다.
이때 여러 가지 공개 키 표현 (representation)이 있으며, CryptoKit에서 지원한다. 여기서는 compactRepresentation으로 지정하고 있으며, 개인 키는 항상 Keychain에 저장한다.

다음으로, 서명을 생성하기 위해 개인 키에서 signature 메서드를 호출하고, 서명하려는 트랜잭션 데이터를 전달한다.
이런 작업을 수행하는 키는 ```Secure Enclave```와 같은 안전한 장치를 사용하여 보안을 강화해야 한다.

![](https://velog.velcdn.com/images/marisol/post/4853c3e8-357d-4f0b-95a6-42289f2d2b7d/image.png)

Secure Enclave는 별도의 프로세서에서 작동하는 하드웨어 기반 키 관리자로, 추가적인 보안 레이어를 제공한다.

![](https://velog.velcdn.com/images/marisol/post/23b0feaf-76c7-4403-aaaf-bf41649b3b8b/image.png)

Touch ID 또는 Face ID와 같은 중요한 시스템 기능의 일부로 사용되기도 한다.

![](https://velog.velcdn.com/images/marisol/post/df538ad8-7b51-451d-8f84-d24286e897d0/image.png)

Secure Enclave를 사용하여 서명을 생성하려면, 우선 디바이스에 Secure Enclave가 있는지 확인해야 한다. SecureEnclave에서 isAvailable을 호출하여 확인할 수 있다.

이후에는 기존과 동일한 코드를 사용하여 Secure Enclave 내에서 키를 생성하고 서명을 생성할 수 있다. 이를 위해 키를 생성하는 PrivateKey 초기화 메서드 호출 앞에 Secure Enclave를 붙이면 된다.

![](https://velog.velcdn.com/images/marisol/post/bfa5c3fb-dcfd-4b4a-842a-671be738a338/image.png)

Secure Enclave를 사용하는 장점 중 하나는, 키 사용을 제한할 수 있다는 것이다.
위 코드는 Secure Enclave에서 생성하는 키는 디바이스가 잠금 해제된 경우에만 접근할 수 있으며, 이 키는 이 디바이스에서만 사용할 수 있도록 제한한다.

키 사용을 더 제한하기 위해, 개인 키를 사용하여 작업을 수행할 때 사용자 인증을 요구하도록 하려고 한다.

사용자 인증을 요구하는 것은 사용자에게 생체 인증 또는 디바이스 비밀번호를 요청한다. 이제 액세스 제어 정책을 구성했기 때문에, 생성하는 키의 초기화 메서드에 그 정책을 전달하기만 하면 해당 정책이 적용된다.

![](https://velog.velcdn.com/images/marisol/post/23690294-bac8-4878-8f2f-c68529fc1fd4/image.png)

사용자에게 인증을 요구하는 이유를 추가적으로 설명하려고 할 때에는 LocalAuthentication context를 전달할 수 있다. 위 코드에서는 LocalAuthentication context가 10초 동안 유효하도록 하고, Bob에게 10달러 이체하려는 것을 인증해야 한다는 것을 사용자에게 알린다.
이 인증 컨텍스트를 사용하려면, 해당 컨텍스트를 키의 초기화 메서드에 전달하면 된다.(위에서 authenticationContext)

![](https://velog.velcdn.com/images/marisol/post/9f030529-5ba5-4ccd-9af6-b791f95203a6/image.png)

마지막으로 성능에 대해 얘기하려고 한다.

![](https://velog.velcdn.com/images/marisol/post/f70c4d7d-9038-4d5c-a1f9-bb897253e451/image.png)

CryptoKit은 Apple의 기본 암호화 라이브러리인 corecrypto를 기반으로 구축되었으며, 이 라이브러리는 지원하는 각 마이크로 아키텍처의 모든 사이클을 최적화하기 위해 수동으로 튜닝된 어셈블리 코드를 사용한다.

또한 corecrypto를 사용하므로 사이드 채널 저항(side-channel resistance)과 같은 corecrypto의 보안 개선 사항을 활용할 수 있으며, corecrypto가 FIPS 인증을 받았기 때문에 FIPS 규정을 준수하는 방식으로 CryptoKit을 사용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/7442232e-3861-4d49-82a4-b4119c87ffc7/image.png)

![](https://velog.velcdn.com/images/marisol/post/3e613339-4c06-40c7-89b0-b81ec3964bce/image.png)

CryptoKit은 강력한 저수준 암호화 API이며, CryptoKit을 사용하여 더 안전한 앱을 출시해야 한다.
