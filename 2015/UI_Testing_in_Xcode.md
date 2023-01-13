# UI Testing in Xcode

![](https://i.imgur.com/jOiu7bP.png)


## Overview

![](https://i.imgur.com/FCwT1nf.png)

**UI testing**
- User Interface 요소를 찾고 상호 작용하며 상태에 대한 UI properties들의 유효성을 검사할 수 있음.

**UI recording**
- 프로젝트에 대한 UI Test를 빠르게 설정할 수 있는 기능

**Test reporting**
- 테스트 결과의 pass, fail 및 result를 보여주는 기능

![](https://i.imgur.com/yo3L3Wu.png)

UI Testing의 핵심 기술은 두 가지이다. 바로 XCTest와 Accessibility

![](https://i.imgur.com/UDFZOWe.png)

XCTest는 Xcode에서 제공하는 테스트 프레임워크이다.
XCTest가 제공하는 기능은 다음과 같다.
- 테스트 케이스에 대한 하위 클래스를 만들어 테스트용 메서드를 구현하여 테스트를 실행할 수 있다.
- Assertion을 통해 예상 결과와 테스트 결과를 비교할 수 있다.
- Xcode와 통합되어 있어 코드 작성이나 디버깅 등을 IDE에서 모두 처리할 수 있다.
- Swift와 Objective-C 모두 지원

(* XCTest가 처음 도입된 것은 Xcode 5. Xcode 6에서는 Performance Test를 지원하도록 확장되었고 이번 Xcode 7에서는 UI Test를 지원하게 됨)

![](https://i.imgur.com/xrFEjNS.png)

Accessibility(접근성)은 다른 모든 사용자에게도 동일한 User Experience를 제공하기 위해 도입된 기술이다. 예를 들어 Voice Over를 통해 시각 장애인들에게도 일반 사용자들과 같은 동일한 경험을 제공할 수 있는데 UI Test에서 이를 사용할 수 있다. 
또한 접근성을 통해 테스트 환경에서 실제로 사용자가 사용하는 것처럼 조작할 수 있다.

![](https://i.imgur.com/Vw1Eezv.png)

UI Test를 하기 위한 필수 조건
- iOS 9/OS X 10.11 이상
- iOS Device가 신뢰할 수 있는 상태


## XCTest and UI Testing

### 1. Xcode target type

![](https://i.imgur.com/CpAZZ0v.png)

UI Testing Xcode Targets
- 테스트 중인 앱과 별도의 프로세스에서 실행될 수 있어야 함
- 개인 정보 보호에서 접근성 사용 권한이 있어야 함
- Cocoa Touch UI Testing Bundle(iOS)/Cocoa UI Testing Bundle(OS X)
- Target to be Tested에 테스트 대상으로 설정이 되어야 함



### 2. API

![](https://i.imgur.com/qwUfNr6.png)

새로운 API는 크게 세 가지가 있다.
**XCUIApplication**, **XCUIElement**, **XCUIElementQuery**

### 3. UI Recording

![](https://i.imgur.com/PedQJ4X.png)

Device 혹은 시뮬레이터에 직접 상호작용을 하는 기능으로 
해당 동작에 대한 코드를 생성해준다.


### UI 테스트 과정

![](https://i.imgur.com/KLGJAID.png)



1. UI testing target 추가
2. UI Recording으로 앱과 상호작용 하는 코드 생성
3. XCTAssertion으로 유효성 검사 수행


## UI Testing API

![](https://i.imgur.com/XxFPNKy.png)

UI 테스트의 과정을 API별로 핵심만 간단하게 나타낸 코드

### XCUIApplication

![](https://i.imgur.com/yFfkdrE.png)

```swift
let app = XCUIApplication()
app.launch()
```
- 테스트 앱의 프록시. 앱의 Life Cycle과 무관하게 별도의 프로세스에서 실행된다.
- 시작할 때 항상 새로운 프로세스를 생성한다.
    - 중간 상태인 경우 테스트 환경이 달라질 수 있기 때문에 처리해야 하는 변수를 최소화
- 시작점 역할을 한다.

### XCUIElement

![](https://i.imgur.com/vagk47M.png)

- 테스트할 App의 인터페이스 요소에 관한 것
- Cell, Button 등과 같은 타입이 있다. 
- 타입과 레이블 값을 조합하여 요소를 찾는다.
- App 상에서 트리 구조를 형성한다.
- 트리 구조와 타입, 식별자를 통해 쿼리는 참조 대상을 찾는다.

![](https://i.imgur.com/Xp1JMNH.png)


이렇게 짜여진 이유는 **요소의 고유성** 때문

![](https://i.imgur.com/kLt1LWh.png)

- 모든 요소는 쿼리를 통해서 접근하고 쿼리는 단일 인스턴스이어야 한다.
- 그렇지 않으면 사용자의 이벤트를 정확하게 반영할 수 없기 때문
    - 예를 들어 "하트 모양 버튼을 클릭한다." 라는 이벤트가 주어질 때, 동일한 모양의 버튼이 여러 개 있다면?
    - 사람이 직접 테스트를 진행하는 것이 아니기 때문에, 일치하는 항목이 정확히 하나만 있음을 보장해야 한다.
    - 단 한가지 예외는 `exists` 프로퍼티


이벤트 합성

![](https://i.imgur.com/5efcCTH.png)

사용자와의 상호 작용을 시뮬레이션 하는 방법. 대체로 플랫폼 별로 다름
Example)
- button.click() : OS X
- button.tap() : iOS

두 플랫폼에서 동일한 유형도 있음
- textField.typeText("Hello, World!") : OS X & iOS


### XCUIElementQuery

![](https://i.imgur.com/391Qj0t.png)

요소를 특정하기 위한 API. 액세스할 수 있는 요소의 컬렉션 형태
접근성에 표시되는 요소만 찾을 수 있다.
주로 사용하는 features
- `count`
- identifier로 특정
- `elementAtIndex()`

XCUIElementQuery는 계층 구조와 필터링을 통해 사용한다.

1. 계층 구조(Relationships)

![](https://i.imgur.com/PMhfqUg.png)

Descendants: 본인 하위 모든 subview들을 포함
Children: Descendants보다 제한적으로 본인 바로 아래 단계에 있는 elements만을 지칭
Containment: 고유한 데이터를 포함하는 경우에 유용

2. 필터링(Filtering)

![](https://i.imgur.com/Ddblm8O.png)

요소에 대한 타입(Button, Table 등) 혹은 Identifier 등을 통해 쿼리를 필터링 가능

#### 쿼리 표현 방법

쿼리를 생성할 때 Relationships와 Filtering을 결합하여 적절하게 활용한다.

\# **descendantsMatchingType()**

![](https://i.imgur.com/SMcaZT4.png)

![](https://i.imgur.com/FG6E6Bq.png)

첫 번째는 `descendantsMatchingType()`으로, 가장 일반적으로 사용하는 쿼리이다. 
타입을 통해 매칭되는 모든 Descendants Elements를 편리하게 찾을 수 있다.

\# **childrenMatchingType()**

![](https://i.imgur.com/xzO0FCG.png)

두 번째는 Direct Child 관계의 Elements만 찾는 `childrenMatchingType()`이다.


일반적으로 잘 쓰이진 않지만 descendantsMatchingType()과 비교하여 차별점을 갖고 있다.



\# **containingType()**

![](https://i.imgur.com/XYbm8h5.png)

마지막은 고유한 특정 값을 통해 찾는 `containingType()`이다.

Cell들은 모두 익명이지만, Cell의 Label 값을 통해 원하는 특정 Cell을 골라낼 수 있다.



#### 쿼리 결합

![](https://i.imgur.com/XmpGQGI.png)


쿼리의 또다른 특징 중 하나는 서로 결합할 수 있다.
쿼리의 출력을 가져와 다음 쿼리의 입력으로 만들 수 있다.


쿼리를 통해 요소에 접근하는 방법

![](https://i.imgur.com/ojoexDh.png)


위에서 언급했듯이 컬렉션 형태이므로 Subscript 혹은 Index로 찾을 수 있다.
Unique한 요소를 찾는다면 element도 사용 가능하다.(예: 화면에서 항상 UINavigationBar는 유일함)


#### 쿼리 평가

![](https://i.imgur.com/XtO4Pau.png)


쿼리에 대한 값 등은 호출 시에 결정된다.
또한 UI가 변경되면 쿼리의 값 또한 변경되어 항상 최신 View에 대한 쿼리와 값을 유지한다.
이런 면에서 쿼리와 요소는 URL과 유사하다.
- URL을 자유롭게 만들 수 있다.
- 리소스를 즉시 가져오지 않기 때문에 URL이 잘못되었는지 생성 시점에서는 알 수 없다.


### 정리

XCUIApplication
- 테스트할 App을 실행하기 위한 클래스

XCUIElement
- 앱을 구성하는 UI Element에 대한 클래스

XCUIElementQuery
- UI Element를 지정하는 방법에 대한 클래스


## Test Reports

![](https://i.imgur.com/o0XjKsu.png)

테스트 결과에 대한 보고서
- 성공, 실패 여부와 실패 시 실패 원인 제공
- Xcode, Xcode Server 모두 동일한 UI 제공
- (Xcode Server) Device 별 테스트 결과 제공
- 실패했을 때의 스크린 샷

![](https://i.imgur.com/AUkmlYP.png)

또한 API 호출에 대한 Nested Activities 제공



## When to Use UI Testing

이미 Unit Test가 있는데, UI Test는 언제 사용하는지??

![](https://i.imgur.com/NhcNeJS.png)

UI Test는 Unit Test를 대체하는 것이 아닌 보완하는 역할을 한다. Model과 Controller의 논리적인 부분에 대해서는 Unit Test를 여전히 사용해야 한다.


![](https://i.imgur.com/3P4abLc.png)

그럼에도 불구하고 다음과 같은 상황에서는 UI Test가 효과적이다.
앱의 사용방법을 안내하거나, 앱의 자동화하기 좋은 workflow 등



