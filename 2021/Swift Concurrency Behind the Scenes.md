# Swift Concurrency Behind the Scenes

이야기할 것
Threading model
Synchronization


### Threading model
GCD
Swift Concurrency

예시: 피드에서부터 가장 최근의 내용들을 가져오는 화면
GCD queue 사용해서 한다
유저가 최근 뉴스 보는 것을 요청한다.

![](https://i.imgur.com/UuKZ0oY.png)

시리얼 큐로 한다. 메인쓰레드 보장하도록하고 유저 동작한 것이 끝날때 까지 기다려준다.
또한 데이터베이스가 보호되도록 해서 Mutual exclusion 를 막는다.

유저가 URLSession를 통해 요청해서 피드의 내용들을 다운로드한다.

![](https://i.imgur.com/Ixq7ahG.png)

그리고 요청에 대한 response를 Concurrent Queue에 넣는다.

![](https://i.imgur.com/s2WAdpO.png)

그 다음에는 이제 Serial Queue인 데이터베이스에 최신의 요청대로 결과를 하나씩 넣어준다.

그리고 UI를 refresh해준다.

이제 코드로 보자!

![](https://i.imgur.com/sLYIwEW.jpg)

concurrentQueue로 하고
반복문을 돌려서 각각에 대해 업데이트 해주고 데이터를 가져온다.
그리고 데이터베이스큐에서 데이터를 업데이트 해준다.

하지만 문제가 성능적으로 숨겨진 문제가 존재한다.

GCD에서 만약 여러개의 작업을 한번에 해야하면 시스템은 여러개의 Thread를 가져온다

![](https://i.imgur.com/9MoYLzG.png)

만약 여기에서 첫번째 쓰레드에서 block이 생기고 하던일을 못하게 되면

GCD는 첫번 째 쓰레드에서 해야할 남은 일들을 하기위해서 더 많은 Thread를 가져온다.

2가지가 생긴다.

첫 번쨰로는 다른 쓰레드에 프로세르를 줌으로써 이제 주어진 시간에 하기 위해서 각 Core에서 Thread를 계속적으로 가지는 것을 보장하게 된다.
이렇게 함으로써 application에서 concurrency를 게속적이고 좋게 사용할 수 있게 해준다.

두 번째로는 블록된 thread는 세마포어처럼 기다려야한다. 이게 추후 작업이 일어나기전에는 아무것도 하지 못한다. 하지만 여기에서는 새 Thread를 가져와서 하기 때문에 기다림없이 다른 쓰레드로 처리해줄 수 있다.

이제 CPU 실행에 대한 이야기

![](https://i.imgur.com/seHtTth.jpg)

2개의 Thread에서 여러 block이 생겼을 때 어떤 식으로 되는지 보여준다.
여러 Thread가 계속 생길 수 있다.

이래서 많은 Threads를 활용해서 URlSession에서 각 피드에 내용을 요청하고 가져오는 것을 Concurrent하게 Queue에 저장하고 이를 데이터베이스 큐에 넣어서 업데이트 시켜준다.

### 많은 Concurrnecy

![](https://i.imgur.com/WgZ8nCP.png)

이런 문제점을 일으킬 수 있다.

Overhead

![](https://i.imgur.com/zryCf4J.jpg)

Thread를 잠가두고 사용못하고 block되어 있으므로 overhead가 발생할 수 있다.

또한 Scheduling overhead가 발생한다.
왜냐 각각 스케쥴을 변경할 때마다 스위치를 해줘야하므로 overhead가 발생할 수 있다.

![](https://i.imgur.com/iVyqcRv.jpg)

이를 Timesharing으로 해결할 수 있다.
하지만 만약 수없이 많은 스케쥴이 존재하는 경우 또 달라진다!

이런 경우에는 context switching하는 것이 실제 사용하는 것보다 더 많은 비용을 사용하기 때문에 비효율적이다.

효율을 안좋게하고 성능을 더 나쁘게 만든다!

이제 Concurrency를 활용해 성능 더 좋게 만드는 법 알려준다!

![](https://i.imgur.com/hdUbZrK.png)

두개의 쓰레드만 존재하므로 여러 쓰레드가 있을 필요도 없고 Context switching이 일어나지 않는다.

아래에서는 continuation이라고 가벼운 객체를 가지고 있는다.
이를 통해 작업이 재개하는 것을 트래킹할 수 있다.

만약 Concurrency에서 작업이 재개하게 되면 continuation 들을 switch하도록한다.
이렇게 해서 full thread를 context switch하지 않고 할 수 있다.

![](https://i.imgur.com/c8u2FrP.jpg)

단순히 함수콜로 바꾸게 된다.

![](https://i.imgur.com/3ilaVeU.png)

이렇게 하기 위해서는 OS는 runtime contract가 있어야한다.
그래서 쓰레드들이 block되지 않을 것이다.
그리고 language가 우리에게 제공되어야만 이것이 가능해진다

이제 Language features를 통해 런타임에 Contract를 지속하도록 해준다.
2가지가 필요하다

![](https://i.imgur.com/VcwKOae.png)

이제 코드에서 보여준다.

![](https://i.imgur.com/C3WToz1.jpg)

async await 사용해서 바꾼다.
현재 쓰레드를 블록하지 않고 사용한다. 그래서 요청 후 다른 작업 하게 해준다.

non-async function에 대한 예시

![](https://i.imgur.com/MzGn1EX.png)

stack이며 하나 실행하고 끝나면 pop한다

async functions에 대한 예시

![](https://i.imgur.com/7hmhjY6.jpg)

id, article같은 경우 정의한 다음 바로 Stack Frame에 저장한다.

그 다음 힙에 async frame2개가 존재한다(add, updateDatabase)

![](https://i.imgur.com/GOzBj41.jpg)

newArticles는 await 전에 정의 되지만 사용되는 것은 await 이후에 된다.
이 의미는 add라는 asnyc frame이 newArticles를 지속적으로 트래킹 할것이다.

그리고 thread가 만약 계속 실행된다고 가정해서 save function이 그이후 실해된다면 add를 대체할 것이다.

![](https://i.imgur.com/kogQCVJ.jpg)

그 다음에는 heap에도 저장

![](https://i.imgur.com/E0817K4.png)

이렇게 해서 block되지 않고 계속 실행되도록 한다.
만약 save function이 suspended된다고 가정하면

![](https://i.imgur.com/vTtbpJG.png)

보는 것처럼 heap에 suspendation point로 저장되고 있고 Continuation를 활용해서 스위치해줄 수 있다. 그래서 추후에 다른 함수를 사용하려면 Continution만 switch하면된다.

![](https://i.imgur.com/ynuWgEE.jpg)

그래서 만약 save가 끝나게 되면 heap에서 제거하고 continuation를 add로 바꾸고 스택도 바꾼다.

이게 이제 Language features에서 await와 non-blocking thread에 대해 설명한 것이다ㅣ.

다음으로는 2번째에 대한 설명 시작!

![](https://i.imgur.com/DkNyspi.jpg)

async function이 끝난 후에 다음 것이 실행되는 것이
스위프트 concurrency runtime에 의해 추적되는 dependency를 말한다.

또한 group를 할 수 있는데 이는 위의 parent task의 child task로 parent task가 모두 실행되기 전에 실행되게 한다.
task group의 범위를 정해서 할 수 있다.

continuation 이나 child task를 활용해서 dependency를 정할 수 있다.

![](https://i.imgur.com/w3sm8kb.jpg)

swift에서 thread를 무한정하게 만드는 것을 제한한다. 그래서 무한한 concureency의 문제를 막을 수 있다.

![](https://i.imgur.com/lIOw13i.png)

GCD에서는 설정을 통해 queue의 숫자를 정확하게 정해주는 것이 중요하다.
Swift에서는 기본 runtime이 적절한 concurrency 제한을 설정해주도록 도와준다.

### Concurrency 적용

성능

![](https://i.imgur.com/M631Zpr.jpg)

사용하는 것이 관리하는 것보다 비용이 적을 때 사용해야한다.

await and atomicity

![](https://i.imgur.com/xk2BfFX.png)

await 이전에 코드가 실행된다는 쓰레드가 continuation를 실행한다는 것을 보장할 수 없다.
자발적으로 task를 deschedule함으로써 영속성을 위배할 수 있다.

그래서 조심해야할점!

![](https://i.imgur.com/tnGBXyD.png)

마지막으로 Runtime Contract

![](https://i.imgur.com/mTkQNNQ.png)

첫 번째는 안전하고 compiler가 체크하기 때문에 primitives에 대해 도와준다. 그러므로 안정성이 보장된다.

두 번째는 동기적이어서 안전해보이지만 compiler가 도와주지 않으므로 스스로 locks를 정확하게 사용하는지에 대해 체크해야한다.

세 번째는 스위프트 concurrency에서 안정적이지 않고, runtime이 dependency도 모르므로 알아서 해야한다.

사용하지 말아야 할 방법!!

![](https://i.imgur.com/uJjTnVS.jpg)

threads들이 앞으로 진행되는 것에 대한 runtime contrast를 위배하고 있다. locks에 대해서도 알아서 관리해야한다

만약 unsafe한 곳에서 사용할 때 이를 테스트하는 방법은 아래와 같다

![](https://i.imgur.com/PzYlV9e.png)

이것을 활용해서 테스트 해볼 수 잇다.

![](https://i.imgur.com/YvVhNDX.png)

이렇게 hang이 발생한 것을 확인할 수 있고 unsafe한 blocking primitive를 쓴 것을 확인할 수 있다.

### Synchronization

#### Mutual exclusion
동시에 concurrenctly하게 동일한 자원을 접근하지 못하도록 한다.

![](https://i.imgur.com/45fkD4r.png)

기존의 방식들이 하나씩 문제가 발생 -> Actors를 활용해서 해결!!!

Actor를 활용하면 아래처럼 actor로 만들어지고 Networking에서도 이전에는 하나의 Queue가 있었는데 이를 각 피드마다의 actor로 바꾼다.
또 데이터베이스와 네트워킹 두개가 소통되도록 한다.

Actor를 사용할 때에는 Non-blocking으로 이루어진다.
또한 Hopping할 때에 다른 쓰레드를 요구하지 않는다. 동일한 쓰레드에서 동작된다.

![](https://i.imgur.com/oAWkRe7.jpg)

하나의 피드 또 생긴다! 동일한 DB 필요하면 새 work item를 만들어서 사용한다.

![](https://i.imgur.com/eWxKngn.png)

![](https://i.imgur.com/jaSWVKd.jpg)

그리고 초기 데이터베이스가 다 완료되면 database actor 없앤다.
그럼 이제 runtime이 D2를 이어서 하거나 새로운 것을 하거나 이전의 feed를 하거나 결정할 수 있다.

#### Reentrancy and prioritization

![](https://i.imgur.com/MDlsEgI.jpg)

이렇게 DB Queue에 넣어둔다.

A가 끝나고 1,2,3,4,5가 있는데 이에 대한 우선순위가 정해져있지 않다.
이를 정하느 것을 prioirty inversion이다. 이렇게 해서 큐에 우선순위를 정해서 가장 먼저 끝나는거나 이런 것을 정해서 해결한다.

Actor reentracny

![](https://i.imgur.com/NhG1H97.jpg)

이렇게 중간에 바꾸기도 한다. 그래서 mutual exclusion를 유지하면서 정해진 시간에 원하는 것을 실행하도록 교체하게 된다.

이제 위에서 사용되는 방식을 actor로 보여준다.

![](https://i.imgur.com/soW0xBP.jpg)

priority를 다시 정해서 B가 앞으로 오게한다.

![](https://i.imgur.com/BRtTJ2n.jpg)

#### Main Actor
Main actor는 thread들과 분리되어있는 main thread이므로 context switch를 요구한다.

![](https://i.imgur.com/WxHHVjw.jpg)

요런 문제가 발생!!!! context switching 너무 자주 일어난다.

![](https://i.imgur.com/0mgEeM7.png)

![](https://i.imgur.com/N065KqW.jpg)

이렇게 해결

![](https://i.imgur.com/q6SA9GQ.jpg)
