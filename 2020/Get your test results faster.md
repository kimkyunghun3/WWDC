# Get your test results faster

Testing Feedback Loop
 
테스트를 만드는 것이 Loop 시작점

![](https://i.imgur.com/O8eACFY.jpg)

Write에서 시작해서 한바퀴 돈다.

불충분한 결과가 나오면 다시 Loop 돌고 제대로 나오면 다음으로 넘어간다.

테스트 간단하게 만들면 빠른 테스트 가능해지고 빠른 구현과 빠른 코드 구현이 가능해지는 선순환 구조

매번 테스트 기다리고 테스트 실패하는 것을 Xcode 12에서는 더 쉽게 만들어 준다.

![](https://i.imgur.com/1RZMsQ7.png)

이것은 테스트가 취소되었을 때 나오는 것

![](https://i.imgur.com/AXUcRrE.png)

Cl job를 취소해야한다 왜냐하면 이것이 정확하게 동작하지 않기 때문이다.

왜 테스트가 취소되고 멈추었는지 생각해보야아 한다.

여러 조건이 테스트를 멈추게하고 취소하게 만든다.

1. 데드락
2. 진행이 매우 느리다
3. 타임아웃 값이 매우 적다
4. 메인 쓰레드에서 많이 일을 한다.

다음으로는 새 테스트 잘 되게 만드는 옵션

![](https://i.imgur.com/M3ve7WG.jpg)

- spindumps

![](https://i.imgur.com/gA7xSqj.jpg)

테스트에 대한 조건

![](https://i.imgur.com/YaIraIs.jpg)

요거 테스트 할 것이다

![](https://i.imgur.com/tCvjxrI.jpg)

잘 동작되고 멈추지 않는다.

멈춘다. 왜냐하면 대기할 이유가 떄문이다

spindump 체크하는 법 

![](https://i.imgur.com/q5jdyfW.jpg)

실패 요인이 다르다.

시간이 초과되었고 spindump 문제있다고 말한다.

![](https://i.imgur.com/51BVkkf.png)

이제 spindump 내용 볼 수 있다

![](https://i.imgur.com/Fa4HDez.jpg)

spindump 2개로 나뉘어져있다

metadata ,스택 trace

stack trace에서 내 코드를 보고 어디가 문제인지 확인가능하다.

기본 allowance를 커스텀할 수 있다

![](https://i.imgur.com/uZ0mnlf.jpg)

기본값에서부터 특별한 케이스로 값을 설정해줄 수도 있다

precedence로 설정여부 어떤 것으로 할지 결정가능

![](https://i.imgur.com/7wghNSt.jpg)

또한 최대 허용량을 설정해줄 수도 있다

![](https://i.imgur.com/LMMVVQc.jpg)

추천! 어떤 것을 쓰냐?

![](https://i.imgur.com/Ivb06Ep.jpg)

이제 다음으로는 여러 디바이스에서 병렬로 테스트하는 법

![](https://i.imgur.com/vq25VHr.png)

병렬로 한다는 증거!

![](https://i.imgur.com/LnlnABv.jpg)

클래스별로 병렬로 각 기기마다 테스트한다

![](https://i.imgur.com/uiqQ1m3.jpg)

Xcode 12 전과 후 어떻게 바뀌었는지?

![](https://i.imgur.com/bMOnflT.jpg)

2개 기계에서 테스트할 때 30% 더 빨라짐

![](https://i.imgur.com/ALPS9cq.jpg)

병렬 테스팅 팁!

![](https://i.imgur.com/9FJgX4L.jpg)

요약

![](https://i.imgur.com/NEjsJYV.jpg)

