# Building Responsive and Efficient Apps with GCD

<img width="100%" alt="스크린샷 2022-08-17 오후 1 32 16" src="https://user-images.githubusercontent.com/63997044/185035204-99119bdf-6f8e-4a11-a98d-0d53daf8c35d.png">

### GCD(Grand Central Dispatch)의 도입
- OS X Snow Leopard에서 GCD가 도입되었다.
- 멀티코어 맥북에서 코어를 모두 활용하여 하나의 애플리케이션에서 서로 다른 부분을 동시에 실행하고, 스레딩을 쉽게 해준다.
- 더 나아가서 GCD는 앱이 효율적으로 실행하게끔 활용할 수 있다.
    - 멀티태스킹 기능을 갖춘 iOS 9 이상의 경우 앱이 말 그대로 다른 앱과 나란히 실행되어야 하는데, GCD는 시스템에 현재 어떤 종류의 작업을 수행하고 있는지 알려주고 두 앱 사이에 리소스 공유를 원활하게 해준다.
- 또한 Watch OS처럼 크기가 작은 Device에서 실행해야 하는 코드 부분을 시스템이 알도록 도와준다.

## Quality of Service Introduction

### Handling Events

<img width="100%" alt="스크린샷 2022-08-17 오후 1 33 05" src="https://user-images.githubusercontent.com/63997044/185035297-728eefa5-dc15-4afd-a93c-5b04f0e47533.png"><br>
- 앱 실행 (기본적인 상황)
    - 앱을 실행하는 과정을 단계적으로 살펴보겠다. 
    - 앱이 실행되면 메인 스레드를 갖고 Run Loop를 불러온다. 
    - 이제 이 스레드는 이벤트를 기다린다. Delegate Method 호출이 일어나면 데이터베이스에서 데이터를 읽고 다시 돌아온다. 
    - 이 데이터를 가지고 UI 업데이트를 하고, 끝나면 프레임워크에 컨트롤을 반환하고 다시 이벤트를 기다리는 상태가 된다.

<img width="100%" alt="스크린샷 2022-08-17 오후 1 33 26" src="https://user-images.githubusercontent.com/63997044/185035357-ff835ea0-5960-42ba-a2c5-52fde56aa635.png"><br>
- 그러나 데이터베이스에서 값을 읽어오는 것은 다소 시간이 걸리는 작업이고 그림처럼 회전중인 커서를 보게 될 것이다.
- 이것은 좋지 못한 UX이다. 

<img width="100%" alt="스크린샷 2022-08-17 오후 1 34 07" src="https://user-images.githubusercontent.com/63997044/185035421-0cf7ff5b-0ec7-4008-9df0-c5bee3b77bd2.png"><br>
- 앱 실행(비동기적인 처리가 이루어지는 상황)
    - GCD가 개입하여 이를 해결할 수 있다. 
    - 작업을 즉시 수행하는 것이 아니라 GCD 큐를 생성하고, dispatch_async를 사용하여 작업을 큐로 이동시킨다. 
    - 이 코드는 메인 스레드와 비동기적으로 실행되므로 해당 작업이 GCD 큐에서 진행되는 동안 메인 스레드는 계속해서 이벤트를 기다릴 수 있게 된다.

### Competing Threads

<img width="100%" alt="스크린샷 2022-08-17 오후 1 36 13" src="https://user-images.githubusercontent.com/63997044/185035712-7632caed-7777-4910-9517-4e86112c8612.png"><br>
- GCD를 사용하는 경우에는 이전에는 생각하지 않았던 문제에 직면하게 된다. 
- 우리의 Device는 싱글 코어인데, 코드를 실행하려는 스레드는 두 개가 되었다. 
- 이 경우에 어떤 스레드를 실행해야 할까? 

