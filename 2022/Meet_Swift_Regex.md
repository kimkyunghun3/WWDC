# Meet Swift Regex

![](https://i.imgur.com/S9cKiJm.png)

영상 - [바로가기](https://developer.apple.com/videos/play/wwdc2022/110357/)

🤔 다음과 같은 제무제표를 정리해야 하는 상황..

![](https://i.imgur.com/GRonwzj.png)

1) Collection으로 생성

![](https://i.imgur.com/IcHmyyD.png)

문자열의 한 줄을 `.split` 메서드를 통해서 컬렉션으로 만들면? 
- 구분 조건: 연속된 2개 이상의 공백 혹은 하나 이상의 탭 간격
- 구분자의 조건이 복합적이기 때문에 Item 내에 공백이 포함되어 있는 경우에 원하는 대로 조정이 불가능하다.

2) Index로 Field 추출

![](https://i.imgur.com/9AxjF9I.png)

![](https://i.imgur.com/rN7m1RO.png)

코드가 길고 번거로운 작업


이러한 문제의 해결책으로 정규식을 사용한다.

## Regex

![](https://i.imgur.com/GChfTsc.png)

Regex는 구조체이고, iOS 16이상에서 사용할 수 있다.
이것을 생성하는 방법은 3가지가 있다.
1. `//` 사이에 정규표현식 작성
2. `#{String}#`을 통해 문자열에서 런타임에 생성
3. Regex builder 사용

![](https://i.imgur.com/Wp8CxUo.png)

다시 앞으로 돌아와서, split으로 한 줄을 나눌 때 정규식(`\s{2,}|\t`)을 사용하였다.

![](https://i.imgur.com/E18zh6p.png)

기존 정규식 사용에는 몇 가지 문제가 있었지만, Swift Regex를 통해 몇 가지 혁신을 이루어냈다.
- Regex Literal은 간결하고, Builder를 통해 구조화하고 조직화한다.
- Parser를 통해 정규식의 개별 구성 요소로 데이터를 결합한다.
- Swift의 정규식은 Unicode를 지원한다.
- 정확한 표현을 통해 실행 결과를 정확하게 예측할 수 있다.

## RegexBuilder


문자열 처리 방식인 RegexBuilder를 사용하여 위의 거래 구문을 분석해보자.

![](https://i.imgur.com/wJccjzB.png)

transactionMatcher 정의
1. 첫 번째 필드 = `/CREDIT|DEBIT/`
CREDIT 또는 DEBIT

2. 두 번째 필드 = `One(.date(.numeric, locale: _, timeZone: .gmt))`
RegexBuilder에서 제공되는 Parser 사용하여 날짜 생성

3. 세 번째 필드 = `OneOrMore{ ... }`
`OneOrMore { CharacterClass.any }`라고 사용했을 때의 문제점: 공백(space)까지 포함되어 원하는 만큼의 데이터를 포함하지 않아서 fieldSeparator와의 구분이 필요함
`NegativeLookahead { fieldSeparator }`: 데이터를 미리 살펴보고 fieldSeparator를 만나기 전까지 데이터를 자르는 매칭 도구

4. 네 번째 필드 = `One(.localizedCurrency(code:_).locale(_)`
금액 매칭을 위해 RegexBuilder에서 제공하는 Parser 사용

**Capture**

![](https://i.imgur.com/9UTJnZg.png)

우리가 필요한 것은 한 줄만이 아니라 데이터 일부를 추출하는 것이다. 이때 `Capture`를 사용하면 입력값의 일부를 추출하게 된다.

![](https://i.imgur.com/sYYL8Mb.png)

0번째 캡쳐는 전체 정규식과 매칭되며 그 이후 번호부터는 명시적으로 선언한 캡처에 해당하는 데이터가 추출된다.

**Parser**

날짜 순서에 대한 모호함 발생
- 예) 미국은 월/일/년 순서를 사용하지만 영국은 일/월/년 순서

![](https://i.imgur.com/db73TA6.png)

이때 제공된 Parser를 통해 해결할 수 있다.

![](https://i.imgur.com/APf6PxO.png)

거래에 사용된 화폐에 따라 맞는 Date.ParseStrategy를 반환하는 함수 구현.

![](https://i.imgur.com/5Pq8qvr.png)

Date를 생성할 때 해당 Strategy를 사용하여 생성하고 출력한다.


**Comparison**

![](https://i.imgur.com/fzcwK9F.png)

String은 Character의 Collection이며, Character는 1개 이상의 유니코드 스칼라 값으로 구성된다.

첫 번째 이모티콘인 좀비 이모티콘을 위주로 보면, 이 이모티콘은 네 개의 스칼라 값으로 구성된다. 이 값들이 모두 합쳐저 하나의 이모티콘으로 렌더링된다.

같은 값이어도 다른 스칼라 값으로 매칭되는 경우도 있다.

![](https://i.imgur.com/ZvSbp17.png)


Swift에서 정규표현식은 기본적으로 Unicode를 따른다.

![](https://i.imgur.com/4pk7NdL.png)

정확성을 위해 유니코드 스칼라 값을 직접 처리하는 경우 `matchingSemantics()`를 사용한다. 일단 여기에서는 두 비교 방법에 대한 결과가 다르다는 것만 알고 넘어가자.

![](https://i.imgur.com/46XBnkZ.png)

`TryCapture`
매칭되는 필드를 클로저에 전달하는데 timestamp와 details의 값을 확인하여 매칭되는 경우 필드의 값을 반환하고 실패하는 경우 nil을 반환한다

**Local**

만약 Field에 대한 정규식 매칭이 실패하게 되면 다음 fieldSeparator를 찾을 때 Mismatch되는 문제가 발생한다.
- DEBIT 이후의 whitespace들이 fieldSeparator로 캡쳐됨
- 그러나 Field 매칭이 실패한 경우 다음 fieldSeparator를 찾기 위해 앞으로 돌아가 기존의 separator 공간을 하나 감소시킴

![](https://i.imgur.com/rqJ6sE8.png)

`Local`
- 정확하게 지정된 토큰을 매칭시킬 때 유용함
- 더 적은 공간을 소비하려고 다시 앞으로 돌아가지 않는다.
