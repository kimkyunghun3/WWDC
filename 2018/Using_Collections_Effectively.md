# Using Collections Effectively

![](https://i.imgur.com/hn30mtj.png)

> 최적의 성능을 위한 컬렉션의 사용법


## Collections

**Without Arrays**
![](https://i.imgur.com/iBzjIRO.png)

**Without Dictionaries**
![](https://i.imgur.com/NYa7vES.png)


**With Collections**
![](https://i.imgur.com/5nxGEAx.png)


### Collection Protocol

![](https://i.imgur.com/6icnH49.png)

Collection은 startIndex와 endIndex, 그리고 subscript를 내부적으로 저장하고 있다.

Collection 프로토콜이 어떻게 구현되어 있는지 살펴보자. 

![](https://i.imgur.com/skvthGr.png)

- Collection의 정의는 `Element`의 연속된 집합
- Comparable을 채택한 `Index`
- 해당 인덱스에서 요소를 검색하거나 사용하기 위해 `subscript`를 제공
- Collection의 경계를 나타내기 위해 `startIndex`, `endIndex`
- 다음 인덱스로 이동할 수 있는 `func index(after i: Index) -> Index`

![](https://i.imgur.com/dSY0peV.png)

그리고 Collection의 여러 기능들이 이들을 응용하여 만든 것이다.

![](https://i.imgur.com/dwUHtXL.png)

예를 들어 그림과 같은 한 칸씩 건너뛰는 기능을 확장을 통해 구현한다고 해보자.

![](https://i.imgur.com/lELLEuH.png)


직접 배열 요소들을 모두 순회하면서 일부 값을 건너뛰는 `everyOther(_ body: (Element) -> Void)` 메서드를 주어진 프로퍼티와 메서드를 응용하여 구현할 수 있다.

![](https://i.imgur.com/rw4nzVi.png)

Collection 프로토콜은 단일 프로토콜이 아니다. 그림처럼 계층 구조로 이루어져 있으며 각 프로토콜은 Collection의 기능들을 향상시킨다.

![](https://i.imgur.com/G4MFNMW.png)

예를 들면, BidirectionalCollection 프로토콜은 Collection에서 이전 인덱스로 이동할 수 있는 `func index(before: Self.Index) -> Self.Index`를 제공한다.

![](https://i.imgur.com/yohA0No.png)

RandomAccessCollection은 인덱스에서 다른 인덱스를 계산하거나 두 인덱스 간의 거리를 계산하는 `func distance(from start: Index, to end: Index) -> Int`를 제공한다.

![](https://i.imgur.com/X7rCQKd.png)

흔히 Collection이라고 일컫는 Array, Set, Dictionary 뿐만 아니라 다른 많은 타입들도 Collection 프로토콜을 채택하여 Collection을 사용하고 있다. 
이는 프로토콜의 범용성 덕분

프로토콜을 준수하는 타입이 Collection의 기능을 어떻게 사용하는지 먼저 타입의 Index를 계산하는 방법부터 확인해보자.

### # Collection Index

Indices - 타입이 인덱싱되는 방법

![](https://i.imgur.com/pc6tg4I.png)

- 각 Collection에는 고유한 Index가 존재한다.
- 그리고 Index는 Comparable해야 한다.

Collection에서 첫 번째 요소를 구하는 방법

![](https://i.imgur.com/dz35YZA.png)

- Array는 `array[0]`처럼 정수 형태의 Index로 접근할 수 있지만 Set은 순서가 없기 때문에 사용할 수 없다.
- `startIndex`를 사용하여 `array[array.startIndex]`, `set[set.startIndex]`를 사용할 수 있지만 Collection이 비어있는 경우도 고려해야 한다.
- 따라서 Optional하게 값을 반환하는 `first` 프로퍼티를 사용하는 것이 안전하다.


Collectioin에서 두 번째 요소를 구하는 방법

위와 같이 두 번째 요소를 반환하는 프로퍼티를 확장을 통해 구현한다고 가정해보자.
- Collection이 Array, Set, Dictionary 심지어 아직 존재하지 않는 Collection 타입일 가능성이 있음.
- 모든 Collection에 두 개 이상의 요소가 있는 것이 아니므로 `[1]` 혹은 `[startIndex + 1]`과 같이 사용할 수 없음


![](https://i.imgur.com/08yTjN5.png)

따라서 다음의 단계를 거쳐야 한다.
1. Collection이 비어있는가?
2. SecondIndex에 해당하는 Index를 구함
3. SecondIndex가 유효한가?
4. 해당 Index위치의 Element 반환

모든 종류의 Collection에 대응하기 위해 이런 복잡한 과정을 거쳐야만 했고 이를 개선하기 위해 **Slice** 개념이 도입되었다.

### # Slice

![](https://i.imgur.com/HLApoOY.png)

Slice는 Collection의 일부만을 묘사하는 타입이다.
Slice의 특징
- 항상 시작과 끝이 존재한다.
- 원본 Collection과 별도로 존재한다.
- 추가적으로 저장소를 사용하지 않고 원본을 참조한다.

그렇다면 Slice를 사용하여 secondIndex를 확인해보자.

![](https://i.imgur.com/15WuVU6.png)

1. 원본 Array로 시작하여 첫 번째 요소를 제거한 더 짧은 Slice 생성
2. 원본 Array의 두 번째 Index를 가리키는 secondIndex 생성
3. secondIndex와 Slice의 firstIndex가 같은지 확인

![](https://i.imgur.com/VmfUsFg.png)

따라서 Slice를 통해 Collection의 두 번째 요소를 구하는 코드를 이렇게 바꿀 수 있다.

![](https://i.imgur.com/79ya4Rq.png)

Array 뿐만 아니라 Collection을 채택한 다른 여러 타입들에서 Slice를 사용한다. 다만 Set의 경우 순서가 없기 때문에 표준 라이브러리의 Slice를 사용하고 나머지 타입의 경우 자체 Slice 타입으로 다양한 옵션을 갖고 있다.


### Lazy Functions

![](https://i.imgur.com/KgxKSdE.png)

Slice가 존재하는 한 Array는 참조하고 있는 객체가 있으므로 버퍼에서 해제되지 않는다. 이것을 제거하고 싶다면, Slice로부터 Array의 복사본을 만든 다음 Slice를 초기화해주어야 한다. 이 작업은 lazy copy와 유사하게 동작한다.



이렇게 해서 얻을 수 있는 이점은? - 성능
Swift의 함수 호출은 기본적으로 eager. 즉 input이 들어오면 응당 output이 존재한다는 것

![](https://i.imgur.com/ZMwWs1o.png)

예시를 보면, 4000개의 요소를 모두 계산을 한 후 4000개에 모두 필터링을 해야 한다. 그러나 이것은 불필요할지도 모르는 중간 계산이다. 실제로 우리가 필요한 자리는 딱 4개.

![](https://i.imgur.com/JxTtv23.png)

이렇게 원본 Collection을 `lazy`한 Collection으로 래핑하고 작업을 수행하면 실제로 매핑하지 않고 나중에 사용할 필요가 있을 때 저장한다.

![](https://i.imgur.com/NlkCkXo.png)

결국 items의 first를 호출하면 함수들이 대상 Collection에 대해 알지 못하는 상태로 동작한다.

![](https://i.imgur.com/mAOwQ6T.png)

이에 대해 조금 더 자세한 예시를 살펴보자.
"Bear"를 포함하는 Array를 얻기 위한 코드이다. 

lazy 하게 동작하기 때문에 다음 두 번의 호출은 필터링 로직을 각각 타게 될 것이다. 

![](https://i.imgur.com/whUEmkf.png)

이 문제를 해결하는 방법은 간단하다. 연산 결과를 Collection으로 복사한 뒤 사용하는 방법이다.

![](https://i.imgur.com/NapRb6P.png)

따라서 언제 Lazy Collection을 사용해야 할까?
- Chain Compute 상황에서 오버헤드를 줄일 수 있다.
- Collection 계산 결과의 일부만 필요한 경우
- Side Effect가 없는 경우
- API의 경계를 넘는 경우 일반 Collection으로 구체화 필요
 
### Mutable Collection & RangeReplaceableCollection

![](https://i.imgur.com/XXvrPW1.png)

다시 Collection 프로토콜로 돌아와서, MutableCollection은 Collection의 내용을 변경할 수 있도록 setter가 제공된다.

![](https://i.imgur.com/DxDkoFM.png)

RangeReplaceableCollection은 요소를 제거하거나 삽입할 때 사용하는 기능을 제공하는 프로토콜이다.


## Crash About Collection

![](https://i.imgur.com/gFc9qpc.png)

근본적인 원인은 대부분 이 두 가지
- Collection이 변경되거나
- 다수의 Thread가 Collection에 접근하거나

### Avoid Index Invalidation

![](https://i.imgur.com/Don3907.png)

![](https://i.imgur.com/uAjcZHq.png)

해당 Index가 더 이상 유효하지 않다. Collection을 변경하는 순간 index가 무효화되었기 때문이다.

![](https://i.imgur.com/ZWCEV4u.png)

그렇기 때문에
- Collection 변경 후 Index를 계산해야 한다.
- Slice는 변경된 후에도 기존 상태를 유지하므로 주의해야 한다.
- Index 계산에는 적지 않은 시간이 소요될 수 있으므로 필요한 경우에만 계산하는 것이 좋다.

### Multithreaded Mutable Collection

![](https://i.imgur.com/0A6EaTF.png)

Multi-Thread 환경에서 Collectiion에 접근했을 때 발생할 수 있는 결과는 다양하다. 동일한 코드로 동일한 결과를 얻지 못한다는 것은 즉 프로그램의 실행 결과를 예측할 수 없다는 뜻.
Xcode의 Thread Sanitizer를 사용해서 진단했을 때 Access race가 발생했음을 알 수 있다.

![](https://i.imgur.com/YHbGEl6.png)

DispatchQueue를 Serial한 Queue로 바꾸어 해결한 모습

![](https://i.imgur.com/Hdk21Bg.png)

- Collection을 Single Thread에서만 볼 수 있도록 격리하는 것이 좋다.
- 그렇지 못한 경우 상호 배제 기능을 제공할 적절한 방법을 사용해야 한다.(`Serial Dispatch Queue` 또는 `Locks`)

### Prefer Immutable Collections

![](https://i.imgur.com/u2lf8jA.png)

변경 불가능한 Collection을 사용한다면 이런 위험의 가능성을 모두 피할 수 있다.

### Forming New collections

![](https://i.imgur.com/W2S8RrH.png)

혹은 새로운 Collection을 구성할 때 필요한 만큼의 크기를 정확하게 구할 수 있다면 성능상의 이점을 활용할 수 있다.


## Foundation Collections

![](https://i.imgur.com/LsIPW8Q.png)

Foundation에서 제공하는 이런 **NS** 접두사가 붙은 Collection들은 모두 참조 타입이다.

![](https://i.imgur.com/fCURPKF.png)


값 타입과 참조 타입의 Collection에 변경이 발생했을 때의 동작은 차이가 있다. 
값 타입을 보면 y는 x를 참조하고 있다가 y에 변경이 일어나면 동일한 배열을 복사한 후 append를 수행한다.
반면에 참조 타입은 x와 y가 동일한 위치를 가리키는 포인터가 되어 결국 y에 대한 append는 x에도 append를 한 것과 같다.

![](https://i.imgur.com/11i5kIs.png)

Swift의 모든 Objective-C API는 값 타입으로 동작한다.
NS 접두사가 붙은 타입은 참조 타입이므로 두 Collection에 대한 구현이 다르게 되어있을텐데, 어떻게 둘을 연결한 것일까?

이에 대한 해답은 **Bridging**이라는 기술이다.

### Bridging

![](https://i.imgur.com/s0Jt7UI.png)

Bridging은 서로 다른 런타임 표현 사이를 변환하는 방법이다. 즉 Swift와 Objective-C 서로가 호환되도록 변환하는 것을 의미한다. 이 과정을 거칠 때마다 비용이 발생한다.

![](https://i.imgur.com/MvPfEal.png)

기본적으로 내부 요소 타입이 Bridge될 경우에는 Eager하게, 그렇지 않으면 Lazy하게 동작한다.

![](https://i.imgur.com/lYea0ZF.png)

예시 코드를 보자. 분석 결과 두 번의 Bridge가 발생한 것을 확인했다.

![](https://i.imgur.com/8Rh824W.png)

코드 수정을 통해 Bridge를 하나로 줄였다. 이 경우는 작은 Bridge이므로 비용이 저렴하지만 루프를 통해 비용이 합산될 위험이 있다. 따라서 작은 문자열을 반복적으로 연결하지 않도록 주의해야 한다.


