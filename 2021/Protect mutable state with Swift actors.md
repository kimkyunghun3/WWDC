# Protect mutable state with Swift actors

data race가 concurrency 어렵게 만든다.

데이터 레이스는 발생할 가능성이 매우 크다

OS에서 매 시간마다 여러개의 Task를 돌리기 때문에 Data race가 발생할 가능성이 크다

![](https://i.imgur.com/FN839GO.jpg)

concurrency하게 동작하지 않거나 데이터 여러개가 있지 않다면 일어날 일은 없다.

변수를 value type으로 가지고 있는다!
아니면 let으로 바뀌지 않도록 해서 안정성을 추구한다.

![](https://i.imgur.com/r7tHdk1.png)

이렇게 변경한다

![](https://i.imgur.com/7K5p7bm.png)

counter를 변수로 담아서 사용해서 해결할 수 있지만 공유된 변할수 있는 상태를 얻는게 필요해지게 된다.

공유된 변할수 있는 상태

![](https://i.imgur.com/seOJJMA.png)

동기적으로 동작해야하고 내부적으로 이러한 특징들이 존재한다.

Actors

![](https://i.imgur.com/3D3xuSy.png)

동기적으로 동작하지 않으면 컴파일러가 알려준다.

Actor types

![](https://i.imgur.com/QdDXgsF.png)

2개의 concurrent task가 동일한 counter의 수를 증가시킨다

1 2 or 2 1 순으로 증가한다.
왜냐하면 내부적으로 동기적으로 작동하기 때문에 동시에 하나의 값을 증가시킬 수는 없다

![](https://i.imgur.com/0TORy8Z.png)

여기에서 한 곳에서 일을 하고 있으면 다른곳에서는 그냥 막연하게 기다리느냐?

그렇지 않다. CPU가 이제 판단해서 suspended상태로 바꾼 다음 다른 일을 수행한다.

Actor 내부에 있으면 동기적으로 동작한다.
 
![](https://i.imgur.com/oLGLsmG.png)
 
### Actor reentrancy

액터가 동기적으로 동작하지만 여러 곳에서 상호작용할 때 비동기로 동작하는게 어떻게 할껏인가?

![](https://i.imgur.com/92N63CM.png)

캐시에서 이미지를 다운로드 하는 프로세스

![](https://i.imgur.com/TvyMz9b.jpg)

캐시에 이미지가 없을 경우 await로 비동기로 작동하므로 cPU는 suspended task에 넣어두고 다른 일을 한다.

2번째에도 동일

![](https://i.imgur.com/nNJJmoJ.jpg)

그리고 다운다되면 돌아와서 배열에 들어가고 끝나게 된다

![](https://i.imgur.com/aAzzKeb.png)

여러번 중복 다운로드 되는 것을 피하고 deadlock이 되는 것을 막는다

![](https://i.imgur.com/ENeWiXG.png)

결과적으로 이렇다

![](https://i.imgur.com/Ury00Zb.png)

### Actor isolation

프로토콜방식

![](https://i.imgur.com/RDO38zK.png)

해쉬 방식

![](https://i.imgur.com/bkZWraC.png)

이렇게 하면 안된다.

이 함수가 밖에서 불려야한다 하지만 이건 asnyc가 아니라서안된다!

그래서 해결법

![](https://i.imgur.com/1eYWtx8.png)

동기적 요청에 대해 actor 밖에서 할 수 있다.

하지만 이럴때 에러가 난다

![](https://i.imgur.com/UEtRydM.png)

왜냐하면 밖에서 부르므로 data race현상이 일어날 수 있다

클로저 형식

![](https://i.imgur.com/Sw6f97q.png)

클로저는 내부에서 actor-isolated가 되어있으므로 에러가 나지 않는다.

동기적으로 동작하고 클로저가 밖으로 나가지 않기 떄문에 괜찮다.

만약 나중에 read하려고 하면 어떻게 해야할까?

안에서 밖으로 데이터를 내보낼 때 

구조체인 경우 

![](https://i.imgur.com/HpcFVk8.jpg)

클래스인 경우

![](https://i.imgur.com/n4hDcwh.jpg)

클래스는 문제가 존재한다. 왜냐하면 여러곳에서 접근하므로!

sendable type에 대한 설명

![](https://i.imgur.com/AlRhxBp.png)

Sendable 검사하는 것

![](https://i.imgur.com/TmFbe35.jpg)

Sendable는 프로토콜로 되어있어서 채택하면 된다.

그래서 채택해서 sendable type으로 만들 수 있다

Sendable 타입은 여러 곳에서 접근하는 것을 막아주고 concurrent하게 동작하게 해준다

![](https://i.imgur.com/yuLnLIr.jpg)
 
Sendable functions의 의미는 함수의 값을 pass하는 것이 안전하다

예를 들어 클로저가 있다. 클로저에서 data races가 일어나는 것을 막아줄 수 있다.

또한 sendable 클로저는 변할 수 있는 지역변수를 캡쳐할 수 없다.

동기적 sendable 클로저인 경우 actor-isolated가 될 수 없다 왜냐하면 이것 자체가 actor가 밖에서 실행된다는 것을 의미하기 때문이다.

![](https://i.imgur.com/5cNqS8i.jpg)

초반에 했떤 것 value type counter에 대해

datarace 유발할 수 있는 것에 대한 에러

![](https://i.imgur.com/5ciyVu5.jpg)

또 다른 예시

![](https://i.imgur.com/nafEBHz.png)

Sendable type 의 장점 data race 방지 및 actor-isolated

### Main actor

메인 쓰레드와 상호작용

![](https://i.imgur.com/dy8frxM.jpg)

메인 액터

![](https://i.imgur.com/B0Y4IhF.jpg)

메인액터는 모두 매인 디스패치큐에서 동기적으로 작동한다.

데이터는 메인쓰레드에서 있어서 항상 메인 액터에서 동작한다.

밖에 있다면 await하고 비동기적으로 메인쓰레드에서 동작하도록 한다.

### Main actor types

메인액터에 타입이 위치되어 있어야 한다.

![](https://i.imgur.com/iz7HxaF.jpg)

결론적으로 concurrency하게 동작하도록 사용한다.

![](https://i.imgur.com/75DhNHX.png)

값타입과 actor 동시에 동작하여 data race 제거한다.

메인엑터를 사용해서 UI와 관련있는 것은 모두 메인쓰레드에서 작동하고 실행되도록 한다.


