# Eliminate data races using Swift Concurrency

## Swift 동시성이란?

### Swift 언어 자체의 특성
- async/await
- structed concurrency
- actor

### data-race-free 동시성을 위한 모델 (오늘 다룰 내용)

![](https://i.imgur.com/9kWwtSG.jpg)

#### 이제부턴 비유와 같이 설명합니다
- 바다 = 동시성 (Concurrency)
- 보트 = 작업 (Task)

# 목차
**Task isolation**
**Actor isolation**
**Atomicity**
**Ordering**

## Task isolation

### Tasks

Task는 특정한 작업을 시작부터 끝까지 수행한다

![](https://i.imgur.com/O3tId5H.png)

**순차적**

**비동기**
- await 키워드를 사용한다면 몇번이고 중단될 수 있음

**독립적**
- 개별 리소스가 있고 바다에 있는 다른 보트들과 독립적으로 동작한다
- 보트가 완전히 독립되어 있다면 Data race는 발생하지 않지만, 통신을 못한다면 유용하지 못함

### Communication between boats

![](https://i.imgur.com/DSE06ty.png)

**값 타입의 파인애플 구조체를 정의해보자**

```swift
enum Ripeness {
    case hard
    case perfect
    case mushy(daysPast: Int)
}

struct Pineapple {
    var weight: Double
    var ripeness: Ripeness
    
    mutating func ripen() async {  }
    mutating func splice() -> Int {  }
}
```

![](https://i.imgur.com/uUMFv3S.png)

실제로 파인애플 구조체는 한 보트에서 다른 보트로 복사되고, 보트는 각자 복사본을 챙긴다.
이 경우, 즉 **값타입**에서는 아무 문제가 없음

**이번엔 참조타입의 치킨 클래스를 정의해보자**

```swift
final class Chicken {
    let name: String
    let currentHunger: HungerLevel
    
    func feed() {}
    func play() {}
    func produce() -> Egg {}
}
```

![](https://i.imgur.com/6SnAj3n.png)

이때 닭 객체는 두 보트에 의해 공유된다 (같은 객체를 서로 다른 보트가 참조하고 있음)

![](https://i.imgur.com/45Z5Pag.png)

서로 독립적인 작업을 수행하면 문제가 될 수 있음

### 해결방법

컴파일러에게 안전하게 공유할 수 있는 유형이 뭔지 알려줘야함 => 그것이 **Sendable**

![](https://i.imgur.com/PqskmMl.png)

Task를 보면, Success 부분이 Sendable을 채택하고 있는것을 확인할 수 있다

![](https://i.imgur.com/wmPcjUM.png)

### 데이터를 Sendable 하게 만드는 법

값 타입의 경우
- 기본적으로 Sendable

참조 타입의 경우
- final 키워드
- 불변 스토리지만 있어야함

```swift
final class Chicken: Sendable {
    let name: String
    var currentHunger: HungerLevel (x)
}
```

@unchecked 키워드 사용
- 컴파일러가 체크하지 않기때문에 주의가 필요!

```swift
class ConcurrentCache<Key: Hashable & Sendable, Value: Sendable>: @unchecked Sendable {
    var lock: NSLock
    var storage: [Key: value]
}
```

불변 타입은 공유 가능하다는걸 알겠는데..
가변 타입을 공유할 수 없다면 사실 의미가 없음.
그래서 문제를 해결하기 위한 Actor의 도입

## Actor isolation

- actor = 섬

actors는 가변 상태, 그리고 연관된 코드들을 **격리** 시킨다

![](https://i.imgur.com/LO1gt5P.png)

그래서 한번에 오직 하나의 배(Task)만 섬(Actor)에 방문할 수 있음

![](https://i.imgur.com/T9jzMVC.png)

어떤 코드가 actor - isolated인지 알아보자

- instance method, property: (O)
- reduce로 전달된 클로저, 즉 Sendable 하지 않은 Closure도 actor-isolated context에 있다면: (O)
- 새로운 Task: (O, actor isolation을 상속함)
- detached된 Task: (X, await 필요, actor외부에 있는것으로 간주)

![](https://i.imgur.com/s0D0w9V.png)

nonisolated 키워드를 사용해서 명시적으로 매서드를 비격리 상태로 만들수도 있음

![](https://i.imgur.com/SlGSdrU.png)

**Non-isolated synchronous code**

greet라는 매서드가 있습니다.
```swift
func greet(_ friend: Chicken) {}
```
이 매서드는 island, actor 이런것에 대해 아무것도 모름

actor에서 호출된다면? 격리됨 (섬에서만 실행됨)
actor가 아닌곳에서 호출된다면? 격리되지 않음 (다른 배들에서 실행될 수 있다)

### Actor isolation 요약
- 각각의 actor는 프로그램에서 완전히 격리됨
- 오직 한번에 하나의 task만 actor를 실행시킬 수 있다
- actor에 진입하거나 나올때 Sendable을 체크함

### @MainActor

MainThread에서 실행되는 Actor

```swift
// 완전히 동일함

await MainActor.run {
    // ...
}

DispatchQueue.main.async {
    // ...
}
```

**코드를 MainActor에서 실행시키는 방법**

```swift
class ViewModel {
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    func doSomething() {
        print(name)
        print(Thread.isMainThread)
    }
}

let vm = ViewModel(name: "dudu")
```

name 프로퍼티를 무조건 MainThread에서 업데이트 시키고 싶다만?


- 매서드에 @MainActor 붙이기

```swift
@MainActor
func doSomething() {
	print(name)
	print(Thread.isMainThread)
}
```

- print(name) 부분을 Task로 격리시키기

```swift!
Task { @MainActor in
	print(name)
}
```

- 클래스에 @MainActor 붙이기

```swift
@MainActor
class ViewModel {
```

- 프로퍼티에 @MainActor 붙이기

```swift!
class ViewModel {
    @MainActor var name: String
    
    init(name: String) {
        self.name = name
    }
    
    func doSomething() async {
        await print(name)
        print(Thread.isMainThread)
    }
}
```

## Atomicity (원자성)

- 원자성: 트랜잭션과 관련된 일은 모두 실행되던지 모두 실행되지 않도록 하던지를 보장하는 특성

![](https://i.imgur.com/IV9CCNe.jpg)

- Actor는 한번에 하나의 Task만 수행가능
- Actor의 실행을 중단했을때, 다른 작업을 수행할 수 있다


다음 예제를 보면, 전혀 문제 없어 보이는 코드가 있음

![](https://i.imgur.com/y5H1xBq.png)

![](https://i.imgur.com/vH1Vx0m.png)
- 첫번째 줄에서 섬의 파인애플을 Task에 복사함

![](https://i.imgur.com/GU4FFXq.png)
- 두번째 줄에서 Task의 파인애플을 몇개 더함

![](https://i.imgur.com/rRkY5tz.png)
- 세번째 줄에서 Task의 파인애플을 다시 섬에 복사함

문제는 세번째 줄에서 await으로 기다릴때, 또 다른 Task가 섬에 접근해서 파인애플을 모두 가져갔다면?

![](https://i.imgur.com/gMAfcTN.png)
원자성을 보장하지 못함..!


그래서 컴파일러는 Actor-isolated 프로퍼티를 격리되지 않은 곳에서 바꾸는걸 허락하지 않음

![](https://i.imgur.com/tk015DM.png)

대신 Actor 내부에서만 변경 가능하다

```swift
extension Island {
    func deposit(pineapples: [Pineapple]) {
        var food = self.food
        food += pineapples
        self.food = food
    }
}
```

## Ordering

![](https://i.imgur.com/h1kIZKp.jpg)

분명 순서가 필요한 (특히 비동기) 작업이 있음
그런데 Actors는 First-in, First-Out을 엄격하지 지키지 않음 (독립적, 우선순위)

### Tools for ordering

- Tasks를 사용하자

Task는 코드를 순서대로 실행시키니깐?

- AsyncStreams을 사용하자

```swift
for await event in eventStream {
    await process(event)
}
```

### Strict Concurrency Checking

![](https://i.imgur.com/jBGv53v.png)

![](https://i.imgur.com/vWbAtaP.png)
