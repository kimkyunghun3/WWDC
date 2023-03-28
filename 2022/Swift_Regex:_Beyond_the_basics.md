# Swift Regex: Beyond the basics
[영상 보러가기](https://developer.apple.com/videos/play/wwdc2022/110358)

![](https://i.imgur.com/fxk2fii.png)

> Swift Regex를 통해 문자열을 파싱하는 방법과 동작 방법에 대해 알아보자!
> \* Swift Regex는 iOS 16 이상에서만 지원한다.

Swift 5.7에서 문자열 처리를 위해 다음 기능들이 새롭게 추가되었다. 
Regex 타입, Regex 리터럴 구문, 그리고 RegexBuilder
이것들을 DSL(Domain-Specific Language)라고 한다.

![](https://i.imgur.com/23iXM6h.png)

각각의 사용법
1. String으로 생성
```swift
let regex = try Regex(#"user_id:\s*(\d+)"#)
```

2. Regex 리터럴
```swift
let regex = /user_id:\s*(\d+)/
```

3. RegexBuilder
```swift
let regex = Regex {
    "user_id:"
    OneOrMore(.whitespace)
    Capture(.localizedInteger)
}
```
Regex Builder를 사용하는 것이 일반적인 Swift API를 사용하는 것처럼 읽기 쉽다.

## How does Regex work?

![](https://i.imgur.com/Ghv3w5V.png)

RegexBuilder를 사용하여 이렇게 정의한 Regex가 있다고 가정해보자.
이 정규표현식은 문자열 "aaa12"를 어떻게 매칭시킬까?

![](https://i.imgur.com/edr9tkX.png)

Regex 엔진은 문자열의 처음부터 끝까지 매칭을 시도한다.

![](https://i.imgur.com/rXTB4bR.png)

불일치가 발생할 때 Regex의 다음 패턴으로 넘어간다.

![](https://i.imgur.com/36ycLtv.png)

문자열의 끝에 도달했다면 매칭이 성공한 것이다.

![](https://i.imgur.com/aoHQsLG.png)

Regex engine이 Regex를 사용하여 문자열을 매칭시키고, 이 때 사용하는 것이 Regex builder와 Regex-powered algorithms이다.

![](https://i.imgur.com/LjSDXa7.png)

Regex builder와 알고리즘은 Regex의 기능과 표현을 확장한다.
Regex 기반 알고리즘은 다음 기능들을 포함한다.
- firstMatch(of: regex): 문자열에서 정규식의 첫 번째 매칭 항목을 찾는다.
- wholeMatch(of: regex): 정규식에 대해 문자열 전체를 매칭시킨다.
- prefixMatch(of: regex): 정규식에 대해 문자열의 접두사를 매칭시킨다.
- 그 외에 우리에게 익숙한 몇몇 String function에서도 Regex를 지원한다.
- 또한 패턴 일치 구문에서도 사용할 수 있다.

![](https://i.imgur.com/BA2myyY.png)

마지막으로 Date, Number 등 Foundation Framework에서도 Regex 지원을 할 수 있게 되었다. Foundation의 Regex 지원을 통해 Foundation Parser를 Regex builder에 포함할 수 있다.


## Use a Regex

![](https://i.imgur.com/Sk2DNys.png)

XCTest의 테스트 로그를 파싱해보자.
- 테스트 로그는 Test Suite의 상태로 시작하고 끝남.
- 각 테스트 케이스를 실행하고 결과를 보고한다.
- 여기서는 로그의 첫 줄과 마지막 줄을 파싱해 볼 것임.

![](https://i.imgur.com/9XE20YT.png)

가장 먼저 RegexBuilder를 import한다.
**RegexBuilder**는 Swift 표준 라이브러리의 새로운 모듈로, RegexBuilder DSL을 제공한다.

첫 줄의 로그 메시지를 분석하면 다음과 같이 나눌 수 있다.
`Test Suite '|RegexDSLTests|' |started| at |2022-06-06| 09:41:00.001`

1. 테스트 이름

![](https://i.imgur.com/aF4DgFg.png)

2. 테스트 상태

![](https://i.imgur.com/x7nexlP.png)

`ChoiceOf { str1;str2;str3;... }` 를 사용하면 목록 중에서 매칭 여부를 확인하게 된다.

3. 날짜+시간

![](https://i.imgur.com/RbziT17.png)

at 이후에 나오는 데이터는 모두 날짜와 시간 관련 정보이니 `OneOrMore(.any)`로 하나로 처리한다. 때때로 마지막 줄에 `.`이 나올 때도 있으니 이를 걸러주기 위해 `Optionally(".")`를 사용한다.

![](https://i.imgur.com/1WPWuPi.png)

우리가 생성한 Regex의 매칭 여부를 확인해보았다.

### Regex Literal

![](https://i.imgur.com/Z3a9OhL.png)

> `/.../`
- 컴파일러가 타입을 자동으로 유추한다.
- 캡처링 그룹

![](https://i.imgur.com/bwRFbud.png)

> `#/.../#`
- 확장된 Regex 리터럴
- 의미 없는 공백을 허용한다.

![](https://i.imgur.com/AuwYbsG.png)

Regex 리터럴을 적용하면 이렇게 바꿀 수 있다.


### Capture

![](https://i.imgur.com/J5pK6Rb.png)

문자열이 Regex와 일치하는지 여부만 알고 싶은 것이 아닌, 테스트 이름, 상태, 타임스탬프 등 관심있는 정보를 추출하고 싶을 때 사용한다.
`Capture("")` 또는 Regex 리터럴 내에서 `()`로 사용한다.
캡처는 매칭하는 동안 Input의 일부를 저장하는 방법으로 일치하는 문자열을 Output 튜플에 추가한다.

![](https://i.imgur.com/nx41upw.png)

출력 튜플은 Regex와 일치하는 문자열 전체 Substring으로 시작하여 캡처 순서대로 채워진다.

![](https://i.imgur.com/btRstzm.png)

테스트 로그 정규 표현식에서 캡처를 적용하여 테스트 도구의 이름과 상태, 날짜와 타임스탬프를 캡처한다.

![](https://i.imgur.com/tIsQ01s.png)

출력 튜플에서 캡처한 데이터를 사용할 수 있다.

🤔 **문제 발생**

![](https://i.imgur.com/0gCvKhT.png)

캡처를 사용한 이유는 정보를 정확하게 추출하기 위함인데, `dateTime`에 `.`이 추가된 상황이다.
원인은 바로 `OneOrMore(.any)`이 줄 끝까지 포함해버려 `Optionally(".")`가 제대로 적용되지 않은 것이다. 

### Repetition behavior

![](https://i.imgur.com/GSlnOF0.png)

`OneOrMore`, `ZeroOrMore`, `Optionally`, `Repeat` 등 이런 구문은 모두 Repetition이라고 부르는 행동이다. 이는 일치하는 Substring들을 하나의 문자씩 반복적으로 확인한다는 의미로 `OneOrMore`의 경우 첫 번째 문자부터 시작하여 모든 문자를 받아들이고 더 이상 일치하지 않을 때까지 매칭시킨다.

- `eager`: 가능한 가장 많은 항목과 일치시킨다.
- `reluctant`: 가능한 가장 적은 항목과 일치시킨다.

![](https://i.imgur.com/OzHhO36.png)

아까의 상황에서 우리는 `.reluctant` 옵션을 사용하여 문제를 해결할 수 있다.

![](https://i.imgur.com/nOOvUpK.png)

일반적으로는 `eager`하게 동작하고, 재정의하고 싶을 때는 `repetitionBehavior()`를 사용하면 지정하지 않은 모든 반복 동작을 제어할 수 있다.

![](https://i.imgur.com/u1zdtW3.png)

따라서 `Capture(OneOrMore(.any, .reluctant))`로 코드를 수정하고 나면 의도한 대로 dateTime만 정확히 추출할 수 있게 된다.


### Transforming capture

![](https://i.imgur.com/1p7GYMG.png)

캡처를 사용하여 값을 추출할 때 타입은 Substring이다. Transforming capture는 캡처한 값의 타입을 우리가 원하는 대로 변환할 수 있다.
`transform: { }`을 통해 출력 타입을 변환하고 결과가 `Int?` 타입으로 반환된다. 

![](https://i.imgur.com/ZAQB1t0.png)

만약 옵셔널이 아닌 출력을 얻으려면 `TryCapture`를 사용한다.
(캡처에 실패하거나 TryCapture로 변환에 오류가 발생하는 경우 정규식이 일치하지 않는 것으로 간주한다고 한다.)

![](https://i.imgur.com/9GwGjBw.png)

기본 타입 외에도 직접 정의한 enum 타입을 사용할 수 있다.

![](https://i.imgur.com/QFeTjig.png)

또한 Foundation이 Regex 타입을 지원하기 때문에 내장 Parser를 사용하여 캡처 타입을 Date 타입으로 변환할 수 있다.

## Reuse an existing parser

0.001과 같은 부동 소수점 숫자를 파싱해보자. Regex 구문 내부에서 localizedDouble을 캡처하였다.

![](https://i.imgur.com/1kiyZvv.png)

strtod는 C 표준 라이브러리의 함수로 문자열을 파싱하여 일치 항목의 수를 end 변수에 반환하는 함수이다. 이 함수를 사용하여 Parser를 직접 정의할 수 있다.

![](https://i.imgur.com/4Uhda5j.png)


정의할 구조체의 이름은 `CDoubleParser`
소수를 분석할 것이기 때문에 RegexOutput의 타입은 Double이 된다.

`consuming` 메서드를 구현한다. 시작 주소를 얻기 위해 `withCString` 메서드를 사용하고 `strtod` 메서드를 통해 결과의 끝의 주소를 얻는다. 이렇게 하면 일치하는 부분의 시작과 끝, 길이 모두를 알 수 있기 때문에 upperBound와 output을 반환한다.

![](https://i.imgur.com/e34oRdq.png)

이렇게 구현한 CDoubleParser()를 Parser로 사용할 수 있다.
