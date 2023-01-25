# Modernizing Grand Central Dispatch Usage

[영상](https://developer.apple.com/videos/play/wwdc2017/706/)

![](https://i.imgur.com/uncdCEt.png)

최적의 성능을 얻기 위한 iOS 11에서의 GCD(Grand Central Dispatch) 사용법

![](https://i.imgur.com/IQ7ul44.png)


사용자가 어떤 하드웨어에서 앱을 실행할 것인지 개발자들은 너무 많이 신경쓸 필요가 없다.
싱글 코어인 Apple Watch부터 미니 코어인 Mac까지 GCD를 통해 우리는 여러 기기에 걸쳐 애플리케이션의 코드를 확장할 수 있기 때문이다. 

![](https://i.imgur.com/WY1gFX2.png)

dispatch_async와 같이 GCD API를 사용하여 Queue를 생성하고 작업을 디스패치하는 것은 Grand Central Dispatch라고 부르는 concurrency technology의 일부이다.

![](https://i.imgur.com/pThHl4j.png)

Device에 사용되는 칩은 점점 더 빨라지고 있다. 빨라진다는 것은 코드 실행을 점점 똑똑하게 코드를 실행한다는 의미.
또한 시간이 지남에 따라 보다 효율적으로 동작하기 위한 학습을 통해 능률을 향상시킨다.


## Overview

1. 병렬과 동시성 표현 방법
2. GCD를 통한 동시성 표현
3. GCD의 주요 개선 사항인 Unified Queue Identity
4. 코드에서 문제 지점을 찾는 방법



## Paralleslism and Concurrency

**병렬(Paralleslism)**

![](https://i.imgur.com/6H2lk2u.png)

![](https://i.imgur.com/2p8t7Ft.png)

하나의 연산들이 동시에 실행되는 것



**동시성(Concurrency)**

독립적으로 실행되는 여러 동작들의 조합

![](https://i.imgur.com/v3DM37t.png)

이와 같은 앱을 구성한다면 네트워크 부분, 데이터베이스 부분 등 독립된 서브시스템으로 나눠 구성할 것이다.

버튼을 클릭하고 뉴스 앱에서 목록을 새로고침하면 다음과 같이 동작한다.

![](https://i.imgur.com/uhMw1Ck.png)

이때 CPU에서 일어나는 일
- 문맥 교환(Context Switching)


Context Switching과 Concurrency

![](https://i.imgur.com/DwA4IDu.png)

문맥 교환이 일어나는 원인
- 우선 순위가 높은 스레드가 CPU를 필요로 할 때
- 작업 중인 스레드가 작업을 완료
- 자원를 얻기 위해 대기중인 스레드가 있음
- 비동기 요청이 완료되기를 기다리고 있음

문맥 교환 덕분에 동시성을 활용하여 Responsive한 App을 구축할 수 있게 되었으나 동시에 문제점도 갖고 있다.
- 잦은 문맥 교환은 오버헤드가 증가하여 성능이 떨어진다.
- 순서를 기다려야 하므로 지연의 위험

![](https://i.imgur.com/Wx01a1P.png)

과도한 문맥 교환을 유발할 수 있는 원인 세 가지
- 자원에 대한 접근 대기 반복
- 독립적인 동작들 사이의 스위칭 반복
- 스레드 간 반복적인 작업 교환


### Lock Contention

![](https://i.imgur.com/XBS1kTa.png)

자원에 대한 접근 대기가 발생하는 경우는 Lock과 관련이 있다.
자원에 대한 Lock이 존재하고 많은 스레드가 해당 리소스를 획득하려고 시도하는 경우이다.

![](https://i.imgur.com/HTzj2JA.png)

자원이 많은 스레드에게 번갈아가면서 할당되고 이 때문에 문맥 교환이 발생한다.

자원 획득에 대한 잠금을 통해 불필요한 문맥 교환을 방지할 수 있다.

Fair Lock vs Unfair Lock
![](https://i.imgur.com/XB5XVdK.png)

![](https://i.imgur.com/21ZLbr3.png)

Unfair Lock의 경우 대기 스레드의 획득 순서를 보장해주지 않음.

![](https://i.imgur.com/J0gKVcd.png)

Lock Ownership
- 우선 순위 반전(priority inversion)을 해결하기 위한 방법


## Using GCD for concurrency


### Serial Dispatch Queue

![](https://i.imgur.com/3SsPslP.png)

Serial Dispatch Queue는 가장 기본적인 형태이며 다음과 같은 특징을 갖고 있다.
- 상호 배제
- FIFO
- 여러 스레드가 큐에 동시에 작업을 enqueue할 수 있음
- 단일 dequeue 스레드

사용 예시를 보자. Dispatch Queue를 생성하여 직렬 큐를 만들고 두 개의 스레드에 의해 큐를 호출하고 있다고 가정한다.

![](https://i.imgur.com/cq77SBA.png)

비동기 메서드 호출이기 때문에 메서드가 즉시 반환되고 스레드가 계속 진행되어 `3`이 호출된다.


### Dispatch Source

![](https://i.imgur.com/2dqpr8d.png)

GCD의 이벤트 모니터링 프리미티브이다. 동작은 다음과 같다.
- `makeReadSource`: dispatch source 생성
- `setEventHandler`: 이벤트 핸들러의 동작 설정
- `setCancelHandler`: invalidation 구현
- `activate`: dispatch source 호출하여 모니터링 시작

### Target Queue Hierarchy

![](https://i.imgur.com/veTiWXA.png)

Serial Queue를 트리 구조로 구성한 형태를 의미한다.
이런 식으로 큐를 구성하면 Q1과 Q2를 EQ에서 총체적으로 관리하게 된다.

![](https://i.imgur.com/krpikDH.png)

![](https://i.imgur.com/8so6mIS.png)

누가 먼저 실행될 것인지 EQ에서 선택하게 된다.


### Quality of Service

![](https://i.imgur.com/WW7bKQ6.png)

큐에 들어올 작업의 우선 순위를 나타낸다. 


## Granularity of Concurrency

![](https://i.imgur.com/OMVcJha.png)

네트워크 서브시스템에서 연결을 모니터링하는 작업을 다음과 같이 하나의 연결마다 하나의 큐를 생성해서 구성했다고 치자.
여러 네트워크 연결이 생긴다면 그림과 같은 형태가 될 것이다.
![](https://i.imgur.com/hE5KdPJ.png)


이렇게 되면 과도한 문맥 교환이 발생한다. 이를 위에서 설명한 단일 상호배제 큐와 계층 구조를 통해 개선할 수 있다.

![](https://i.imgur.com/dpAO1E0.png)


맨 아래에 Serial Queue가 있기 때문에 여러 스레드 대신 단일 스레드에 와서, 순서대로 실행된다.

![](https://i.imgur.com/rlLXizW.png)

이 방법을 통해서 실제로 1.3배의 성능 향상을 얻었다고 함


### Avoid Unbounded Concurrency

![](https://i.imgur.com/Nnvkjej.png)

![](https://i.imgur.com/aNViQbO.png)

무분별한 동시성을 피하기 - 독립된 작업 간 반복적인 스위칭이 일어나는 상황은 다음과 같다.
- 많은 대기열들을 한 번에 모두 활성화될 때
    - 클라이언트 소스 별 독립 패턴
    - 객체 별 큐 독립 패턴
- 글로벌 큐에 너무 많은 항목들이 들어왔을 때
    - Thread Explosion의 위험성이 있음

![](https://i.imgur.com/49FeTTg.png)

따라서 이러한 문제를 피하기 위해 하나의 서브시스템 당 단일 큐를 사용하거나 단일 계층 구조 큐를 사용하는 것이 좋다. 

### Good Granularity of Concurrency

![](https://i.imgur.com/yNBAH25.png)

적절한 양의 동시성을 선택하는 방법
- 고정된 개수의 Serial Queue 계층 만들기
- 계층 간의 작업은 크기가 크게
- 계층 내부의 작업은 크기가 작게 세분화


## Introducing Unified Queue Identity

![](https://i.imgur.com/C6QgfYc.png)

![](https://i.imgur.com/tE7XVTB.png)

Unified Queue Identity는 커널에서 큐를 표시하는 카운터 객체이다. 
큐에서 보낸 작업을 실행되는데 필요한 우선 순위와 엮어주는 역할을 한다. 

![](https://i.imgur.com/E7pxNGi.png)

위와 같은 큐 계층 구조를 기반으로 설명한다.

![](https://i.imgur.com/XVfCYAk.png)

![](https://i.imgur.com/Y6Qwq1Y.png)

먼저 Dispatch Source 객체를 생성한다. 큐에서 QoS를 상속받아 Utility QoS를 갖고 Unified Identity로 소스를 등록한다.

![](https://i.imgur.com/EpnijZx.png)

![](https://i.imgur.com/ZnoyiBH.png)

다음은 이보다 우선순위가 높은 User Interactive QoS 작업이다. 소스는 활성화 시에 스냅샷을 저장한다. 커널에 저장하면서 동일한 Unified Identity로 두 번째 소스를 다시 등록한다. 이 작업을 사용할 때는 hint를 사용한다.

### Without Unified Identity

macOS Sierra와 iOS 10까지, Unified Queue Identity가 없을 때의 동작을 보자.

![](https://i.imgur.com/as9zrV2.png)

기존에는 스레드 요청은 익명으로 이루어졌다. 익명 스레드를 요청하고 스레드에서 이벤트를 전달한다.

![](https://i.imgur.com/2qlZTt4.png)

우선 순위가 더 높은 소스가 실행되면 어떻게 될까? 이 소스만을 위한 새로운 스레드를 불러오게 되고 이는 문맥 교환을 발생시킨다.

### Leveraging Ownership and Unified Identity

![](https://i.imgur.com/qKQrQZq.png)

macOS High Sierra, iOS 11 이후에는 이렇게 바뀌었다.
커널은 이제 이벤트를 신경쓸 필요가 없이 큐를 실행하기만 할 뿐이다.

![](https://i.imgur.com/6TFMeai.png)

또한 다음 이벤트가 있다면 스레드에 '보류 중인 이벤트가 있음'을 표시한다. 첫 번째 핸들러가 끝난 이후 이벤트를 큐에서 제거한 뒤, 보류 중인 이벤트가 있다면 커널에서 이벤트를 가져와 핸들러를 큐에 넣어 순차적으로 실행된다.


## Modernizing Existing Code

![](https://i.imgur.com/B3WFblW.png)

이러한 개념들을 가지고 GCD의 핵심 기술들을 사용하기 위해서 코드를 어떻게 작성해야 할까? 두 가지 단계가 있다.
1. 활성화 후 디스패치 객체에 대한 변경이 일어나지 않아야 한다.
2. 큐 계층 구조를 보호해야 한다.

### 1. No Mutation Past Activation

![](https://i.imgur.com/vUoQvvp.png)

소스 핸들러, 타겟 큐 등 디스패치 객체에 대해 활성화하기 전에 미리 설정해야 한다.

![](https://i.imgur.com/xliyMbz.png)

활성화 시점에 객체의 속성에 대한 스냅샷을 찍고 이를 기반으로 프로그램이 동작하기 때문에 이후에 타겟 큐를 변경하면 `우선 순위 역전 방지` 알고리즘, `디스패치 동기화에 대한 핸드오프`와 같은 최적화 기능들이 제대로 동작하지 않는다.

또한 디스패치 소스를 직접 생성할 필요가 없이 시스템 프레임워크를 통해 알아서 제어되고 있는데, 변경이 이루어지면 이런 모든 최적화가 제 기능을 하지 못한다.

### 2. Protecting the Target Queue Hierarchy

![](https://i.imgur.com/Cvs6hP8.png)

안전한 타겟 큐 계층 구조를 만드는 방법
- 맨 아래부터 시작하여 위를 향하여 빌드하도록 구성하고 변경하지 못하도록 관계를 잠금 - Swift 3 이상에서는 이미 기본적으로 적용되어 있음

- 기존 코드
```swift
Q1 = dispatch_queue_create("Q1", DISPATCH_QUEUE_SERIAL)
dispatch_set_target_queue(Q1, EQ)
```
- 개선된 코드
```swift
Q1 = dispatch_queue_create_with_target("Q1", DISPATCH_QUEUE_SERIAL, EQ)
```


