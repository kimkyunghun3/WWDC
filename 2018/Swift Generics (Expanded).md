# Swift Generics (Expanded) 

제네릭이 발전해가는 과정을 보여준다.

![](https://i.imgur.com/XiqLHwb.jpg)

오늘 할 것은 아래와 같다.

![](https://i.imgur.com/NMQtgho.png)

제네릭이 왜 Swift에서 중요할까?

![](https://i.imgur.com/qFaeIjQ.png)

제네릭을 안쓰면 리턴타입을 정해주어야한다.

타입을 하나로 고정해두었기 때문에 다른 타입을 넣을 수가 없다.

![](https://i.imgur.com/Us3aP9a.png)

이렇게 Any 타입으로 되어있으면 매번 컴파일러에서 어떤 타입인지 하나씩 확인하고 박스에 넣고를 해야하므로 overhead가 매우 증가한다.

![](https://i.imgur.com/EPN8hf5.png)

그래서 컴파일러의 유연성을 위해 다른 작업을 해준다.

![](https://i.imgur.com/sqyUZpd.jpg)

어떤 타입이 너무커서 안의 저장공간에 들어가기에 애매한경우가 있는데 이를 해결하기위해 Point를 활용해서 value를 indirection를 해주면된다.

이렇게 value가 모든 메모리에 저장되어있게 된다.

이렇게 사용하는 것이 단순히 사용하기 쉽고 정확한 것도 있지만 성능상의 이점도 존재한다.

이를 아래와 같이 부른다.

![](https://i.imgur.com/pY1KScq.png)

→ Generics

이렇게 타입을 Generic하게 정하는 것을 Element를 사용해서 할 수 있다.

Element가 여기에서 generics의 파라미터로 parametric polymorphism이라고 한다.

이것이 컴파일러에게 어떤 것이 들어있는지 말해주고 있다.

그리하여 Any → Element로 바꾼다

![](https://i.imgur.com/vaY8xmt.png)

이제 아까처럼 사용하게 되면 컴파일러가 타입이 맞지 않다면 바로 캐치해서 알려주고 있다.

![](https://i.imgur.com/j6LkyvP.png)

그러므로 타입 안정성을 추구할 수 있다.

가끔 이렇게 타입을 정하지 않고 할 수 있다. 

![](https://i.imgur.com/EeAGRpw.png)

이것은 모두  알다시피 컴파일러가 추론을 해서 타입을 알게되기 때문이다.

하지만 타입을 정해주면 compile, runtime에 모두 어떤 타입인지 바로 알 수 있다.

![](https://i.imgur.com/UTkdmxy.png)

![](https://i.imgur.com/Jr0Ihwj.png)

이렇게 되면 overhead가 발생하지 않는다. 심지어 임의적으로 large type이더라도 문제가 없다!

왜냐하면 컴파일때 어떤 타입인지 미리 알 수 있기 때문이다.

그래서 최적화 기회가 존재한다.

![](https://i.imgur.com/qy3FyjV.png)

Buffer에 어떤 타입이 있는지 바로 확인할 수 있으며 integer인지 바로 알 수 있다.

그래서 반복문에서 유용하며 CPU instruction이 효율적으로 사용되고 있다.

이렇게 작성하면 사용하기도 편하고 Generic 사용에 좋다.

![](https://i.imgur.com/dCGOvtn.jpg)

하지만 문제가 있다! 모든 타입을 sum할 수 없기 떄문에 컴파일 이슈가 발생한다.

타입을 제한해서 사용하면 쉽게 해결할 수 있다.

![](https://i.imgur.com/62Yu4Tp.png)

하지만 Int만 가능하므로 다른 float 등을 가능하게 하기 위해서 아래처럼 바꾸면 더 좋다.

![](https://i.imgur.com/8EuQqeh.png)

이제 Generic를 여러 Collection에서 어떻게 사용하는지 볼 것이다.

![](https://i.imgur.com/EiC8VgZ.png)

![](https://i.imgur.com/ffN7bfb.png)

![](https://i.imgur.com/PKo7A0k.png)

이를 프로토콜에서 Collection를 사용해서 여러 타입들을 제네릭하게 만들 수 있다.

![](https://i.imgur.com/8g1lR0K.jpg)

연관타입을 활용해서 원하는 Collection에 추가해줄 수 있다.

![](https://i.imgur.com/7DhXYhu.png)

이제 이렇게 Collection 에서 해주고 싶은데, Equatable때문에 제대로 동작하지 않는다.

![](https://i.imgur.com/iF50I4M.jpg)

이제 해결법은 모두 알다시피 아래처럼 해주면 된다

![](https://i.imgur.com/7KAdoZJ.jpg)

하지만 매번 Equatable를 구현해야하는 번거로움이 있다.

이를 프로토콜을 사용하면 더 좋게 해결할 수 있다

![](https://i.imgur.com/i3RBVHF.png)

이러면 구현에서 해줄필요없이 protocol에서 하므로 할 필요없다.

하지만 Swift 4.2에서는 이렇게 사용할 수도 있다

![](https://i.imgur.com/mKaj8mT.png)

### Customization Points

Dictionary가 있는데 이를 숫자 세게되면 0(n)이 아니라 O(1)로 동작하게 된다

![](https://i.imgur.com/5sm30dW.png)

딕셔너리로 하면 더 쉽고 간단하게 할수 있다.

제네릭으로하면 어떤지?

Array에서는 이렇게 되어있으면 result를 초기화하고 transform 해준다음 append한다.

하지만 가끔 내부 저장된 것을 재배치할 때가 있다. 왜냐하면 새로운 것이 들어오기 때문이다!

이렇게 메모리를 할당하는 것은 매우 비싸다.

그래서 최적화에 좋은 것이 있다!

![](https://i.imgur.com/PNoZdIx.png)

그래서 만약 끝까지 가게되면 array가 다시 새로 시작하기 전에 맨 앞으로 가게한다.

하지만 count가 제네릭으로 되어있어서 이것이 배열인지 딕셔너리인지 무엇인지 알수 없고 컴파일러도 컴파일할 때 알 수 없다.

하지만 이렇게 되어있어서 일반적으로 되게 해준다

![](https://i.imgur.com/sFL0NJJ.jpg)

그래서 프로토콜에 count를 추가한다.

하지만 모든 Collection에서 이것을 활용해서 구현해줄 필요가 없다.

필요한 것이 내장되어있는 경우도 있다.

하지만 이렇게 내장으로 컴파일러는 어떤 것인지 알 수 있다.

프로토콜을 통해서 컴파일러는 다이나믹하게 디스패치한다.

이러한 기술은 struct, class, enum 모두에서 사용할 수 있다.

하지만 모든 메서드에서 이렇게 최적화되는 것은 아니다.

특정 메서드에서는 불필요하므로 굳이 최적화를 해줄 필요가 없다.

예를 들어 map이 있다. 이것은 어떤 Collection에서도 최적화에 필요하지 않다

이럴 때에는 그냥 extension으로 남아있는다.

![](https://i.imgur.com/6QkSsvw.png)

### Protocol Inheritance

프토토콜 상속이 필요한 이유가 있다.

![](https://i.imgur.com/L8oYAmd.jpg)

마지막 인덱스를 찾으려면 뒤집어서 하면 효과적이다

랜덤으로 섞는 것은 모든 요소들을 swap해야한다.

이렇게 만든 것이 Collection이 나쁜 것이 아니다.

이거에 대해 추가적으로 제네릭 알고리즘을 알아야 한다.

프토로콜 상속에서 모든 Collection이 사용되고 있다.

하지만 모든 곳에서 양방향 컬렉션이 아니다

![](https://i.imgur.com/SEk58kU.jpg)

단방향에서는 사용할 수 없는 것을 볼 수 있다.

이것을 활용해서 실제로 뒤에서부터 할 수 있게 만들 수도 있다.

![](https://i.imgur.com/IsixM78.jpg)

다음 소개하는 알고리즘 

![](https://i.imgur.com/bLrapUK.jpg)

내부 코드를 보면 아래와 같다.

![](https://i.imgur.com/8XYoPcM.png)

ShuffleCollection → NO! 이렇게 하지마라!

하나를 위한 알고리즘도 좋지만 많은 프로토콜이 생겨서 좋지 못하다!

이제 이렇게 해주면 더 좋다.

![](https://i.imgur.com/kSIlohP.jpg)

이렇게 사용해서 클라이언트가 여러 개의 프로토콜들을 구성할 수 있게 만든다.

![](https://i.imgur.com/98jig4v.jpg)

Collection Protocol Hierarchy를 보면 아래로 갈수록 구체적이고 위로 갈수록 많은 타입들을 가져야한다.

![](https://i.imgur.com/S0G6Dz1.jpg)

### Conditional Conformance

Swift4.2에서 나온 새 기능

![](https://i.imgur.com/WsQn9B4.jpg)

여기에서 문제가 뒤에서부터 찾을 떄 생긴다.

이 타입이 존재하지않는다!

이제 여기에서 where를 통해서 이 Collection의 타입을 제한적으로 넣어줌으로써 해결할 수 있다

![](https://i.imgur.com/rXulQan.png)

RandomAccessCollection일 떄만 동작하는 것도 만들어서 제한적으로 해줄 수 있다.

![](https://i.imgur.com/bJWXUOA.jpg)

이게 Conditional conformance다.

또 다른 예시를 보자!

범위를 정해서 하는 것인데 이곳에서 Int로 하고있지만 이거 제네릭이다!
그래서 Double이 드러오면 Double에도 대응해준다.

![](https://i.imgur.com/ciULRQ5.jpg)

Swift 4.2 이전에 다른 타입인 경우에 이렇게 해야했다.

![](https://i.imgur.com/ewpFLGU.jpg)

이제 Conditional Conformance에서 더 낫게 해줄 수 있다.

![](https://i.imgur.com/rAlU0Ab.jpg)

이렇게 하면 될 것 같지만, 에러가 난다. 왜냐하면 컴파일러에서는 더 정확하게 제한된 것으로 알려주어야 한다.

![](https://i.imgur.com/VyuNUXP.png)

이것보다 더 좋은 것은 typealias를 활용해서 하는 것이다

![](https://i.imgur.com/r3SB42p.jpg)


이거 활용해서 남은 모든 요청들을 범위에 넣을 수 있게 만든다.

### Recursive Constraints

Divide and Conquer Binary Search 

에대한 설명나온다..
