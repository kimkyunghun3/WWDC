# Explore structured concurrency in Swift

Swift 5.5에서 새로운 concurrency 제공한다.

구조적 프로그래밍의 장점

![](https://i.imgur.com/hEPQs3U.png)

위에서부터 아래로 이동한다

block -> Static code이다.
Static code란?
블록안에서만 정의되어있어서 안에서만 보이는 것을 말한다.
lifetime도 block 내에서만 이루어진다.

구조적 프로그래밍 + static code
control flow를 이해하기 쉽게 만들어준다.

### concurrency programminga이 어떻게 asyncronous를 더 간단하게 만들까?

completion handler로 만든 코드

![](https://i.imgur.com/cGW5Lml.jpg)

에러 핸들링

![](https://i.imgur.com/HvSkYtx.jpg)

내부에서 하지 않고 함수 밖에서 이루어진다.

async/await 

![](https://i.imgur.com/xde1aU7.jpg)

만약 썸네일 몇천개를 가져와야한다면?

concurrency를 활용해서 병렬로 다운로드 시킨다!

![](https://i.imgur.com/1fgqyxD.png)

Tasks라는 것을 활용해서 가능하게 된다!

### Async-let binding

![](https://i.imgur.com/C7F7izw.png)

이전 statements에서 시작해서 초기화를 통해 image를 다운로드 받는다.
다운받는데 잠시 시간이 걸린다음 이것을 result에 할당해준다.

다운 받는 시간이 조금 걸리지만 그 기간동안 시스템이 아무것도 안하지 않고 다른 일을 했으면 좋겠다고 생각한다.

![](https://i.imgur.com/JNE75Xi.png)

이렇게 해서 다른 일을 하도록 지시하는 것이다. 이것이 async-let이라고 부른다.

![](https://i.imgur.com/rdHpw9a.png)

이제 실제로 동작하는 것은 위와 같다.

child task를 만들어서 이미지 다운로드를 시작하고 다운로드가 완료되면 parent task를 통해 result에 할당한다.

그리고 parent task는 child task의 작업이 다 끝나기를 기다린다. 그 이후에 result에 정확한 값이 들어오게 된다.

error가 올 수 있으므로 try를 던져준다.

이제 asnyc-let이 동작하는 것을 코드로 보여준다.

![](https://i.imgur.com/RWfqkxb.jpg)

metadata, data 2가지를 다운로드 받아서 저장한다.

![](https://i.imgur.com/IpphV01.png)

Task tree라고해서
child task - parent task를 이렇게 표현한다.

childe task 다 끝내야 parent task가 끝날 수 있다.

![](https://i.imgur.com/tT5rOBl.png)

한 쪽에서 부적절한 exit로 인해 error를 던지게 되면 다른 쪽에서 다운로드할 때에는 그 함수가 exit가 되기전에 이미 Swift에서는 이것이 이미 실패할 것을 알고 mark 해둔다. 하지만 cancel를 하지않고 실행이 되며 단순히 task에게 이것의 결과는 더이상 필요없다는 것을 알려준다.

만약에 위에서 cancel이 되면 그 아래의 것들도 다 cancel이 된다.

이것이 structured concurrency이다.
이렇게해서 leak를 막을 수 있다.

그러면 task가 그래서 마지막 언제 stop 되나?

![](https://i.imgur.com/WV2luiB.png)

불필요한 다운도르를 없애기 위해 cancellation를 확인한다.

![](https://i.imgur.com/Bve00xb.png)

### Group tasks

dynamic하게 tasks 사이즈를 정해서 concurrnecy하게 동작한다.

![](https://i.imgur.com/xxM22cK.png)

하나의 task에 대해 그룹을 만들어서 async-let tasks를 2개 생성해서 동작하게 만든다.

![](https://i.imgur.com/xJI05k7.png)

하지만 data-race 문제가 있다!

2개의 tasks가 하나의 썸네일을 만들어서 넣기위해 경쟁해서 문제가 생긴다.

이것을 해결하는 방법

![](https://i.imgur.com/otOXl6f.png)

async-let
tasks 중 하나가 error가 던져지면 다른 task 들도 암시적으로 cancel이 되고 await 된 것이다.

하지만 for try await에서는 만약 그룹이 block 밖에서 문제가 생긴 경우에는 암시적으로 cancel이 되지 않는다.
이럴 때 cancelAll method를 통해 한번에 모든 tasks를 cancel할 수도 있다.

### Unstructured tasks

![](https://i.imgur.com/mqBKjv6.png)

모든 tasks에서 이 패턴을 사용할 수는 없다.

활용 예시

![](https://i.imgur.com/8glcskz.png)

Task를 넣어서 unconstructured한 것을 constructure하게 만들어 준다.

![](https://i.imgur.com/u9uDMyE.jpg)

collectionView 실행 중에 Task를 돌게 되고 이는 main thread에서 동작하므로 뒤에 넣고 앞에서부터 뒤로 실행시킨다.

Unstructured tasks

![](https://i.imgur.com/gAShtIb.png)

cancellating이 자동으로 이루어져있지 않다.
그래서 구현이 필요하다!

Task에서 나온 것을 thumnailTasks 배열에 넣어준다.
그리고 defer를 통해 사용하고 끝난 것을 없애줌으로써 접근을 못하도록 한다.

그리고 작업이 끝나면 아래의 delegate method에서 cancel를 호출해줘야한다.

![](https://i.imgur.com/a4tOAqP.png)

### Detached tasks

보통 상속을 다른 context 통해서 사용하는데, 상속을 통해 사용하고 싶지 않을때 사용한다.

그래서 다른 context로부터 독립적이다.

![](https://i.imgur.com/fTGvn8X.png)

서버에서 썸네일을 가져오고 우리는 local disk cache를 써서 가져올 때 네트워크를 통해 가져오는게 아니라 로컬에서 가져오고 싶다.

![](https://i.imgur.com/7C3CcEB.jpg)

캐싱은 메인 actor에서 할 필요가 없다.
그래서 caching를 detached task로 구현한다.
caching는 낮은 우선순위로 메인쓰레드가 아닌 곳에서 사용된다.

만약에 background에서 여러개를 가져와야한다면?

![](https://i.imgur.com/IloshbG.jpg)

이것의 장점은 만약 가장 위에서 cancel하면 아래도 자동적으로 cancel이 되도록 되어있다.

정리

![](https://i.imgur.com/az6LWyI.png)
