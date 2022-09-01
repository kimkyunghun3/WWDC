# Understanding Swift Performance

## 📌 Choosing the Right Abstraction Mechanism

스위프트의 여러 타입들 중 어떤 타입을 골라 써야 할까?
`모델링`과 `성능`을 고려하여 타입을 선택할 것.

성능에서 이야기할 3가지 : `Allocation` `Reference Counting` `Method Dispatch`

![](https://i.imgur.com/JjjTvwm.png)

이 세 가지 관점에서 타입의 성능을 판단하게 된다.

- Allocation: 메모리가 stack 또는 heap 영역 중 어디에 할당 되는지
- Reference Counting: 참조 관리
- Method Dispatch: 어떤 메서드가 실행되어야 하는지를 알아내는 부분

우측으로 갈 수록 성능이 좋지 않음을 나타낸다.

## 📌 Allocation

### Stack vs Heap

Stack

- Stack은 컴파일 타임에 그 크기가 정해진다
- Stack은 메모리 할당을 위해 stack 포인터를 감소시키고, 메모리 해제를 위해 stack 포인터를 증가시키면 되는 단순한 구조이다

Heap

- Heap은 크기가 런타임에 결정된다
- Heap은 메모리에 할당하기 위해 사용하지 않은 빈 메모리 블록을 찾아야 하는 비용이 있다. 또한 해제를 위해 메모리 블록을 다시 삽입해야 하는 과정이 있다.
- 동적으로 할당/해제가 되기 때문에 thread-safe를 보장하기 위한 오버헤드가 존재한다

### Struct와 Class의 Allocation

#### Struct
![](https://i.imgur.com/fMLxZqv.png)

Point라는 구조체가 있고, point1을 point2에 할당하면 복사된 모습을 볼 수 있다.
같은 인스턴스가 아니고 값만 같은 복사된 인스턴스이기 때문에 

![](https://i.imgur.com/VLfO2dQ.png)

point2의 x의 값을 바꿨을 때 point1에는 영향이 없는 것을 볼 수 있다.

#### Class

똑같은 Point가 class로 구현되어 있다.
참조 타입의 데이터는 Heap 영역에 할당되지만, Stack에도 Heap을 가리키는 주소값을 저장한다.

point1을 point2에 할당주면 참조 타입이기 때문에 동일한 heap 메모리 주소를 가리키게 된다.

![](https://i.imgur.com/2VzYMqZ.png)

그래서 point2의 x값을 5로 바꾸면 point1의 x도 5로 바뀌게 된다.

struct와 class의 성능을 그림으로 비교해보면 아래와 같다

![](https://i.imgur.com/Zva0Djm.png)

![](https://i.imgur.com/uIBU3xH.png)

위 그림을 보면 struct가 class보다 성능이 뛰어난데, 어떻게 개선을 할 수 있을까?

![](https://i.imgur.com/NvZk9dp.png)

딕셔너리 타입인 cache의 키는 String 타입이다.
String은 struct로 구현되어 있지만, 크기가 동적으로 변하기 때문에 컴파일 타임에 그 크기를 알 수 없어 heap 영역을 사용한다.
이때 String을 사용하는 것이 아니라, 타입을 만들어서 해결해볼 수 있다.

![](https://i.imgur.com/OPX51Au.png)

enum으로 구현되어 있는 프로퍼티로 구성된 struct를 만들어 key로 활용해 String 사용을 개선할 수 있다.

## 📌 Reference Counting

### Class

![](https://i.imgur.com/8jk7EK4.png)

인스턴스를 할당하고, 사용을 완료하는 사이에 retain과 release 코드가 호출되는 것을 볼 수 있고, Point의 프로퍼티로 refCount가 프로퍼티로 존재 한다.
point1을 point2에 할당하면, retain이 실행되기 때문에 refCount는 1이 더해져 2가 된다.

![](https://i.imgur.com/5XZuyBw.png)

point1과 point2가 사용을 마치고 refCount가 0이 되면 ,
더 이상 필요하지 않은 것으로 인식하고 메모리에서 해제한다.
![](https://i.imgur.com/ooRiMwE.png)

stack과 heap 영역에서 모두 메모리 해제된다.

### Struct

struct는 기본적으로 참조를 가지지 않기 때문에 참조 카운트도 가지지 않지만, struct가 내부적으로 참조 타입을 가지는 경우에는 참조 카운트를 보유하게 된다.
![](https://i.imgur.com/svl3L2S.png)

위 Label은 struct 이지만, text는 String 타입이며 (heap에 할당), UIFont는 클래스이기 때문에 reference를 가지게 된다.
그래서 Label의 인스턴스를 생성하는 시점에 참조가 하나 생기게 되고, 

![](https://i.imgur.com/YJ8nMLM.png)

label1을 복사해서 label2 인스턴스를 만들 때에도 이전에 생성된 주소를 가리키는 참조가 생기게 된다.
label1을 복사하면 프로퍼티 두 개 모두 reference count가 증가하게 된다. 
그렇기 때문에 struct 내에 참조 타입이 존재할 경우, 참조 타입의 프로퍼티가 많을 수록 성능이 떨어진다.

#### 🧐 예시: 구조체 프로퍼티가 RC 발생시키면 어떻게 해결?
mimetype: ex. png, jpg
* 문제
![](https://i.imgur.com/FsII0Y5.png)

* 해결
![](https://i.imgur.com/D2YJzNA.png)

UUID는 128bit random ID인데, 구조체에 바로 저장할 수 있다.
이를 통해 String보다 type safety(직 UUID만, ID의 의미가 있는 것만 들어옴)가 보장된다.

indirectly store in heap! 해결: enum 타입
![](https://i.imgur.com/ghdbDcF.png)


![](https://i.imgur.com/TGxJHLI.png)

![](https://i.imgur.com/M2Azq8p.png)

## 📌 Method Dispatch

### Static Method Dispatch

어떤 메서드 구현부를 실행해야 하는지 알고 있기 때문에, 런타임에 바로 구현부로 이동할 수 있다.
인라이닝으로 컴파일러의 최적화가 가능하다.
![](https://i.imgur.com/Zjb8NZ7.png)

![](https://i.imgur.com/aSymqaB.png)

drawAPoint를 실행할 때 Point.draw()의 구현부로 대체할 수 있다.

### Dynamic Method Dispatch

런타임마다 v-table을 통해 메서드 구현부를 찾게 된다. 그 이후 구현부로 이동하여 메서드를 실행하게 된다. 그렇기 때문에 인라이닝이 어려워진다.
=> private, final 키워드를 이용하여 상속이 되지 않음을 나타내면 static dispatch로 실행될 수도 있다.

그럼 Dynamic Dispatch를 사용하는 이유는?? 다형성 (Polymorphism) 때문

V-Table은 수직 상속 관계를 포함하는 테이블로, 메서드 오버라이딩에 따라 실행 시점에 어떤 메서드를 실행할지 결정하는 dynamic dispatch를 지원하기 위해 사용되는 메커니즘이다.

상속을 하면 메서드를 재정의할 수 있게 되는데, 이때 각 메서드의 구현부가 달라지기 때문에 컴파일러는 어떤 메서드의 구현부로 이동해야 하는지 모르는 상황이 온다.
![](https://i.imgur.com/ifUK4gf.png)

![](https://i.imgur.com/xgOhWwI.png)

d.draw()에서 Line의 draw()를 실행해야 하는지, Point의 draw()를 실행해야 하는지 알 수 없을 때, 컴파일러는 어떤 draw를 호출해야 하는지 어떻게 결정할까?
컴파일러는 그 class 타입의 정보를 static memory에 저장하고, 실제로 draw를 호출할 때 vtable을 조회한다.
그리고 실행하기에 적합한 draw() 메서드를 찾고, 파라미터로 실제 인스턴스를 전달한다.

## 📌 Protocol Types

### 상속이나 reference semantics가 아닌 다형성 (Struct + Protocol)

![](https://i.imgur.com/IKrnHtE.png)

Point와 Line은 구조체이기 때문에 공통 상속 관계가 아니다.
따라서 vtable을 조회하지 않는데, 컴파일 시점에 어떤 draw()가 호출되어야 할지도 모르는 상황.
=> Protocol Witness Table

pwt의 엔트리는 해당 타입의 구현에 연결(link)된다.
![](https://i.imgur.com/rKwmZ1X.png)

그런데 [Drawable] 배열에서 어떻게 pwt 테이블로 이동할 수 있을까?

![](https://i.imgur.com/QShQbNj.png)

Point는 프로퍼티가 2개, Line은 프로퍼티가 4개 있어서 사이즈가 다른데,
배열은 고정된 offset에 요소를 저장하려고 한다.
이럴 때 Swift는 Existential Container라는 특수한 storage layout을 사용한다.

### Existential Container

![](https://i.imgur.com/r3A2agY.png)

Existential Container는 valueBuffer + vwt + pwt로 되어 있다.
Existential Container의 처음에는 3개가 valueBuffer용으로 예약 되어 있다.

그래서 Point와 같이 값이 2개만 필요하면
![](https://i.imgur.com/y1Od8aR.png)

valueBuffer에 들어갈 수 있다.

하지만 Line은 값이 4개가 필요하고, 이런 큰 타입은 Heap에 메모리를 할당하고, 해당 메모리에 대한 포인터를 Existential Container에 저장한다.

![](https://i.imgur.com/qBR0v2A.png)

이렇게 되면 Point와 Line이 다른 방식으로 저장되기 때문에 Existential Container는 이 차이를 관리할 필요가 있다.
=> VWT (Value Witness Table)

### Value Witness Table (VWT)

: value의 lifetime을 관리하며, 타입마다 Value Witness Table을 갖고 있다.
(Point도, Line도 VWT가 있다)

1️⃣ allocate
![](https://i.imgur.com/CZllRb5.png)

프로토콜 타입의 로컬 변수의 lifetime이 시작될 때, Swift는 VWT의 allocate 함수를 호출한다
(Line은 Existential Container에 들어맞지 않기 때문에 Heap 메모리 할당 필요)
allocate 함수 안에서 Heap에 메모리를 할당하고, 해당 메모리에 대한 포인터를 Existential Container의 valueBuffer 내에 저장한다.

2️⃣ copy
![](https://i.imgur.com/ftXOE9U.png)

로컬 변수를 초기화 하는 소스에서 Existential Container로 값을 복사 해야한다.
(Point와 같이 valueBuffer에 들어맞는 타입은 valueBuffer 내에 값을 저장하기 때문에, copy 부분에서 Existential Container로 값을 복사함)
Line은 valueBuffer에 들어맞지 않기 때문에 copy에서 Heap에 값이 복사된다.

3️⃣ destruct
![](https://i.imgur.com/cIYOHjS.png)

local 변수를 다 써서 lifetime이 끝나면 Swift는 VWT에서 destruct를 호출한다.
destruct는 값에 대한 레퍼런스 카운트를 감소시킨다.

4️⃣ deallocate
![](https://i.imgur.com/RkoFRno.png)

그리고 deallocate을 호출하여 Line이 Heap 메모리 할당을 해제한다.

vwt에서는 저장 프로퍼티를 관리하고, pwt에서는 프로토콜 메서드를 관리한다.

*** Swift는 existential container 필드에서 pwt를 조회하고, 해당 table의 fixed offset에 있는 메서드를 조회하여 구현으로 이동한다 ***

## 📌 Generic Code

Generic Code는 static한 형태의 polymorphism을 지원한다.
static한 형태의 polymorphism은 Generic의 specialization이라고 불리는 컴파일러 최적화를 가능하게 한다.
![](https://i.imgur.com/IULWU4V.png)

drawACopy에서 Point를 파라미터로 호출하면
Swift는 해당 타입을 사용하여 함수에서 Generic 매개변수를 대체하고, 해당 타입 버전의 함수를 만든다.
Line을 파라미터로 호출하면 Line 버전의 drawACopy가 생긴다.

![](https://i.imgur.com/240i9jq.png)

타입마다 해당 함수의 버전을 만들기 때문에 code size를 증가시킬 가능성이 있다.
하지만 aggressive compiler optimization이 가능하기 때문에 실제로는 Swift가 code size를 줄일 수 있다.
specialization을 통해 existential container가 필요 없어지고 (해당 타입 버전으로 함수를 만들기 때문에, 굳이 해당 메서드가 누구껀지 저장할 pwt가 필요 없어지기 때문),
static dispatch가 가능해진다.

최소한의 dynamic 런타임 요구사항으로 적합한 추상화 선택
- struct types: value semantics    
- class types: identity or OOP style polymorphism
- Generics: static polymorphism
- Protocol types: dynamic polymorphism
- 큰 값들을 처리할 때는 indirect storage 사용





