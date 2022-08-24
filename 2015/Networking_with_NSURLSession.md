# WWDC Networking with NSURLSession 정리

URLSession
- 주로 웹에서 HTTP 콘텐츠를 다운로드 하는데 사용되는 네트워킹 API이다.
- 인증 및 기타 중요한 이벤트 처리에 사용할 수 있는 delegate methods가 있다.
- 가장 큰 특징은 앱이 백그라운드 다운로드 API를 통해 실행되지 않는 동안에도 네트워킹을 할수 있다는 것 이다.

HTTP는 서버에 요청하고 응답하여 데이터를 가져오는 것이 핵심인데, HTTP 자체는 clear text로 전송되기 때문에 불안정하다.

(clear text는 암호화되지 않은 상태로 텍스트를 전송하거나 저장한다. 따라서 clear text를 표시하기 위한 암호 해독이 필요하지 않다.)

이 문제는 HTTPS라고 하는 검증된 솔루션이 있다.
HTTPS는 TLS(Transport Layer Security)위에 HTTP 가 있는 구조로 되어 있다.

![](https://i.imgur.com/pWy7xXy.jpg)

TLS (전송 계층 보안): 개인 정보 보호 및 안전한 전송을 위해 이메일을 암호화하는 표준 인터넷 프로토콜

TLS는 공개 키 암호화(public key cryptography)를 사용하여 multi-leg handshake를 수행하고 완료되면 보안 연결을 생성한다.

이 보안 연결은 세 가지 속성 때문에 안전한 것으로 간주된다.
1. 앱에서 나가는 데이터가 네트워크를 통해 암호화된 상태로 전송된다 -> 그래서 읽을 수 없음 <Encryption>
2. 메시지 무결성(integrity)을 제공하므로 detection 없이 메시지를 변경할 수 없다 <Integrity>
3. 인증 기능을 제공하기 때문에 정확히 누구와 대화하고 있는지 확인할 수 있다 <Authentication >

NSURLSession에 HTTPS support가 내장되어 있기 때문에 클라이언트 코드에서 HTTP 대신 HTTPS를 사용하기 쉽다. (일부 추가 서버 지원이 필요함)

앱에서 네트워크로 나가는 데이터가 중요하지 않다고 생각할 수 있지만, 고객은 중요하다고 생각할 수 있다.

예를 들어 TV 스트리밍 앱이 있다면, 누군가가 그냥 TV를 보고 있다고만 생각할 수도 있지만, 고객들은 자신들이 무엇을 보고 있는지 다른 사람이 아는 것을 원치 않을 수도 있다.

왜 HTTPS를 사용하는 것이 중요할까? 
=> 고객은 데이터와 개인 정보를 가지고 당신을 신뢰한다. (Your customers trust you with their data and their privacy)

## 📌 App Transport Security

App Transport Security란 iOS 9, OS X EL Captan 이후부터 사용가능한 Apple의 새로운 기능이다.
    
ATS는 본질적으로 고객의 중요한 데이터의 공개를 방지한다.
- cleartext HTTP URL 로드 X
- 안전한 연결을 위한 모범 사례 권장
- 더 강력한 보안
- 앱의 Info.plist를 통해 구성 가능

![](https://i.imgur.com/sCOmBm4.jpg)

대부분의 경우 보안 연결을 사용하지만, 일부 경우에는 HTTPS를 지원하지 않는 서버가 있는데, 
위와 같이 HTTP를 통해 로드해야하는 도메인을 선언하기만 하면 NSURLSession에서 cleartext 로드를 허용한다.

NSAllowsArbitraryLoads는 ATS 제한이 모든 네트워크 연결에 대해 비활성화되는지 여부를 나타내는 부울 값이다.
NSException Domains에 지정되지 않은 모든 도메인에 대해 ATS제한을 사용하지 않으려면 이 키 값을 YES로 설정한다. 
이 키 값을 YES로 설정한 경우, AppStore 검토 중 정당성을 제공해야 한다. 이 키는 앱의 보안을 크게 저하시키기 때문에 주의해서 사용해야 하며, 대부분의 경우 ATS에서 요구하는 요구사항을 충족하도록 서버를 업그레이드하거나, 최소한 좁은 예외를 사용하는 것이 좋다.

CF Network Diagnostics 라고 알려진 디버깅 도구가 있는데,
CF Network Diagnostics를 level 1로 설정하면 실패한 모든 URL이 로드되며, 실패한 모든 로드는 URL과 TLS 오류를 기록한다.
그 다음 단계는 TLS 오류를 찾아 transport.h를 찾아보고, 기본 문제가 무엇인지 정확히 확인하는 것이다.

새로운 앱을 작성하려면 HTTPS부터 시작하여 모범 사례를 통해 서버를 가동하고,
기존 앱을 사용하는 경우, 먼저 HTTPS로 이동할 수 있는 것부터 시작하고, 그렇지 못한 곳에서는 Info.plist를 통해 제공되는 예외를 사용해야 한다.

## 📌 New protocol support in NSURLSession

### 1. 왜 새로운 프로토콜을 사용해야 할까?

우리는 이미 많은 모든 종류의 의사소통을 위한 많은 프로토콜을 가지고 있지만, 대부분의 프로토콜은 그 시대의 요구에 맞게 수년 전에 설계되었다.
오늘 날의 요구는 수년 전과 크게 다르기 때문에 업데이트를 해야한다.

HTTP는 웹의 시작부터 존재해왔으며, 많은 문제를 가지고 있다.
가장 유명한 HTTP/1.1의 문제는 TCP 연결 당 하나의 미해결 요청만 발생하는 것이다.
이에 대한 해결책은 HTTP 파이프라인이었지만, HTTP 파이프라인은 일부 서버나 네트워크에서 사용할 수 없다. 실제로 대부분의 인기 있는 데스크톱 웹 브라우저에서는 사용할 수 없다.

또 다른 해결책은 호스트에 대해 여러 연결을 여는 것이다.
이는 여러 리소스를 더 빨리 가져오는데 도움이 될 수 있지만, 텍스트 프로토콜 오버헤드, 해더 압축 부족과 같은 문제들과 함께 클라이언트와 서버 모두에서 더 높은 시스템 요구 사항과 더 낮은 성능으로 이어진다.

오늘날에는 NSURLSession이 HTTP/2 프로토콜을 지원하도록 확장되었다.

### 2. HTTP/2 살펴보기

**HTTP/1.1과 HTTP/2의 차이**

| HTTP/1.1 | HTTP/2 |
| -------- | -------- |
| Multiple TCP connections to a host | Only one TCP connection |
| Head-of-line blocking | Fully multiplexed |
| FIFO restrictions | Request have priorities |
| Textual protocol overhead | Binary |
| No header compression | Header Compression (HPACK) |


HTTP/1.1과 달리 HTTP/2는 호스트에 대한 TCP 연결을 하나만 연다. (하나의 TCP 연결을 사용하여 복수의 HTTP 요청/응답을 주고 받는다. 매 요청/응답 마다 새로운 TCP 요청을 여는 것과 반대됨.)
네트워크 친화적이며, 클라이언트와 서버 모두에서 시스템 리소스가 적게 필요하다.

HTTP/2는 완전히 다중화(multiplexed)된다. 즉, 서버가 이전 요청에 대한 응답을 발송할 때까지 새 요청을 기다릴 필요가 없다.
HTTP/2에는 더 중요한 리소스가 클라이언트에 더 높은 우선순위로 전달될 수 있도록 한다.

웹서버 상에 리소스에 대한 요청이 아래와 같이 3개 있다고 할 때, Pipelining을 사용하지 않는 경우 HTTP/1.1에서는 첫번째 요청을 보내고 답변 받고, 두 번째 요청을 보내고 응답 받고, 세번째 요청을 보내고 답변을 받는다. 

![](https://i.imgur.com/9nisKA6.png)

아래와 같이 Pipelining을 활성화하면 요청은 동시에 보낼 수 있지만, 여전히 같은 순서로 응답을 받는다. 첫번째 응답이 다음 두 개의 응답을 차단한다.

![](https://i.imgur.com/ml2JSqb.png)

HTTP/2의 경우, 처음에 모든 요청을 보내고, 동시에 모든 답변을 받고 있다. 또한 우선순위가 높은 요청일수록 클라이언트에게 더 빨리 전달하고 있다.

![](https://i.imgur.com/NNYwd1j.png)
    
HTTP/2는 Binary protocol이다 (전송에 필요한 메시지들을 Binary 단위로 구성하며, 필요한 정보를 더 작은 프레임으로 쪼개서 관리한다)
이를 통해 데이터 처리 및 파싱 속도가 빨라진다.

HTTP/1.1은 헤더 압축을 사용하지 않는다. 매 요청마다 중복된 헤더 값을 전송하게 되며, 서버 도메인에 관련된 쿠키 정보도 헤더에 함께 포함되어 전송된다. 이러한 반복적인 헤더 전송, 쿠키 정보로 인해 헤더 크기가 증가한다.

SPDY는 보안 공격 때문에 헤더 압축을 사용할 수 없다. HTTP/2는 헤더 압축을 위한 보다 안전한 메커니즘인 HPACK을 사용한다.

헤더에 중복이 있는 경우, Static/Dynamic Header Table 개념을 이용하여 중복을 검출해내고, 해당 테이블에서의 index 값 + 중복되지 않은 Header 정보를 Huffman Encoding 방식으로 인코딩한 데이터를 전송한다.

**HPACK**

HTTP/2의 Header 압축은 내부적으로 HPACK이라고 불리는 모듈이 담당하는데, 
HPACK은 Huffman Algorithm, Static Table, Dynamic Table을 통해 압축을 수행한다.
- Huffman Algorithm: 자주 나오는 문자열 순서대로 짧은 Bitmap으로 매핑하여 데이터를 압축하는 기법
- Static Table: HTTP/2 Spec에 정의된 Table로, Header로 자주 사용되는 Key-value 값을 저장하고 있는 Table (61번 index까지 가지고 있음)
- Dynamic Table: 한번 전송/수신한 Header의 Key-value 값을 저장하는 Buffer 역할을 수행하는 Table (index가 62번부터 시작. FIFO 형태로 동작하기 때문에, 새로운 Key-value를 저장할 공간이 부족할 경우, 가장 오랜 기간 저장된 Key-value를 제거하고 새로운 Key-value를 저장한다)

![](https://i.imgur.com/YoRubjD.png)
    
### 3. NSURLSession을 이용한 HTTP/2 프로토콜 채택

NSURLSession이 HTTP/2 프로토콜을 자동으로 지원하며, 소스 코드의 변경이 필요하지 않다.
HTTP/2 서버만 있으면 된다.

현재 homekit 액세서리와 icloud 간 통신에 HTTP/2 프로토콜을 사용하고 있다.
많은 대기업들이 이미 HTTP/2 프로토콜을 사용하고 있다. (구글, 트위터)
많은 HTTP/2 오픈 소스 웹 서버가 있으며, 일부 CDN(Content Delivery Network) 서비스 제공자들이 HTTP/2 프로토콜 지원에 힘쓰고 있다.

## 📌 NSURLSession on watchOS

- 패스

## 📌 NSURLSession API changes

### 1. Sharing Cookies(Between Apps and Extensions)
- 앱과 Extensions는 기본적으로 다른 쿠키 저장소를 가지고 있다.
- 공유된 데이터 컨테이너를 사용하기 위해 application groups 라는 것을 사용할 수 있다.

### 2. NSURLSessionStreamTask (TCP/IP networking)
- HTTP(S)가 아닌 다른 프로토콜이 필요한 경우가 있다. (채팅 앱이나 영상 통화 앱 등을 구현하려면 HTTP(S)가 아닌 프로토콜이 필요할 수도 있다.)
- 그런 경우 TCP/IP 네트워킹 위에 직접 커스텀 기능을 수행할 수도 있다. -> NSURLSessionStreamTask는 TCP 직접 연결을 통한 새로운 API.
- 과거에는 NSInputStream과 NSOutputStream을 사용하여 유사한 작업을 수행할 수 있었지만, NSURLSessionStreamTask는 NSStream API에 비해 이점을 갖고 있다.
- NSInputStream / NSOutputStream에 비교한 장점
    - 1. 비동기 읽기/쓰기 인터페이스
        - 매우 간단하고 편리한 비동기식 읽기/쓰기 인터페이스를 제공하는데, NSStream에서는 이벤트를 읽기 위해 delegate을 설정해야 하고, 읽기/쓰기가 차단될 수도 있다.
    - 2. HTTP 프록시 자동 통과 가능
        - HTTP 프록시가 있는 경우에도 원격 서버에 연결할 수 있다.
- NSStream은 매우 널리 보급된 API이기 때문에, NSStream과의 호환도 지원한다.

---
    
참고 자료
- https://velog.io/@wiostz98kr/HTTP1.1과-HTTP2.0의-차이-e2v4x4t1
- https://ssup2.github.io/theory_analysis/HTTP2/ 

