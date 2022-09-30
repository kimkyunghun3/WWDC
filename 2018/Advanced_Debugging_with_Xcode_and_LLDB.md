# Advanced Debugging with Xcode and LLDB

[바로가기](https://developer.apple.com/videos/play/wwdc2018/412/)

## Swift debugging Reliability

Xcode 10 에서 개선된 사항 설명

![](https://i.imgur.com/WuOEa2V.png)
![](https://i.imgur.com/L0GTgpV.png)

## Advanced Debugging Tips and Tricks
## Demo

<img src="https://i.imgur.com/oDwS3J5.png" height="600">


* tap 하면 사람이 점프하고 점프한 높이를 수치로 나타내는 앱

![](https://i.imgur.com/az5h4T1.png)

* 위 문제를 하나씩 해결할 예정

### bug1: bar 높이까지 도달하지 못하고 사람이 떨어진 상황에서 사람은 다시 서 있어야 한다!(지금은 누워있는 상태라서 문제!)

<img src="https://i.imgur.com/TN4IyIS.png" height="600">

* 문제인 상황: 사람이 떨어진 상태에서 누워있음

![](https://i.imgur.com/Rthsvnn.jpg)
![](https://i.imgur.com/FcHOQUv.jpg)
![](https://i.imgur.com/HB0I7B5.jpg)

* breakpoint에 걸린 상태에서 (1) 조건문을 expression을 통해서 변경한 후 (3)다음 step over the line으로 진행하도록 해서 (초록색 라인) break 위치가 변경된 상황

![](https://i.imgur.com/xEDkMn5.png)
![](https://i.imgur.com/XvpxQr4.png)

* 프로퍼티 변경하고 자동으로 continue하도록 액션 추가하고 체크박스 체크한 상황
* 위 설정으로 인해 tap 을 해도 항상 사람은 bar 높이까지 도달하지 못하도 떨어진다.

![](https://i.imgur.com/2Mkhnay.png)
![](https://i.imgur.com/tnNCd66.png)

* delegate callback은 잘 구현된 상태

![](https://i.imgur.com/kJX1Tzh.png)

* 해결법: delegate 를 설정한다.
* 위 코드가 맞는지 확인하기 위해서는 다시 build 해야하지만, 시간을 줄이고 싶다면?

![](https://i.imgur.com/gDNxf8Z.jpg)

* 위 처럼 breakpoint를 설정하고 action을 추가하면 다시 compile 하지 않고도 코드를 확인할 수 있다.

<img src="https://i.imgur.com/YSjkE2c.png" height="600">

* bug 2~4는 play mode일 때 생기는 문제들

### bug 2를 해결해보자: text가 갱신되지 않는 문제

<img src="https://i.imgur.com/zFa1lJd.png" height="600">
<img src="https://i.imgur.com/wIvvaev.png" height="200">


* breakpoint navigatior 에서 Symbolic Breakpoint를 추가한다.

![](https://i.imgur.com/3589TwU.png)

* Symbol을 objective-c 문법으로 작성한 상황

![](https://i.imgur.com/wOX30AT.png)

* 문제를 해결할 수 있는 위치를 1개 이상 child로 보여준다.

![](https://i.imgur.com/SQgrLum.jpg)

* tap 을 한 번하면 이제 assembly code를 보여주면서 break 된다.
* `po $arg1` 를 통해서 첫 인자값을 확인할 수 있다.

<img src="https://i.imgur.com/b92PDnK.png" height="100">

* arg2는 `selector`라고 한다.(selector를 알고 싶다면 Objective-c 문법 검색해보기!)

<img src="https://i.imgur.com/vDgc7Fq.png" height="50">

![](https://i.imgur.com/U4S1YVK.png)

* continue하고 다시 arg1을 살펴보면 0ft인 것을 알 수 있다.

* 점프하는 애니메이션이 끝나면 레이블이 업데이트 되도록 수정하려는 상황이다!
* 위에서 설정한 symbolic breakpoint는 삭제한다.

![](https://i.imgur.com/MSpOore.jpg)


* jump가 끝났을 때 호출되는 메서드에 breakpoint를 설정하고 action을 추가한다.
* one shot으로 설정한 breakpoint는 trigger를 만족했을 떄만 설정됐다가 자동으로 delete된다.
* 즉, breakpoint에 걸리면(jumpt가 끝나면) 임시 breakPoint를 UIlabel에 설정한다.

![](https://i.imgur.com/yIDGAfF.jpg)

* 임시 breakpoint에 걸린 상태에서 po로 첫 arg를 확인한다.
* 수정 대상을 찾은 상황


![](https://i.imgur.com/ZlZCHBF.jpg)

* debug navigator에서 (1) tap해서 UILabel에 (2 초록색 라인) text가 할당되는 코드 위치를 찾을 수 있다.

![](https://i.imgur.com/z816xm6.png)
![](https://i.imgur.com/diVGCIE.png)

* 커서를 값 위에 옮겨 가면서 실제 들어있는 값을 확인한다.
* text에 할당될 값(신규 데이터)이 valueText에 있음을 확인한다.

![](https://i.imgur.com/MZqOh5M.png)

* 다시 compile하지 않고 확인하려고 한다.
* 수정이 필요할 것 같은 코드 "다음 줄"에 breakpoint를 설정한다.

![](https://i.imgur.com/qgEdGuk.png)

<img src="https://i.imgur.com/IrRtprV.png" height="500">
<img src="https://i.imgur.com/8Ujs87T.png" height="500">

### 이제 bug3(attemp 10번 이하)을 해결할 예정!

![](https://i.imgur.com/a8DlkDN.png)

* 위에서 breakpoint에 걸린 부분은 animation을 진행하는 부분
* **빠른 실행 확인**을 위해서 애니메이션 하는 것을 기다리고 싶지 않은 상황!
* but, 지금 상태에서는 false 할당 불가능

![](https://i.imgur.com/C9JXk0r.png)

* (1) instruction pointer(초록색 라인)을 드래그해서 line 141에 위치시키고 (2) expression 을 통해 false로 설정한다.
* continue하면 이제 애니메이션은 생략된다!

* 이제 위 상황이 반복적으로 적용될 수 있도록 action을 설정해보자!

![](https://i.imgur.com/8wWIhPH.jpg)


* "+" 로 action을 추가할 수 있다.
* (1) 라인 1개를 jump하고 (2) false로 바뀐 명령어를 수행하도록 action 2개를 추가한다.

* 위 설정이 가능한 이유: breakpoint에 걸리면 해당 Line은 실행되기 이전 상황이라서!


![](https://i.imgur.com/Z4XzAym.png)

* `po` 를 통해 custom한 debug 메시지를 확인할 수 있다.

![](https://i.imgur.com/TWWPPNa.png)

* CustomDebugStringConvertible을 채택한다.
* extenstion을 통해서 debugDescription을 구현한다!

![](https://i.imgur.com/vWdq6nL.png)

* `p`를 사용하면 기본적인 object에 대한 설명을 확인할 수 있다.
* maxAttempt는 잘 설정된 상태 -> 따라서, 로직 에러라고 판단

![](https://i.imgur.com/XE1koh6.png)

* 중앙 하단에 있는 검색창을 통해 gamePlay를 검색한다.

![](https://i.imgur.com/2orh2Nr.png)
![](https://i.imgur.com/76zzbDe.png)

* watchpoint가 추가됐다.
* breakpoint와 유사하지만, 해당 값이 변경됐을 때 멈추는 특성이 있다.

![](https://i.imgur.com/POJxC8j.jpg)

* 값이 수정되는 위치에서 watchpoint가 걸린 것을 확인한다.
* 로직 수정할 위치 발견!

![](https://i.imgur.com/ZNkxsZ1.jpg)

* action 추가

<img src="https://i.imgur.com/WwlScAz.png" height="500">
<img src="https://i.imgur.com/tzjrNlT.png" height="500">

![](https://i.imgur.com/N3kXfpG.png)

* 간단한 테스트를 통해 검증된 코드를 원하는 위치에 작성한다!

### 이제 bug 4을 수정하자: score, attemps 레이아웃 에러 수정하기!

![](https://i.imgur.com/XMU130y.png)

* 레이아웃을 수정할 view를 찾아보자!

![](https://i.imgur.com/7hQUAgi.png)

* `po`를 통해 해당 view의 주소값을 알 수 있다.

![](https://i.imgur.com/xl6PUyr.png)

* 해당 view의 하위 view를 알고 싶다면? view hierarchy를 이용한다.
* 나중에 살펴보자!

* console에서는 확인하는 방법?

![](https://i.imgur.com/DHOjvEe.png)

* Swift 코드로는 에러 발생
* recursiveDescription은 debugging 목적으로만 존재한다. 즉, public API가 아니다.
* 또한, Swift는 strictly defined 되지 않은 메서드를 호출하지 못한다.
* objctive-c 로는 자유롭게 해당 메서드 호출 가능!

* objc 관점에서 po를 사용하려고 한다.

![](https://i.imgur.com/Nwpanr5.png)

* 에러가 발생한 이유: expression이 objc 를 위한 임시적인 환경 제공함

![](https://i.imgur.com/qWBOzXk.png)

![](https://i.imgur.com/TQvodBC.png)

* 레이아웃을 수정하려는 view의 메모리 주소를 찾았다!

![](https://i.imgur.com/E7Br8Dd.png)

* po가 제대로 먹지 않는 이유: Swift 는 숫자를 pointer로 고려하지 않는다.

![](https://i.imgur.com/xtPqNPs.png)

* objc 관점에서 po를 사용하려고 한다.
* 너무 길어서 간단하게 명령어를 사용하고 싶어서 `alias`를 통해 `poc`라는 커스텀 명령어를 생성한다.
* `poc`를 통해 간단히 objc 관점에서 po를 사용할 수 있다.

![](https://i.imgur.com/IQmtECE.png)

* Swift로 메모리 주소를 사용해서 po를 하려면 위와 같이 `unsafeBitCast`를 사용해서 직접 타입을 명시해야한다.

![](https://i.imgur.com/IlLg0tr.png)

* 반환값이 있어서 frame 같은 프로퍼티를 확인할 수 있다.

![](https://i.imgur.com/a1labtS.png)

* 우리는 center의 y를 300으로 수정할 것이다!

![](https://i.imgur.com/OD0wL31.png)

* 300으로 바뀐 것을 확인할 수 있다.
* 그렇지만 지금 breakpoint에 걸려있는 상태여서 simulator 레이아웃은 변경되지 않은 상태이다.

![](https://i.imgur.com/Vgt6bzU.jpg)

* 위 코드를 작성하면 break 인 상태에서도 시뮬레이터에 새로 세팅된 레이아웃이 적용된 것을 확인할 수 있다.

## custom LLDB 명령어 사용하는 법

python 파일로 custom 명령어가 구현되어 있다.

![](https://i.imgur.com/6XivUJs.png)


![](https://i.imgur.com/ZJxEmMi.png)


![](https://i.imgur.com/8HYNz4d.png)
![](https://i.imgur.com/VeCHS6m.png)

![](https://i.imgur.com/bR1GI5L.png)


* custom 명령어를 통해 간단하게 명령어를 사용할 수 있다.

![](https://i.imgur.com/ujxvRc4.png)
![](https://i.imgur.com/018VbmF.png)

![](https://i.imgur.com/5txjGDd.png)
![](https://i.imgur.com/9b1DIsV.png)

![](https://i.imgur.com/1NQycpI.png)

* variable은 compile/expression을 하지 않는다. name variable의 값을 메모리에서 바로 읽고 LLDB built-in formatter를 사용한다.
* p나 po를 사용하는 것을 실패했을 때 variable을 사용하면 될 수도 있다.

### MacOS 앱 문제 해결해보자!

![](https://i.imgur.com/zhadqNr.png)



* popOverView가 dismiss 돼서 view hierarchy로는 살펴보기 힘든 상황
* 즉, app이 active state에서 확인하고 싶은 상황
* 2가지 방법이 있는데,

![](https://i.imgur.com/jcRhhgR.png)

* popOverView 보는 방법 1: touch bar를 사용한다.

![](https://i.imgur.com/3ZX3WyU.jpg)

* spray icon을 클릭하면

![](https://i.imgur.com/n2syELB.jpg)

* touch bar 에서 view hierarchy 사용할 수 있다.

### bug1 해결하기: 이미지가 center에 있지 않다.

![](https://i.imgur.com/nt7fm1a.jpg)

* 레이아웃 문제를 먼저 해결해보자!
* 참고: _ prefix인 프로퍼티는 system framework에서 internal로 사용되는 것이라 코드적으로는 접근할 일이 없는 프로퍼티

![](https://i.imgur.com/Fiqrakw.png)

* view를 선택하고 reveal in debug navigator를 하면 

![](https://i.imgur.com/8QzvstE.png)

* navigatore에서 파란색으로 해당 프로퍼티가 표시된다.

![](https://i.imgur.com/zKlZJdI.jpg)
![](https://i.imgur.com/KGa8do2.jpg)

* 하단 메뉴에서 2번째 아이콘을 클릭하면 레이아웃에 대한 정보를 볼 수 있다.

![](https://i.imgur.com/sC7C7VH.png)

* 중앙 라인을 클릭하면 인스펙터에서 상세 정보를 확인할 수 있다.

* superView의 horizontal center인 것을 확인한다.

![](https://i.imgur.com/AFcy9x4.jpg)

* 상위 view를 선택하고 살펴보자
* 레이아웃이 이상하다는 사실을 알고 leading, trailing 제약을 자세히 살펴본다.

![](https://i.imgur.com/gwQDQZZ.jpg)
![](https://i.imgur.com/TIw9JCl.jpg)
![](https://i.imgur.com/b65bW2D.png)

* window를 벗어난 상태로 중앙 정렬되어서 이상하게 보인 것!
* constant에 -30을 넣어야 하는데 실수로 30을 넣은 상황
* console에서 확인해보려고 한다.

![](https://i.imgur.com/rhM4Weh.jpg)

* trailing을 선택하고 copy 한다.

![](https://i.imgur.com/KmxMRyL.png)
![](https://i.imgur.com/oYK4gdZ.png)

* expression의 간단한 형태가 e 이다.

![](https://i.imgur.com/tEqS0ff.jpg)

* 위에서 정의한 custom 명령어를 통해 간단하게 레이아웃이 반영된 것을 확인할 수 있다.
* 이제 실제 코드에 수정된 내용을 반영해보자!

* 제약 조건이 선택된 상태에서 location backtrace를 살펴볼 것이다.
* backtrace 보이도록 세팅하는 법

![](https://i.imgur.com/xMwSa1C.png)

![](https://i.imgur.com/0bHaRXM.png)

* Malloc Stack logging을 "all location and Free History"를 선택한다.
* 그러면 메모리 그래프나 view debugger 에서 location backtrace를 확인할 수 있다.

![](https://i.imgur.com/Hb1cHm6.jpg)

* 커서를 올리면 말줄임표로 생략된 내용 전체를 확인할 수 있다.

![](https://i.imgur.com/nwxwf2Z.png)
![](https://i.imgur.com/Qfh9yBF.jpg)


* 커서 올렸을 때 나오는 화살표를 클릭하면 해당 코드에 바로 갈 수 있다.

![](https://i.imgur.com/mUb7NmI.png)

### bug2 해결하기: popover 문제

* popOverView 보는 방법 2

![](https://i.imgur.com/aFYv2oq.jpg)
![](https://i.imgur.com/0ZuApwI.jpg)


![](https://i.imgur.com/6A3nZCl.jpg)


* asset catalog로 색상 제공
* 상황(appearance)에 따라 색상을 정의할 수 있다.

![](https://i.imgur.com/57x7Jdx.png)

![](https://i.imgur.com/Z7vyOPr.png)

* custom debug description을 정의하면 console로도 볼 수 있고, view debugger에서도 확인 가능


![](https://i.imgur.com/268WaC8.png)

* system color로 자동으로 light/dark mode에 맞게 색상 변경됨

![](https://i.imgur.com/0qDW6iH.jpg)

* 문제인 부분: custom color를 설정했지만 appearance에 따라 설정X
* 해결: system color로 변경할 예정

![](https://i.imgur.com/Y6lsYxh.png)

* copy해서 주소값을 가져오고 custom 명령어로 시뮬레이터에 반영되는 것을 확인한다.

![](https://i.imgur.com/Q2WV7jY.png)

![](https://i.imgur.com/4CpAmxO.png)
![](https://i.imgur.com/C9Fu7gW.png)

![](https://i.imgur.com/O3u2Oj3.png)

* appearance mode는 touch bar 에서도 설정 가능

![](https://i.imgur.com/kKjhqYL.png)
![](https://i.imgur.com/1KCHNvV.png)


