# WWDC 2015 Networking with NSURLSession

## NSURL Session과 HTTP프로토콜에 대한 배경지식
![](https://i.imgur.com/iAC6WOh.png)
- **URLSession**
    - 1. 주로 웹에서 HTTP 콘텐츠를 다운도르 하는데 사용되는 네트워킹 API입니다.
    - 2. 인증 및 기타 중요한 이벤트 처리에 사용할 수 있는 delegate methods가 있다.
    - 3. 가장큰 특징은 앱이 백그라운드 다운로드 API를 통해 실행되지 않는 동안에도 네트워킹을 할수 있다는 것 이다.

![](https://i.imgur.com/igPZyn3.png)
- **HTTP(HyperText Transfer Protocol)**
    - 핵심은 서버에 요청을 하고 응답으로 데이터를 가져오는 것 이다.
    - HTTP는 Text를 통해 전송된다. 그렇기에 해커들에게 노출되어 불안하다.
    - 이러한 문제는 HTTPS를 도입해 해결되었다.
    - HTTPS는 HTTP계층 위에 보안 계층이 추가된것이다.
    - 앱에서 나가는 데이터가 암호화된 네트워크를 통해 이동하게되며 무결성을 제공해 탐지 없이는 메세지를 변경할 수 없다.
    - 인증을 제공하여 실제로 대화 상대의 신원을 증명할 수 있다.

NSURLSession 에는 HTTPS가 내장되어 HTTP대신 HTTPS 를 사용할 수 있다.
HTTPS를 사용하기 위해서는 서버의 지원이 필요하며 현재 대부분의 서버는 HTTPS를 지원한다.

![](https://i.imgur.com/2ijJgs0.png)

데이터는 민감한것으로 간주되어야 하며 개발자가 앱에서 네트워크로 전송되는 데이터가 중요하지 않다고 생각할지라도 앱을 사용하는 유저는 중요하다고 생각할수 있기 때문이다.

**HTTPS 를 사용하는 이유는 무엇일까?**
앱을 사용하는 유저는 개인정보와 데이터가 해커들의 위협에서 안전하다고 믿고 신뢰하기때문이다.
그리고 Apple은 이러한 신뢰를 유지하기 위해 보안을 강화하는 방법을 시도하고 해결하는것이다.

## App Transport Security
![](https://i.imgur.com/sZrJ31v.png)

### 1. ATS가 적용되는 버젼
App Transport Security란 iOS 9, OS X EL Captan 이후부터 사용가능한 Apple의 새로운 기능이다.

Xcode7 버전 이상에서 앱 생성 시 기본값으로 가져가게 되는 보안 정책이다.

### 2. ATS 역할과 정책

유저 데이터가 우발적으로 공개되는 것을 방지하는데 핵심적인 역할을 한다.
ATS(App Transport Security)는 NSURL Session의 기본 정책도 강화한다.

![](https://i.imgur.com/YS6Yj49.png)

- 1. ATS에서 가장 중요한것은 NSURL Session이 텍스트로 이루어진 HTTP를 로드하는것을 허용하지 않는다는 것 이다.
- 2. HTTPS 연결만 사용한다.
- 3. ATS는 TLS 버전, 인증서 신뢰 및 해당 트랜잭션에 사용되는 인증서 키 크기에 제한을 둔다. 
- 4 ATS는 앱 Info.plist를 통해 간단하게 구성된다.
![](https://i.imgur.com/1kBzryV.png)

앱이 보안 연결만을 사용한다면 ATS를 구성하기 위해 다른 작업을 수행할 필요가 없다.

위에서 ATS는 키 크기에 제한을둔다고 했는데 모든 서버가 이러한 제한을 준수하지는 않기 때문에 예외를 사용하여 허용할수 있다.

![](https://i.imgur.com/teNlmXN.png)

ATS를 사용하면 예의를 선언하고 사용하려는 TLS 버전을 알려주거나 기타옵션을 선택 해제할 수 있다.

![](https://i.imgur.com/48YYFZp.png)

예를들어 HTTPS 를 사용하지 않는 서버가 있다고 가정해보자.

![](https://i.imgur.com/WbFPfeG.png)

HTTPS는 지원하지 않고 HTTP만 지원하는 미디어 서버가 있는 경우 이처럼 예외를 사용할수 있다.

HTTP를 통해 로드해야하는 도메인을 선언하기만 하면 NSURL Session에서 텍스트를 로드 하는것이 허용된다.

### 3. HTTP, HTTPS를 모두 허용하는 키워드 NSAllowsArbitraryLoads
![](https://i.imgur.com/OIwDvG2.png)

NSAllowsArbitraryLoads 정책을 사용하여 NSURL Session이 HTTP, HTTPS 리소스 모두 로드하도록 허용할 수 있다.

![](https://i.imgur.com/Q6z4aTz.png)

HTTP, HTTPS 모두 허용했지만 예외로 보호하려는 서버가 있다면 이와 같이 선언하면된다.
그러면 NSURL Session이 HTTPS를 통해서만 로드하여 게속 보호할 수 있다.

### 4. ATS에 의한 URLSessionError
기존의 앱에 ATS 정책이 적용될경우 문제가 생길수 있기 때문에 현재 SDK에 대해 빌드하는 경우에만 활성화된다.(기존것은 잘 동작하지만 App을 update를 하기위해서는 ATS 정책을 따라야한다.)

NSURLSession은 HTTP리소스를 전송하거나 URL을 HTTPS로 자동으로 로드한다.
앱의 새 네트워크 오류가 ATS 때문인지 확인 하기 위해서는 NSAllowsArbitraryLoads 키워드를 사용하여 확인할수 있다.
(NSAllowsArbitraryLoads사용해서 모두 허용했을때 문제가 없다면 아마 ATS 정책에의한 네트워크 오류일듯 하다.)

### 5. CF NETWORK DIAGNOSTICS 네트워크 진단 디버깅
네트워크를 디버깅 할수있는 CF NETWORK Diagnostics 를 사용할수 있다.
CF NETWORK DIAGNOSTICS 레벨을 1로 설정하면 실패한 모든 URL을 로드하고 실패한 URL과 TLS오류를 기록한다.
따라서 TLS오류를 가져와 Transport.h 를 조회하여 문제가 무엇인지 확인 할 수 있다.

![](https://i.imgur.com/Mk3HIst.png)
Apple은 개발자와 협력하여 유저에게 보다 안전한 환경을 제공하고자 한다.
유저의 데이터를 보호하기 위해서 앱을 새로 만드는 경우 HTTPS를 사용해야한다.

---
## NSURLSession New Protocol
![](https://i.imgur.com/eatvBqU.png)
NSURLSession은 HTTP/2 protocol을 지원한다.

앱에서 NSURLSession을 사용하고 있다면 코드를 변경할 필요없이 자동으로 HTTP/2 를 사용하게 된다.

![](https://i.imgur.com/B8MWAPe.png)

### 1. 새로운 프로토콜이 필요한이유와 HTTP/1.1 의 문제
통신을 위한 많은 프로토콜이 이미 존재하지만 왜 새로운 프로토콜이 필요할까?

이유는 대부분의 프로토콜이 그 시대의 필요에 따라 수년 전에 설계되었기 때문이다.

HTTP는 웹이 시작될때부터 존재했으며 많은 문제가 있다.

![](https://i.imgur.com/y7NHzvf.png)

가장 유명한 문제는 TCP 연결당 미해결 요청이 하나만 있다는 문제다.

이에 대한 해결책은 HTTP 파이프라이닝 이었지만 모든 서버나 네트워크에서 HTTP 파이프라이닝을 사용할 수 있는것은 아니다.
실제로 가장 많이 사용되는 테스크탑 웹 브라우저에서는 비활성화 되어 있다.

또다른 해결책은 호스트에 대한 다중 연결을 여는것이다.
이러한 방법은 여러 리소스를 더빠르게 가져오는데 도움이 될 수 있지만 클라이언트와 서버 모두 더 높은 시스템 요구사항과 더낮은 성능을 추가할 뿐이다.

![](https://i.imgur.com/W2nSdCu.png)

2014년도에 NSURLSession 에서 SPDY지원을 도입했다.
SPDY는 웹을 더 빠르게 만들기 위한 시도였다.
실험적인 프로토콜이었지만 새로운 버전의 HTTP를 위한 기반으로 사용되었다.

새 프로토콜 버전에 대한 사양은 IETF 표준화를 거쳐 RFC 번호를 부여받았다.

그리고 현재 NSURLSession이 HTTP/2프로토콜을 지원하도록 확장되었다.

### 2. HTTP/2 의 기능
HTTP/2는 HTTP/1.1과 달리 호스트에 하나의 TCP연결만 가능하다.
이는 네트워크 친화적이며 클라이언트와 서버 모두 시스템 리소스를 덜필요로 하게 된다.

![](https://i.imgur.com/xa4YO5R.png)

HTTP/2는 multiplexing(다중화) 되어 요청은 서버가 이전 요청에 대한 응답을 보낼때까지 기다릴 필요가 없다.

HTTP/2는 요청 우선 순위가 존재하여 더 중요한 리소스를 더 높은 우선 순위로 클라이언트에 전달할 수 있다.

HTTP/2의 multiplexing이 HTTP/1.1의 Head-of-Line-Blocking문제를 해결하는 방법을 살펴보자.

![](https://i.imgur.com/SSVfqSD.png)

HTTP/1.1에서는 웹 서버에 세가지 요청이 있을경우를 예로 들어보자.
첫번째 요청을 보내고 응답을 받았다.
그후 두번째 요청을 보내고 두번째 응답을 받을 수 있다.
세번째 요청도 마찬가지다.

그후 파이프 라이닝이 활성화 되어 이전 응답이 도착할 때 까지 기다리지 않고 모든 요청을 차례로 보낼수 있다.

![](https://i.imgur.com/wIRkJVX.png)

하지만 여전히 같은 순서로 응답을 받게된다.

이미지의 파란색 그림을 보면 첫번째 응답은 다음 두개의 응답을 차단한다.

![](https://i.imgur.com/6j03dlb.png)

HTTP/2사용 예시를 살펴보자.
우선순위가 다른 동일한 세가지 요청이 있다고 예를 들어보자.
첫번째 요청에의한 응답보다 우선순위가 높은 요청의 응답이 먼저 도착한것을 알 수 있다.

우선순위가 낮은 응답이 우선순위가 높은 응답을 차단하지 않게되며 이는 앱의 성능에 적합하다.

HTTP/1.1과 다른것도 비교해보자.
HTTP/2는 바이너리 프로토콜 이다.
따라서 데이터 처리 및 구문 분석이 더 빨라진다.
HTTP/1.1은 헤더 압축을 사용하지 않으며 SPDY는 보안 악용으로 인해 헤더 압축을 사용할 수 없다.
HTTP/2는 헤더 압축을 위한 보다 안전한 메커니즘인 HPACK을 사용한다.

![](https://i.imgur.com/dmA1BCe.png)

HPACK에 대해 살펴보자.
HPACK 헤더 압축은 static table과 dynamic table 두가지 테이블을 기반으로 한다.
static table에 포함되지 않은 헤더는 동적 테이블에 추가할 수 있다.
테이블의 헤더는 인덱스로 참조할 수 있다.

파란색으로 강조된 문자는 서버로 보낼 데이터를 뜻한다.
아래는 동일한 요청의 HTTP/2의 표현이다.

해당 요청을 인코딩 해보자.

헤더(headers), 메서드(HTTP method), 스킴(scheme), 패스(path) 는 StaticTable을 참조하여 사용할수 있다.

authority 헤더는 StaticTable에 포함되지만 값은 없다.
따라서 이 요청을 인코딩 하려면 처음 3개의 헤더에 대해 3바이트가 필요하고 authority 헤더를 추가하기위한 추가 바이트가 필요하다.
추가된 바이트는 authority 헤더를 DynamicTable에 추가한다.

![](https://i.imgur.com/Vrbr5RI.png)

이렇게 만들어진 헤더 프레임은 서버로 전송된다.

HTTP/2 의 경우 Static, Dynamic Table 을 사용하여 모든 헤더를 참조할 수 있다.
각 헤더에대해 1바이트를 사용하며 HTTP/1을 사용할 경우보다 적은 바이트를 사용하게된다.

![](https://i.imgur.com/m4lr47X.png)

HTTP/2 프로토콜은 NSURLSession API와 통합된다.
NSURLSession을 사용했다면 앱, OS X 프로그램이 자동으로 해당 기능을 갖게된다.

이러한 기능을 사용하기 위해 코드를 수정하거나 새로운 코드를 작성하지 않아도 된다.

![](https://i.imgur.com/p7oDzJr.png)

앱에서 HTTP/2 프로토콜을 채택하는 데 필요한 사항 은 HTTP/2 를 지원하는 서버만 있으면된다.
서버가 HTTP/2 를 지원하지 않을경우 HTTP/1.1을 사용하게된다.
네트워크 통신에 가장 적합한 프로토콜이 자동으로 선택된다.

NSURLSession은 암호화된 연결을 통해서만 HTTP/2프로토콜을 지원한다.

![](https://i.imgur.com/cLrC6mg.png)

현재 Apple에서 iCloud를 통한 HomeKit 원격 액세스는 HomeKit 액세사리와 iCloud간의 통신을 위해 HTTP/2 프로토콜을 사용하고 있다.

많은 대기업이 HTTP/2를 사용하고 있다.

## watchOS의 NSURLSession
이미지

2015 watchOS 2베타와 함께 출시된 WatchKit SDK 를 통해 watchOS에서 HTTPS, HTTP/2, ATS 모두 사용 가능하게 되었다.

일반적으로 유저의 시계가 페어링된 iPhone 근처에 있는 경우 사용자간의 Bluetooth 연결을 활용하고 전화자체에서 HTTP로드를 수행하고 Bluetooth를 통해 결과를 시계로 전달하게된다.

페어링된 iPhone이 멀리 떨어져있고 Wi-Fi 네트워크에 연결되어있다면 시계는 Wi-Fi네트워크를 직접 사용할 수 있다.

이미지

시계는 Screen이 매우 작다 따라서 이미지를 다운로드 하는 경우 iPhone에 표시하려는 전체 해상도 이미지를 다운로드할 필요가 없다.
더 작은 이미지를 다운로드 하면된다.

또한 시계는 iPhone, Mac 보다 처리 능력이 훨씬 낮고 휴대폰에 대한 Bluetooth 연결의 대역폭 및 대기 시간 제약으로 인해 제한을 받는 경우가 많다.
그렇기 때문에 다른 플랫폼처럼 빠르게 데이터를 시계로 가져올수 없다.

시계에서 주목해야할 또다른 점은 시계의 앱은 일반적으로 iPhone, Mac앱 보다 훨씬 짧은 기간 동안 실행 된다는 것이다.

따라서 기본 세션과 임시 세션 구성을 사용하는 경우 네트워킹 전송은 앱이 실제로 실행되는 동안에만 발생한다는 점에 유의해야 한다.

## 2015 에 적용된 API변경 사항
#### 1. NSURL Connection
![](https://i.imgur.com/tFe4KBd.png)

NSURL Connection은 OS X, EI Capitan, iOS 9 에서 deprecated되었다.

이는 NSURL Connection이 사라지는것을 의미하는것이 아니며 기존의 것은 제대로 동작하지만 NSURLSession으로 전환하는것을 추천한다는 의미이다.

#### 2. NSURL Connection을 NSURLSession 으로 변경하는 방법
![](https://i.imgur.com/nvdLj0z.png)

HTTPS 를 통해 www.example.com 에 연결하고 있으며 이를 나타내는 NSURL 객체가 있다.

URL을 래핑하는 NSURLRequest 를 구성하고 NSURLConnection의 sendAsynchronousRequest()(비동기 요청 보내기)메서드를 사용하여 해당 요청을 비동기적으로 실행한다.

메서드의 클로저로 HTTP응답 헤더를 나타내는 NSURL Response와 응답 데이터 NSData, 그리고 오류를 나타내는 NSError 를 받는다.

![](https://i.imgur.com/TgMEjNs.png)

NSURLSession을 살펴보자. NSURLConnection과 유사하게 생겼다.
NSURL과 NSURLRequest를 사용하기 때문에 메서드만 변경하여 인자값으로 전달해주면 된다.

## NSURLSession API에 추가사항
### Sharing Cookie
![](https://i.imgur.com/MBlmlRz.png)

NSURL 세션을 사용해도 쿠키는 실제로 다른 위치에 저장된다.
응용 프로그램을 사용해야 공유 데이터 컨테이너에 실제 액세스 할 수 있었다.

2015 컨테이너와 연결된 쿠키 저장소를 생성할 수 있는 새로운 API를 도입했다.

![](https://i.imgur.com/yptczaT.png)

위의 이미지는 새로운 쿠키 저장소에 쿠키를 저장하는 방법이다.
쿠키 저장소를 만든후 NSURLSession 구성 개체의 HTTP 쿠키 저장소를 만들어둔 쿠키저장소로 설정하고 NSURLSession을 생성하면 해당 세션에서 생성하는 모든 작업이 해당 쿠키를 사용하게되고 저장소에 저장하게 된다.

## HTTP, HTTPS 이외의 프로토콜이 필요한경우
채팅 프로그램, 화상통화 프로그램 등을 구현하는 경우 HTTP가 아닌 프로토콜이 필요할수 있으며 TCP/IP 위에 직접 사용자 지정 작업을 수행하려는 경우가 있을수 있다.
이때 사용할수 있는것이 NSURL Session Stream Task API이다.
이는 TCP연결을 하게된다.

![](https://i.imgur.com/B2RcZw7.png)

과거에는 NSInput Stream 및 NSOutput Stream을 사용해 비슷한 작업을 수행했지만 NSURL Session Stream Task 를 사용하면 몇가지 이점이 생기게 된다.

delegate를 설정해야하는 NSStream보다 NSURL Session Stream Task를 사용하면 매우 간단하고 편리한 인터페이스를 제공하기때문에 사용하기 편리하다.

NSURLSession은 HTTP프록시를 자동으로 통과하기 위한 기능을 가지고 있으며 NSURL Session Stream Task는 이 기능을 활용하여 서버에 HTTP프록시가 있고 NSStream이 실제로 연결할 수 없는 경우에도 원격 서버에 연결 할 수 있다.










