# Protocol-Oriented Programming in Swift

Crusty : 나는 객체 지향을 하지 않아. <br>
Dave : 왜? 객체지향이 얼마나 멋있는데!

## class가 별로인 이유 

![클래스는 대단해](https://i.imgur.com/S8lQ42d.png)

OOP 는 휼륭하다
Class 에는 수많은 장점이 있고, 이런 것들을 사용해서 확장성이 좋아지고 프로그래밍이 유연해 졌다

![정확히는 타입은 대단해](https://i.imgur.com/yZTkkbE.png)

하지만 모든것은 Struct, Enum으로도 가능하다. 클래스가 아니라, 타입이 대단하다

![클래스의 상속은 대단해](https://i.imgur.com/o9cHY7p.png)

클래스의 강력한기능: 상속

![](https://i.imgur.com/gTJOLVs.png)

슈퍼 클래스에서 복잡한 로직을 가진 메소드를 정의하고 
서브 클래스는 슈퍼 클래스가 가진 메서드를 어떠한 비용도 없이 사용할 수 있다
여러운 로직일 경우 수퍼 클래스에 정의 해 두고 계속 서브 클래스에서 쓰면 엄청 좋다

그러나 class 에 대해 크게 3가지 불만이 있다

### 1. implicit sharing (암시적인 공유) 문제 

![](https://i.imgur.com/kYcYUoD.png)
![](https://i.imgur.com/caeCsaW.png)

A 랑 B 가 data 를 공유 하고 있다가, A 가 더 이상 Data 를 사용하지 않고 Pony 를 사용한다면, B는 갑자기 Pony를 사용하게 됨

![](https://i.imgur.com/DJ5rxsw.png)

암시적인 공유가 가져올 비극

- 코드에서 버그가 일어나지 않도록 모든 것을 복사함 
- dispatch queue 를 사용함 
- race condition 에 걸림
- 쓰레드 끼리의 보호를 하기위해 lock 를 추가함
- lock 은 속도를 느리게 하고 교착 상태를 만듬
- 그리고 결론은... 버그!

코코아 프로그래머에게 이것은 일상

![](https://i.imgur.com/3lIYAuI.png)

이런 경고 문이 붙어 있기도 함
그러나 swift 의 컬렉션은 모두 값타입 이기 때문에! 수정하는 것과 반복하는 것이 구분 된다 

### 2. 단하나의 상속만 가능한 문제

![](https://i.imgur.com/HXvmK43.png)

클래스는 오직 단 하나의 수퍼 클래스만 가질 수 있기때문에, 다중 추상 모델을 만들 수 없다

수퍼클래스는 저장 프로퍼티가 있음
* 그걸 꼭 받아들여야함
* 초기화 해야함

무엇을 오버라이드 할 것인지 알고 있어야 함
우리는 이미 이 단점들을 알고 있고, 그래서 delegate 를 쓴다

### 3. 타입간의 관계 문제

![](https://i.imgur.com/LsSyKG4.png)

클래스의 타입 관계의 문제는 정말 나쁘다

2개를 비교하는 연산을 클래스를 통해서 한다고 하자
그런데 구현하는 시점에서는 어떤 타입이 들어올지 알 수 없다
(Ordered의 하위 클래스가 들어온다는 것만 알 수 있음)

그래서 실제 타입을 알기 위해 강제 다운 캐스팅을 사용한다
!(강제 다운캐스팅) 를 쓴다는 건 대부분 나쁜 징조

![](https://i.imgur.com/wdSkhtq.png)

## 우리에게 필요한 메커니즘

* 값 타입을 지원할 것
* 정적 타입 간의 관계를 지원할 것 (다이나믹 디스패치에서)
* 낮은 의존성
* 추상 모델링 지원할 것
* 모델에 데이터 인스턴스를 강요하지 말것
* 모델에 초기화를 강요하지 말 것 
* 무엇을 실행할지 확실히 할 것

#### 프로토콜은 위의 모든 것을 해결해 줄 수 있다!

> 클래스로 시작하지 마세요, **프로토콜**로 시작하세요

## 프로토콜이 대단한 이유

### 예시1) Order

Order 를 이제 protocol 로 변경

![](https://i.imgur.com/MsWHSUV.png)

프로토콜은 메서드에 body 가 필요하지 않다.

![](https://i.imgur.com/sLNQ0DR.png)

상속을 하지 않으니, override 할 필요가 없다.
지금 여기코드에서 프로토콜은 위에서 클래스가 했던 것과 완전히 똑같은 역할을 하고 있다



![](https://i.imgur.com/s1garE4.png)

기존의 다운캐스팅 문제 같은 경우, Self 를 사용해서 해결할 수 있다
(Protocol에는 정의만, 실제 구현은 실제 타입에 하기 때문에 가능)

### 예시2) binarySearch

![](https://i.imgur.com/86b3fpw.png)

![](https://i.imgur.com/8LtclvX.png)

제네릭과 프로토콜을 활용해서 타입에 제약을 줄 수도 있다
protocol extension, where을 활용해서 중복을 피하고, 서로 다른 타입에 동일한 코드를 작성할 수 있다

![](https://i.imgur.com/5AlmZ4f.png)

---

### 예시3) Render
![](https://i.imgur.com/tthav2z.png)
Render 라는 구조를 만들어 보자
Drawble이라는 프로토콜을 생성하면 draw 라는 메서드에서 Render 을 받아 그림을 그린다. 
![](https://i.imgur.com/FINvxOj.png)
Polygon 이든 Circle 이든 채택하기만 하면 같은 메서드로 다른 모양의 그림을 그릴 수 있다.
![](https://i.imgur.com/VDVMdmE.png)
![](https://i.imgur.com/LP0kqcY.png)
다이어그램에서 element 들은 모두 Drawable 를 채택하고 있으므로 draw 메서드가 존재한다. 
![](https://i.imgur.com/weEp5v5.png)
구조체였던 Renderer 를 프로토콜로 변경하였다. 
![](https://i.imgur.com/Nnv3AAW.png)

![](https://i.imgur.com/g5wmHzj.png)
CGContext 에 Renderer 을 채택해 바로 그림을 그릴 수 있게 되었다. 

![](https://i.imgur.com/1xU6FsX.png)
Renderer 가 추상 클래스가 된 것은 좋았지만, 아직 까진 Renderer 를 채택하는 객체에서 매번 moveTo, lineTo 등의 메서드 들을 정의해줘야 하는 불편함이 있다. 

extension 을 사용해서 그 문제를 해결 할 수 있다.

extension 을 사용하면 기본 구현을 제공할 수 있다.
![](https://i.imgur.com/dAbrMYD.png)

![](https://i.imgur.com/owpDEE7.png)
같은 extension 이 있을 경우, class 의 overrring 처럼 채택된 곳에 있는 메서드를 호출한다. 

### 이외의 기능들...
![](https://i.imgur.com/P00WnoE.png)
where 절을 활용해 조건을 추가해 줄 수 도 있다. 


![](https://i.imgur.com/gXAmbj9.png)
지금 우리의 Drawable 은 공유하고 있는 저장 프로퍼티가 없기 때문에 타입 끼리 비교가 불가능하다. 
Drawable 자체에 Equtable 을 채택하기 보다는, Drawble extension 을 활용해서 조건을 걸어주는 것이 유연하게 사용할 수 있는 방법이다.

## 결론

![](https://i.imgur.com/VibwNxD.png)

프로토콜(POP)가 상속(OOP)보다 좋다
그러나 OOP의 상속도 여전히 쓸 곳이 많음

