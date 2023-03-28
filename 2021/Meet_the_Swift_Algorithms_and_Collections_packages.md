# Meet the Swift Algorithms and Collections packages

[영상 보러가기](https://developer.apple.com/videos/play/wwdc2021/10256)

![](https://i.imgur.com/L1Mt6Dp.png)

> Swift 표준 라이브러리의 기능을 뒷받침하는 Swift Algorithms과 Swift Collections에 대해 알아보자!


## Swift Algorithms

Swift Algorithms은 표준 라이브러리를 보강하는 Sequence 및 Collection에 대한 알고리즘을 포함하고 있는 오픈 소스 패키지이다.

![](https://i.imgur.com/JXyeD7S.png)

선택한 메시지를 따로 처리하기 위해 selectedMessages에 담아야 할 때
만약 생각한 대로 구현에 있어 접근한다면 for 반복문을 사용해서 처리할 것이다.

![](https://i.imgur.com/kxgi6nh.png)

map을 사용하면 입력을 변환하여 새로운 컬렉션을 만들 것이라고 명확하게 알 수 있다.
또한 배열 크기 조정으로 인한 중간 할당 과정이 없어지기 때문에 더 효율적이다.

![](https://i.imgur.com/1zHVvKS.png)

이번엔 Quick Look을 사용하여 모든 첨부 파일들을 수집하려고 한다. 필요한 동작은 nil을 걸러내고 옵셔널 언래핑을 진행하는 것이다.
조건문과 반복문 대신 map과 filter의 사용을 통해 수행할 수 있다.

![](https://i.imgur.com/gB3rCy5.png)

이 과정을 compactMap으로 압축할 수 있다.

![](https://i.imgur.com/L6TpJNg.png)

다음 상황은 `[Message]`타입 배열을 `[TranscriptItem]` 배열로 변환하고 싶은 상황이다.
joined() - 내부 배열을 하나의 flat한 collection으로 만들어줌
이와 동일한 역할을 하는 알고리즘은 바로 **flatMap**

![](https://i.imgur.com/VVwf7Rp.png)

가장 최근에 찍은 사진부터 6개까지 채팅에 표시하고 싶은 상황
원시 루프을 통해 구현한 코드는 다음과 같다.
이 기능을 위한 동작은 다음과 같다.
1. 항목들을 뒤집음
2. 사진인지 확인하고 photos에 추가함
3. photos의 크기가 6이 되면 멈춤

![](https://i.imgur.com/SFOC7GU.png)

알고리즘을 사용하면 이 기능들을 chaining하여 복잡한 코드를 간결하게 표현할 수 있게 된다.
그렇기 때문에 그 로직에 문제가 있는 경우 파악하기 쉽고 유연하게 수정할 수 있다. 성능 상의 문제는 없을까?
사실 체인의 각 단계에서 결과를 리턴하기 때문에 원시 루프 구현보다 성능이 떨어진다. 그러나 이를 극복하기 위한 장치가 존재한다.

![](https://i.imgur.com/INUPcg2.png)

앞서 봤던 joined 알고리즘을 다시 보자.
실제로 여기에서 새로운 collection을 할당하고 반환하지 않는다. 대신 배열을 flat하게 만든 새로운 [FlattenSequence]를 반환하고 있다.
반환 타입인 FlattenSequence는 lazy한 어댑터이다.


![](https://i.imgur.com/ZMwWs1o.png)

예전에 Using Collections Effectively 세션에서 다룬 내용을 다시 보면, Swift의 collection들은 기본적으로 eager하게 동작하고 있다.

![](https://i.imgur.com/JxTtv23.png)

그러나 lazy하게 사용한다면 필요할 때 크기를 계산하기 때문에 효율적이다.

![](https://i.imgur.com/NqokRV0.png)

다시 이 예시를 보면, suffix는 실제로 Array를 반환하는게 아니라 ArraySlice를 반환한다. 
그리고 reversed 또한 lazy 어댑터 중 하나이다.

![](https://i.imgur.com/QZE1lkH.png)

compactMap과 같이 클로저를 사용하는 알고리즘의 경우는 앞에 lazy를 붙여주면 된다.
lazy한 알고리즘은 매우 큰 컬렉션에서 소수의 요소를 추출하여 처리하는 경우에 적합하다.

![](https://i.imgur.com/MItsner.png)

lazy 어댑터 타입을 다시 온전한 배열로 변경하고 싶다면 Array 생성자를 통해 만들 수 있다.

![](https://i.imgur.com/EerMqmN.png)

디자이너가 연속된 메시지 사이에 1시간 이상 경과한 경우 타임스탬프를 표시해달라는 새로운 기능을 요청했다.
이 기능을 구현하면서 이번에 새로 추가된 **Swift Algorithms**에 대해 알아보자.

![](https://i.imgur.com/zeIu4TY.png)

지금까지의 알고리즘들은 표준 라이브러리에서 제공하는 것들인데, 아직 여기에 없는 것들이 Swift Algorithms에 들어있다.
이 패키지의 목적은 표준 알고리즘에 누락된 새로운 기능들을 추가한 것(미래에는 어쨌거나 표준 라이브러리에 합쳐질듯??)
아무튼 여기에 40개 이상의 알고리즘이 추가되었는데, 이를 사용하기 위해 해당 패키지를 import 해주어야 한다.

![](https://i.imgur.com/5Mn49a1.png)

추가된 알고리즘들 중 몇 가지를 소개한다.


1) windows

![](https://i.imgur.com/745T1qg.png)

배열을 잘라서 반환하는 알고리즘
반환 결과의 타입은 ArraySlice(lazy adapter)이다.

2) windows(ofCount: 2) = adjacentPairs

![](https://i.imgur.com/ad58ydt.png)

배열의 인접 쌍을 확인하는 코드는 많이 쓰기 때문에 따로 구현되어 있다.


3) chunk

![](https://i.imgur.com/gvZzGZK.png)

windows와 달리 겹치지 않고 배열을 나눈 결과를 반환하고 크기가 나누어 떨어지지 않는 경우에는 나머지를 반환한다.
반환 타입 또한 lazy 어댑터인  SubSequence이기 때문에 생성 비용이 저렴하다.

![](https://i.imgur.com/LfR6Hiu.png)

`chunked(on:_)` 을 사용하면 주어진 기준에 맞게 동일한 값을 반환하는 요소들을 연속된 청크로 묶어서 반환할 수 있다. 예제 코드를 보면 소수를 기준으로 청크를 나누고 있는데 2와 3 그리고 8과 9는 연속적으로 isPrime(소수 여부)에서 같은 값을 반환하기 때문에 하나의 청크로 반환된다.

![](https://i.imgur.com/14tGO7Q.png)

이전 요소와 현재 요소와 다른 경우에만 특수 동작을 하기 위해 이런 코드를 작성한 적이 있다면 청크를 사용하면 된다.


다시 돌아가서, 디자이너의 요구사항을 우리가 알아본 몇 가지 알고리즘을 통해 구현해보자.
- 메시지들 사이에 시간 차이가 1시간 미만인 경우 하나로 그룹화한다.
- 그룹들 사이에 타임스탬프를 생성하고 flat한 하나의 컬렉션으로 통합한다.

![](https://i.imgur.com/RRkYVjX.png)

chunk
- 클로저 뒤에 청크 생성 조건을 넣을 수 있다.
- 인접한 요소들이 클로저에 동일한 값을 반환할 경우 동일한 그룹에 속하게 된다.

![](https://i.imgur.com/q8Vh1lI.png)

joined
- separator로 쉼표나 줄바꿈을 통해 보통 배열을 문자열을 결합하는 것이 일반적
- 여기서는 분리된 청크들을 하나의 flat한 collection으로 만들어주는 기능을 한다.

![](https://i.imgur.com/meLt9r9.png)

기능을 모두 구현한 코드.
이러한 과정에서 중간 할당에 비용을 전혀 지불하지 않아도 된다는 점이 매우 효과적인 장점이다.

하지만 lazy가 만병통치약은 아니다.
시퀀스를 한 번만 반복한다면 onDemand한 lazy작업이 중간 할당을 피할 수 있어 효율적이겠지만, 시퀀스를 여러 번 반복해야 한다면 앞에서 한 동일한 작업을 그때마다 여러 번 반복하게 된다.
이럴 때는 lazy로 실행하고 결과를 Array로 한 번에 묶어 어딘가에 할당해주는 것이 좋다.


## Swift Collections

![](https://i.imgur.com/RPIMHQY.png)

표준 라이브러리는 Collections에 다음 세 가지를 제공하고 있다.
- Array
- Set
- Dictionary

![](https://i.imgur.com/HjEh1Hx.png)

Swift Collections 패키지는 이에 더해 새로운 데이터 구조를 제공한다.
대표적인 자료구조는 Deque, OrderedSet, OrderedDictionary
각각은 Array, Set, Dictionary와 관련이 있다.


### Deque

![](https://i.imgur.com/2kwRnay.png)

Double-ended Queue의 약자로 기본적으로는 Queue이지만 양방향으로 push와 pop을 지원한다. Swift에서 Deque는 Array과 거의 동일하며 Array에서 사용하는 여러 API들을 마찬가지로 사용할 수 있다.

![](https://i.imgur.com/91vzdjG.png)

그러나 요소를 삽입할 때를 보면 Array와의 차이점을 알 수 있다. Array의 경우 기존 요소를 이동하여 새 항목을 위한 공간을 만들어야 하므로 Array의 크기가 크면 상대적으로 비용이 많이 소요된다. 
그러나 Deque는 다르게 동작한다. 새로 삽입하는 데이터를 저장 버퍼에서 0번째 인덱스로 설정할 뿐이다.

![](https://i.imgur.com/FbOUD0z.png)

자료를 삽입할 때 소요되는 시간을 비교하면, Array와는 다르게 Deque는 일정한 시간이 소요됨을 볼 수 있다. 

![](https://i.imgur.com/UZmWDd6.png)
![](https://i.imgur.com/0U0w0Q5.png)

Deque의 자료를 삭제할 때도 마찬가지이다. Array와 같이 항상 뒤의 아이템을 당기는 것이 아니라 선택적으로 이전 요소를 이동시키는 방법을 사용할 수 있으며 Array보다 평균적으로 2배 빠르고 효율적으로 사용할 수 있다.

### Ordered Set

![](https://i.imgur.com/mCX8Sql.png)

일반적으로 Set의 순서는 무작위이다. 이는 곧 동일한 요소를 가진 Set 인스턴스가 있어도 내부 순서는 무작위로 주어짐을 의미한다. 둘의 순서는 다르지만 동일한 요소를 포함한다면 두 인스턴스는 동일하게 취급된다. 이처럼 Set은 **고유성**을 보장할 때 사용한다. 그러나 때로는 요소의 순서를 제어할 필요가 있다.

![](https://i.imgur.com/XNPTcb0.png)

Ordered Set은 요소를 고유하게 갖는 배열처럼 작동한다. 당연하게도 일반적인 Set과는 다르게 동일한 요소를 가지면서 동일한 순서를 가져야 동일한 인스턴스로 간주한다.
`unordered`: OrderedSet에서 요소들의 포함 여부만 비교하고 싶을 때 사용

![](https://i.imgur.com/5XRSj1p.png)

Ordered Set은 이렇게 보면 Array에 더 가깝다. index로 요소에 접근할 수 있고, 삭제, 추가, 삽입 등의 메서드를 사용할 수 있다. 

![](https://i.imgur.com/Bb5EnIn.png)

또한 Ordered Set에서의 Set 기능을 사용할 수 있다.

![](https://i.imgur.com/GXTJOuc.png)

일반적인 Set의 경우 해시 테이블에 요소를 직접 저장한다. 이 경우에는 요소에 대해 뛰어난 조회 성능을 얻을 수 있지만 순서를 보장할 수 없다.

![](https://i.imgur.com/vlZ6SGG.png)

반면에 OrderedSet은 요소 값을 배열에서 관리하고 해시 테이블은 값에 대한 순서를 나타내는 정수 인덱스만 저장하고 있다.

![](https://i.imgur.com/moRMnxQ.png)

조회 성능은 일반적인 Set과 거의 비슷하다. 

![](https://i.imgur.com/hpejj0s.png)

새 항목을 추가하는 것도 성능적으로 비슷하다. 해시 테이블에 새로운 항목을 추가해야 하며 요소가 이미 존재하는지도 확인해야 하므로 Array에 값을 추가하는 것보다는 비용이 크다. 

![](https://i.imgur.com/jor7Qki.png)

그러나 컬렉션의 중간에 값을 삽입하거나 삭제하는 것은 효율적이지 못하다.
늘 그렇듯이, 만능인 방법은 존재하지 않는다. 그러므로 요구사항과 최적화에 맞게 적합한 자료구조를 선택하는 것이 정말 중요하다.
(❗️주의 - OrderedSet은 Foundation의 NSOrderedSet에 대응되는 Swift의 자료구조이지만, Foundation이 아닌 Collection에서 제공된다.)


### Ordered Dictionary

![](https://i.imgur.com/LvQb9BV.jpg)

표준 Dictionary와 마찬가지로 키-값 쌍으로 이루어져 있다. 차이점은 순서가 정의되어 있다는 점이다. 기본적으로 키가 삽입된 순서를 따르고 기존 키에 nil을 할당 시 요소가 제거된다.

![](https://i.imgur.com/TTbi4VD.png)

Dictionary가 순서를 갖는다는 것은 의도치 않게 모호성을 유발하는데, 다음과 같은 경우는 offset 0의 키-값을 요구하는 것인지 아니면 키 값이 0인 요소의 값에 접근한다는 것인지 이해하기 힘들다.
Swift에서는 이러한 모호성을 방지하기 위해 이 표현은 Dictionary와 마찬가지로 키에 대한 값을 의미한다. Ordered Dictionary에서는 인덱싱을 제공하지 않는다.
이 성질은 Ordered Dictionary가 Collection 프로토콜을 준수할 수 없음을 의미한다. 따라서 Sequence 프로토콜만 준수한다.

![](https://i.imgur.com/2MiTvVm.png)

표준 Dictionary의 기본 구현을 살펴보면 키와 값을 각각 저장하기 위해 두 개의 해시 테이블을 사용한다.

![](https://i.imgur.com/uRWkhII.png)

Ordered Dictionary는 단일 해시 테이블과 두 개의 병렬 배열을 사용한다.


## Recap

Swift Algorithms에는 소개한 기능 외에도 많은 여러 유용한 기능들이 있다.
- 원시 루프로 코드를 작성하기 전에 다른 알고리즘으로 대체할 방법이 없는지 확인해보는 것이 좋다.
- 알고리즘을 사용하면 코드의 동작 절차와 목적을 쉽게 알 수 있고 그들의 순서를 유연하게 바꿀 수 있다.
- Swift Algoritms Github 등에서 문서를 활용하거나 Forum에 질문을 올려도 좋다.

Swift Collections에서 제공하는 새로운 자료구조를 통해 구현하고자 하는 특별한 기능에 최적화된 performance를 수행할 수 있는 적절한 자료구조를 선택해야 한다.
- 좋은 자료구조를 사용하는 것만으로도 비약적인 성능 향상을 이끌어낼 수 있다.

Swift Collections와 Swift Algorithms는 앞으로 점점 늘어날 오픈 소스 패키지의 목록들 중 일부에 불과하다. 
