# Understand and eliminate hangs from your app

목차
hangs이 무엇인지

hangs 발생 원인

hangs을 진단하는 법

hangs을 제거하는 법

### hangs이 무엇인지

예를 들어 테이블뷰에서 셀의 디테일로 들어갈 때 몇초간 멈춰져있다가 들어가지는 현상을 말한다

![](https://i.imgur.com/CPGqeD9.png)

먼저 메인 런루프를 알아야한다.

![](https://i.imgur.com/d3zsmjL.jpg)

다가오는 이벤트에 reponse해서 event handlers를 실행하기 위해 사용된다.

이 기간동안 유저의 input, 이벤트 업데이트 동안 기다려야한다.
그래서 hang이 발생한다!

![](https://i.imgur.com/X1xC2Wr.jpg)

유저가 view를 진입할 때 생기는 delay는 잘 감지하지 못하는데 스크롤때 생기는 버벅거리는 delay는 감지를 한다.

![](https://i.imgur.com/YzCr6sh.png)

### hangs 발생하는 이유

메인 쓰레드에서 work를 초과해서 사용해서 그렇다.

예시

![](https://i.imgur.com/nj84M8s.png)

메인 쓰레드 하나는 바쁘고 하나는 blocked 되어 있는 상태다.

Proactively doing work

![](https://i.imgur.com/8AMbTDD.png)

UI를 업데이트에 필요한 작업을 필요 이상으로 하는 것을 말한다.
그리하여 main thread가 바쁜 상태를 오래 유지한다.

![](https://i.imgur.com/68owVE8.jpg)

여기에서 많은 성분 이미지들 중 4개만 보여주고 있다.
하지만 메인 쓰레드에서 하넙ㄴ에 모든 성분 이미지들을 load하게 되면 매번 이미지마다
read, prepare, composite를 해야한다.

![](https://i.imgur.com/No3JRbR.png)


또다른 hangs 유발 원인이 있다

![](https://i.imgur.com/06qyvZt.png)

메인쓰레드에서 관련없는 작업을 수행할 때 이다.

main dispatch queue로부터 메인쓰레드 서비스가 block된다.
그러나 이것은 다른 큐에서 dispatch sync를 경유한 것으로부터도 블록이 된다.

![](https://i.imgur.com/ufbebVi.png)

![](https://i.imgur.com/0qmrW4r.png)

앞에 work를 기다린 후에서야 load가 되므로 hangs이 유발될 수 있다.

![](https://i.imgur.com/ZO4OwLI.png)

여기에서도 메인쓰레드에서 실행하려고 block이 된다.

또다른 원인

![](https://i.imgur.com/ignPLe5.png)

API docs를 읽어서 사용해라!

![](https://i.imgur.com/OnGzylF.jpg)

rounding corner를 만들고 있다.
뷰를 들어갈때 이미지들이 있으므로 latency가 생긴다

![](https://i.imgur.com/iV1XyDa.png)

bitmap 사용해서 하므로 CPU를 강렬하게 사용하므로 메모리를 많이 사용한다.

CPU 사용하는 대신 GPU를 사용하도록 하자!

![](https://i.imgur.com/yWpBuUn.png)

앞에서는 busy의 원인을 찾았다

이번에는 이제 blocked 가 되는 것을 찾도록 하자!

![](https://i.imgur.com/nPTH4wq.png)

Synchronous API는 리턴하는 시간에 부르는 시간으로부터 block를 실행한다.

![](https://i.imgur.com/8kaj3ir.png)

API가 많은 일을 하게되면 main thread를 사용하지 않을 것이다.
아니면 오랜 시간동안 잠재적으로 block이 될 수 있다.

또한 추가적으로 실패를 할 수도 있다

앱의 메인쓰레드는 네트워크에 동기적 요청이 이루어진다.

5G에서는 delay가 아마 없을텐데 속도가 느린 곳에서 많은 시간이 걸릴 것이다.
메인쓰레드에서 왜 동기적 작동을 피해야하는지에 대해 말한다.

또 다른 원인

![](https://i.imgur.com/mbptRlD.png)

하드웨어에 따라 latency가 발생하고 다른 읽고 쓰는 것에서 동시에 생길 수 있다.
이것들은 앱에서 컨트롤 하는 것을 뛰어넘는다.

그래서 app에서 메인쓰레드 File I/O의 hang 문제를 방어해야한다.

Data 저장하는 곳이 concurrency를 지원하지 않으므로 특히 문제가 된다.

만약 메인쓰레드에서 하나를 읽고 있는 동시에 writes가 이미 실행중이면

![](https://i.imgur.com/P2e2jp8.png)

read는 write가 끝나기 전까지 block된 상태로 기다린다.

또 다른 원인

![](https://i.imgur.com/WDuwuL6.png)

이것은 block 유발하므로 잘 사용해야한다!

![](https://i.imgur.com/R2LZIE4.png)

간접, 직접적으로 lock를 발생한다.

관련 용어들

![](https://i.imgur.com/TdEbg6K.png)

세마포어 사용을 주의해야한다.

![](https://i.imgur.com/cW0Zi4C.png)

우선순위를 전파하지 않으므로 선점에 의해 hang이 더 길어질 수 있다.


또 다른 원인

![](https://i.imgur.com/ty8DKQy.png)

IPC 작업에 의해 발생
어떤 값을 가져오기 위해 시스템 자원들을 사용할때도 그렇다

![](https://i.imgur.com/4rC9u7P.jpg)

친구랑 컨택이 되었을 때에만 이 아이콘이 생긴다. 그러나 뷰에서 모든 tap에서 이 컨택들을 쿼리해서 체크해야하므로 불필요한 오버헤드와 지연을 발생시킨다.

![](https://i.imgur.com/K7ZVZwt.jpg)

CPU, Memory가 hang을 가장 많이 일으킨다.

다른 하드웨어를 모두 가지고 있으므로 이 말은 즉, 테스트 하는 모든 desk에서 결과가 다를 수 있다.

**hangs를 진단하는방법**

profiler, system trace 도구를 사용할 것이다.

![](https://i.imgur.com/WYNvV79.png)

가상 메모리 trace에서 보라색 그래프는 virtual memory faults를 말한다.

블루 bar는 메인쓰레드가 바쁘게 work를 하고 있다고 말한다.

밑에 stack trace에서 약 4.6초 정도 hang이 생긴 것을 볼 수 있다.
loadAllMessages method 때문에 생긴것이다!

hangs에 대한 call trees를 모으는 필드로 metricKit를 사용하면 좋다

![](https://i.imgur.com/YxtXOWz.png)

metricKit이 보여주는 화면

![](https://i.imgur.com/2FpD9ni.jpg)

여기에서 소셜 icon를 넣어서 사용하는데 이 부분이 hangs를 유발하고 있다.

![](https://i.imgur.com/1eIjAch.png)

다음으로는 성능 측정가능하다!!

![](https://i.imgur.com/yca4FS6.png)

### hangs 제거하는 방법!

가장 쉬운 방법!

![](https://i.imgur.com/kszei09.png)

메인쓰레드에서 work를 줄인다

![](https://i.imgur.com/TXuskkL.png)

캐시를 사용하면 좋다!

캐시를 사용해서 자주 사용하는 asset이나 이전의 쿼리 값들을 넣어두고 사용한다.

Caches

빠르게 액세스할 수 있다.
캐시를 사용하기 위해서 적절한 캐시 invalidation을 가지고 있는게 중요하다.

![](https://i.imgur.com/tWgMxLz.png)

그래서 주기적으로 캐시를 업데이트 해줘야한다.
이는 비동기적으로 다른 dispatch queue를 사용한다
그리하여 메인쓰레드가 계속 작동할 수 있다.

다른 방법
Observers

시스템 노티에서 관찰할 수있는 부분을 찾아서사용한다

![](https://i.imgur.com/2bbG8gN.png)

메인 쓰레드에 responsive를 유지하기 위해서 이 updates들은 모두 비동기로 되어야한다.

![](https://i.imgur.com/QTguPZD.png)

main thread에서 벗어난다.
중요한 UI 관련된 것은 안된다!
View, ViewCOntroller도 안된다.

UI 요소를 업데이트 하기위한 연산관련된것은 메인쓰레드에서 할 필요 없다!
그래서 completion handler를 활용해 메인쓰레드에서 업데이트 시킬 수 있다. 왜냐? 이부분이 시간이 많이 걸리기 때문이다.

또 다른 방법

![](https://i.imgur.com/jMFaX2Q.png)

동기로 되어있는 것을 비동기로 바꿀 수 있는 것을 바꿔라!

![](https://i.imgur.com/wgO5Lpl.png)

다음은
GCD

hangs를 제거하기 위해 매우 유용하게 사용된다!

![](https://i.imgur.com/Mx7NPcs.png)

![](https://i.imgur.com/qVCVhbO.png)


위에 말한 것들 UX에서 중요하다 

hangs 언제 제거하냐?

![](https://i.imgur.com/HA56cKH.png)

### Wrap up!

![](https://i.imgur.com/F8jASet.jpg)
