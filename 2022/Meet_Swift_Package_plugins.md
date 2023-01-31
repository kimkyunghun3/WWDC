# wwdc: Meet Swift Package plugin
![](https://i.imgur.com/TTmFHEJ.png)
swift package 는 xcode 11 에서 소개 되었고, xcode 14 부터는 plugin도 지원하게 됐다.

![](https://i.imgur.com/SQe4M1j.jpg)
* 패키지 플러그인 이란?
* 플러그인이 어떻게 작동하는가?
* 명령 플러그인
* 빌드 툴 플러그인

## package plug in 이란? 
![](https://i.imgur.com/wIQKjil.png)
* 빌드에서 돌릴 수 있는 스위프트 스크립트 이다.
* 플러그인은 애플에서 플러그인에 사용할 목적으로 만든 API를 사용한다.
* 패키지는 플러그인을 추가로 가지거나, 패키지 자체가 그 플러그인 일 수 있다.
* 패키지 플러그인은 그 패키지 안에서만 사용할 수 있다. 
* 저너럴 플러그인은 해당 패키지 밖에서도 사용할 수 있다. 
* 내 컴퓨터의 개발자 도구에 접근하도록 허용이 필요하다.


플러그인에는 두가지 종류가 있다.
![](https://i.imgur.com/uvlXsql.png)
* Command plugin 
* build tool plugin 

## 커맨드 플러그인
* 사용자 지정 액션을 실행해라, 예를 들어 포멧터나 린터, 배포를 준비하는것 같은 것들 
* 패키지에 있는 파일을 수정해도 되는지 권한을 물어본다. 
* 어떤 경우에는 그냥 보고하기 위해서 정보를 읽기만 하는 경우도 있다.

## 빌드 툴 플러그인 
* 종속성 그래프를 확장한다. 

## plugin 추가하기 
지금은 `CoreLib` 라는 라이브러리에 플러그인을 추가해 볼 것이다. 
`CoreLib` 은 무슨 Object-C 위한 라이브러리이다. 

(사실 정확히 무엇을 위한 플러그인 인줄 모르겠다.
오브젝씨를 쓸 줄모르니...)

![](https://i.imgur.com/6tixHR3.png)
이렇게 하면 패키지에 추가가 된 것이다.

![](https://i.imgur.com/nwHjSnM.png)

패키지를 추가하고 나서 메뉴에 3가지 명령어가 추가로 생겼다.
1. swift 포멧을 쓰는 소스 코드 재 포멧 
2. git 커밋 히스토리 기반의 기여자 파일을 생성하거나 업데이트
3. 소스 파일의 저작권 일자를 갱신

이 상태에서 Run 을 하려고 하면 플러그인인 파일 시스템을 변경할 수도 있기 때문에, 정말로 저 플러그인을 넣은 상태로 빌드를 할 것인지 경고를 해준다.

![](https://i.imgur.com/zx651pn.png)
경고에 동의 한 후에, 왜 접근을 원하는지 보기를 클릭해서 플러그인의 내부를 살펴보자.

![](https://i.imgur.com/wRCIQgd.jpg)
짠!


![](https://i.imgur.com/ZhATkIp.png)

라이브러리와는 다르게, 플러그인의 종속성은 런타임에 컨텐츠를 부르지 않는다. 
대신에 xcode 에 접근해서, 빌드 시점에 자동으로 같이 빌드 되게 한다. 

![](https://i.imgur.com/tAEvUM6.png)
패키지 플러그인을 생성하는 방법에 대해서는 다른 WWDC 가 준비되어 있으니 이것을 보면 된다. 

![](https://i.imgur.com/yq9q50W.png)
명령 플러그인은 개발 워크 플로우를 확장합니다.
각각의 패키지에 직접 적용 되며, 빌드 중에 사용은 안됩니다. 
패키지 소스에 대한 허락을 요구 합니다. 
실제로 작동 하려면 tool 에 의존해야 합니다. 

![](https://i.imgur.com/JVHqA4v.png)

![](https://i.imgur.com/DwOEgNK.png)
이렇게 실제로 실행 명령을 command line tool 에 사용해 볼 수 있다. 
![](https://i.imgur.com/btiaGae.png)
빌드 툴 플러그 인은 빌드 시스템에 대한 명령을 제공한다. 

![](https://i.imgur.com/CZ7Iwsh.png)

![](https://i.imgur.com/bTwczBd.png)

![](https://i.imgur.com/X1shtvq.png)
이런식으로 사용 된다. 

![](https://i.imgur.com/FixHs5L.jpg)
swift package plug in 은 개발 흐름을 위한 강력한 도구이다.

---

개인적인 평은, 마치 라이브러리 처럼 어떤 패키지 매니저를 가져다 쓰느냐에 따라서 다를 것 같다. 
전에 swiftLint 가 SPM 에 라이브러리로 등록이 되어 있지 않아서, 이렇게 유명한 라이브러리 인데 등록이 안되어 있다고? 싶었는데,
검색해보니 이제 플러그인이 나오면서 
swiftLintPlugin 이라는 것이 생겼다. 

아마도 내 코드에 대한 직접적인 접근 허락이 필요하기 때문에, 라이브러리가 아닌 플러그인으로 들어올 수 밖에 없었나 싶다. 

사용해 보니 라이브러리랑 사용하는 법은 거의 비슷한데, 내부 작동 방법이나 권한 허가 측면이 다른 것 같다. 

