# Concurrent Programming With GCD in Swift 3

## 모든 코드를 main thread에서 실행한다면
![](https://i.imgur.com/mmF4VaQ.png)
새로운 프로젝트를 만들때 해당 응용 프로그램은 기본으로 메인 스레드를 갖게된다.
메인 스레드는 UI에 표시될 모든 코드를 실행하는 역할을 한다.
메인 스레드에 데이터 변환이나 이미지 처리와 같은 큰 작업을 하게된다면 **UI에 문제가 생기게된다.**
UI가 느려지거나 멈추는 상황이 발생할 수 있다.

이러한 문제를 방지하기 위해서는 애플리케이션에 동시성 개념을 도입해야한다.
동시성을 사용하면 애플리케이션의 여러 작업을 동시에 할수 있게 된다.
동시성을 사용하기 위해서 새로운 스레드를 생성하고 새로운 스레드에 작업을 시키게된다.

## Concurrency
<img width="1208" alt="스크린샷 2022-08-25 오전 1 03 18" src="https://user-images.githubusercontent.com/63997044/186467083-07a9fd6a-06dc-43b2-bae0-bb9464dcc135.png"><br>

하지만 동시성을 도입할 경우 스레드 안정성을 유지하기가 어려워진다.
새로 만든 스레드에서 수행하는 작업이 다른 스레드에서 수행할 작업에 영향을 끼칠수 있기 때문이다.

## GCD 동시성 라이브러리
![](https://i.imgur.com/mzf3wS0.png)
이러한 문제를 좀더 쉽게 관리하기위해 GCD라는 동시성 라이브러리를 만들었고
GCD는 다중 스레드 코드를 작성하는데 도움이된다.

GCD는 동시성을 지원하기 위해 스레드에 DispatchQueue와 RunLoop를 도입했고 이를 이용해 새로운 스레드를 만들고 그 스레드에서 실행루프를 실행시켜 작업을할수 있도록한다.

## dispatch queue와 run loop
![](https://i.imgur.com/9X4EeT1.png)

* worker
1. dispatch queue `수행할 일`들을 closure 형태로 넣는다.
2. disapatch queue가 thread를 하나 가져온다.
3. dispatch queue 내부의 `수행할 일`이 끝나면 thread는 알아서 해제한다.

* thread
1. own thread를 만든다.
2. 그 스레드에서 run loop가 동작한다.

DispatchQueue는 작업항목을 큐에 저장할수 있도록 하는것이다.
큐에 저장된 작업을 수행하기위해 Dispatch는 적합한 스레드를 만들거나 가져와 작업을 수행하도록 한다.

Dispatch는 해당 스레드에 대한 모든 작업이 실행 완료되면 스레드를 자체적으로 해제시키게 된다.

이처럼 DispatchQueue에 작업항목을 저장하여 새로운 스레드를 생성할수 있고 새로운 스레드에서 해당 작업을 수행하기위해 런루프를 실행할수 있게 되는것이다.

메인 스레드는 유일하게 애플리 케이션의 메인 런루프와 메인 큐를 가질수 있다.

**DispatchQueue에 작업항목을 저장할수 있는 방법에는 2가지가 있다.**
1. Asynchronous Execution. (비동기 실행)
2. Synchronous Execution. (동시 실행)

## 1. Asynchronous Execution. (비동기 실행)

![](https://i.imgur.com/h7cqX0s.png)
여러 작업항목을 DispatchQueue에 저장하면 해당작업을 실행하기위해 새로운 스레드를 생성하고 Dispatch는 Queue 에서 작업항목을 하나씩 가져와 실행한다.

![](https://i.imgur.com/9eBzhUu.png)
대기열에 저장된 모든 작업항목이 완료되면 시스템에서 만들어진 스레드를 해제시킨다.

## 2. Synchronous Execution. (동기 실행)
![](https://i.imgur.com/fWbbopY.png)
DispatchQueue에 저장한 작업이 끝날때 까지 새로만든 스레드를 기다리게 할수 있다.

![](https://i.imgur.com/N0bT366.png)
DispatchQueue는 대기중인 스레드로 제어를 전달하고 해당 항목을 실행한후 DispatchQueue의 제어가 Dispatch에 의해 다시 작업자(Worker)스레드로 전달된다.

![](https://i.imgur.com/OYbU950.png)
DispatchQueue에 남은 작업항목을 모두 실행하게되면 사용중이던 스레드를 헤제 시킨다.

## Getting Work Off Your Main Thread
**위의 설명들은 다른 스레드에 작업을 수행시키는 방법에대한 설명이다.**
하고자 했던것은 메인 스레드에서 작업하게 되면 UI에 문제가 생길수있으니 다른 스레드에 작업을 수행하도록 하는것이었고 메인스레드에서 수행한 Transform을 가져와 다른 큐에서 실행함으로써 이를 수행하게된다.

![](https://i.imgur.com/WuU8u1u.png)
메인 스레드에서 데이터를 변환하던것을 다른 큐(스레드)에서 실행하도록 하면된다.

![](https://i.imgur.com/hXdpP4v.png)
데이터를 변환하고 싶을때 해당 데이터의 값을 다른 큐의 변환 코드로 이동시킨다.

![](https://i.imgur.com/ZTQJnR5.png)
다른 큐에서 데이터를 변환하고 변환된 데이터를 메인스레드로 보낸다.

이를통해 메인 스레트가 이벤트를 처리하는 동안 데이터를 변환하는 작업을 수행할 수 있게된다.

## 코드는 어떨까?
![](https://i.imgur.com/b7y6TVB.png)
1. DispatchQueue객체를 생성하여 작업을 저장할 큐(대기열)를 생성할 수 있다.
2. DispatchQueue 객체를 생성할때 레이블이 필요하며 해당 레이블은 디버깅할때 사용될수 있다.(어떤 DispatchQueue에서 문제가 발생했는지 특정하기위함 인것 같다.)
3. DispatchQueue는 FIFO(선입선출) 순서로 작업을 실행한다. 즉 DispatchQueue에 저장된 순서대로 실행된다는 뜻이다.
4. 생성된 DispatchQueue객체에 비동기 메서드를 사용하여 수행해야할 작업을 제출할 수 있다.(.async 메서드)

다시 이미지의 코드를 보면 이미지의 크기를 조정하는 작업을 다른 스레드에서 수행하도록 저장했다.

## 작업이 완료된 데이터를 Main스레드로 가져오는 방법은 어떻게 해야할까??
<img width="1208" alt="스크린샷 2022-08-25 오전 1 07 25" src="https://user-images.githubusercontent.com/63997044/186468161-75c20c93-419d-4c60-b3cb-dfe19b3882a5.png"><br>
DispatchQueue는 메인 스레드에서 실행해야할 작업이 무엇인지 작성할수 있다. 
DispatchQieie.main을 호출한 다음 async를 호출하고 내부에 메인스레드에서 수행항 작업을 코드로 작성해주면된다.

위와같이 코드를 작성하게되면 이미지의 크기를 조정하는 작업은 다른스레드에서 수행하게되고 크기가 조정된 이미지를 메인스레드에서 UI에 반영하는 작업을 하게된다.

## Controlling Concurrency
![](https://i.imgur.com/kO2kCj5.png)
프로그램에서 동시성을 제어하는것에는 **비용(cost)** 이 발생한다.
Dispatch가 사용하는 스레드 풀은 기기의 모든 호출을 제어하기위해 동시성을 제한한다.

스레드를 차단할 때 애플리케이션의 다른 부분을 기다리거나 시스템 호출을 기다리는 경우 차단된 Worker 스레드로 인해 **더많은 Worker 스레드**가 생성될수 있다. -> **Thread Explosion**의 위험이 있다.

이를 위해 적절한 수의 thread를 가지도록 해야한다.

## Structuring Your Application(애플리케이션 구조화)
![](https://i.imgur.com/htpNnxf.png)
이러한 것들을 애플리케이션에 적용하기 위해서는 독립적인 데이터 흐름으로 애플리케이션의 영역을 식별, 분류 하는것이 좋다. 

**예**를들어 이미지를 변환하거나 데이터베이스가 존재하는 경우 이러한 영역을 서로 다른 하위시스템으로 분할한 다음 각각 DispatchQueue를 사용하게되면 많은 DispatchQueue를 사용하지 않아도되고 많은 스레드 문제를 겪지않고 독립적으로 실행할 수 있는 DispatchQueue를 사용하도록 할 수 있다.

## Chaining vs. Grouping Work
동시성 프로그래밍을 사용하기위해 유용한 방법들에는 두가지 방법이 있다.
1. Chaining
2. Grouping Work

## Chaining
![](https://i.imgur.com/6sKMlvv.png)
위에서 설명하고 사용한 방식으로 하나의 작업을 수행한후 다른 스레드에서 또다른 작업을 수행, 그리고 다른 스레드에서 작업하도록 할수있도록 하는 방법이다.

## Grouping
![](https://i.imgur.com/NALFqax.png)
작업을 그룹화하고 작업이 완료되기를 기다리는것.

여러 개의 서로 다른 작업 항목을 생성하려는 단일 작업 항목이 있고
해당 작업 항목이 완료되었을 때만 작업을 진행하려는 경우 해당 작업을 수행할 수 있다.

## DispatchGroup
![](https://i.imgur.com/4QdRUh8.png)
이전 다이어그램으로 돌아가서 UI가 3가지 작업항목을 생성하면 3가지의 작업항목윽 DispatchGroup으로 묶을수 있고 Group에 속한 모든 작업들이 종료되기를 추적할 수 있다.

사용방법은 DispatchGroup 객체를 생성하면된다.

![](https://i.imgur.com/Xawto43.png)
DispatchGroup을 사용하면 그룹에 작업을 추가하고 다른 큐(스레드)에서 작업을 수행할 수 있으면서도 동일한 그룹과 연관시킬수도 있다.

DispatchGroup에 작업을 저장 할때마다 Group에 저장된 작업 항목의 갯수만큼 카운터를 증가시킨다.

![](https://i.imgur.com/jYVHFcn.png)
그룹에 저장된 마지막 작업을 수행한후 모든 작업이 완료된 시점에 작업을 수행하도록 할수있으며 notify(queue:)를 사용하여 선택한 Queue에서 어떤 작업을 할것인지 작성할수있다.

![](https://i.imgur.com/UqYtALI.png)
그룹에 저장된 작업들이 실행되면 실행횟수에 따라 작업을 저장할때 증가했던 카운터가 감소하게된다.

위의 이미지는 그룹의 Database에 해당하는 작업이 완료된 화면이다.
3개였던 카운터가 2개로 감소한 모습을 확인할 수 있다.

## Synchronizing Between Subsystems
동기식 실행을 사용하여 하위 시스템간의 상태를 직렬화(Serial)할 수 있다.
DispatchQueue는 기본적으로 직렬(Serial)이다.
이것을 이용해 **상호 배제(mutual exclusion)** 속성에 사용할수있다.
Serial Queue 사용하면 해당 작업 끝날때까지 다른작업을 수행하지 않는 것을 말한다.

![](https://i.imgur.com/QBI8nig.png)
이것을 이용해 **스레드 세이프**하도록 구축할수 있다.
(여러 스레드가 동시에 작업하는것을 방지하게 하여 여러스레드가 동시에 공유자원에 접근하는것(=**레이스 컨디션**)을 막을수 있다.)

![](https://i.imgur.com/9xH6W3e.png)
하지만 이러한 패턴을 사용할때는 주의해야한다.
작업을 수행하지 않도록 정지시키기 때문에 각각의 스레드가 서로의 작업이 끝나기를 기다리게될경우 아무작업도 수행하지 못한채 서로 작업이 끝나기를 기다리게되며 이를 **DeadLock(교착상태)** 문제가 발생할수 있다.

## Choosing a Quality of Service
![](https://i.imgur.com/pYFpadb.png)
개발자의 의도에 따라 Dispatch에 저장할 작업을 분류할 수 있다.
그리고 Dispatch는 개발자가 작성한 의도에 따라 CPU우선순위, IO스케쥴링 우선순위 로 나누어 실행된다.
여기서 우선순위란 어떤작업을 먼저해야할지가 아니라 어떤 작업에 자원을 더많이 쓸것이냐의 우선순위 이다.

## Using Quality of Service Classes
![](https://i.imgur.com/AaXSreJ.png)
Qos를 사용하려면 DispatchQueue의 비동기 메서드 .async에 선택적 매개변수(넣어도되고 안넣어도되고, 기본값이 있다는소리)로 전달할 수 있다.

위의 이미지처럼 .async에 Qos인자를 넣게되면 모든작업을 background, userInitiated 우선도로 작업하게된다.

DispatchQueue에 저장된 작업항목들을 수행하던중 우선순위가 더 높은 작업항목을 추가 하게되면 수행하던 작업항목들의 Qos우선도를 올려 우선작업하도록 한다.

## DispatchWorkItem
![](https://i.imgur.com/knkAYH5.png)
DispatchWorkItem을 사용해 실행 방법을 **다양하게** 제어할수 있도록 할수 있다.
DispatchWorkItem의 인자 flags에 assignCurrentContext를 전달하면 "dispatch queue에 submit하는 시점" 말고 "create 시점"의 QoS를 사용하게 된다.

즉, 나중에 사용할수 있도록 작업을 저장할수 있으며 사용할때 Qos와 함께 DispatchQueue에 저장하여 사용할수 있다.

## Waiting for Work Items
![](https://i.imgur.com/cIXWoq9.png)
DispatchWorkItem의 유용한 기능으로 wait을 사용하여 작업완료 시점을 신호로 보낼수 있다.

wait을 사용하여 다른 작업을 진행하기 전에 해당 작업항목을 완료해야함을 Dispatch에 표시할 수 있다.

wait을 사용할경우 wait이전의 작업항목들과 이후의 작업항목이 무엇인지 알수 있기 때문에 이전의 작업항목들의 Qos를 높여 빠르게 수행하도록 할수 있지만(= **priority inversion 해결**을 위한 QoS의 조절) 세마포어를 사용하는 경우는 이전의 항목의 Qos를 높이지 않는다.

## Swift 3 and Synchronization
![](https://i.imgur.com/Rf1vp2q.png)
swfit 언어는 thread-safe를 지원하지 않는다.
그렇기 때문에 동기화를 해야한다.

## Traditional C Locks in Swift
![](https://i.imgur.com/pfa5x8H.png)

Darwin 모듈은 전통적인 C 방식(구조체 기반의)의 lock을 사용한다.
mutex나 하나의 lock을 사용하는 Swift에 적합하지 않다.

* 동기화 확인하는 tool: [WWDC_Thread Sanitizer and Static Analysis](https://wwdctogether.com/wwdc2016/412)

## Correct Use of Traditional Locks
- 그래도 전통적인 lock 방식 사용하고 싶다면?
![](https://i.imgur.com/xcgDbAZ.png)
`Foundation.lock`을 사용하라
이건 class-based!

* 문제 1: unfair lock이다.
* 문제 2: 다른 thread의 진입을 막지 못함
* 문제 3: spin lock 하지 않음(spin lock = busy waiting = lock 풀릴 때까지 기다리기)

### Use GCD for Synchronization
<img width="1209" alt="스크린샷 2022-08-25 오전 1 14 15" src="https://user-images.githubusercontent.com/63997044/186469581-409a4a06-4729-4b69-835e-f17b4617a67d.png"><br>

동기화 목적으로 이제 **Dispatch Queue**를 사용하도록 하자.

DispatchQueue.sync
- 전통적인 lock 방식보다 오용의 위험이 적다
- 디버깅 도구와 더 잘 통합되어 있다.(뇌피셜: dispatch queue 레이블로 확인 용이)

간단한 구현 예시
<img width="1170" alt="스크린샷 2022-08-25 오후 7 59 19" src="https://user-images.githubusercontent.com/63997044/186647468-2c4573fc-64ff-4931-9ab9-66d89a9cfe59.png"><br>

## Preconditions 전제 조건
<img width="1208" alt="스크린샷 2022-08-25 오전 1 14 36" src="https://user-images.githubusercontent.com/63997044/186469661-54fa20c2-b3a4-40ec-9528-76307cbd824e.png"><br>
이번 릴리즈의 새로운 기능?
이 코드는 주어진 큐에서 꼭 실행되어야 한다고 조건을 걸수도 있고, 이 큐에서는 실행되면 안된다고 조건을 걸수도 있다.

## Object Lifecycle in a Concurrent World
<img width="1205" alt="스크린샷 2022-08-25 오전 1 15 20" src="https://user-images.githubusercontent.com/63997044/186469823-65e290fb-74eb-42c4-b50f-de924389d742.png"><br>

동기화가 필요하지 않은 값이 전달되는 방식으로 앱을 구성할 수 있으면 제일 좋음. 그러나 실제 코드에서는 서브시스템에 액세스할 일부 개체가 필요하고 개체 참조가 발생한다. 그리고 이런 참조를 제거하는 것이 실제로는 어려울 수 있다.
따라서 크래시를 피하기 위해 객체 상태는 4단계를 거쳐야 함
setup - activated - invalidated - deallocated

1. Setup
개체를 만들고 목적에 필요한 프로퍼티를 제공한다.
2. Activated
객체를 활성화시킨다. 이 객체를 다른 서브시스템이 알 수 있게 됨. 동시성 프로그래밍 환경에서 해당 객체를 사용할 수 있게 됨
3. Invalidated
객체를 제거하고 싶다면, 비활성화를 거쳐야 한다. 앱의 모든 부분, 모든 서브시스템에서 이 객체가 사용되지 않고 사라져야 함을 확인한다.
4. Deallocation
Invalidated 단계가 끝나면 할당 해제 단계를 수행한다.

## Observer Pattern

### 의도한 UI 동작 방식
![](https://i.imgur.com/AMHUbOR.png)

![](https://i.imgur.com/JXXwuX9.png)

![](https://i.imgur.com/JbKlncK.png)

![](https://i.imgur.com/szCAO7c.png)
=> state가 바뀌면 main queue에서 noti를 받을 수 있도록 구현한다.

![](https://i.imgur.com/SmTEtz2.png)

![](https://i.imgur.com/mkBy9mU.png)

### Deallocation
![](https://i.imgur.com/QBWceMa.png)

deinit 내부에서 unregister 호출
- 서브시스템을 register할 때 참조를 가져옴
- 메인 스레드에서 해제해도 하나가 남아있으므로 참조 카운트가 남아있으므로 deinit이 실행되지 않음.
- 따라서 메모리에서 해제되지 않고 남아있는 상태가 됨

![](https://i.imgur.com/X0bFkz6.png)
그렇다면 약한 참조를 사용하면 해결될까?
- 실제 앱은 예시보다 훨씬 복잡한 구조임
- 따라서 메인 스레드에서 참조를 해제한다고 하더라도 다른 모든 객체가 참조하지 않는다는 보장을 할 수 없다!

### Invalidation as a State
<img width="1207" alt="스크린샷 2022-08-25 오전 1 17 10" src="https://user-images.githubusercontent.com/63997044/186470159-93d0e787-2976-4bbb-a6a6-b8e6f5e3c756.png"><br>

**해결법** - 세 번째 단계인 `Invalidation` 단계에서 명시적 함수 호출.
invalidate 메서드 내에서 unregister를 수행
BusyController의 상태값 flag로 서브시스템의 상태를 체크하도록 함

## GCD Object LifeCycle
concurrently하게 사용되는 GCD 객체 - 정확히 동일한 패턴을 따름

### Setup
<img width="1204" alt="스크린샷 2022-08-25 오전 1 17 29" src="https://user-images.githubusercontent.com/63997044/186470215-e6b17e3a-b6fa-45be-8e3a-c1122055849e.png"><br>

DispatchQueue의 작업과 속성들 
DispatchSource의 속성들과 타겟 큐, 이벤트 핸들러 등등
(label, attributes, queue 등)

### Activation
<img width="1205" alt="스크린샷 2022-08-25 오전 1 17 48" src="https://user-images.githubusercontent.com/63997044/186470274-417439ec-ae2a-48df-9297-289487f02c8a.png"><br>

활성화가 되면 더 이상 개체의 속성이 변경되지 않아야 한다.

### Cancellation
<img width="1205" alt="스크린샷 2022-08-25 오전 1 18 05" src="https://user-images.githubusercontent.com/63997044/186470333-a60c599a-f452-438c-bd41-0dfa89ffe285.png"><br>

취소하면 항목에 대한 이벤트 모니터링이 중지된다.
또한 취소 핸들러가 실행된다.
그리고 소스에 대한 핸들러가 할당 해제된다.

### Deallocation
<img width="1205" alt="스크린샷 2022-08-25 오전 1 18 19" src="https://user-images.githubusercontent.com/63997044/186470380-eb6e950f-1886-4a91-967e-269dd565a057.png"><br>
할당 해제될 때 객체는 활성화(Activate)되어있어야하고, 종료된 상태가 아니어야 한다(Not Suspended). 일시중단되거나 비활성화된 것은 개체를 제거하는 코드를 실행하기에 안전하지 않은 상태라고 판단한다.


## 참고한 문서및 자료
[wwdc Concurrent Programming With GCD in Swift 3](https://developer.apple.com/videos/play/wwdc2016/720/)
