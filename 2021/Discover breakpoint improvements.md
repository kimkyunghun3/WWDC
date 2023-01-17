# Discover breakpoint improvements

![](https://velog.velcdn.com/images/marisol/post/a5bfbd17-ff2c-455d-a807-65b8e184e353/image.png)

프로그램에서 버그가 발생하면 예상대로 실행되지 않았다는 것을 의미하며, 왜 예상과 다르게 실행되는지 디버거에서 확인하고자 한다.
이 시점에서 먼저 프로세스 상태를 검사해서 상황을 이해하고, 프로세스 실행 단계를 따라가며 로직을 확인한다.
이 두 활동 모두 버그가 발생하기 직전에 일시 중지해야 한다.
이렇게 그 과정을 중단시키는 가장 좋은 방법은 ```breakpoint```를 사용하는 것이다.

## 1️⃣ Source file breakpoints

![](https://velog.velcdn.com/images/marisol/post/f38404d3-1cab-4604-8b4d-ef5a9f0c9abb/image.png)

Xcode에서 생성할 수 있는 3가지 breakpoint를 소개하고자 한다.

첫번째는 소스파일 breakpoint이다.

![](https://velog.velcdn.com/images/marisol/post/d5d0d149-f48b-430c-b493-6985f4cf3a88/image.png)

소스파일 breakpoint는 단일 파일 내에 설정되며, 검사하려는 코드 라인에서 일시 중지할수 있도록 해준다.
가장 빠르게 이 breakpoint를 설정하는 방법은, 일시 중지할 line 바로 왼쪽의 gutter를 클릭하는 것이다.

![](https://velog.velcdn.com/images/marisol/post/d3b3c2dd-42c5-4147-b9f6-916151f02116/image.png)

그래서 ```convertedToVolume``` 메서드의 로직을 step into로 확인하려고 한다.

![](https://velog.velcdn.com/images/marisol/post/db69f61a-8be9-4424-bc8e-056166097f34/image.png)

하지만 step into 했을 때, 다른 곳에 먼저 step into한 것을 볼수 있다.
컴파일러가 ```adjustedDensity```가 먼저 실행되어야 한다고 판단했기 때문이다.
물론 step out 했다가 다시 step back 할 수도 있지만, 여러번 반복하기에는 귀찮다..

이렇게 가끔 line breakpoint가 충분히 세분화되지 않을 때가 있는데, 그것은 컴파일러가 LLDB가 멈출 수 있는 2개 이상의 위치를 생성했기 때문이다.

우리가 원하는 것은 ```convertedToVolume``` 메서드가 실행되기 직전에 일시 중지하는 것이다.

![](https://velog.velcdn.com/images/marisol/post/ae6a2588-9859-481a-883d-6d34919e2888/image.png)

Xcode 13에서 이럴 때 사용할 수 있는 ```Column breakpoints```가 도입되었다.
```Column breakpoints```는 특정 expression에서 일시 중지해야할 때 line breakpoint의 단점을 피할 수 있게 해준다.

![](https://velog.velcdn.com/images/marisol/post/ba1a355c-289f-4db7-90e6-5cd7a0bdf56b/image.png)

```convertedToVolume```에서 column breakpoint를 설정하려면, expression을 command + click해서 액션 팝오버를 표시한 다음 설정한다. line breakpoint와 마찬가지로 아이콘을 클릭하여 비활성화하거나 활성화할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/19bb3040-1f71-4785-8767-a9921352c6cf/image.png)

breakpoint를 수정해야 하는 경우, 더블 클릭해서 breakpoint editor를 불러올 수도 있다.
line break는 필요없으니까 이제 삭제!

breakpoint에 걸리면 Xcode는 선 위에 연두색 하이라이트로 일시 중지된 라인을 알려준다.

![](https://velog.velcdn.com/images/marisol/post/ed1d8e8c-1aac-49ff-afbd-a98f00d11e89/image.png)

Column breakpoint는 Swift의 클로저, Objective-C의 블럭에 특히 유용하다.
269번 라인처럼 single line이 여러 개의 클로저를 가질 수도 있다.

만약 마지막 클로저의 익명의 매개변수 $0을 검사하고 싶다면, 269에 line breakpoint를 설정할 수도 있지만,
마지막에 도달하기 위해서는 수많은 step in과 step out을 해야한다.

Xcode 13에서는 마지막 $0에서 column breakpoint를 설정할 수 있으며, 일시 중지되면 원하는 위치에 정확히 도달하여 $0을 쉽게 검사할 수 있다.

## 2️⃣ Symbolic breakpoints

![](https://velog.velcdn.com/images/marisol/post/140d98c4-5b44-48d7-af33-550b1e52b422/image.png)

![](https://velog.velcdn.com/images/marisol/post/9006be32-09c7-42f3-a74c-f7a6aa984a15/image.png)

특정 함수가 실행될 때 프로세스를 일시 중지하는 breakpoint이다. 
source file breakpoint를 사용할 수 없는 상황에서 유용하다.

![](https://velog.velcdn.com/images/marisol/post/c05f349d-1ca1-4676-89c9-77a02428b5d2/image.png)

breakpoint 네비게이터 하단의 + 버튼을 클릭하면 생성할 수 있는 breakpoint의 목록이 표시된다.

![](https://velog.velcdn.com/images/marisol/post/536a59d9-e723-4344-ab5d-39cf118c3dec/image.png)

Symbolic breakpoint를 선택하면 symbol name을 입력할 수 있도록 breakpoint 에디터가 뜬다. 
몇몇 클래스에서 실행되는 toggle 함수의 일시 중지를 하고 싶다고 가정해보면, 각각의 항목을 찾는 대신 toggle을 입력하면 된다.

![](https://velog.velcdn.com/images/marisol/post/d7345ad1-9ec8-4e33-b85f-eeff22fd5212/image.png)

하지만 흔한 단어를 사용하는 함수 이름의 경우에는 주의해야 한다.
LLDB가 시스템 라이브러리를 포함해서, 프로세스에 로드되는 모든 라이브러리의 이름과 매칭시키기 때문이다.
때로는 breakpoint의 위치가 수천 개에 이를 수도 있는데, 실행 경로에 지속적으로 영향을 주는 경우 상당히 짜증날 수 있다..

![](https://velog.velcdn.com/images/marisol/post/db86137b-38cc-4d06-98a4-7eea48652019/image.png)

다행히 특정 모듈로 검색을 제한할 수 있다. 여기에 우리 앱의 바이너리 이름인 "Fruta"를 입력한다.

![](https://velog.velcdn.com/images/marisol/post/010b2015-8ad3-4d54-bc3c-fe5d62e0bd75/image.png)

```converToMass```라는 함수 이름을 찾는 symbolic breakpoint를 만들었는데, 점선으로 표시되고 어떤 메서드도 검색되지 않은 것을 볼 수 있다.

![](https://velog.velcdn.com/images/marisol/post/4d7fbd62-3e5b-49fa-acd5-5419e079eff5/image.png)

Xcode 13의 새로운 기능으로, LLDB에 의해 breakpoint가 어떤 위치로도 resolved되지 않을 경우 점선 아이콘을 표시한다.
unresolved breakpoint 아이콘 위에 마우스를 올려 놓으면 툴팁이 제공된다.

![](https://velog.velcdn.com/images/marisol/post/84ccc5fc-e970-43d0-8109-1c01aa0e72a3/image.png)

처음 두 가지 이유는 breakpoint의 종류와 관련이 있다. symbolic breakpoint는 철자가 정확해야 하며, symbol이 라이브러리 내에 존재해야 한다.

세번째 이유는 breakpoint가 있는 라이브러리가 load되어야 하는데, 그렇지 않은 경우이다. 때때로 라이브러리는 버튼을 클릭하는 것과 같은 사용자 액션을 수행한 후에만 로드되며, 이 때 LLDB가 자동으로 breakpoint를 resolve 시킨다.

![](https://velog.velcdn.com/images/marisol/post/3516eea4-a93f-45ab-8089-64ac68c5e635/image.png)

콘솔에서 regex을 의미하는 lookup-r, 이름을 의미하는 n과 convert를 입력하고, 검색을 제한하기 위해 모듈 이름인 Fruta를 입력한다.

그럼 일치 하는 항목이 4개있고, 이름 철자가 잘못된 것을 알 수 있다.

![](https://velog.velcdn.com/images/marisol/post/7b003038-14bd-4a66-83ce-1c433c630c7d/image.png)

## 3️⃣ Runtime issue breakpoints

![](https://velog.velcdn.com/images/marisol/post/d3d237c9-f852-47f3-bd28-3ac4d36c3935/image.png)

runtime issue는 백그라운드 스레드에서 UI 상태를 변경하는 등, 런타임에 발생하는 문제를 말한다.
Crash 만큼 심각하지는 않으며, 기본적으로 Xcode는 다른 버그에 집중할 때 방해가 될 수 있기 때문에 runtime issue 때 프로세스를 일시 중지하지는 않는다.

![](https://velog.velcdn.com/images/marisol/post/3a0c79b3-14d9-4105-85c7-22d43557d1da/image.png)

대신 런타임에 문제가 발생하면 Xcode는 역추적을 기록하여 issue navigator에 표시한다.

![](https://velog.velcdn.com/images/marisol/post/7a3ec0a0-1fc0-4a98-9d01-373f75d20d07/image.png)

![](https://velog.velcdn.com/images/marisol/post/a4b3a952-d009-432e-85f0-d32e2845f675/image.png)

runtime issue breakpoint에는 여러 타입이 있다. type popup을 사용해서 특정 유형을 선택할 수 있다. 

![](https://velog.velcdn.com/images/marisol/post/837d10ec-17e4-4943-a812-23c43455b4f2/image.png)

![](https://velog.velcdn.com/images/marisol/post/83a218cb-146f-40f4-800c-960c6213539b/image.png)

여기서는 메인 스레드에서 런타임 이슈를 확인하고 싶기 때문에 Main thread Checker를 활성화하면 된다.

---
참고자료
- https://developer.apple.com/videos/play/wwdc2021/10209/?time=292
