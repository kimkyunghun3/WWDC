# Build global apps: Localization by example

현지화

Weather 앱은 매일 예보를 확인하는데 각 나라에 맞게 보여준다

![](https://i.imgur.com/I5en6jF.jpg)

### Translation

![](https://i.imgur.com/aUrXNRy.jpg)

영어로 되어있는 날씨에 대한 코멘트는 translation를 통해 각각의 언어로 바꾸어서 보여주고 있다.

또 다른 예시로 메일 앱이 있다

메일에서 Archive가 영어로 되어 있는데

![](https://i.imgur.com/KS8Z9dT.jpg)

스페인어로 언어가 되어있으면 이렇게 바뀌는 것을 볼 수 있다

![](https://i.imgur.com/NWbl5Q0.jpg)

번역기에 각각 localized에 따른 구분을 보내주어서 해당하는 나라의 언어로 보여주도록 한다.

![](https://i.imgur.com/e0jhI4Y.jpg)

하지만 UI에 따라 문법적으로 오류가 생겨서 적절하지 않게 사용할 수도 있다.

![](https://i.imgur.com/ZeoSDCc.jpg)

여기처럼 'Show weather in \(나라 이름)'이렇게 되어 있으면 show weather in 부분은 고정적으로 되어 있어서 다른 나라의 언어를 했을 때 문법적으로 오류를 범할 수 있다.

독일어인 경우 문장 구조가 다르다

![](https://i.imgur.com/PllUtye.png)

해결책은 단순하다!

두가지의 문자열을 작성하면 된다

![](https://i.imgur.com/topW9fa.jpg)

왼쪽은 특정 부분에만 대입시키는데 오른쪽에는 번역기를 사용해서 문장 전체를 바꾸기때문에 상관이 없다.

![](https://i.imgur.com/FRbx1yN.png)

comment 부분에 해설을 잘 적어야 한다. 그래야만 번역기가 필요한 맥락을 이해하고 제대로 번역해준다.

![](https://i.imgur.com/9WYkI8G.png)

context -> user activity

what each variable is -> a specific city
이 부분은 런타임에 결정나므로 표시해주어야 한다.

Weather 앱은 날씨를 제어하지 않고 서버로부터 데이터를 다운로드 받는다.

![](https://i.imgur.com/fzqt0r3.jpg)

하지만 서버에서 받은 데이터를 어떤 언어로 사용하는지 정확하게 파악하지 못하면 이처럼 제대로된 번역본을 사용하지 못한다.

![](https://i.imgur.com/Y8k3DIU.png)

보통 첫 번재 언어가 자신이 원하는 언어인 경우가 많다.

앱에서 remote content를 요청하고 그에 대해 적절한 언어를 서버에서 내려준다.

이제 또 다른 문제가 발생한다.

![](https://i.imgur.com/om0ltmK.jpg)

영어에서는 hour가 1을 넘어가면 s가 붙는데 우크라이나어에서는 다른 방식으로 보여주고 있다.
그래서 이렇게 작성하면 안된다!

우리는 Apple Framework를 사용해야 한다.

![](https://i.imgur.com/ytz0UFh.jpg)

어느곳에서든 이것을 사용하는 것이 아니라, 숫자가 포함되어서 복수형 규칙이 필요한 곳에서만 이 방법을 사용해야한다.

![](https://i.imgur.com/lbMzKla.png)

### Formatter

Weather에서 습도를 퍼센트로 보여주고 있다.

이를 SwiftUI에서는 이렇게 표현한다.

![](https://i.imgur.com/lb8Pasf.png)

그러면 형식자가 알아서 해준다!

![](https://i.imgur.com/1zHUBSP.jpg)

WWDC: Formatters: Make data human-friendly에서 이거와 관련된 것을 보면 좋다

![](https://i.imgur.com/pTiDSfj.jpg)

Rainfall에서도 문제가 있다!

영어, 스페인어가 좀 다르게 표현하고 .

![](https://i.imgur.com/ldlwZlC.jpg)

이것을 형식자 + 복수형 규칙을 결합해서 풀 수 있다.

우리는 이 데이터를 서버에 요청해서 받아 올 것이다.

그래서 먼저 UnitLengh를 통해 요청 단위를 결정한다.
만약 요청단위가 없으면 선호하는 단위로 쉽게 전환하도록 만들어준다.

그리고 formatter를 통해 원하는 방식으로 바꾸어 준다.

만약 소수점이 있으면 이를 설정해줄 수 있다.

이제 이것을 사용해서 만든다!

![](https://i.imgur.com/90ZtQnm.jpg)

앞에 쓰인 stringsdict는 여기에서 볼 수 있다.

![](https://i.imgur.com/2burPpn.jpg)

이를 사용해서 올바르게 UI가 형성된다!

### Swift Pacakages

모듈을 사용하거나 의존을 하는 경우도 있다.

패키지에서 만약 주 언어가 영어이면 이렇게 설정해줄 수 있다.

Xcode는 현지화를 읽고 이제 반응해주고 있다!!

![](https://i.imgur.com/izylfNv.png)

Export를 누르면 Xcode가 코드를 읽고 모든 문자열을 추출한다.

이것들은 변역기로 전송되는 .xcloc 파일에 들어간다!

![](https://i.imgur.com/gQDD6J2.jpg)

Xcode는 패키지의 올바른 파일 경로에 현지화된 파일을 놓는다

![](https://i.imgur.com/bXdEpKj.png)

그러면 swift Package 현지화 작업 흐름은 앱 현지화와 동일하다

우리가 Swift package를 배포하는 라이브러리 저자라면 프로젝트가 업데이트가 되고 현지화에 저익적으로 하도록 해준다.

![](https://i.imgur.com/X5ihAgv.png)


지원하는 언어가 무엇인지 밝혀서 여러 연어에서 쓸 수 있다고 알리면 좋다!

![](https://i.imgur.com/MUjXHA0.png)

지원하는 언어로 모든 테스트를 해야한다. 만약 맞춰지지 않으면 UI 요소에서 불편함으 느낄 수 있다


### Latyout and SwiftUI

영어로 된것, 아랍어로 된것을 보면 레이아웃에 대해 고민해보아야 한다.

![](https://i.imgur.com/vTSox32.jpg)

이것을 자세히 보고 싶으면 WWDC: Get it right ... to left 를 보면 좋다

이제 힌디어로 된 것과 비교해보면

![](https://i.imgur.com/fxsE7q9.jpg)

힌디어는 영어보다 더 글자 높이가 높고 그에 따라 레이블의 높이가 조절이 된다. 이를 시스템이 자동으로 해주기 때문에 고정된 포인트 값을 주지 말고 동적으로 처리해라!

여기에서 보면, 가장 긴 요일 날짜에 따라 레이블의 넓이가 동적으로 변한다

![](https://i.imgur.com/D4fZ6Qc.png)

다른 언어에서는 이렇게 글자수가 최대인 것이 다르기 때문에 동적으로 처리해주어야한다.

![](https://i.imgur.com/or9fO64.jpg)

레이아웃 만드는 방법은 매우 간단하다. Grid를 활용하면 금방 만든다!

![](https://i.imgur.com/vRxigHi.jpg)

SwiftUI가 뷰의 측정, 크기, 위치 조절, 완전 자동을 알아서 해준다!

애플 워치에서도 이것들이 결정된다

![](https://i.imgur.com/CsucID8.jpg)

한 줄에 할 때 부족하면 아이콘 사이의 간격을 줄이거나, 아니면 2줄로 만들고 있다.

스택에서도 다르게 조정할 수 있다.

![](https://i.imgur.com/FmmDMQJ.jpg)

수평으로만 되어 있는 것을 2열 수직 스택으로 변환해서 만들 수도 있다.

SwiftUI에서는 이것을 더 쉽게 만들어준다!! ViewThatFits 이라는 도구가 나왔다
이는 대안 레이아웃으로 제약이 있어서 뷰가 들어가지 못할때 사용할 수 있다.

![](https://i.imgur.com/TkQsGsC.png)

여기에서 레이아웃만 바꾸어야지 뷰를 숨기거나 그러면 좋지 못하다. 

![](https://i.imgur.com/iBzF4HR.jpg)

다양한 기기에서 사용하기 매우 좋다!

WWDC: Compose custom layouts with SwiftUI 를 보면된다!

### Wrap-up

![](https://i.imgur.com/uUEQEXF.jpg)



