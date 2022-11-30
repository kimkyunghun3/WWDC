# what's new in lldb

이번년도의 하이라이트에 대해서 이야기 해볼까 해요.

작년에 WWDC 에서 발표한 LLDB 에 바뀐 것에 대해서 먼저 알아보자. 

## Since WWDC 2014
![](https://i.imgur.com/u1Z8YwZ.jpg)
> * Swift 디버거 출시 
> * Swift REPL 출시 (Read-Eval-Print-Loop, 커맨드 라인 인터페이스)
    > LLDB 가 위장한 버전 

> * 수많은 향상들
    > Swift Type
    > Help 명령어, 명령어들 축약 가능
    > 데이터 포멧의 향상
    > printf() 프로토타입 표현형?

help 같은 경우의 h 로 축약 해서 사용가능하다. 
expression-O-- 같은 경우에 po 로 축약 가능하다. 

앞으로 우리가 디버깅 하기 편하게 하기 위해서, 
raw한 데이터 더미들을 보여 주는 것이 아니라 Set 이나 NSIndex 에 담아서 줍니다. 

이젠 printf 도 쓸 수 있다. 

### Recent BreakPoint Enhancements

![](https://i.imgur.com/V97NcFy.png)
> 브레이크 포인트에 이름짓기 
> * 동시에 유니크하지 않은 이름 짓기 가능하다.
> * 다른 브레이크포인트 명령들은 이름을 가질 수 있다.
> /.lldbinit 에서 브레이크 포인트를 관리 할 수 있다. 
> * lldb 가 시작할 때 새로운 브레이크 포인트를 설정 가능하다. 
> * 모든 디버깅 타겟을 상속 받을 수 있다.

이젠 브레이크 포인트에 이름을 지을 수 있습니다. 
이름을 테그 처럼 사용할 수 있습니다. 

target이 생성되기 전에 모든 target에 해당하는 breakPoint를 생성 할 수 있다. 

### XCODE 7

![](https://i.imgur.com/rjv4UHF.png)

엄청나게 사이즈를 줄이고 퍼포먼스는 향상했다.

메모리가 언제 할당되고 언제 해지되는지 알 수 있다.

![](https://i.imgur.com/BdNmFE8.png)

파일을 안 찾아가고 바로바로 콘솔에서 그 타입에 대해서 검색 해 볼 수 있다. 

---

## 📌 Compilers in LLDB
(이제부터 새로운 사람이 나와서 말한다.)

LLDB 의 Complier 에 대해 말해 봅시다.

![](https://i.imgur.com/6oNrqtF.png)

컴파일러는 LLDB 에 중요한 부분이예요. 

컴파일러는 프로그램의 거의 모든 부분을 고유한 이해 방식으로 알고 있습니다.
그래서 강력하고, 이것은 디버깅을 더 쉽게 만들어 줍니다. 
(변수를 호출하고 싶거나 함수를 호출하고 싶을 때 컴파일러는 다 알 수가 있죠.)

컴파일러가 강력한 이유는 디버깅을 하기 쉽게 만들어 주기 때문입니다. 

print 를 한다고 하면 
print -> expression -> compiler 
이런 식으로 인계를 받아서 사용합니다.  

LLDB 에는 Object-C 컴파일러와 Swift 컴파일러가 있다. 

Clang 은 강력한 Object-C 컴파일러고, 또한 작년부터는 Swift compiler 이기도 하다. 

Objct-C 컴파일러는 계속 적으로 성장왔고, 그래서 많은 기능들을 가지고 있다.

작년에는 swift 컴파일러를 도입했고 이를 바탕으로 Object-C 컴파일러와 swift 컴파일러를 동시에 발전 시킬 수 있었다.

![](https://i.imgur.com/pg1OTH0.png)

Object-C 관점에서의 표현식이 Swift 와 어떻게 작동하는지 봅시다.

여기서 p 라고 입력하고 있죠? expression-- 의 줄임말이니 실제로 우리는 expression-- 을 부른 것이다.

-- 는 p 이후에 오는 것은 모두 코드 여야 한다는 의미이다. 

-- 를 쓰면 이 후에 명령어에 추가적인 옵션을 추가 할 수는 없다.

쨋든 p 뒤에 우리는 코드를 입력하죠. 간단한 for 문을 찍어보자.
우리가 예상한 값이 나옵니다. (별거 없음) 

방금 이 과정이 대단한 이유는, lldb 랑 우리 프로그램은 별개의 프로세스 입니다. 
lldb 내부에는 별개의 신속한 컴파일러가 있다.

프로그램이 이미 실행 중이지만, lldb 의 도움으로 swift 컴파일러는 실행하기 위해 프로그램에 방금 입력한 코드를 삽일 할 수 있다. 

![](https://i.imgur.com/aecpz7O.png)
변수도 사용할 수 있다. 

이것은 Swift 와 SDK 가 작동하는 방식 입니다. 

![](https://i.imgur.com/WLo9Fdi.png)
NSApplication.sharedApplication과 같은 표현식을 입력하면 먼저 예상한 대로 NSApplication.sharedApplication이 표시됩니다.

그러나 LLDB가 하는 일은 나가서 그것을 포함하는 SDK 모듈을 찾아 
컴파일러에 액세스 권한을 부여한 다음 
컴파일러가 NSApplication을 찾고 
sharedApplication을 사용하는 방법을 파악하는 것입니다.

그리고 스위프트에서 이건 자동입니다. 

그러나, 오브젝씨 에서는 항상 작동하는 건 아닙니다.
(이후에 과거 Object-c NSLog 관련 예시가 나오는데 생략)

![](https://i.imgur.com/TZOAnML.png)
이제는 디버거의 향상으로 우리가 만든 코드 SDK 에 있었던 메서드나 클래스 상수 그리고 매크로 모두 지원합니다.

### Handling Error in Swift Expressions

![](https://i.imgur.com/YVRV9Dw.png)
LLDB 에서는 try 문을 쓸 필요가 없다. 


![](https://i.imgur.com/kaF2Ymk.png)
에러가 났을 때 멈추게 하는 방법은 
br s -E objc
br s -E swift
![](https://i.imgur.com/KhVfmb5.png)
특정한 에러에서 멈출 때 
br s -E language -O type-name 

![](https://i.imgur.com/f5jFMPt.png)
try 를 할 필요는 없지만 catch 해서 에러를 해결 할 수는 있다.


## 📌 Presentation Is Everything
![](https://i.imgur.com/iPl5Zpo.png)
po p fr 이들의 차이가 무엇일까요? 
내부적으로 이들은 다르게 작동합니다.

![](https://i.imgur.com/20kPItB.png)
- frame 변수 명령
    - frame variable
    - 축약어 frv
- 모든 지역 변수를 볼 수 있다.
- 이를 통해 로컬 변수 중 일부만 볼 수도 있다.
    - frv tehYear
- 튜플도 볼 수 있다.(강조하고 싶다고 한다.)
    - 튜플 안에 있는 친구를 children이라고 부른다. 


![](https://i.imgur.com/raTSP7H.png)
- expression(p) 명령
    - 산술 연산 가능 
    - 사용자 지정 형식을 수행 가능
- 집합 형식을 볼 수 있다.(다시 한번 강조)
    - 집합 안에 있는 친구를 children이라고 부른다. 

![](https://i.imgur.com/bB7Ki40.png)
- expression-O(po) 명령
- 개체 생성 가능 
- 설명 인쇄 가능 
- NSArray 생성 가능
- 기존 NSArray 인쇄 가능

![po p fr v 의 구분](https://i.imgur.com/gzrOg6b.png)
이런 용도로 구분해서 사용하는 것 같다.

![](https://i.imgur.com/UIS5mJG.png)
![](https://i.imgur.com/CEXAxW9.png)

|fr v|p|po|
|:--:|:--:|:--:|
|코드 실행 안함|코드를 실행함|코드를 실행함|
|LLDB formatter 를 사용함|LLDB 포멧터를 사용함|object 로 포멧한다|

LLDB formatter model은 out-of-formatter model이라고 부른다.
(formatter는 프로세스 외부에 있기 때문)


![](https://i.imgur.com/Qm1yfXH.png)

![](https://i.imgur.com/9WHB03A.png)

![](https://i.imgur.com/TunI3WS.png)

CustomStringConvertible, CustomDebugStringConvertible, CustomReflectable 
같은 프로토콜들은 lldb 결과에도 영향을 준다.

![](https://i.imgur.com/KVKe4OP.png)

![](https://i.imgur.com/3kaDdq0.png)

![](https://i.imgur.com/LKa1oTI.png)

![](https://i.imgur.com/qpjdVwh.png)

![](https://i.imgur.com/9yeql7a.png)

![](https://i.imgur.com/xlPcpEp.png)

![](https://i.imgur.com/LrBUi5k.png)
위의 예시는 LLDB에서 우리가 원하는 커스텀 타입 형식으로 표현할 수 있다를 보여준다.

![Summary](https://i.imgur.com/YgdkHwL.png)

### 요약
더 많은 정보를 더 자주 가능하게! 
* Object-C 런타임
* SDK 모듈
* In-process 포메팅
