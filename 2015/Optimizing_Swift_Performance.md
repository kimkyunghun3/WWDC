# Optimizing Swift Performance

### ****[Optimizing Swift Performance](https://developer.apple.com/videos/play/wwdc2015/409/)****

스위프트는 ARC와 함께 Flexible, Safe 프로그래밍 언어다.

스위프트에는 좋은 기능들이 있다(ex, 클로저, 프로토콜, 제네릭, ARC)

장점으로는 매우 빠르다. 컴파일러에서 native 코드들을 빠르게 실행하게 해준다.

어떻게 빠르게 해줄까?

![](https://i.imgur.com/opFMiOy.jpg)

이러한 컴파일 통한 시스템은 높은 레벨의 기능들의 overhead를 최소화하게 해준다.

하나의 컴파일러 최적화를 소개한다.

- bounds checks elimination

![](https://i.imgur.com/DvkQMJO.png)

스위프트는 내부에서 Precondition으로 조건을 통해 체크를 한다.

이렇게 하는 것이 코드를 더 느리게 만든다.

이것이 다른 최적화를 막는다. 그래서 개선점을 찾는다!

<img width="730" alt="스크린샷 2022-08-23 오후 9 46 25" src="https://user-images.githubusercontent.com/52434820/186187323-21ad2566-efd9-4051-a51f-44d82e65db6d.png">

이렇게 loop 밖에서 체크하는 것은 무시해도 될정도로 성능 저하를 일으킨다.

각 반복에 대해서 매번 체크하는 것보다 loop 밖에서 Array에 진입하기 전에 한번 체크하는 것이 더 좋다. 이렇게 최적화하고 코드를 더 빠르게한다.

새 최적화를 넣어서 기존에 존재하는 최적화 등을 더 빠르게 할 수 있다.

하지만 최적화되지 않는 코드에 대해 고민해볼 수 있다.

왜냐하면 항상 코드 작성, 빌드, 디버깅하기 때문이다. 항상 최적화되지않는 코드에 대해 고민한다.

디버깅은 최적화되어있지 않는다. 이걸 어떻게 최적화를 시킬까?

2가지 방법

1. Swift runtime를 개선한다.

런타임은 메모리 할당을 책임지고 metadata에 접근한다.

1. Swift Standard Library를 개선한다.

이것은 컴포넌트로 array, dictionary, set의 구현을 가지고 있으며 이를 더 개선시키므로 우리는 최적화되지않는 프로그램을 최적화하게 한다.

최적화하는 것과 최적화되지않는 것 모두를 최적화하려고 노력하고 있다.


### Swift vs Objective-C

![](https://i.imgur.com/HBFWNGI.jpg)

스위프트가 더 빠르다는 것을 입증할 것이다.

컴파일 최적화 시스템을 소개할 것이다.

이는 Whole Module Optimization이다! 이거는 프로그램 실행을 상당히 빠르게 해준다.

이것을 말하기전에 Xcode Compiles files에 대해 이야기해본다.

![](https://i.imgur.com/kkl6PvQ.png)

Xcode는 파일들을 독립적으로 컴파일하고 있다.

이것은 좋은 방법인데, 기계에서 많은 코어들을 병렬로 컴파일할 수 있게 한다.

일부가 바뀌면 그 부분만 업데이트 컴파일해준다.

WMO를 통해 컴파일러는 전체 모듈을 한번에 최적화한다. 이것은 모든 것을 분석하고 공격적인 최적화다

WMO 빌드를 더 오래걸리게 하지만, 일반적인 binaries에 대해 실행을 빠르게 한다.

새 최적화 방법인 WMO 소개한다!!

1. 프로그램 빠르게 실행한다
2. 컴파일하는 파이프라인의 일부분을 병렬로 한다.

<img width="849" alt="스크린샷 2022-08-23 오후 9 56 52" src="https://user-images.githubusercontent.com/52434820/186187436-cb45bdad-2838-4478-a72c-6c24da5d4082.png">

이 2개 + WMO로 더 빠르게 실행한다.

전체 모듈을 분석하고 빠르게한다.

![](https://i.imgur.com/VJNX6GM.jpg)

WMO를 선택할 수 있다! 최적화 옵션에 이것이 존재한다.

### 2번째 세션 Writing High Performance Swift Code

3개의 성능에 대한 관점

- Reference Counting
- Generics
- Dynamic Dispatch

### Reference Counting

컴파일러는 대부분의 reference counting overhead를 어떠한 도움없이 제거할 수 있다.

하지만 이것떄문에 코드가 느려지는 것을 확인할 수 있다.

2개 기술은 이러한 overhead를 감소시키고 제거하는 법 알려준다.

클래스에서 이거 어떻게 사용되는지 알려준다.

![](https://i.imgur.com/cNUDppd.png)

x로 클래스 인스턴스 만든다.
1의 reference counting이 생긴다

<img width="1379" alt="스크린샷 2022-08-23 오후 10 04 29" src="https://user-images.githubusercontent.com/52434820/186187572-fcf75a16-82a9-4b17-8aa8-5adc72aa0b13.png">

다음으로 y로 하나의 클래스 인스턴스를 또 만든다. 
RC가 2가 된다.

![](https://i.imgur.com/zdy2M42.png)

이제 메서드 호출하므로 c가 생성되므로 RC가 3이 된다.

<img width="1344" alt="스크린샷 2022-08-23 오후 10 06 05" src="https://user-images.githubusercontent.com/52434820/186187773-2089f218-2828-45a0-af71-08f1073b9de3.png">

이제 c종료되면 소멸되고 RC가 2이 된다.

<img width="1338" alt="스크린샷 2022-08-23 오후 10 06 15" src="https://user-images.githubusercontent.com/52434820/186187786-39761949-8e7b-4282-89db-42fb5b13599c.png">

y, x  nil로 하게되면 이제 0이 된다. 그리고 deallocate된다.

memory safety를 유지한다.

이제는 구조체가 어떻게 RC를 다루는지 말하겠다.
참조를 포함하지 않는 클래스

![](https://i.imgur.com/XeYN85z.png)

array에서 간접적으로 가지고 있고 반복문에서 p의 변수를 참조할때마다 RC가 올라간다

![](https://i.imgur.com/tMo5o5P.png)

그리고 scope가 끝날때 p가 destroy되면 RC감소한다.

클래스가 가지는 오버헤드를 가지게 된다.

이것을 구조체로 만들어보자!!!

![](https://i.imgur.com/CmV0OaM.jpg)

스위프트 배열이 구조체를 직접적으로 저장할 수 있기 떄문에 구조체로 만들게 되면 즉시 배열에 직접적으로 각각의 Point를 저장할 수 있다.

구조체는 상속하지 않으며 RC와 프로퍼티들을 요청하지 않으며 우리는 즉시 모든 RC overhead를 제거할 수 있다.

구조체를 각각 가지는 것은 프로퍼티들을 각각 독립적으로 가지게 되는 것이다.

<img width="1234" alt="스크린샷 2022-08-23 오후 10 16 02" src="https://user-images.githubusercontent.com/52434820/186187950-3433361c-e8e7-48f0-bf78-2c78d1206167.png">

구조체를 각각 복사해서 가지고 있다.

이제 구조체에 각각 다른 칼라를 넣고 싶어서 UIColor를 넣게 된다. 그러면 UIColor는 클래스 이기 때문에 RC를 구조체가 1를 가지게 된다.

![](https://i.imgur.com/EEqYOGG.jpg)

그럼 구조체가 여러개 복사하게 되면 각각 RC를 증가 시키므로 RC 변화에 대해 수행할 필요가 있어진다.

구조체에서 RC 1개씩 갖는 것은 일반적이다 왜냐하면 대부분 클래스로 구현되어있으므로 이렇게 될 수 있다.

하지만 극단적인 예시를 보면

<img width="1204" alt="스크린샷 2022-08-23 오후 10 19 13" src="https://user-images.githubusercontent.com/52434820/186187994-328a6eca-9cce-4206-87f9-8a12b919f954.png">

구조체 안에 클래스의 요소들이 존재한다. 각각 값타입이지만 RC를 하나씩 증가시킨다.

이렇게 있는데 만약 또 다른 구조체를 복사해서 사용하면 가운데 클래스의 RC가 1씩 다 계속 증가하게된다.

![](https://i.imgur.com/IKG8Mo4.png)

하지만 Wrapper class 사용하면 조금 느낌이 다르다

<img width="1315" alt="스크린샷 2022-08-23 오후 10 20 56" src="https://user-images.githubusercontent.com/52434820/186188008-6edb0a4e-74e9-43cf-a3e6-669bd2f0985c.png">

이 구조체를 포함한 Wrapper class를 참조하면 Wrapper class의 RC만 1이 증가하게 된다.

이게 Value Semantics → Reference Semantics 로 바꾼다.

이것이 예상치못한 데이터를 일으킨다. 이는 이상한 결론이나 원하는 않는 것을 얻을 수 있다.

### Generics

![](https://i.imgur.com/h8K5d5n.png)

위에처럼 있는 코드는 실제로 아래에서 컴파일단에서 이루어진다.

2개의 같은 타입을 비교해서 같은타입인지 확인도 하고 어떤 것이 큰 것인지 비교하다.

![](https://i.imgur.com/WfY5bPS.jpg)

다음으로는 컴파일러는 T가 RC 변화가 있는지 없는지 체크하는 것을 요청하는지 알지 못한다.

<img width="1217" alt="스크린샷 2022-08-23 오후 10 30 42" src="https://user-images.githubusercontent.com/52434820/186188160-1d971218-faa4-4e98-b458-83fdb3c0e853.png">

그래서 간접적으로 방향을 넣어주어야 한다. 그래야만 min T 함수는 두 타입의 T를 다룰 수 있게된다. 

그래서 RC를 요청하고 두 타입이 RC가 있는지 없는지 확인한다.

이거에 대해 컴파일러 최적화가 존재한다. 이를 통해 overhead를 줄일 수 있다.

이 최적화의 이름은 generic specialization이다.

<img width="1483" alt="스크린샷 2022-08-23 오후 10 32 22" src="https://user-images.githubusercontent.com/52434820/186188480-1bfc5b3d-3261-489a-b969-24299c04ee87.png">

foo 함수에서 min 함수 호출할 때 x,y 의 Int 타입을 복사해서 실제 내부 컴파일 단의 코드로 복사해서 실행시킨다.

<img width="1179" alt="스크린샷 2022-08-23 오후 10 35 34" src="https://user-images.githubusercontent.com/52434820/186188210-8277ad75-f32e-4ce8-8cf3-3c5b96609239.png">

그럼 이처럼 보여질 것이고 이렇게 하게되면 함수와 관련된 모든 overhead를 제거해준다.

직접적으로 두개를 비교한다.

하지만 한계점이 있다.

제네릭 정의를 보는 것이 어렵다.

![](https://i.imgur.com/mzixHSN.png)

File1, FIle2가 있는데 File1를 컴파일러가 컴파일하면 Int인 것을 알 수 있다.

하지만 File2에서는 이를 알 수 없고 File1에서 Int로 정의된 것을 볼 수 없다.

그래서 결론적으로 min<T>를 호출한 이후에야 이것이 Int인것을 복사해서 가져오므로 이런 한계점이 존재한다.

WMO를 사용할 수 있다면..?

![](https://i.imgur.com/ge6hmSu.png)

File2에서 컴파일하고 있으면 File1의 정의를 볼 수 있으므로 File1에서 min<Int>를 바로 호출할 수 있다.

각각 정의된 것을 알고있으므로..! 다시 컴파일해서 확인할 필요가 없다.

### Dynamic Dispatch


<img width="1427" alt="스크린샷 2022-08-23 오후 10 39 13" src="https://user-images.githubusercontent.com/52434820/186188728-0ccb706e-58ca-4eaa-be25-2e38bf80736b.png">

만약에 class에서 재정의를 하는 것이 없다고 가정된다면 컴파일러는 direct call를 할 수 있다.
어떤 프로퍼티인지 바로 확인가능하다.

하지만 그게 아니라면 컴파일러는 이 정의에 대해 불확실성을 가지고 있으므로 간접적으로 코드를 넣어서 확인하게 된다.

하지만, 만약에 이름이 있는데 이것은 절대 override되지않고 변하지않는다면 어떻게 해야할까?

Swift API 구조를 제한하는 것과 소통하면된다.

- inheritance
- Access Control

상속에서 final 키워드를 사용한다.

<img width="1327" alt="스크린샷 2022-08-23 오후 10 42 23" src="https://user-images.githubusercontent.com/52434820/186188734-e0e3dfa4-78d9-4386-a9f8-2bb28bc6eb20.png">

이렇게 쓰면 절대 상속하지 않는다고 알려준다. 컴파일러는 이름 얻기 위해 간접적으로 콜한다 왜냐하면 상속할지 여부 모르므로 하지만 이게 절대 상속되지않는다면 절대 바뀌지 않으므로 이것을 이름 그대로 바로 보고 이게 절대 바뀌지 않구나로 알고 바로 부른다.

다음으로는 Access Control
![](https://i.imgur.com/RwvwZch.jpg)

만약 Module A Pet.swift에서 noiseImpl 메서드가 있는데 이 클래스에서 noise에 대해 Module B Dog, Module C Cat이 상속을 받아서 사용한다.

그러면 noiseImpl에 대해서는? 컴파일러가 이것은 상속안하고 있는데 직접적으로 알아낼까?

아니다! 왜냐하면 상속가능성이 존재하므로!!!
![](https://i.imgur.com/SCupath.jpg)

그런데 만약 접근제어자 private를 넣는다면? 이것은 이 클래스 내부에서만 사용하므로 절대 밖에서 변화하지 않을 것임을 알기 떄문에 직접적으로 컴파일하게 된다.

WMO and Acces control

모듈 속에 다른 클래스에 있을 때에는 직접적인 콜을 할 수 없다.

하지만 WMO를 가능하게 한다면?

컴파일러는 모듈-wide 보이게하는 것을 가지고 있으므로 동일한 모듈이면 모든 파일들을 함께 볼 수 있게 한다.

![](https://i.imgur.com/n6yGVhT.jpg)

그러면 dog이 이제 재상속할 것이지 않음을 알게된다면 직접접적으로 컴파일러는 noise를 콜하게 된다.

Turn on WMO

코드 바꿀 필요도 없다..! 이렇게 최적화할 수 있다.

이제 왜 스위프트가 더 Objective-C보다 빠를까? oop에서!!!!!!

OBC는 다이나믹 디스패치를 삭제할 수 없다 인라인도 불가능하다. 그래서 컴파일러는 모든 것이 될 수 있다고 예측하므로 느리다.

컴파일러는 Dynamic dispatch를 삭제할 수 있고 인라인도 가능하고 어떤 것인지 알 수 있으므로 더 빠르다.

### 3번쨰 세션, Demo
![](https://i.imgur.com/toqMdEv.png)

여기에 들어가서 시간 체크..FPS 체크한다
<img width="941" alt="스크린샷 2022-08-23 오후 10 56 46" src="https://user-images.githubusercontent.com/52434820/186189033-f2ee8b6a-4dbd-4218-91be-e7219b2b47de.png">

앱실행하면 이렇게 그림이 나온다

FPS보면 1/3이 성능저하되어서 못쓰고있다.

![](https://i.imgur.com/MftK38H.png)

이 툴 사용해서 CPU 사용량을 체크할 수 있다.
    
![](https://i.imgur.com/tg9bUGt.jpg)

아래에서는.. 각각 Call하는 함수들에 대해 몇초가 걸렸고 시간을 얼마나 사용했는지에 대해 알려주고 있다.
    
<img width="1123" alt="스크린샷 2022-08-23 오후 10 59 56" src="https://user-images.githubusercontent.com/52434820/186189118-db5d9929-a5da-40f4-a171-f486c664a934.png">

오른쪽 인스펙터에서는 아까 본 시간에 대해 가장 많이 소요한 순으로 적혀있다!
    
<img width="516" alt="스크린샷 2022-08-23 오후 11 00 53" src="https://user-images.githubusercontent.com/52434820/186189131-2740bab6-37a1-4863-9b40-480e383aba78.png">

retain , release 볼 수 있고 코드에서 이거 체크하려면 이 부분을 더블클릭하면 코드로 가진다

![](https://i.imgur.com/6IGAaCx.png)

<img width="1253" alt="스크린샷 2022-08-23 오후 11 09 17" src="https://user-images.githubusercontent.com/52434820/186189543-c59ae147-fcb9-4034-8ba6-766e9fb389bb.png">

근데 retain, realse못보니 위에서 책갈피 버튼 누르면 들어가진다. 이곳에서 확인가능!
    
![](https://i.imgur.com/WXbsSaK.png)

코드에서 final 넣기 Dynamic Dispatch 제거하고 어떻게 변하는지 보려고 한다.

<img width="909" alt="스크린샷 2022-08-23 오후 11 11 26" src="https://user-images.githubusercontent.com/52434820/186189925-3dcee7c5-518a-4133-a65a-08344150c392.png">

CPU 사용 더 줄었다네요…
<img width="1068" alt="스크린샷 2022-08-23 오후 11 13 18" src="https://user-images.githubusercontent.com/52434820/186189934-bdca77dc-a4ed-495b-a070-97488a0dd8a2.png">

Build Settings에서 WMO 설정해서.. 하면 더 좋아지는 거 체크하는정도,.,?
