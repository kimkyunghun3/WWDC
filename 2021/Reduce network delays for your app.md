# Reduce network delays for your app

네트워크 앱이 느려지는 원인 + 네트워크 앱의 응답성을 높이는데 사용할 수 있는 기술과 API 소개

## 1. How network delays affect your app

![](https://velog.velcdn.com/images/marisol/post/5255b952-0e1e-4f64-9ce1-42c8f29d13fb/image.png)

![](https://velog.velcdn.com/images/marisol/post/aba41a7a-8941-446a-87c0-790d3997cd13/image.png)

설정 > 개발자에 들어가면 ```Responsiveness```라는 새로운 메뉴가 표시된다. 
업로드 처리량, 다운로드 처리량, idle ping time 모두 흥미롭지만, 
네트워크 앱의 응답성에 영향을 미치는 주요 요인은 idle 상태가 아닌 "working condition"일때의 네트워크 응답성이다.

일반적인 인터넷 속도 테스트 idle ping time 측정은, 인터넷 연결을 사용하지 않을 때 인터넷 연결이 얼마나 잘 작동하는지 알려준다.
하지만 중요한 것은 ```인터넷 연결을 사용할 때 인터넷 연결이 얼마나 잘 작동하는지```이다.

> 💡 ```idle ping time```: 컴퓨터나 디바이스가 일정 시간동안 활동이 없을 때, 다른 디바이스나 서버에게 보내는 작은 데이터 패킷을 말함. 일반적으로 "keepalive packet" 또는 "heartbeat packet"이라고 불린다. 
네트워크 연결 상태를 유지하고 연결이 끊어지지 않도록 하며, 일정 간격으로 전송된다.

![](https://velog.velcdn.com/images/marisol/post/b48337ea-7212-4671-b3a0-b8e44b2888c3/image.png)

Test를 누른다.

![](https://velog.velcdn.com/images/marisol/post/e39df272-3caa-4788-b548-9a012292286a/image.png)

네트워크 트래픽을 생성하고, 몇 초 동안 네트워크를 측정한 다음, 네트워크가 working condition에서 얼마나 잘 작동하는지를 알려준다.

![](https://velog.velcdn.com/images/marisol/post/484906e9-6e72-4926-972a-9447b7d00f0f/image.png)

이 툴은 네트워크 응답성을 밀리초가 아닌 RPM (Rountrips Per Minute) 단위로 보고한다.

![](https://velog.velcdn.com/images/marisol/post/458d4616-93b6-411f-83f7-addcfdcafbd7/image.png)

macOS에는 NetworkQuality라는 이름의 이 테스트 툴의 command-line 버전도 있다.

가정이나 직장 네트워크에서 ping time이 꽤 좋다고 생각할 수 있지만, 이것은 idle time이다.

iPhone 또는 mac에서 이 네트워크 품질 테스트를 직접 실행하면 **네트워크가 사용 중일 때 응답성이 훨씬 더 나빠지는 것**을 알 수 있다.

네트워크 idle round-trip time 20miliseconds은 꽤 괜찮은 것 같지만,
working condition에서는 왕복시간이 600 miliseconds 이상으로 증가할 수 있다. (30배나 느려짐)

![](https://velog.velcdn.com/images/marisol/post/8de58e75-0131-4a4a-a654-5e7d06f725de/image.png)

앱의 사용자 경험에 중요한 것은 working condition에서의 네트워크 응답성이다.

이런 문제를 쉽게 경험할 수 있는데, 네트워크 지연은 모든 앱에 영향을 미치지만, 특히 오디오나 비디오 회의에서 영향이 더 크다.

인터넷은 메가비트에서 기가비트 이상으로 증가했지만, 문제는 여전히 발생하고 있다.
화상 회의에는 mb/s면 충분한데, 왜 여전히 이런 문제가 발생하고 있을까?
네트워크의 버퍼가 지나치게 클 경우, 버퍼가 채워지는 처리량은 향상되지 않고 딜레이가 추가된다.

> ⭐️ 버퍼는 데이터를 임시로 저장하는 공간을 의미하는데, 데이터를 일정량 버퍼에 모았다가 한꺼번에 처리해서 처리 속도를 높인다고 한다. 
하지만 버퍼가 지나치게 크면, 데이터가 버퍼에 계속 쌓이게 되어 처리 속도가 느려지고, 딜레이가 발생함. 따라서 적절한 버퍼 크기를 유지하면서 네트워크 반응성 향상시키는 것이 중요!

![](https://velog.velcdn.com/images/marisol/post/5e69dd8f-8000-4987-bb88-9e9a669aa82f/image.png)

우리는 일반적으로 패킷이 네트워크를 통해 빠르게 흐르는 인터넷이 이렇게(들어간 패킷이 나오는) 작동할거라고 생각한다.

![](https://velog.velcdn.com/images/marisol/post/c8ad0e39-f6b4-4d96-900e-8dc4b9d8c540/image.png)

하지만 클라우드 내부를 들여다보면, 이런 방식으로 작동한다는 것을 알 수 있다.
_**네트워크에서 나오는 패킷은 들어가는 패킷이 아니다.**_
패킷은 네트워크에서 지나치게 큰 버퍼에 앉아 많은 시간을 보낸다.
_**버퍼가 지나치게 큰 이러한 현상**_을 ```bufferbloat``` 이라고 한다.

![](https://velog.velcdn.com/images/marisol/post/79009a3b-7af1-4712-9653-074339b27089/image.png)

좋은 소식은, bufferbloat을 제거하는 CoDel과 같은 모던 Queue 관리 알고리즘이 있다는 것이다.

네트워크가 Queue를 짧게 유지하면, 패킷이 네트워크에서 대기하는 시간이 크게 줄어든다.
높은 처리량과 낮은 지연을 동시에 얻을 수 있다. 

![](https://velog.velcdn.com/images/marisol/post/e4fbc33a-8c84-40c7-a7a5-ac777e939c54/image.png)

bufferbloat은 앱에서 경험하게 될 네트워크 지연의 큰 구성 요소지만, 유일한 원인은 아니다.
다른 원인으로 아래와 같은 것들이 있다.

- 소프트웨어 및 하드웨어 처리 시간
CPU 속도가 점점 빨라짐에 따라 이 처리 시간은 계속해서 줄어들고 있다.


- 데이터 전송 시간
데이터 전송 속도가 kilobits -> megabits -> 초당 gigabits로 증가함에 따라 전송시간은 계속해서 줄어들고 있다.


- 네트워크 버퍼링으로 인한 딜레이

위와 같은 요인들로 인한 딜레이를 줄이기 위해 다른 업계와 꾸준히 협력하고 있지만,
```신호 전파로 인한 딜레이```는 항상 있을 것이라고 얘기하고 있다.

신호 전파로 인한 딜레이란, 전송된 신호가 실제로 상대방에게 도달하는데 소요되는 시간으로,
빛의 속도로 전파됨에도 불구하고 발생하는 지연 시간이다. (전자파가 공간을 통해 전파되는 속도가 한계가 있기 때문에 발생. 빛의 속도인 초당 30만km를 넘지 못하기 때문에, 전파가 전송되는 거리가 멀수록 전파가 도달하는데 걸리는 시간도 늘어난다.)

1990년대에 미국을 가로지르는 해안에서 해안까지의 Stanford-to-MIT ping time은 이미 100밀리초 미만이었다고 한다.
이미 광속 한계에 상당히 가까워서, 별로 더 나아지지 않을 것이다.

나머지 3가지 딜레이를 줄이기 위해 노력하고 있지만, 신호 전파로 인한 딜레이는 결코 사라지지 않을 것이기 때문에, 네트워크 왕복 시간을 고려하여 앱을 설계하는 것이 중요하다.

![](https://velog.velcdn.com/images/marisol/post/0ef4c44b-9e6f-4caa-a816-a2bb2627fa85/image.png)

어떤 종류의 앱에 네트워크 왕복 시간을 고려해야 할까?
화상회의, 온라인게임 등이 떠오르지만, ```네트워크를 사용하는 모든 앱```에서 고려되어야 한다.
일기 예보, 주식 시세, 네비게이션 등등..

네트워크 왕복 시간은 웹 검색에 영향을 미친다. 또한 스트리밍 비디오를 보는 동안 스킵하는 것에도 영향을 준다.
앱이 네트워크를 응답을 기다리는 동안, 사용자가 앱이 중단되었다고 생각하지 않도록 progress view 등을 보여준다.

사용자가 느린 네트워크를 기다리는 동안 UI에 많은 노력을 기울이는 것도 좋지만,
대기 시간을 줄이는 것에도 많은 노력을 기울여야 한다.

![](https://velog.velcdn.com/images/marisol/post/5c4ecd56-93b6-487a-ae97-8458568486a6/image.png)

앱이 네트워크 데이터를 기다리는 시간은 ```네트워크 round trip 한 번에 걸리는 시간```과 앱이 필요로 하는 ```네트워크 round trip의 수```에 따라 결정된다.

앱 개발자로서 기본 네트워크 round trip 시간을 개선하기 위해 할 수 있는 일이 많지는 않지만, 앱에 필요한 round trip 횟수를 제어할 수 있다.

어떻게 할 수 있는지 다음 챕터에서.

## 2. Adopt modern networking APIs
![](https://velog.velcdn.com/images/marisol/post/bbc3876a-a109-4edf-a089-50dff7b1765c/image.png)

앱 개발자로서 앱의 네트워크 지연을 줄이기 위해 무엇을 할 수 있는지 살펴보려고 한다.
**앱 응답성은 네트워크 round trip 횟수에 반비례한다.**

최신 네트워킹 프로토콜을 채택하여 이러한 네트워크 round trip 횟수를 줄이고, 애플리케이션을 매우 빠르게 만드는 방법을 살펴보자.

![](https://velog.velcdn.com/images/marisol/post/4a996140-040e-4223-8c6f-9569f743c61e/image.png)

앱의 속도를 높이려면, HTTP/3 & QUIC, TCP Fast Open, TLS 1.3 및 Multipath TCP와 같은 최신 네트워킹 프로토콜을 채택해야 한다.
이러한 기술을 사용하면 사용자에게 데이터를 전달하는 과정에서 잠재적으로 여러 번 round trip을 줄일 수 있다.

![](https://velog.velcdn.com/images/marisol/post/ce1ae02c-7607-4b43-a048-5176a86ca9a0/image.png)

이러한 모든 최신 프로토콜은 서버 측 지원이 필요하므로, provider에게 준비되었는지를 확인해봐야 한다.
이런 모든 기술은 iOS와 macOS에서 사용 가능하다.
이제 각 기술에 대해 살펴보자.

![](https://velog.velcdn.com/images/marisol/post/6bf263ff-f2d4-4e38-b030-579eaf189263/image.png)

먼저 iOS 15와 macOS Monterey에서 기본적으로 활성화된 HTTP/3와 QUIC가 있다.
QUIC은 TCP 및 TLS보다 훨씬 빠르게 연결을 설정할 수 있는 전송 프로토콜이다.
QUIC은 head-of-line blocking을 줄임으로써 사용자에게 데이터를 전달하는 지연을 크게 줄일 수 있다.

(head-of-line blocking: 전송된 패킷 중 하나가 네트워크를 통과하지 못하면 그 이후의 패킷도 기다려야 하는 현상)

만약 URLSession을 이미 사용하고 있다면? 모든 준비 끝남.

만약 TLS 프로토콜을 구현하지 않고 Network framework API를 사용하여 자체 application layer를 제공하고 QUIC을 활용하려면, 
QUIC 파라미터를 사용하여 NWConnection을 생성하고 TLS Application-Layer 프로토콜 또는 ALPN을 설정하기만 하면 된다.

(NWConnection: Newtork Framework에서 제공하는 클래스. 네트워크 연결을 생성하고 관리하는데 사용됨. 스트림 전송과 데이트 패킷 처리 지원.)

![](https://velog.velcdn.com/images/marisol/post/fe8acc4f-fcd1-4b0b-9f46-6b8cfe00470c/image.png)

QUIC은 많은 시나리오에서 유용하지만, 일부 앱에서는 TCP가 여전히 적절한 선택일 수 있다. (이미 TCP는 널리 사용되고 있어 어떤 앱에서는 TCP의 안정성이 더 중요한 경우도 있음)
TCP를 사용하는 경우, TCP handshake와 함께 앱 데이터를 전송하여 전체 roundtrip을 제거할 수 있다. == ```TCP Fast Open```
> TCP Fast Open 
- 송신자와 수신자 사이에 세션을 설정할 때, 데이터를 함께 보내 속도를 높이는 방법임. 기존 TCP Handshake 방식은 3-way handshake인데, handshake가 완료된 후에 데이터를 보내기 때문에 handshake를 한번 더 해야함. TCP Fast Open을 사용하면 handshake를 하면서 데이터도 함께 보낼 수 있음!

TCP Fast Open은 Network frame과 Socket에서 지원된다.

NWConnections에서 사용하려면 2가지 옵션이 있다.
첫번째 옵션은 연결에서 Fast Open을 허용하는 것이며, 이 경우 앱은 핸드셰이크와 함께 전송할 초기 데이터를 제공할 수 있다.

이 옵션을 사용하려면 ```allowFastOpen``` 파라미터를 true로 설정하고 connection을 생성한다.
그런 다음 ```start```를 호출하기 전에 초기 데이터와 함께 ```send```를 호출한다.

TCP Fast Open을 사용할 때는 핸드셰이크와 함께 ```Idempotent 멱등한``` 요청만 전송하도록 주의해야 한다.

멱등하다는 것은, 기본적으로 데이터를 재전송해도 안전한 경우를 말한다.

![](https://velog.velcdn.com/images/marisol/post/b885be46-5fb3-411f-98fd-5f92333d9298/image.png)

앱이 직접 초기 데이터를 전송할 필요가 없는 경우  TCP Fast Open을 사용하는 또 다른 방법이 있다.
프로그램이 TCP를 통해 TLS를 사용하는 경우, TLS 핸드셰이크 메시지를 초기 데이터로 보내도록 선택할 수 있다.

이 옵션을 사용하려면 TCP 관련 옵션으로 이동하여 ```enableFastOpen``` 을 true로 설정한다.

![](https://velog.velcdn.com/images/marisol/post/8335ce80-1b7d-4fdc-b58c-1ed2d57163b0/image.png)

TCP Fast Open을 사용할 때 권장되는 방법은, Network Framework API를 사용하는 것이지만,
앱이 Sockets를 통해 구축된 경우 connectx API를 해당 플래그와 함께 호출하여 핸드셰이크와 함께 동일한 데이터를 전송하도록 지정할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/b97c61f4-ab72-48dd-9deb-a2e66aca1489/image.png)

앞서 ```멱등하다``` 라는 것에 대해 얘기가 나왔었는데, 멱등하다는 것이 무엇을 의미하는지, 그리고 왜 멱등한 요청만 핸드셰이크로 보내는 것이 중요한지 설명하려고 한다.

멱등하고 replay-safe한 작업은, 두 번 이상 수행해도 추가 효과가 없는 작업을 말한다.

예를 들어 사용자가 developer.apple.com 웹페이지로 이동하면, 이 웹페이지에 대한 HTTP GET 요청이 TCP 핸드셰이크와 함께 전송된다.

이 요청에 대한 확인 응답이 네트워크에서 지연되거나 삭제된 경우, 디바이스는 다른 서버로 라우팅될 수 있는 HTTP GET 요청을 다시 보낸다.
그리고 이번에는 HTTP 응답과 함께 승인이 도착했다.

HTTP GET 요청은 네트워크를 통해 다시 전송될 때 추가적인 효과가 없으므로, **멱등한 요청**으로 간주된다.

![](https://velog.velcdn.com/images/marisol/post/2bde891d-b514-42c6-a00e-a72ea1bf51f6/image.png)

이제 사용자가 새로운 아이폰 12를 사려고 한다고 가정해보자.
이 작업에 대해 보낸 HTTP 요청은 멱등한 요청이 아니다.

데이터가 네트워크를 통해 재생될 때마다, 요청이 다른 서버로 전송되면 여러 트랜잭션이 발생할 수 있다.

이 점을 염두에 두고 TLS 1.3에 대해 살펴보자.

![](https://velog.velcdn.com/images/marisol/post/4e377685-2b32-48b7-897c-bc9919260aed/image.png)

TLS 1.3은 TLS 1.2와 비교하여 1번의 round trip을 제거하여 핸드셰이크의 속도가 빠르고, 보다 강력한 보안을 제공한다.
iOS 13.4부터 URLSession 및 NWConnection이 기본적으로 활성화되어 있다.
TLS 1.3에서는 early data 기능을 제공하는데, 이 기능은 TLS 핸드셰이크와 함께 멱등한 요청을 보낼 수 있도록 해준다.

![](https://velog.velcdn.com/images/marisol/post/38e64ac4-eb69-4e96-a725-b11618973641/image.png)

네트워크 지연을 줄이는 데 약간 다르게 동작하는 Multipath TCP에 대해 알아보자.
Multipath TCP (다중 경로 TCP)를 사용하면 디바이스가 한 네트워크에서 다른 네트워크로 전환될 때 단일 TCP 연결을 계속할 수 있다.
Multipath TCP의 낮은 지연 기능을 사용하려면, interactice mode API를 사용할 수 있다.
그러면 새 연결을 설정하는 데 필요한 왕복이 모두 절약되고, 시스템은 데이터 패킷에 대해 더 빠른 네트워크 경로를 자동으로 선택한다.
클라이언트에서 선택하려면 ```URLSessionConfiguration``` 또는 ```NWParamters```에서 ```multipathServiceType``` 프로퍼티를 ```interactive``` 로 설정하면 된다.

![](https://velog.velcdn.com/images/marisol/post/19162e07-2d17-492b-8231-be0f0cbd9e4e/image.png)

이러한 최신 기술로 얼마나 많은 round trip을 절약할 수 있는지를 보기 위해, 참조 포인트부터 살펴보자.
현재 앱이 TCP를 통해 TLS 1.2를 실행하고 있다고 가정해보자.
이 경우, 첫 번째 바이트를 사용자에게 전달하려면 round trip 4회가 필요하다.
> 1. 클라이언트는 서버에게 클라이언트 버전, 암호화 알고리즘, 압축 알고리즘 및 랜덤 데이터를 보냄.
2. 서버는 클라이언트에게 서버 버전, 선택된 암호화 및 압축 알고리즘, 인증서, 랜덤 데이터를 보냄.
3. 클라이언트는 서버 인증서를 검증하고 세션 키를 생성함. 클라이언트는 서버에게 인증서 검증 및 세션 키 생성이 완료되었음을 알림.
4. 서버는 세션 키 생성이 완료되었음을 알림.
(위 과정 2 round trip + 데이터 교환을 위해 2 round trip 더 소요됨 -> 총 4 round trip 필요)

서버가 TLS 1.2에서 TLS 1.3으로 전환되는 경우, 연결을 통해 전체 왕복이 제거된다.
연결에서 TCP Fast Open을 활성화하면, 왕복이 한 번 더 저장된다.
iOS 15에서 HTTP/3 over QUIC는 왕복 2회로 단축된다.
QUIC 프로토콜은 early data 기능을 제공하며, 이를 통해 round trip을 1회로 추가 절감할 수 있다.

Apple이 측정한 결과에 따르면, round trip 시간이 600miliseconds까지 치솟기도 한다고 한다.
그것이 앱에 어떤 의미냐면,

![](https://velog.velcdn.com/images/marisol/post/0ae2ce83-7083-4d10-b754-b8681e980dd2/image.png)

600miliseconds의 왕복 4회는, 사용자가 데이터가 도착할 때까지 거의 2.5초를 기다린다는 것을 의미한다.

![](https://velog.velcdn.com/images/marisol/post/59a8412b-2efb-4454-8df1-fd38818f4050/image.png)

최신 네트워킹 프로토콜을 채택하여, 첫 번째 바이트까지의 시간을 2.4초에서 약 0.5초 가까이까지 줄일 수 있다.
데이터가 1.5초 일찍 도착하면, 사용자는 분명 차이를 알아차릴 것이다.
뛰어난 네트워크 성능을 원하는 모든 개발자는 round trip 횟수에 주의해야 한다.

방금까지 말한 모든 기술은 실제 네트워크 조건에서 앱의 네트워크 지연을 줄이는 데 도움이 된다.
5G, LTE 또는 고속 Wi-Fi 네트워크에서 앱을 테스트하면, 앱 응답성이 괜찮아 보일 수 있다.

![](https://velog.velcdn.com/images/marisol/post/7a40cc08-8c2c-49ef-b420-e0eb52e2bcef/image.png)

하지만 사용자가 항상 최상의 네트워크 환경에서 앱을 사용하는 것은 아니다.
현실적인 네트워크를 시뮬레이션하기 위해, 개발자 설정 메뉴에서 iOS용 ```Network Link Conditioner``` 툴을 사용할 수 있다.

이 툴은 사용자가 일상생활에서 경험할 수 있는 다양한 네트워크 조건에서 앱을 테스트할 수 있는 신뢰할 수있고 반복 가능한 방법이다.
![](https://velog.velcdn.com/images/marisol/post/7f81f57d-df4e-4631-8d61-d30500e056d4/image.PNG)
![](https://velog.velcdn.com/images/marisol/post/4efe7e99-a8c8-4f9c-88f5-c00106d27c34/image.PNG)


## 3. Inform the system about your traffic

앞선 세션 발표자가 네트워크 왕복 시간을 줄이는 것에 대해 앱 개발자가 할 수 있는게 많지 않다고 했는데,
완전 사실은 아니다.
앱 트래픽을 시스템에 올바르게 알릴 때 네트워크 왕복 시간을 줄일 수 있는 방법에 대해 설명하려고 한다.

![](https://velog.velcdn.com/images/marisol/post/9059e944-8d00-44c4-8a07-0e224294003d/image.png)

![](https://velog.velcdn.com/images/marisol/post/758f4d69-2fee-4b63-8084-833126ae45bf/image.png)

대부분의 앱에는 송수신하는 트래픽이 혼합되어 있다.
많은 앱을 실행할 때 사용자의 기기에서는 많은 데이터가 교환된다.

![](https://velog.velcdn.com/images/marisol/post/4cfa5ea5-5af0-446c-b182-3e2902fab8cb/image.png)

가정이나 사무실 Wi-Fi와 같은 실제 네트워크에서는 여러 디바이스가 동일한 네트워크를 공유한다.
이러한 장치는 일련의 앱을 사용하는 동안 상당한 양의 데이터를 동시에 주고 받는다.

이 공유 네트워크에서 긴 queue가 쌓이지 않도록 하려면, 네트워크 지연을 낮게 유지하기 위해 시스템이 트래픽을 효율적으로 관리할 수 있도록 앱 데이터를 적절하게 분류하는 것이 중요하다.

![](https://velog.velcdn.com/images/marisol/post/be9d6f6d-2f08-4013-ac65-6ba586081786/image.png)

시스템 네트워크 지연 시간을 낮게 유지하도록 하면, 앱의 포그라운드 트래픽이 더 빨라지므로
사용자에게 가장 중요한 데이터가 신속하게 전달된다.

![](https://velog.velcdn.com/images/marisol/post/39a362b0-083b-4b7b-b4c7-97a6d01aa903/image.png)

예를 들어 설명하자면, 많은 앱이 그래픽, 오디오 파일 등의 컨텐츠를 나중에 사용할 수 있도록 prefetch한다.
앱이 상당한 양의 데이터를 prefetch할 때, 네트워크는 이렇게 보일 수 있다.
병목현상으로 queue가 가득찰 수 있다.

![](https://velog.velcdn.com/images/marisol/post/82dd57a1-6618-44ed-a66a-9e1be08298de/image.png)

이 시점에서 사용자가 프로필 페이지 보기와 같은 네트워크 활동을 시작할 경우,
이 요청에 대한 응답은 네트워크 queue의 맨 끝에 저장되며, 프로필이 표시되기까지 몇 초가 걸릴 수 있다.
이것은 좋은 UX가 아니다.

![](https://velog.velcdn.com/images/marisol/post/b41ceba6-4e41-4bcb-87d9-713071fe924c/image.png)

이제 background로 task를 prefetching하면 네트워크에 어떤일이 발생하는지 보자.
이러한 사용자가 요청하지 않은 태스크(개발자가 처리하는 prefetching)를 백그라운드로 표시하면 네트워크 queue의 크기가 크게 줄어들어 다른 포그라운드 데이터에 사용할 수 있게 된다.

```
let config = URLSessionConfiguration.default
config.isDiscretionary = true // 백그라운드로 데이터 전송 처리

let session = URLSession(configuration: config)

let url = URL(string: "https://www.example.com")
let task = session.dataTask(with: url!) { (data, response, error) in
    // handle response
}
task.resume()

```

![](https://velog.velcdn.com/images/marisol/post/e65d43fb-8f27-4f60-872b-c5675682ccd5/image.png)

따라서 녹색 패킷은 신속하게 즉시 전달될 것이다.

iOS 15와 macOS Monterey에서 백그라운드 서비스 타입이 획기적으로 개선되었다.
백그라운드 업로드 및 다운로드를 위한 새로운 혼잡 제어 알고리즘이 추가되었다.

이러한 새로운 알고리즘은 네트워크 지연을 크게 줄여 UX를 개선할 뿐만 아니라,
백그라운드 전송이 다른 트래픽과 거의 동시에 완료되도록 해준다.

![](https://velog.velcdn.com/images/marisol/post/d49d7405-83a9-44a1-aa5d-97090e27f314/image.png)

백그라운드 서비스 타입을 활용하기 위해 채택할 수 있는 Networking API를 살펴보자.

앱이 포그라운드에 있고, 사용자가 시작하지 않은 전송(우리가 하는 prefetching 등)을 수행하는 경우,
기본 URLSession을 사용하고 URL 요청에 대해 네트워크 서비스 유형을 백그라운드로 설정한다.
이를 통해 시스템은 낮은 네트워크 지연을 유지할 수 있다.

그리고 NWConnection의 경우, NWParameters에서 serviceClass를 background로 설정한다.

![](https://velog.velcdn.com/images/marisol/post/fc8c07d1-1a78-4b72-a08b-f2f8b785b059/image.png)

앱이 긴 시간동안 수행되는 전송 작업을 시작하려면, 백그라운드 URLSession을 만들어 앱이 일시 중단된 경우에도 계속 실행할 수 있다.
시간이 민감하지 않은 작업의 경우, ```isDiscretionary``` 속성을 true로 설정할 수 있다. 이렇게 하면 시스템이 전송을 수행하기에 최적의 조건을 기다릴 수 있다. (시간에 민감한 작업은 최대한 빠르게 전송되도록 isDiscretionary 속성을 false로 설정해야함)

![](https://velog.velcdn.com/images/marisol/post/cd5dec3d-a6d5-4c53-bb15-d47457f33ff1/image.png)

지금까지 앱이 네트워크 대기열을 짧게 유지하는 데 어떻게 도움이 되는지에 대해 이야기 했는데,
또 다른 지연 원인은 송신 디바이스 자체에 있을 수 있다.

과거에는 네트워킹 스택에서 매우 큰 전송 버퍼를 사용했다. 이로 인해 패킷이 네트워크에 들어가기도 전에 불필요한 지연 시간이 종종 초 단위로 늘어나기도 했다.
2015년에 URLSession과 NWConnection을 통해 이 문제를 해결했었다.
하지만 인터넷에 있는 대부분의 서버는 리눅스에서 실행되면, BSD 소켓을 사용한다.

서버 운영자에게 문의하여 TCP가 낮은 워터마크 소켓을 전송하지 않음 옵션(TCP_NOTSENT_LOWAT)을 사용하여 소스의 지연을 줄이도록 할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/c2e1849b-5758-4407-ae85-0a1f3a938ed5/image.png)

최신 네트워킹 프로토콜을 채택하여 여러 번의 round trip을 방지하고,
asset prefetching, 대량 전송 및 긴급하지 않은 작업에 백그라운드 모드를 사용하고,
다양한 네트워크 조건에서 앱의 성능을 테스트해라. (Network Link Conditioner 툴 사용)
네트워크 지연을 낮게 유지 -> 앱 응답성 향상, 전반적 UX 향상!
