# Embrace Swift Type Inference

### 타입 추론이란?

![](https://i.imgur.com/Ie4GU7N.png)

타입 추론을 사용하면 중복되거나 덜 중요한 정보를 코드에 담지 않을 수 있게 된다.

따라서 코드가 깔끔해지고, 생산성이 증가한다.

디테일에 대해 생략하고 어떤 타입인지 추론한다.

컴파일단에서 찾는다.

SwiftUI에서 대부분 타입추론으로 동작한다.

![](https://i.imgur.com/0AR6Pt7.png)

![](https://i.imgur.com/jQLXvQI.jpg)

이 타입 파라미터(혹은 Placeholder)들은 호출 시점에 실제 타입으로 교체될 것이다.

제너릭을 사용한 코드는 두 가지 방식 중 하나를 사용하여 타입 파라미터를 concrete type 으로 교체한다.

1. 컴파일러의 타입 추론에 의존한다.

2. 직접 호출 지점(call-site) 에서 타입을 명시한다.

### 타입추론 코드

![](https://i.imgur.com/YxXIXtL.jpg)

데이터 있고 filetBy로 필터를 하며 포함되느지 여부를 필터키를 통해 확인한다. 그래서 탈출 클로저로 하는 이유는 외부에서 프로퍼티로 저장해야하기 때문이다.

ViewBuilder는 여러개의 자식뷰를 선언할 수 있는 용어이며 rowContent를 리턴하여 외부 프로퍼티에 저장한다.

이제 타입을 정한 경우 어떻게 동작하는지 아래와 같이 볼 수 있다.

### 명시 코드

![](https://i.imgur.com/HvEIuI7.jpg)

타입추론이 코드 빠르게 구현할 수 있다. 타입을 정해줄 필요 없으므로 시간이 적게 든다!

타입추론이 이처럼 퍼즐을 맞추는 것처럼 타입을 찾는 것 같다고 한다.

![](https://i.imgur.com/hqgjtxH.jpg)

이제 타입이 정해졌을 때 이렇게 된다. 하나씩 힌트를 통해 컴파일러가 타입을 추론하는 것이다.

![](https://i.imgur.com/6XIMKYx.jpg)

하나의 조각이라도 제대로 맞춰지지않으면 에러가 발생한다.

컴파일러가 Swift5.3, Xcode12부터 이제 힌트를 줘서 더 편하게 해준다.

이제 툴로 어떻게 에러 수정하는지 알려줄 것이다.

Xcode → Behaviors → Falis 탭의 Show issue Navigator 로 설정

![](https://i.imgur.com/bA94bo5.png)

에러 메시지 외에 추가 커맨트가 있다

![](https://i.imgur.com/dhIFGZx.png)


### 마무리

![](https://i.imgur.com/QTvRs1l.jpg)
