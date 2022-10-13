# LLDB: Beyond "po"
### [바로가기](https://developer.apple.com/videos/play/wwdc2019/429/)

# po

LLDB로 변수를 출력하는 법
### 사용법 알아보기

![](https://i.imgur.com/lZUg1by.png)

* po를 통해 객체의 설명(description)확인가능

![](https://i.imgur.com/LSYzcjT.png)

* 시스템이 기본값을 제공하지만 커스텀도 가능하다!

![](https://i.imgur.com/PVCiP37.png)

* subtree 값을 변경하고 싶다면 CustomReflectable 사용한다.

![](https://i.imgur.com/DXsnLl7.png)

* po를 print 외에 더 다양히 사용해보기

![](https://i.imgur.com/AUMlZFu.png)


* po는 축약어이다.
* 커스텀 축약어는 alias로 만들 수 있다.

![](https://i.imgur.com/lyNvP10.png)

### po는 어떻게 동작할까?

![](https://i.imgur.com/34CJ2Cm.png)
![](https://i.imgur.com/oX4Gg68.png)
![](https://i.imgur.com/hDgBP36.png)
![](https://i.imgur.com/CWQIxvb.png)

# p

* p는 po와 동일하게 first-class command 아님
* 즉, alias 형태(축약어)이면서 - - description 가 없는 형태이다.

![](https://i.imgur.com/lliCnzT.png)


![](https://i.imgur.com/Cfguxhx.png)

### p는 어떻게 동작할까?

* po와 유사
* description이 없어서 조금 더 간단
* dynamic type resolution에 대해 자세히 알아보자!

![](https://i.imgur.com/g3fCVj8.png)

### dynamic type resolution: dynamic type 출력하는 것!

* Activity는 static type: source code에 명시된 타입 기반
* Trip은 dynamic type -> p를 통해 정확한 타입 출력됨

![](https://i.imgur.com/pfpXAaI.png)

* 프로퍼티에 접근시 static type이 사용돼서 에러

![](https://i.imgur.com/hVc8jxH.png)

* Formatter로 사람이 읽기 용이한 형태로 변경

![](https://i.imgur.com/BtFsyI1.png)

### Formatter

* `expression --raw --` : 읽기 어려움
* `p`: human readable

![](https://i.imgur.com/AiMhuOU.png)


# v

* 결과는 p 명령어와 동일
* formatter를 통한 결과값

![](https://i.imgur.com/QATIBvb.png)

![](https://i.imgur.com/X47nt4C.png)


* frame variable command의 축약형

![](https://i.imgur.com/xtoxJ7t.png)

* p나 po와 달리 compile, execute 과정이 없어서 매우 빠름

### v는 어떻게 동작할까?

* . 연산자 사용 가능(이유: dynamic type resolution 여러번하고 formatter!)

![](https://i.imgur.com/YDC4oGk.png)

* 명시적인 타입 캐스팅 없이 편하게 출력

![](https://i.imgur.com/cP8KSdo.png)

# p, po, v의 차이점

![](https://i.imgur.com/DWV5zjS.png)

# data Formatter를 커스터마이징하기

* 커스텀하지 않고 디버깅 프린트 했을 때

![](https://i.imgur.com/CIkTSRU.png)

![](https://i.imgur.com/Yp8v0UV.png)

### filter

![](https://i.imgur.com/5JBXPBx.png)


### string summaries

![](https://i.imgur.com/Z7XkYjX.png)

* `${ }` 안에 변수 넣어서 문자열을 작성

![](https://i.imgur.com/0QwgIbX.png)

* LLDB api 접근

![](https://i.imgur.com/6SJADnW.png)
![](https://i.imgur.com/DhlaJOy.png)


* xocde 11 부터는 python3를 사용해서 script
* `script` 작성하면 python 작성가능

![](https://i.imgur.com/FHXLy52.png)
![](https://i.imgur.com/QWqkQw7.png)

![](https://i.imgur.com/5mZL9Vw.png)
![](https://i.imgur.com/aNzPwR2.png)
![](https://i.imgur.com/mY2pS8c.png)

* python 파일 활용해보기

![](https://i.imgur.com/i55Avfc.png)
![](https://i.imgur.com/PMKBVom.png)

### synthetic children

* cf. cruise 설명이 반영된 것 확인 가능(Trip with ~)

![](https://i.imgur.com/w5zpaxn.png)

![](https://i.imgur.com/lBb2gy1.png)
![](https://i.imgur.com/Jil30xu.png)
![](https://i.imgur.com/98BgtcQ.png)
![](https://i.imgur.com/qiNcgxa.png)

