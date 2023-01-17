# Swift concurrency: Update a sample app

앱을 통해 concurrency with SwiftUI 사용한다.

Coffee tracker 앱

![](https://i.imgur.com/NTnD9Tl.png)

UI layer, model layer(UI Model), backend layer

![](https://i.imgur.com/yvoLCoY.jpg)

concurrency 관점에서의 3개의 분리가 가능하다.

main, background, arbitary queues 가 있다.

![](https://i.imgur.com/JYxsx1g.jpg)

Concurrency architecture 와 함께 나눠지는 것을 보여준다.

![](https://i.imgur.com/TOa0tcW.jpg)

간단하고 쉽게 구현할 수 있다.
async/await, actor는 다른 WWDC에서 이미 설명했다.

healthkitController에 completion handler가 존재한다.

![](https://i.imgur.com/x8mynS5.jpg)

thread safety 에 대해 먼저 설명하자면 store 메서드가 있는데 이것은 안전한지 파악하기 어렵다.
왜냐하면 밖에서 여러곳에서 접근할 수 있기 때문이다.

여기에서 dispatchqueue, locks 아무것도 사용하지 않고 있기 때문에 어떤 곳에서 만약 thread-safe를 만들고 있다면 이것이 어느곳인가에 있을 것이다.

여기에서 간단히 코드만 보고 다른 곳을 보지않고도 판단할 수 있는 것을 local reasoning이라고 한다.

value type인 Swift emphasis는 local reasoning이다.

reference type과 달리 value type는 프로그램에서 변할 것이라고 생각할 필요없다 왜냐하면 복사해서 사용하기 때문이다.

이 save 함수는 thread-safe하다.

여기에서 completion handler를 사용해서 success, error를 내보내고 있다.

![](https://i.imgur.com/9VJOmdp.png)

error를 던질 수 없으나 async에서 이를 활용해서 던질 수 있다!

코드를 바꾸면 아래와 같다.

![](https://i.imgur.com/lUXSlBn.png)

첫번째 에러가 근데 남아있다! async 함수를 적어야한다.

![](https://i.imgur.com/gnDqnC4.png)

이것을 쓰면 이부분에서 컴파일 에러는 해결되지만 실제 사용하는 곳에서도 이를 바꾸어줘야한다.

async 메서드를 호출하기 위해서 이를 task로 감싸서 사용한다
이는 async function를 호출하는 것을 허락하는 의미이다.

![](https://i.imgur.com/vI4WhFI.png)

또한 변할 수 있는 global state를 동기적으로 건들지 않는 의미이며 이는 thread-safe하다는 의미이기도 하다.

그러면 이제 컴파일 에러가 끝나서 실행된다.

이제 유저의 health data를 접근하는 곳으로 가자.

![](https://i.imgur.com/7zCs5xB.png)

유저에게 권한 요청하는 것인데, 이곳에서도 comletion handler를 사용하는데 이를 리팩토링한다.

여기에서 바꾸고 싶은 함수에서 command + shift + a 를 누른다음 async를 호출하고 add Async alternative 누르게 되면 이 함수를 async/await를 사용하도록 애플 내부 시스템에서 바꾸어준다.

![](https://i.imgur.com/IrXwhwl.png)

그럼 사용처, 호출하는곳 모두 바꾸어준다.

![](https://i.imgur.com/n1HosII.png)

원래 내부에서 thread-safety라고 하는데 안에서 사용하느 isAuthorized는 동기적으로 사용되어서 안전하지 않다고 생각한다.

바뀐 코드에서도 어떤 것도 safe하게 completionhandler를 다루고 있다.

그래서 이를 해결하기 위한 작업이 필요하다.

![](https://i.imgur.com/Xyq7MI6.png)

isAvailable를 체크하고

권한 요청하는 것도 결과 가져오는게 없어서 받는 것을 없애고 success대신 true로 값을 바꾸어 준다.

실패에서도 return false를 한다.

이렇게 바꾸어준다.
 
![](https://i.imgur.com/8UcSvBx.png)
 
single async function이 query만들고 실행되도록 할 것이다.
helper function인 queryHealthKit를 호출해서 내부는 아래에서 사용하는 코드를 가져와서 continuation를 만들어서 바꾸어 준다.

![](https://i.imgur.com/LpUVHI4.jpg)

그리고 새 데이터를 로드하는 곳에서 이 메서드를 호출해서 값을 가져온다.

![](https://i.imgur.com/AkAaiEl.png)

메인 쓰레드에서 호출하는 것을 MainActor를 통해 바꿔줄 수 있다.
async function를 await를 호출해야한다. 왜냐하면 suspended 상태가 생길 수 있기 때문이다.

여기에서 이제 Capture에 대한 에러를 준다.
비동기 함수 내부에서 일어난다.
newDrinks 배열이 참조인 곳에서 변화가 일어나기 때문에 race condition문제가 생길 수 있다.

![](https://i.imgur.com/vpvtQcM.png)

캡쳐를 통해 복사해서 사용한다.

이거 대신에 변할 수 없게 상수로 바꿔서 해줄 수 있다.

![](https://i.imgur.com/4JWq1en.png)

else 대신 nil-coalescing를 활용해서 해줄 수도 있다.

이렇게하면 어떤 캡쳐도 사용할 필요없어진다.

메인쓰레드에서 사용하지 않으면 build에서 에러가 나곤 한다. 하지만 이렇게 하게 되면 일일이 찾아봐야 하는 귀찮음이 발생할 수 있다.

![](https://i.imgur.com/SmKo1Gd.png)

이것을 컴파일단에서 해결할 수 있는 방법이 있다 -> @MainActor

그래서 만약 MainActor 밖에서 호출하게 되면 이처럼 컴파일 에러가 나타난다.

![](https://i.imgur.com/7iZoXZs.png)

그리고 MainActor내에서 호출할 필요 없이 밖에서 호출하는 actor의 경우에는 await를 작성해서 해주면 에러가 나지 않는다.

![](https://i.imgur.com/ZacOqA0.png)

suspended되었다가 main thread의 작업이 끝나면 실행되게 된다.

그리고 class에도 사용할 수 있다

![](https://i.imgur.com/2rxjQFO.png)

이렇게 사용하면 내부의 모든 프로퍼티, 메서드가 다 main thread에서 동작하도록 한다.

하지만 이 클래스에서 일부가 뒤에서 동작하는 것이 있을 수 있다.

그래서 actor라고 앞에 표시해서 바꿔준다 actor class HealthkitController { }

actor conversion를 사용하는 것보다 actor를 사용해서 동기에서 비동기 코드를 실행한다

CoffeeData.swift로 옮긴다.

![](https://i.imgur.com/EjmijwG.png)

모델에 관련된 것이기 때문에 HealthkitController에서 옮겨준다.

옮기면 이제 다른 부분에서 에러가 나는데,

![](https://i.imgur.com/NEvEUQN.png)

이 메서드는 actor에 의해 보호되어 있어서 직접적으로 호출할 수가 없다.

하지만 actor의 상태의 어떤 부분도 건드리지 않고 있다.

여기에서 내부의 어떤 메서드에서도 호출되고 있지 않기 때문에 이를 nonisolated로 바꾸어 준다.

![](https://i.imgur.com/2sZmPv8.png)

nonisolated란?

컴파일러에게 isolated의 상태이므로 어디에서도 touch하지 않으므로 이 메서드는 다른 어떤 곳에서 호출될 것임을 알려주는 용어다.

그래서 이렇게 내부의 프로퍼티를 호출하게 되면 컴파일 에러가난다

![](https://i.imgur.com/5l8XWHd.png)

main thread에서 동기적으로 동작하는 것을 보여주고 있다.

![](https://i.imgur.com/MTayHTL.png)

handle -> loadNewDataFromHealthKit -> updateModel -> didSet -> save -> loadNewDataFromHealthKit에서 completionhandler를 통해 handle에게 전달하고 이것을 WatchKit에게 알려준다.

이는 동기적으로 강제로 실행되도록 만들고 있다.

이를 수정하는 것으로 didSet를 값이 변하면 자동적으로 호출되게 되는데 이런 것들을 비동기로 바꿔야 한다.

![](https://i.imgur.com/TwnOri2.png)

내부에서만 이 클로저가 호출되도록 private(set)를 넣어준다.
그리고 메서드로 만든다음 async/await를 활용한다.

![](https://i.imgur.com/8FX3MQw.png)

그리고 currentDrinks를 값이 업데이트가 된 다음 사용하도록 만들어야한다.

![](https://i.imgur.com/T32JOCR.png)

그리고 update하는 부분에서도 async/await를 작성해주어야 한다.

마지막으로 이것을 load하는 곳을 저장하는 파트에 복사한다.

![](https://i.imgur.com/mva0hb5.jpg)

background, main 부분이 나누어져 있는 것을 볼 수 있다.

이렇게 되어있는데 이 부분은 대부분 앱이 실행할때만 사용된다.

그래서 시스템에게 이것을 변하지 않고 항상 correct할 것이라고 만들 수 있다.

![](https://i.imgur.com/CuOSnzZ.png)

background, main 나눈 부분을 다 없앤다.

그리고 이를 실제 모델에서 이 메서드를 actor로 부터 await를 호출해주면 된다.
그리고서 actor를 기다리고 있기 때문에 async로 사용한다.

![](https://i.imgur.com/cAoGrn4.png)

결론

![](https://i.imgur.com/0sfPMdN.jpg)

















