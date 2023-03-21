# What's new in SwiftUI

![](https://velog.velcdn.com/images/marisol/post/f13db02d-25f6-49f3-8a06-6737a80e38f0/image.png)

모던 네트워킹 기술을 사용하여 iOS 앱의 성능과 보안을 최적화하는 방법을 소개하는 세션이다.

Apple 플랫폼에는 견고한 보안 기능이 내장되어 있다. 디바이스가 서로 다른 네트워크 간에 이동할 때 강력한 이동 환경을 제공한다.
또한 사용자의 개인 정보를 보호한다.

앞으로 설명할 내용의 대부분은 클라이언트 디바이스와, 클라이언트 디바이스가 소통하는 서버 모두에서 지원되어야 하는 기술과 관련이 있다.

![](https://velog.velcdn.com/images/marisol/post/08f8bfec-da49-423b-9446-4805f70d91dc/image.png)

클라이언트에서 이미 Apple 플랫폼, URLSession 및 Network.framework에서 최신 네트워킹 API를 사용하고 있다면 이러한 모든 기술에 대한 지원을 자동으로 받을 수 있다.

## 1. Performance
![](https://velog.velcdn.com/images/marisol/post/d11b7121-4045-43a5-b772-3b94824589ea/image.png)

![](https://velog.velcdn.com/images/marisol/post/05bf3847-f0c8-49d6-9ca5-d49d8faff975/image.png)

IPv6는 인터넷을 지원하는 기본 프로토콜이며, 기존 32비트의 IPv4 주소가 고갈되는 문제를 해결하기 위해 개발된 새로운 인터넷 프로토콜이다.
애플 플랫폼은 IPv6 전용 네트워크 지원을 포함하여 수년간 네이티브 IPv6 지원을 해왔다.
네트워크에서 IPv6을 사용하는 연결은 지연시간이 짧고, 일반적으로 IPv4보다 성능이 우수한데, NAT 수가 적고, 최신 네트워킹 장비가 더 많기 때문이다.

> 📌 NAT(Network Address Translation): 네트워크 주소 변환. 여러 대의 호스트가 하나의 공인 IP 주소를 사용하여 인터넷에 접속하기 위해 쓰임. 
여러 명이 동시에 인터넷을 접속하게 되므로, 실제로 접속하는 호스트 숫자에 따라 접속 속도가 느려질 수 있다. IPv6에서는 IP 개수가 사실상 무한에 가깝고, End-to-End 연결을 이용하기 때문에 NAT 기능이 일반적으로 필요없다.

App Store 제출 요구 사항이므로, Mac에서 NAT64를 사용하여 IPv6 전용 네트워크에서 애플리케이션이 작동하는지 테스트해볼 수 있다. 만약 최신 네트워킹 API를 사용한다면, 정상적으로 작동할 것이다.

> 📌 NAT64: IPv4-to-IPv6 전환 및 IPv4-IPv6 공존을 위한 메커니즘. IPv4랑 IPv6간 통신을 지원하기 위한 기술

![](https://velog.velcdn.com/images/marisol/post/51c7701b-e06f-4bbd-a783-c0dff55c026b/image.png)

인터넷에서 IPv6 사용이 증가 추세를 보이고 있다. 전세계적으로 애플 디바이스들의 지난 달 TCP 연결 현황을 보면, IPv6가 현재 전체 연결의 26%를 차지하는 것을 알 수 있다.
20%의 경우, 연결이 IPv6를 사용할 수 있었지만, 서버에서 IPv6를 사용할 수 없었던 경우이다.
또한 IPv6를 사용할 경우, 중앙값 연결 설정 시간(IPv6과 IPv4 연결을 설정하는 데 각각 걸리는 시간을 비교했을 때 어느 쪽이 더 빠른 연결 설정 속도를 보이는지 비교한 시간)이 IPv4보다 1.4배 더 빠르다. 이것은 주로 NAT 사용량이 감소하고, 라우팅이 개선되었기 때문이다.

![](https://velog.velcdn.com/images/marisol/post/ddd2dbcb-af7e-432d-913d-5f5ee803710e/image.png)

클라이언트에서 URLSession 또는 Network.framework를 사용하여 IPv6가 제공하는 향상된 기능을 활용할 수 있다. 이제 사용자에게 최상의 환경을 제공하기 위해 서버에서 IPv6를 사용하도록 설정했는지 확인해야 한다.

![](https://velog.velcdn.com/images/marisol/post/0027482c-5244-43c4-9b15-6ded69ec7afd/image.png)

다음으로, URLSession은 로드 성능을 향상시키는 빌트인 HTTP/2 지원을 제공한다.
HTTP/2는 동일한 서버에 대한 여러 요청을 단일 연결로 만든다.(Multiplexing) 이렇게 하면 다음 요청을 보내기 전에 각 응답이 끝날 때까지 기다릴 필요가 없으므로 시간이 절약된다.
HTTP/2는 또한 연결 병합을 통해 성능 향상을 제공한다. 시스템은 동일한 서버에서 서로 다른 요청을 처리하고, 기존 연결을 재사용할 수 있음을 감지하여 연결 설정 비용을 절약한다.

헤더 압축 지원을 통해 요청 헤더와 응답 헤더 모두에서 불필요한 바이트를 제거할 수 있어, 대역폭 활용률이 향상된다.

(HTTP/2는 웹페이지 로딩 속도를 높이는데 매우 유용하고, 모바일 기기와 같이 대역폭이 제한된 환경에서도 높은 성능을 발휘한다고함!)

![](https://velog.velcdn.com/images/marisol/post/b16304a3-90f5-43ab-a6c3-958616d995e8/image.png)

Safari에서 HTTP 사용량을 살펴보면, 지난 한 달 동안 요청의 79%가 HTTP/2를 사용하고 있으며, HTTP/2를 사용하는 요청에 대한 URLSession task는 HTTP/1.1을 사용하는 요청에 비해 1.8배 더 빠르다.

![](https://velog.velcdn.com/images/marisol/post/a68bfc9d-6cc8-4ac5-a0f6-eb8fce70bd16/image.png)

클라이언트에서 URLSession을 사용하면, 서버에서 사용 가능한 경우 기본적으로 HTTP/2를 사용한다.

## 2. Security
![](https://velog.velcdn.com/images/marisol/post/55617c5d-1fea-4016-8a7d-053e8283d736/image.png)

![](https://velog.velcdn.com/images/marisol/post/16288837-f5f0-4ebc-a80d-703aa268bdd3/image.png)

TLS 1.3은 최신 버전의 TLS로, handshake에서 왕복 1회를 제거하여 연결 설정 시간을 단축한다. (핸드쉐이크 과정에서 1라운드만에 연결을 설정할 수 있어, 연결 속도를 향상시킴)
TLS 1.3은 TLS 1.2보다 암호화 방식을 단순화하여, 구현의 복잡성을 줄이고 보안성은 강화했다고 한다.
iOS 12와 macOS Mojave에서는 TLS 1.3의 초기버전을 활성화하여 서버 배포와 테스트할 수 있는 프리뷰를 사용할 수 있다.

iOS 13.4부터 URLSession 및 Network.framework용으로 TLS 1.3이 기본적으로 활성화되었다.

![](https://velog.velcdn.com/images/marisol/post/03c1f1df-d7bb-4b12-b1e7-00a94dd9c34e/image.png)

지난 한 달 동안, 모든 연결의 약 49%가 최신 iOS를 실행하는 기기에서 TLS 1.3을 사용한 것을 확인할 수 있다.
그리고 TLS 1.3을 사용하는 연결은 TLS 1.2를 사용하는 연결보다 1.3배 더 빠르게 설정된다.

![](https://velog.velcdn.com/images/marisol/post/b077fd8a-9c8b-44fc-b0f1-d0e6964549ea/image.png)

서버에서 TLS 1.3이 활성화되어 있으면, 최신 네트워킹 API가 기본적으로 TLS 1.3을 사용한다.

## 3. Mobility
![](https://velog.velcdn.com/images/marisol/post/aea9c088-0999-481c-bceb-bec1d8266f8f/image.png)

이제 디바이스가 다른 네트워크로 전환될 때 원활한 환경을 제공하는 방법을 살펴보려고 한다. 

![](https://velog.velcdn.com/images/marisol/post/f27683c2-c8fb-4de7-951c-4673a9a9099e/image.png)

Multipath TCP를 사용하면 디바이스가 네트워크를 변경해도 앱에서 단일 TCP 연결을 계속 유지할 수 있다.
이렇게하면 연결이 불안정하거나, 사용자가 네트워크를 이동하더라도 앱을 처음부터 다시 시작할 필요가 없다.
클라이언트에서는 URLSessionConfiguration의 multipathServiceType 프로퍼티를 설정하거나 Network.framework를 사용하는 경우 NWParameters 객체에서 설정하여 Multipath 지원을 선택할 수 있다.

Apple 서비스에서 Multipath TCP를 사용해서 큰 개선이 있었다.
작년에 Siri 외에도 Apple Music용 Multipath TCP도 활성화할 것이라고 발표했다.

![](https://velog.velcdn.com/images/marisol/post/0306e899-e9ca-4eef-8ba9-4178576c2a3b/image.png)

그 이후로 music 중단이 13% 감소했다. 또한 중단이 발생할 경우, 중단 시간이 22% 감소했다.

![](https://velog.velcdn.com/images/marisol/post/94a209cd-7e39-4a20-a7f7-4cc730c18ea4/image.png)

Apple 플랫폼에서는 configuration이나 parameters에서 multipathServiceType 프로퍼티를 설정하여 Multipath 프로토콜을 쉽게 사용할 수 있다. 그러나 서버에서 Multipath TCP를 활성화하려면 추가 작업이 필요하다. 서버 배포에서 Multipath TCP를 활성화하기 위한 지침을 찾으려면 multipath-tcp.org를 방문해서 확인하면 된다.

## 4. Privacy

![](https://velog.velcdn.com/images/marisol/post/a6caa8c3-74fe-4e1d-9533-bcae9f5bf005/image.png)

iOS 14에서는 로컬 네트워크의 개인정보 보호를 강화하기 위해 새로운 개인정보 보호 기능을 도입했다.
앱과 third-party 라이브러리, 또는 SDK가 네트워크에 있는 다른 디바이스를 사용하여 사용자를 식별하거나 추적하는 것을 방지할 수 있다.

AirPrint, AirPlay 및 HomeKit과 같은 빌트인 시스템 서비스는 로컬 네트워크에 대한 개인 정보를 앱에 제공하지 않는다. 하지만 만약 멀티캐스트 및 브로드캐스트 사용을 포함한 로컬 네트워크 리소스에 직접 접근하려면, 명시적인 사용자의 권한 허용이 필요하다.

앱의 Info.plist에 이유에 대한 문자열을 제공해서 앱이 로컬 네트워크를 어떻게 사용하는지 사용자가 이해할 수 있도록 해야한다.

(이전에는 앱이 로컬 네트워크 자원에 액세스하는 것에 대한 권한이 암묵적으로 주어졌지만, iOS14부터 사용자에게 앱이 로컬 네트워크를 사용하는 이유를 제공하고, 명시적인 허가를 얻어야 함. 예를 들어, 앱이 로컬 네트워크 자원에 액세스하여 프린팅 기능을 제공하려면 "프린트를 하기 위해 로컬 네트워크에 접근합니다." 같은 이유에 대한 문자열을 제공해야 한다.)

![](https://velog.velcdn.com/images/marisol/post/d9f04a64-5d24-4ce1-a684-993e80c99274/image.png)

또한 iOS 14와 macOS BigSur에 새로 지원되는 기능으로는 DNS-over-TLS와 DNS-over-HTTPS를 포함한 안전한 도메인 이름 해결(secure domain name resolution: 도메인 이름을 해석하는 것을 보안화하는 것. 중간자 공격 방지하고 사용자의 개인정보 보호함.)이 있다.

각 앱이나 브라우저마다 개별적인 해결 서비스를 제공하는 대신,
이 기능은 시스템 해결 프로그램(system resolver: 운영체제에서 DNS 해석 요청을 처리하는 서비스)에 적용되어 안전한 DNS를 구성하면, 디바이스의 모든 앱이 이점을 얻을 수 있다. (모든 앱이 동일한 시스템 리졸버를 사용하기 때문에, 하나의 설정으로 모든 앱이 DNS-over-TLS 및 DNS-over HTTPS를 사용할 수 있는 것이다.)

NetworkExtension을 사용하여 이러한 시스템 전체 설정을 제공하는 앱을 작성하는 것 외에도, 앱에서 암호화된 해결(encrypted resolution: DNS-over-TLS 또는 DNS-over-HTTPS와 같은 보안 프로토콜을 사용하여 DNS 요청 및 응답을 암호화함.)을 요구할 수도 있습니다.

> DNS: 도메인 이름을 IP 주소로 변환하는데 사용되는 프로토콜. 이 프로토콜을 암호화하면 중간자 공격 및 감청을 방지할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/de775ba5-c2e9-4499-91bb-fd289e1765fe/image.png)

암호화된 해결책을 위한 시스템 전체 설정을 클라이언트에서 NetworkExtension을 사용하여 앱을 작성해서 전달할 수 있다.
DNS provider가 DNS-over-HTTPS 옵션을 제공하지 않은 경우, 해당 옵션을 제공할 것을 요청할 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/0e837434-8508-4995-96fa-a05c618e960f/image.png)

![](https://velog.velcdn.com/images/marisol/post/e61c9c9a-e35a-4368-93ff-26e4a3d46f9b/image.png)

암호화된 DNS를 사용하여 name resolution을 보다 프라이빗하게 설정한 경우에도, 서버와 TLS handshake를 수행할 때마다 클리어 텍스트 서버 이름 표시(SNI)를 제3자가 관찰할 수 있다.

따라서 현재 IETF와 협력하여, 더 많은 TLS handshake를 암호화하는 방법을 표준화하여 제3자가 트래픽을 훔쳐볼 수 없도록 하고 있다.
이는 특히 암호화된 DNS와 함께 사용하면, 통신 중인 서버 간의 네트워크 통신이 안전하게 보호된다.

![](https://velog.velcdn.com/images/marisol/post/e2df8d41-a346-4839-abc2-115aaca89f9b/image.png)

HTTP3는 새로운 QUIC 전송 프로토콜을 기반으로 구축된 차세대 HTTP이다. 

> QUIC(Quick UDP Internet Connections)은 구글에서 개발한 범용 목적의 전송계층 통신 프로토콜이다. QUIC는 TCP를 사용하는 연결 지향 웹 애플리케이션의 성능을 개선한다. HTTP/2의 다중화 연결과 협력하여 동작하며, 여러 데이터 스트림들이 개별적으로 모든 종단점에 도달할 수 있고, 다른 스트림을 수반하는 패킷 손실에 독립적이다.

![](https://velog.velcdn.com/images/marisol/post/d3c97daf-9f2e-41a8-8fef-2eef9e2c3774/image.png)

이 전송 프로토콜에는 TLS 1.3 보안이 내장되어 있으며, HTTP/2와 동일한 다중 스트림 지원을 제공하지만,

![](https://velog.velcdn.com/images/marisol/post/7fb6179a-3a14-456d-85dc-b7ab54e72bbe/image.png)

개별 요청이나 응답의 손실이 잠재적으로 관련이 없는 다른 메세지의 지연을 유발하지 않도록 head-of-line blocking을 더욱 감소시켰다.

![](https://velog.velcdn.com/images/marisol/post/6b02b116-534d-458a-bae9-d3e866b3176d/image.png)

QUIC을 사용하는 HTTP/3는 개선된 혼잡 제어 및 분식 패킷 복구를 위해 더 높은 정밀도의 정보를 제공한다.

![](https://velog.velcdn.com/images/marisol/post/acb8b4bd-165e-4a3c-953c-4484c7992ae0/image.png)

또한 네트워크 전환으로 인해 진행 중인 작업이 실패하지 않도록, 이동성 지원 기능이 내장되어 있다. 
이로 인해 작업을 중단하지 않고 새로운 네트워크에서 작업을 계속할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/fc7077f3-0f1f-4b44-a84a-aaa9b0e450f4/image.png)

![](https://velog.velcdn.com/images/marisol/post/f20cb68d-31ee-4753-a714-df99fd1f4030/image.png)

HTTP/3는 여전히 IETF에서 진행중인 사양이며, HTTP/3가 전세계적으로 배포될 수 있도록 협력하고 있다.
이 방향으로 중요한 첫 단계로, iOS 14와 macOS Big Sur에는 URLSession을 사용하는 앱의 HTTP/3 지원 실험적 미리보기가 포함되어 있으며, 개발자 설정에서 활성화할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/eb20b80b-07ac-402f-b965-839474577f97/image.png)

macOS BigSur의 경우, CFNetworkHTTP3Override 사용자 기본값을 설정하여 URLSession을 사용하는 앱에 대한 HTTP/3 지원을 활성화할 수 있으며, Safari의 Develop -> Experimental Features를 통해 활성화할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/a6c66520-bcbc-4802-a889-c17175335803/image.png)

IPv6, HTTP/2, TLS 1.3, Multipath TCP, 암호화된 DNS를 활용하여 성능, 보안, 이동성 및 개인 정보 보호 이점을 제공하는 방법에 대해 살펴본 세션.

![](https://velog.velcdn.com/images/marisol/post/281ab5c9-0398-4671-b36a-3b8523fb4967/image.png)

이 모든 기술은 현재 Apple 플랫폼의 최신 네트워킹 API에서 지원되므로, 애플리케이션에서 URLSession 또는 Network.framework를 사용해야 한다. 그런 다음 서버 배포와 함께 테스트하여 모든 것이 최신 상태인지 확인하고, 이러한 기능이 사용 가능한지 확인하여 오른쪽(Server)을 채울 수 있다.