### QoS(Quality of Service) Classes
<img width="100%" alt="스크린샷 2022-08-17 오후 1 36 39" src="https://user-images.githubusercontent.com/63997044/185035763-ae1a4aa8-63fd-47ee-ab0d-c28a62b872b4.png"><br>
- iOS 8과 OS X Yosemite와 함께 공개된 API이다.
- QoS는 시스템에 수행 중인 작업이 어떤 작업인지 작업의 종류를 알려주는 용도로 사용한다. 
- 이를 통해서 시스템은 코드를 효과적으로 실행하도록 리소스 제어를 제공한다. 
- QoS는 작업의 의도와 분류를 나타낼 수 있는 단일 추상 매개변수로 설계되었다. 
    - 그래서 특정 값을 모두 조절하는 대신 "User Initiated 작업입니다." 라고 알리면 시스템이 적합한 값을 자동으로 선택한다.

<img width="100%" alt="스크린샷 2022-08-17 오후 1 37 23" src="https://user-images.githubusercontent.com/63997044/185035841-723f0453-5558-4968-ad6c-28cb8fea4c59.png">
<img width="100%" alt="스크린샷 2022-08-17 오후 1 37 44" src="https://user-images.githubusercontent.com/63997044/185035881-03e7f512-0932-488e-ab87-ae9b936dd234.png"><br>

