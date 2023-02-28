# Embracing Algorithms

[영상 바로가기](https://developer.apple.com/videos/play/wwdc2018/223/)

> 앱을 수행하는 데 필요한 기본 작업들을 구성하는 알고리즘에 관한 이야기.


![](https://i.imgur.com/R8y4cfB.png)

보통 면접이나 인터뷰에서 구조에 대해 설명해달라고 하면, 흔히 ViewController나 delegate와 같은 것들을 이야기한다. 컴퓨터가 계산을 어떻게 하는지에 관해서는 비교적 무관심한 편이다.

![](https://i.imgur.com/hLv837u.jpg)

알고리즘의 정의
- 계산 또는 기타 문제 해결 작업에서 따라야 하는 프로세스 혹은 그 집합


in 캔버스 앱

![](https://i.imgur.com/g0iqifP.png)

선택한 도형들을 삭제하는 '선택 삭제 기능'을 갖는 함수를 구현한다면?

![](https://i.imgur.com/2tbFbtn.png)

이 코드의 문제점
- 배열의 원소를 삭제하면서 배열의 길이가 변화한다.
- 선택된 도형이 연달아 나오는 경우 배열 탐색이 올바르게 되지 않는다.

![](https://i.imgur.com/32ZuZOv.png)

배열의 원소가 제거되면서 배열이 앞으로 당겨지고 재조정이 일어나기 때문에 위의 문제들이 발생하므로 배열을 뒤에서부터 확인하도록 코드를 수정했다.
그러나 이 방법도 완전하지 않다.

바로 성능적인 결함이 존재하기 때문이다.
그 원인은 바로 O(N)의 시간복잡도를 갖는 remove(at:) 메서드

![](https://i.imgur.com/0ptIJ92.png)

이렇게 생긴 배열에서 녹색 돌을 제거하는 작업을 한다고 해보자.
각각의 돌 하나씩 접근하여 돌이 녹색인지 확인하고, 녹색이라면 돌을 제거하는 작업을 수행한다. 최악의 상황은 N의 길이인 배열의 원소가 모두 녹색 돌이라면 총 N^2회의 작업이 필요하다.

![](https://i.imgur.com/3bLf3EY.png)

N이 15라면 225회의 연산 비용이 필요하다. 그러나 N이 100이라면 10000회의 연산 비용이 필요하게 된다. N^2의 시간복잡도를 갖는다면 N이 증가함에 따라 비용이 기하급수적으로 늘어나게 된다.

![](https://i.imgur.com/4Oyg6Pw.png)

deleteSelection 메서드의 코드를 이렇게 바꿔보았다.
removeAll 메서드가 O(N)의 시간복잡도를 가지므로, 성능 상의 개선이 확실히 이루어졌을 것이다.

![](https://i.imgur.com/7cfuByG.png)

O(N)과 O(N^2)을 비교한 그래프이다.
차원(dimension)이 다른 두 방법은 문제의 크기가 작은 경우, 그 차이점이 별로 느껴지지 않는다. 오히려 어떤 구간에서는 O(N^2)이 더 효율적인 것처럼 보이기도 한다.

그러나 특정 교점이 반드시 발생하고, 그 이후로부터는 O(N)이 항상 우위에 있다. 알고리즘은 항상 확장성을 갖고 있어야 하기 때문에 크기가 한계까지 큰 경우에도 효율적인 방법을 사용할 수 있도록 코드를 짜야 한다.

![](https://i.imgur.com/7nuvrlS.png)


removeAll 메서드의 내부 구현을 살펴보자.

뒤섞여있는 배열에서 조건을 만족하는 원소들을 뒤로 보내고(`halfStablePartition()`)
suffixStart 이하 항목들을 한번에 삭제한다.

![](https://i.imgur.com/xojLJEj.png)


다음은 halfStablePartition 메서드의 내부 구현이다.
swapAt 메서드를 통해 항목끼리 교환하고 resulting이 끝난 startIndex를 반환한다. 이 메서드의 시간복잡도는 O(N)이다.

![](https://i.imgur.com/hbVyV1k.png)

Before 코드의 경우 동작에 대한 상세한 주석이 필요했다.
왜 j 위치의 항목을 삭제하는지, 왜 배열을 역순으로 탐색하는지

그러나 After 코드는 성능은 물론이고 가독성 면에서도 더 좋다.
두 마리 토끼를 다 잡은 셈.

![](https://i.imgur.com/87hUPY0.png)


다시 캔버스 앱으로 돌아와서, 선택 항목 삭제를 해결했으니 다음 기능들도 다시 점검해야 한다.

- Bring to Front(맨 앞으로 가져오기)
- Send to Back(맨 뒤로 보내기)
- Bring Forward(위로 올리기)
- Send Backward(아래로 내리기)
- Dragging(끌어서 옮기기)

![](https://i.imgur.com/7wNMeBW.png)

기존에 구현했던 bringToFront 메서드.

![](https://i.imgur.com/sNUgrlm.png)
![](https://i.imgur.com/mFz4NUH.png)

O(N^2)으로 동작하기 때문에 수정이 필요해보인다.
우리는 이제 이 모든 것들이 비효율적이라는 것을 알고 있다.

![](https://i.imgur.com/wQN4aPx.png)

알고리즘을 사용하여 구현한 새로운 bringToFront 메서드와 sendToBack 메서드

![](https://i.imgur.com/umXiCRc.png)

이 알고리즘은 O(NlogN)의 시간복잡도를 갖는다.

![](https://i.imgur.com/lStZDF3.png)

마찬가지로 효율적이지 않은 bringForward 메서드이다.
개선 아이디어를 찾아보면 배열 전체가 필요한 것이 아니라 선택된 항목의 위치에서 바로 앞 인덱스부터의 배열만 필요한 것을 알 수 있다.

![](https://i.imgur.com/klX9Cn9.png)

그래서 ArraySlice로 배열을 임시적으로 추출한 뒤 stablePartition을 호출하게 변경하였다.

![](https://i.imgur.com/zbSIXQA.png)

우리는 지금까지 새로운 알고리즘의 구현을 끝냈다. 그러나 이 코드는 Canvas에 종속적이다. 

우리는 이 알고리즘을 확장할 수 있어야 한다. 이 알고리즘은 단지 도형을 효율적으로 옮기는 것이 아닌, 선택된 한 묶음의 Element들을 효율적으로 옮기는 방법이기 때문이다.

예를 들어 이 코드를 테스트한다면 캔버스를 생성하고 도형을 수백 개 추가하고, 수 개의 도형을 선택하여 해당 메서드를 테스트할 것인가? 아니면 숫자 배열에서 선택한 수를 앞으로 당기도록 기능을 테스트할 것인가?

![](https://i.imgur.com/Q5oT1QO.png)

MutableCollection에서 모든 타입에 사용할 수 있도록 알고리즘을 확장한 모습.

![](https://i.imgur.com/sZd8ecx.png)

우리의 알고리즘은 이미 문서화된 몇몇 알고리즘에 의해 만들어진 새로운 기능이다. 만약 이것을 공통으로 사용할 수 있도록 제공한다고 했을 때, 설명해줄 문서가 없기 때문에 사람들이 이 기능을 바로 이해할 수 없을 것이다.

![](https://i.imgur.com/zdSOrgA.png)

따라서 알고리즘을 구현했다면 문서화를 통해 다른 사용자가 내용을 쉽게 알 수 있도록 해주어야 한다.

![](https://i.imgur.com/tA6K9vB.png)

stablePartirion의 내부 구현을 위와 같다.
배열을 같은 크기의 둘로 나누고, 각각은 재귀 호출을 통해 정렬이 이루어진다.


![](https://i.imgur.com/GrT5BaD.png)

선택된 항목을 드래그해서 옮기는 기능이 구현된 기존 메서드이다.
코드가 매우 길고 복잡한데, 요약하면 선택된 항목들을 다른곳에 순서대로 모아놓았다가 원하는 위치에 한 번에 삽입하는 형태로 구현되어 있다.

![](https://i.imgur.com/bW6RS6T.png)

MutableCollection의 확장으로 구현한 gather 메서드를 사용하여 리팩토링한 모습.
이렇게 Generic하게 알고리즘을 구성할 수 있다면 필요한 곳에서 재사용할 수 있다.

## Recap

![](https://i.imgur.com/MfURHrj.png)

알고리즘을 구현할 때 Generic하게 만들어야 하는 이유는 테스트하기 쉽고 다른 곳에서 재사용이 가능해야 하기 때문이다.
코드 품질 개선을 하고 싶다면 No Raw Loops를 목표해야 한다.
