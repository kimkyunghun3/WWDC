
# wwdc swift concurrency 시각화 및 최적화 

concurrency 관한 시각화 도구를 만들었다.

우선 concurrency 에 대한 요약 정리를 다시 보자. 

그리고 concurrency 시각화 도구로 실제로 동시성 작업을 할 때 생기는 문제들을 보고, 그것을 해결해 보는 작업을 해보기로 하자. 

## Swift Concurency Recap
![](https://i.imgur.com/lK3Fqxr.png)

얼마 전에 이런 것들이 추가 되었다.
자세하게 알고 싶으면 다른 세션들을 들으면 된다. 
여기서는 그냥 간단하게 정리해 보겠다.

![](https://i.imgur.com/HmBdTeT.png)

Async/await는 
실행 스레드 블로킹 없이도 작업을 실행 도중에 일시 중단했다가 나중에 재개할 수 있는 함수를 생성하고 호출할 수 있게 해준다.

![](https://i.imgur.com/tGXzmLg.png)

Task 는 동시성 코드의 기본 작업 단위이다. 상태 및 데이터를 관리한다.

![](https://i.imgur.com/YveVf3f.png)

구조화된 동시성 코드를 사용하면 병렬로 실행하고 완료될 때까지 기다릴 하위 작업을 쉽게 만들 수 있다. 
swift 언어는 await 이 있을 경우 기다리는 것을 보장 해주고, 그 이후에 await 에서 가져온 값이 사용되지 않는다면, 내부적으로 그 작업 자체를 취소해 버린다. 

![](https://i.imgur.com/vQ5uAVE.png)

Actor 는 여러개의 Task 들이 data 를 공유할 수 있게 도와 줍니다. 
Actor 에서는 외부와 정보가 분리되고, Actor 내부에서는 한번에 한 Task 만 실행 되도록 되어 있다.

동시 변화 될 수 있는 값들이 데이터 레이스를 일으키는 것을 막기 위해서 한번에 하나씩 실행됨.


![](https://i.imgur.com/gKJHonr.png)

애플이 새로운 vizualization tool(instrument) 를 만들었다고 한다. 

## Concurrency Optimization 

### 자주 일어나는 문제들 
* Main actor blocking 
* Actor contention 
* Thread pool exhaustion
* Continuation misuse


Main actor blocking 은 앱을 멈추게 한다. 
Actor contention (actor들 끼리의 논쟁) 과 Thread pool exhaustion(쓰레드 풀 고갈)은 병렬 실행을 줄여 앱의 성능을 저하시키고

Continuation misuse(연속성 코드 오용)은 메모리 누수나 충돌을 유발함. 

우리가 만든 짱 멋진 도구로 그 원인을 찾아내서 해결해 보자!

### Main actor blocking 
Main actor를 장시간 실행시 잃어남. 
![](https://i.imgur.com/EorbTxe.png)

Main 쓰레드는 엄청 엄청 중요하다. 
우리는 모든 UI작업을 메인 쓰레드 해서 한다. 그리고 다른 동시성 코드들을 통합해 주는 역할 도 하고 있다. 
그래서 결론적으로 메인 쓰레드가 해야할 일이 아주 많다. 

그래서 그 일들을 할 수 있게 너무 점유를 많이 해서는 안된다.
(가용성 유지)

막 무거운 계산 같은 작업을 메인 쓰레드에서 하게 되면 앱이 멈춘것 처럼 보일 수도 있게 된다. 

![](https://i.imgur.com/tfIbAJg.png)

Actor 를 사용하거나, detached Task 를 사용해서 좀 분산을 시켜주는 것이 좋다.

예시를 함께보자.
![](https://i.imgur.com/ExdgCfj.jpg)
이렇게 파일을 업데이트 해주는 앱인데 메인 쓰레드에서 작업을 해주고 있다보니, 큰 파일을 넣었더니 움직이질 않는다. 

동시성 코드를 시각화하고 최적화 할 수 있는 완전한 도구 모음을 제공한다. 

![](https://i.imgur.com/JAlwOmP.png)
가장 먼저 할 일은 최상위 통계를 살펴보는 것이다. 
실행중인 태스크로 
동시에 실행중인 작업의 수를 보여준다. 

![](https://i.imgur.com/lovKCXL.png)
활성 태스크는 특정 시점에 얼마나 많은 작업이 있는지를 나타낸다. 

![](https://i.imgur.com/FnKZNxs.png)
마지막으로는 총 태스크로 해당 시점까지 생성된 작업의 총 개수를 그래프로 표시 된다. 

앱의 메모리 사용량을 줄이려면, 활성 태스크 및 총 태스크 통계를 자세히 살펴봐야 하며, 이러한 모든 통계를 조합하면 코드가 얼마나 잘 병렬화 되어 있는지를 볼 수 있다. 
![](https://i.imgur.com/Wdr5m5v.png)
화면 아래쪽에 있는 태스크 포레스트는 Instruments 의 많은 세부 보기 항목중 하나로 구조화된 동시성 코드를 그래픽 적으로 나타낸 것. 

![](https://i.imgur.com/WaMJbsM.png)
task summery view 는
각 태스크에 소요된 시간을 보여준다. 

오른쪽 버튼을 통해 타임라인의 고정 하는 등...의 옵션들이 존재한다. 

task 를 타임라인에 고정하게 되면 
4가지의 주요 기능이 있다. 
![](https://i.imgur.com/Nt3ZsRZ.png)

1. swift task 가 어떤 상태인지 보여줌

![](https://i.imgur.com/ZZfHgJi.png)

2. 세부 사항 보기에서 작업 생성을 역추적 하는 기능

![](https://i.imgur.com/82tgaur.png)

3. 나래이터 보기를 통해 현재 swift task 가 어떤 작업을 기다리고 있는지 와 같은 맥락 정보를 제공한다. 

![](https://i.imgur.com/zyGHRnZ.png)
4. 요약 보기에서와 같은 핀 작업에 접근 가능 


이제 이 도구를 사용해서 문제를 해결해 보자.

![](https://i.imgur.com/PoZSnjX.png)

UI가 정지된 위치부터 얼마만큼 정지가 되었는지 확인해보자. 

(가장 위에 통계부터 확인하는 것이 좋다.)

![](https://i.imgur.com/poUoLkP.png)

Main 쓰레드를 찾아서 보니, 지금 메인 쓰레드가 작업들로 꽉 차 있는 것이 보인다. 

![](https://i.imgur.com/vxGrQBD.png)

여기서 생성 경로 역추적 정보를 보면, 
`compressAllFiles` 함수에서 태스크가 생성 됐음을 알 수 있으며, 내러티브 보기는 태스크가 `compressAllFiles` 을 클로저 1로 실행함을 보여 준다. 

오른쪽 버튼을 눌러서 소스 코드를 열 수 있다. 

클로저 1이 하고 있는 작업은 압축 파일을 호출하는 것이었다. 

이제 우리는 코드에서 어떤 부분을 고쳐야 할지 알고 있다. 

![](https://i.imgur.com/DFdQ2c9.png)


![](https://i.imgur.com/CDt277V.png)

우선은 `CompressionState` 클래스는 MainActor 에서 실행되게 되어있다. 
그리고 @Published 속성은 메인쓰레드에서만 업데이트 되어야 하기 때문에, 클래스를 액터로 바꾸면 오류가 뜬다.
![](https://i.imgur.com/iZCql2O.png)

이렇게... 기본적으로 
![](https://i.imgur.com/L53esgT.png)

file 변수는 메인액터로 부터 고립되어야 한다. 그래도 thread safe 하다.
왜냐하면, 이건 스위프트 UI 로부터 감시되고 있어서, 그 부분은 swiftUI 가 알아서 할 것이다. 

그런데 logs 는 동시 접근으로 부터 보호할 필요가 있다.

![](https://i.imgur.com/kD28oEQ.png)


![](https://i.imgur.com/aRVz6xO.png)

우선 `ParallelCompressor` 이라는 Actor 을 따로 만들었다. 

그리고 logs 라는 변수를 두었다. 

`compressFilse` 라는 메서드도 가져왔다. 

![](https://i.imgur.com/tygtoAp.png)

그리고 원래 있던 `CompressionState` 클래스에 `ParallelCompressor` 타입의 `compressor` 이라는 변수를 추가해 주었다. 

> 두개를 다른 쓰레드에서 돌릴수 있도록 분리 한 것 같다. 

![](https://i.imgur.com/27fGAnZ.png)

이렇게 코드를 고치고 돌려보면 이제는 UI가 멈추는 현상이 더이상 나타나지 않는다. 
근데 한번에 한 파일씩 정직하게 받는다. 
동시에 받을 수 있도록 바꿔보자. 

![](https://i.imgur.com/s2aEDLO.png)

![](https://i.imgur.com/kjyuRE2.png)

![](https://i.imgur.com/Aw0HLk8.png)
주의 할 점이 있는데 우리가 동시성의 좋은 점을 잠시 잊을 수도...?

왜냐면 모든 테스크가 액터를 기다려야 되니까...

![](https://i.imgur.com/u6MfY3N.png)
![](https://i.imgur.com/zX3MyAn.png)

어떤 것들은 무조건 actor 에서 작동해야 하는데, 어떤 것들은 그렇지 않다. 
이 둘을 분리하는 것이 중요하다. 
그럼 코드가 정말 엄청 빨라진다. 

![](https://i.imgur.com/kSa9byn.png)

자 여기 태스크 요약을 보면, 동시성 코드가 큐에 작업을 넣은 상태에서 맣은 시간을 소요하고 있는데... 그 이유가 다들 actor 독점이라서 actor 를 기다리고 있는 것이다. 

작업하나를 pined 해서 그 이유를 보자. 

![](https://i.imgur.com/dhPEpRo.png)

보니 actor 를 기다리느라 작업이 멈춰 있다. 

![](https://i.imgur.com/2jJa78w.png)

지금 이런 상황이다. 

우리는 compressFile 작업을 actor 에서 하고 있는데, 그래서 동시에 compressFile 메서드들이 실행되지 못하고 한번에 하나씩 actor 를 점유 하면서 실행된다. 

이것에서 자유하기 위해서는 아래와 같은 그림이 되어야 한다. 


![](https://i.imgur.com/nJpNtPp.png)

이렇게 되면, 
![](https://i.imgur.com/92Sf1f3.png)
![](https://i.imgur.com/ClGMh91.png)

![](https://i.imgur.com/yJqfVJP.png)


`files` 에 대한 접근이 필요할 때는 main actor 에 가서 가져오고, logs 에 대한 접근이 필요할 때는 ParallelCompressor Actor 에 접근해서 가져오게 되고,
실행은 Thread Pool 에서 하게 된다. 

결론적으로는 Thread의 개수 만큼만 제약을 받으면 되고, 어떤한 제약 없이 비동기적으로 실행되게 된다. 

이렇게 코드를 바꿔보자.

![](https://i.imgur.com/wWMJ6Ev.png)

우선 `compressFile` 메서드에`nonisolated` 를 붙여 준다. 
그러면, 오류가 난다. 

왜냐면, 일단 nonisolated 를 붙였다는 의미가 우리는 공유 자원 안써 이런 의미인데 실제로는 쓸거니깐. 

문제를 해결하려면, 해당 함수를 비동기식으로 만든 다음 모든 log 호출을 await 로 표시해야 한다. 
(막 가져다 쓰는게 아니고 우리도 이제 외부에서 실행되니 번호표 뽑고 기다렸다 쓰겠다.)

![](https://i.imgur.com/JqjgBlN.png)

그리고 이부분도 Task 를 detached 로 만들어 주어야 한다. 

(명시적으로 self 도 캡쳐해 줘야함.)

![](https://i.imgur.com/GQWFwYz.png)

이제 이 앱은 모든 파일들을 동시에 다운로드 한다!

![](https://i.imgur.com/Lf40Qw8.png)

다시 우리의 tool 을 보자. 
전이랑 그림이 좀 달라졌다. 작업들이 엄청 작아졌고, 이제는 대기열을 넘기는 수준의 큰 작업들은 아예 없어졌다. 

🎉🎉🎉

그런데 아직 2개의 잠재적인 문제가 남아있다. 

## Thread Pool Exhaustion 
![](https://i.imgur.com/vvNFkt6.png)
쓰레드 풀이 고갈되면, 성능이 저하 되거나 앱이 교착상태에 빠질 수 있다. 

thread 의 수가 모자르면, 여러 thread 에서 작업 되려고 하다가, blocking call 이 되어 쓰레드들이 멈출 수 있다. 

이렇게 되면 사용가능한 쓰레드의 수는 더욱 줄어들게 되고, 더욱 성능이 저하된다. 

운이 없으면 아예 모든 쓰레드가 bloking 될 수도 있다.

![](https://i.imgur.com/2O4UZEe.png)

**blocking calls 피하기**

Task 에서 blocking 을 호출하지 않는다. 
파일 및 네트워크는 IO 는 비동기 API 를 사용해서 수행해야 한다. 

또한 조건 변수나 세마포어에 대기하지 않도록 해야 한다. 

짧은 단위의 lock 은 허용되지만 긴 단위의 lock 은 안하는 것이 좋다. 

이러한 작업이 꼭 수행되어야 할 경우,
해당 코드를 아예 분리된 대기열에서 실행하게 하거나 그래야 한다. 

## Continuation Misuse

`continuation` 이라는 표현을 처음 접하게 되어서, 뭐지? 하고 한참 봤는데 callback closure 에 대해서 이야기 하는 것 같다. 

continuation 은 지금의 task 를 중단하고, call back 을 먼저 호출한다. 

call back 을 base 로 만든 API 를 사용해도 되지만, 주의 해야 할 점들이 있다.

swift concurrency 관점에서 보자면, 이는 Task 를 중단하는 것이다. 
그리고 continuation 이 실행될 때, 다시 실행된다. 

우리가 만든 툴에서도 그걸 볼 수 가 있는데 
Task 가 continuation 이 불리길 기다리고 있다. 

![](https://i.imgur.com/dPuD5Lu.png)
![](https://i.imgur.com/7to8CYe.png)

continuation 은 한번만 호출 해야 한다는 규칙이 있다. 

call back closure 가 두번 불리면, crash 가 날 것이다. 

근데 이런 것은 우리가 암시적으로 알고 있는 규칙일 뿐, 
언어적으로 이 규칙을 어겼을 때, 제한해 주고 있지 않는다. 

만약이 callback 이 불리지 않게 된다면, Task 의 메모리가 누수가 되는 것이다. 


![](https://i.imgur.com/iELWhfJ.png)

![](https://i.imgur.com/y5UbSn6.png)

쨋든 이렇게 사용성이 애매하니, 쓸거면 규칙을 잘 지켜라 이런 건 것 같다. 