#### 1. UI(User Interactive)
> UI업데이트에 적극적으로 관련되어 있는지 고려해야 한다.
메인 스레드(사실은 아님: [H4NJUN
님 블로그](https://h4njun.tistory.com/m/entry/동시성-프로그래밍3-DispatchQueue))
사용자와 상호 작용이 필요한 이벤트들이 포함된다.
초당 60프레임의 애니메이션이 원활하게 실행되어야 한다.

#### 2. IN(User Initiated)
> 사용자가 수행한 작업의 결과를 로드한다.
스크롤 뷰를 스크롤하여 다음 셀을 불러오거나, 이메일을 탭하여 이메일 내용을 로드하는 등의 액션
이러한 작업은 UI로 처리될 필요가 없으므로 IN이 적절하다.
사용자와 상호작용을 유지해야 하는지 고려해야 한다.
 
#### 3. UT(Utility)
> 오래 실행되는 작업들이 포함된다.
사용자와의 상호작용을 방해하지 않는다.
사용자가 진행 상황을 알고 있는지 고려해야 한다.

#### 4. BG(Background)
> 사용자가 진행 상황을 알 필요 없다.
유지 관리 작업, 정리 작업, 데이터베이스 청소 등이 포함된다.
언제 수행할 지 사용자가 인식하지 못한다.

### 멀티태스킹
<img width="100%" alt="스크린샷 2022-08-17 오후 5 03 50" src="https://user-images.githubusercontent.com/63997044/185067184-cb838e04-6844-4217-bdd3-c1b8b4781a80.png"><br>
- 실행중인 앱 #1의 메인 스레드와 GCD 스레드, 멀티태스킹으로 실행중인 앱 #2의 메인 스레드, 그리고 PIP로 실행 중인 앱의 메인 스레드가 있는 상황.
- CPU는 2개.
- 비디오를 디코딩하기 위해 PIP에서 하나를 사용한다면, 나머지는 어떻게 해야 할까?
    - QoS를 활용하여 리소스를 관리를 통해 원활하게 앱을 실행시킬 수 있다.

## GCD Design Patterns with QoS
### Asynchronous Work
<img width="100%" alt="스크린샷 2022-08-17 오후 1 38 38" src="https://user-images.githubusercontent.com/63997044/185035976-51417086-eccd-4eec-87b0-40fc8331e090.png"><br>
- 이 예시에서는 어떤 QoS가 적합할 것인가? 
    - Update UI가 있으므로 - User Interactive
    - 메인 스레드에서 발생하지 않는 비동기 작업은 UI 렌더링이 일어나지 않음 - User Initiated
- 그렇다면 모든 작업들의 QoS를 알맞게 설정해주어야 할까? 
    - NOPE!
- dispatch_async()로 GCD 큐에 작업을 이동할 때, QoS가 자동으로 User Interactive를 User Initiated으로 변환한다.
    - User Interactive는 메인 스레드 사용 그리고 UI 렌더링으로 제한되어야 하기 때문에 QoS를 실수로 과도하게 전파하지 않도록 이 작업을 수행한다.

### QoS Propagation(추론된 QoS)
<img width="100%" alt="스크린샷 2022-08-17 오후 1 39 45" src="https://user-images.githubusercontent.com/63997044/185036111-5b4cb9fb-71e7-4ab8-a36f-04be5cdb5fc7.png"><br>
- propagation: 전파. QoS의 상태를 전달하는 것을 의미한다.
- 블록이 큐로 이동할때 캡쳐된 QoS
    - 예시에서는 User Interactive에서 User Initiated로 변환된 것을 가리킨다.
- 블록을 받는 큐가 지정된 QoS를 갖지 않을 때 어떤 QoS로 처리될 것인지 추론한다.
- 메인 스레드로 이동해도 QoS가 낮아지지 않습니다.

### Long Running Job(오래 지속되는 작업)
<img width="100%" alt="스크린샷 2022-08-17 오후 1 43 10" src="https://user-images.githubusercontent.com/63997044/185036462-3b76290b-2ba6-4b5f-8a48-0a914a5cd241.png"><br>
- UI를 방해하지 않도록 비동기적으로 처리하는 계산 작업이 있고, 작업이 끝나는 대로 비동기적으로 UI를 업데이트하는 상황
- 이 예시에서는 어떤 QoS가 적합할 것인가?
    - 오래 지속되는 작업이므로 - Utility
- 사용자는 UI를 계속 사용할 수 있고, 결과를 기다리지 않지만 진행 상황을 볼 수 있으며 어떠한 작업을 시작할 수 있으나 그것이 즉각적인 진행을 방해하지 않는다.

<img width="100%" alt="스크린샷 2022-08-17 오후 1 45 01" src="https://user-images.githubusercontent.com/63997044/185036653-5f2bc9df-fadd-420d-83e0-6aaf7d2fb49e.png"><br>
- 명시적으로 QoS를 Utility로 지정해준다.

### Block QoS
<img width="100%" alt="스크린샷 2022-08-17 오후 1 45 45" src="https://user-images.githubusercontent.com/63997044/185036723-b50d0345-c903-408a-84fd-297b17db1b1e.png"><br>
- 명시적으로 QoS 속성을 블록에 추가해서 생성하는 방법이다.
- 블록 객체가 생성될 때 QoS가 캡쳐된다.
    - DISPATCH_BLOCK_CREATE_WITH_QOS_CLASS: 블록 객체를 생성하면서 QoS를 할당한다. DISPATCH_BLOCK_ASSIGN_CURRENT보다 우선순위가 높다.
    - [DISPATCH_BLOCK_ASSIGN_CURRENT](https://developer.apple.com/documentation/dispatch/dispatch_block_flags_t/dispatch_block_assign_current) 플래그를 사용하여 생성하면 나중에 해당 블록을 큐에 제출하는 시점에 할당된 QoS값으로 실행된다.

### Maintenance Task
<img width="100%" alt="스크린샷 2022-08-17 오후 1 46 26" src="https://user-images.githubusercontent.com/63997044/185036806-4cea56ff-13b8-42ac-8c39-586ed6689e0a.png"><br>
- 이 예시에서는 어떤 QoS가 적합할 것인가?
    - 정리 작업 등 앱을 유지하기 위한 작업이므로 - Background
- UI 작업을 실행하는 응용 프로그램에서 유지 관리 작업, 정리 작업, 데이터베이스 청소 등 작업이 일어날 때 QoS가 Background인 큐를 만들어 둔다. 

<img width="100%" alt="스크린샷 2022-08-17 오후 1 47 14" src="https://user-images.githubusercontent.com/63997044/185036892-bdb2abc5-c3c1-4192-bc87-c5197aa92111.png"><br>
- 작업이 여기로 들어오게 되면 큐에 QoS가 할당되어 있기 때문에 블록의 QoS가 무시되고 백그라운드에서 실행된다.
- 이와 같이 실행 흐름과 관련없는 작업은 DISPATCH_BLOCK_DETACHED 플래그를 사용하여 이 작업이 실행 흐름과 관련이 없음을 운영체제에 알린다.

<img width="100%" alt="스크린샷 2022-08-17 오후 1 47 32" src="https://user-images.githubusercontent.com/63997044/185036931-22add3a7-ac9d-47e8-b28f-c7eb19db653a.png"><br>
- DISPATCH_BLOCK_ENFORCE_QOS_CLASS 플래그를 사용하면 블록의 QoS가 무시되지 않는다.

<img width="100%" alt="스크린샷 2022-08-17 오후 1 47 49" src="https://user-images.githubusercontent.com/63997044/185036951-258827d2-d49e-4211-b074-1a6a4b2582ef.png">

### Asynchronous Priority Inversion
<img width="100%" alt="스크린샷 2022-08-17 오후 1 49 02" src="https://user-images.githubusercontent.com/63997044/185037068-9f558717-b7e8-4e82-94e8-98bcae959781.png"><br>
- 낮은 우선순위의 QoS를 가진 블록이 큐에 들어있는데 높은 우선순위의 QoS를 가진 블록이 직렬 큐에 들어온 상황. 즉 우선 순위가 반전된 상태
- GCD가 자체적으로 우선 순위를 조정하여 해결해준다.
    - 현재 작업하고 있는 블록의 QoS를 재정의해서 해결한다.
(부연 설명: (naljin님 블로그)[https://sujinnaljin.medium.com/ios-차근차근-시작하는-gcd-15-3fef697f9aab])

### Queue QoS(QoS를 갖는 대기열)
<img width="100%" alt="스크린샷 2022-08-17 오후 1 49 20" src="https://user-images.githubusercontent.com/63997044/185037083-5d333a5a-1a65-45ce-b41a-824aa1040c41.png"><br>
 - 단일 목적의 큐에 적합하거나 제출된 블록의 우선순위가 중요하지 않을 때 사용한다.
 - 특히 유지 관리나 청소 등 백그라운드 작업에 사용하기 적합하다.
 - ENFORCE FLAG를 사용하여 예외를 둘 수 있다.

### Queue as Locks
<img width="100%" alt="스크린샷 2022-08-17 오후 1 53 14" src="https://user-images.githubusercontent.com/63997044/185037555-443f092a-71a3-4dde-930d-bab6f7014e6c.png"><br>
 - 직렬 큐를 자물쇠 용도로 사용하는 것을 의미한다.
 - 직렬 큐와 dispatch sync를 사용하여 해당 블록이 데이터구조에 독점적으로 접근하게 한다.
 - 이 때 실행되는 블록의 QoS는 User Interactive, Utility 등 서로 다른 QoS를 가진 다른 블록이 dispatch sync로 접근하는 상황도 발생할 수 있다.

<img width="100%" alt="스크린샷 2022-08-17 오후 1 52 15" src="https://user-images.githubusercontent.com/63997044/185037473-c6ebc492-ce10-4f79-b9ba-268dd40193fb.png"><br>

### Synchronous Priority Inversion
<img width="100%" alt="스크린샷 2022-08-17 오후 1 54 04" src="https://user-images.githubusercontent.com/63997044/185037647-9031ec64-955e-461f-b91d-db75322fa0c2.png"><br>
 - 높은 우선순위의 작업이 낮은 우선순위의 작업을 기다리는 현상
 - 우선순위의 상속으로 해결할 수 있다.
    - (우선순위의 상속 부연설명: [J.Bear님 블로그](https://jbear.tistory.com/entry/우선순위-전도-우선순위-상속-Priority-Inversion-Priority-Inheritance))


## Queues, Threads, and Run Loops
### Run Loop Versus Queue
- 코드 블록의 수행이 완료되면 스레드가 사라진다.
-> 임시 스레드 풀의 스레드. 수명을 보장할 수 없다.

<img width="100%" alt="스크린샷 2022-08-17 오후 1 55 16" src="https://user-images.githubusercontent.com/63997044/185037805-0d31e707-4301-404c-a701-de677a5ba7a2.png"><br>

### Run Loop와 Serial Queue의 비교
<img width="100%" alt="스크린샷 2022-08-17 오후 1 55 36" src="https://user-images.githubusercontent.com/63997044/185037840-344115c8-70b6-4864-8c47-7acda161ab8f.png"><br>
- Run Loop는 특정 스레드에 의존적이다. Serial Queue는 수명이 짧은 스레드를 사용한다.
- Run Loop는 위임 메서드의 콜백을 받는다. Serial Queue는 블록을 콜백으로 사용한다.
- Run Loop는 각각을 반복한 후 Autorelease pool을 통해 pop된다. Serial Queue는 스레드가 완전히 유휴 상태가 되었을 때 pop된다.
- Run Loop는 재진입적으로 사용할 수 있다. Serial Queue는 재진입 구조가 아니므로 deadlock이 발생한다.

### Thread Creation and Pooling
<img width="100%" alt="스크린샷 2022-08-17 오후 1 56 48" src="https://user-images.githubusercontent.com/63997044/185037980-c09d0fd6-5068-4222-a03e-f8ba3a5226b1.png"><br>

 - GCD는 임시 스레드를 사용한다고 했다.
 - 많은 dispatch_async 호출이 발생하면 시스템은 스레드 풀에서 스레드를 가져와 각 블록에 제공한다. 
 - 만약 우리의 Device가 2개의 코어를 가졌다면, 두 블록이 스레드를 받은 것이 이상적인 상황이고, 하나의 블록의 작업이 끝나면 스레드가 반환되고 이를 다음 블록에 전달되는 식으로 진행될 것이다.

### Waiting

<img width="100%" alt="스크린샷 2022-08-17 오후 1 57 28" src="https://user-images.githubusercontent.com/63997044/185038049-d5bb692b-2497-4514-9fb9-190489f59e81.png"><br>

 - Waiting은 블록이 스레드를 할당 받았지만 자원에 접근을 할 수 없어서 기다리는 상태이다.
 - 스레드가 대기 상태가 되면 GCD는 코어 당 하나의 스레드가 실행되도록 다른 스레드를 또 불러온다.

### Thread Creation and Waiting & Thread Explosion
<img width="100%" alt="스크린샷 2022-08-17 오후 1 58 08" src="https://user-images.githubusercontent.com/63997044/185038145-48bfd485-b002-4136-90b2-05b1f9eed74e.png"><br>

<img width="100%" alt="스크린샷 2022-08-17 오후 1 58 26" src="https://user-images.githubusercontent.com/63997044/185038176-73282457-3eb5-4358-a7e6-5e4b2de78cf1.png"><br>

 - 많은 블록들이 실행되어야 하고 이들이 모두 Waiting 상태가 되어 많은 스레드가 대기 상태가 되는 것 -> Thread Explosion이 발생한다.
 - 다량의 스레드가 대기를 멈추면 자원에 대한 경쟁이 발생한다.
 - 또한 생성할 수 있는 스레드의 수도 무한적이지 않은데 새로운 작업이 또 들어오면? 
     - -> 교착 상태를 유발한다.

### Thread Explosion Causing Deadlock

<img width="100%" alt="스크린샷 2022-08-17 오후 1 59 47" src="https://user-images.githubusercontent.com/63997044/185038334-b7cb28ce-821f-4f4e-b663-ee6b370c432f.png"><br>

 - 사용할 수 있는 모든 스레드를 사용했기 때문에 스레드 풀에서 추가적인 스레드를 가져올 수 없는 상황이다.
 - 메인 스레드의 어떤 작업이 직렬 큐에 dispatch async로 들어간다고 가정하면, 현재 사용가능한 스레드가 없기 때문에 아직 실행되지 않고 대기하는 상태가 된다.
 - 그 다음, 메인 스레드가 같은 직렬 큐에 dispatch sync를 한다.
 - 이렇게 되면 직렬 큐에 사용할 수 있는 스레드가 없기 때문에 호출은 영원히 차단되고, 교착 상태가 발생한다.

### Avoiding Thread Explosion

<img width="100%" alt="스크린샷 2022-08-17 오후 2 01 35" src="https://user-images.githubusercontent.com/63997044/185038549-27187602-997b-489c-a125-60b1418316ee.png"><br>

 - 특히 입출력의 경우 가능하면 항상 비동기 API를 사용하는 것이 좋다.
 - 직렬 큐를 사용한다.
 - NSOperationQueue 사용한다. - maxConcurrentOperationCount 옵션으로 조절 가능.
 - 무제한 작업을 생성하지 말아야 한다.
 
### 잘못된 예제

<img width="100%" alt="스크린샷 2022-08-17 오후 2 01 50" src="https://user-images.githubusercontent.com/63997044/185038580-e491b8fa-4338-4c83-be25-091fb0147a6c.png"><br>

 - Sync와 Async의 혼합. main thread에서 할 때 특히 주의

<img width="100%" alt="스크린샷 2022-08-17 오후 2 02 07" src="https://user-images.githubusercontent.com/63997044/185038614-847e3ac1-fe27-43e2-ab01-ea7baea1ff0b.png"><br>
- 너무 많은 블록을 생성했기 때문에 Explosion 및 Deadlock의 위험이 있다. 
    - dispatch_apply: GCD가 병렬 처리를 관리해줌.

<img width="100%" alt="스크린샷 2022-08-17 오후 2 02 19" src="https://user-images.githubusercontent.com/63997044/185038628-64e1d6e8-c104-40db-b70b-afbef7362c3a.png"><br>

 - dispatch semaphore: 실행하려는 동시 작업 수를 지정하여 작업 시작하고 나머지는 차단하여 대기 상태로 만든다.

## GCD and Crash Reports
### Manager thread
<img width="100%" alt="스크린샷 2022-08-17 오후 2 03 10" src="https://user-images.githubusercontent.com/63997044/185038723-e4afe1ae-6d62-480b-9067-e5e741416bce.png"><br>
 - 응용 프로그램을 사용하는 거의 모든 GCD에 존재한다.
 - Dispatch Manager Thread가 Root Frame이다.
 - 일반적으로 무시한다

### Idle GCD thread
<img width="100%" alt="스크린샷 2022-08-17 오후 2 03 29" src="https://user-images.githubusercontent.com/63997044/185038753-ceb3bc8b-87ae-426a-9996-c2a755bfd449.png"><br>
 - 스택의 맨 아래에서 작업 대기열 시작 스레드를 볼 수 있다. 
 - GCD 스레드라는 표시가 있다. 

### Active GCD thread
<img width="100%" alt="스크린샷 2022-08-17 오후 3 37 08" src="https://user-images.githubusercontent.com/63997044/185051262-4a3b47de-156d-4759-8b87-1b8b67ced0d3.png"><br>
 - 작업 대기열 스레드를 시작하지만 디스패치 클라이언트 호출 및 디스패치 호출 블록 및 릴리스와 같은 항목이 표시되고 그 뒤에 코드가 표시된다. 
 - 대기열을 생성할 때 전달한 디스패치 대기열 이름도 함께 표시된다. 설명적인 대기열 이름을 지정하는 것이 중요하다.

### Idle main thread
<img width="100%" alt="스크린샷 2022-08-17 오후 3 37 35" src="https://user-images.githubusercontent.com/63997044/185051316-169f3622-4e82-458c-8c60-52d277b313e8.png"><br>
 - 메인 스레드가 유휴 상태일 때 모의 메시지 트랩, CF 실행 루프 포트 및 CF 실행 루프 실행에 앉아 있는 것을 볼 수 있으며 com.apple.main.thread를 볼 수 있다.

### Main queue
<img width="100%" alt="스크린샷 2022-08-17 오후 3 37 46" src="https://user-images.githubusercontent.com/63997044/185051336-ac9b8160-fc10-4ae9-8d8c-71b40853f6e2.png"><br>
 - 메인 큐가 활성화 상태인 경우 메인 큐의 GCD 큐로 인해 CF Run loop가 메인 디스패치 큐를 서비스한다.
 - 우리가 호출하는 NSBlock 작업이 존재한다.
