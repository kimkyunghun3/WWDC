# Explore Xcode Cloud Workflows

팀에서 PR로 합쳐지는 경우 이에 해당하는 worflow 생성하는 방법

![](https://i.imgur.com/UA8EU6H.png)

Workflow 생성하는 곳

![](https://i.imgur.com/o6ej1rJ.png)

정해진 사람만이 edit할 수 있도록 설정해줄 수 있다.

![](https://i.imgur.com/Q4qzpvx.png)

또한 정해둔 레퍼지토리, 워크스페이스같은 것을 변경이 있으면 수정해줄 수 있다.

![](https://i.imgur.com/DxpkXk6.png)

### Start Conditions

모든 PR과 main branch로 합치는 것에 대해서 사용하고 싶다.

![](https://i.imgur.com/gdT2fS0.png)

그림으로 보면 이렇다.

![](https://i.imgur.com/oOxMHNb.png)

팀원 모두 merge할 때마다 테스트를 걸쳐서 동작되는지 확인이 가능하다.

File, Folders를 정해줄 수 도 있다.

![](https://i.imgur.com/ewSq97s.png)

그리고 이전에 실행했던 builds를 모두 cancel 시키고 새로 처음부터 하는 build를 만들어줄 수도 있다.

또 다른 조건들을 설정해줄 수도 있다.

![](https://i.imgur.com/qs2Ql00.png)

#### Set up environment

MacOS, iOS 버전 정해줄 수도 있다.

최신 software로 테스트 가능하다.

보통 프로젝트를 하면 많은 파일들과 많은 tools이 있기에 build 시간이 오래 걸린다.

그러나 Xcode Cloud에서 변화가 생긴 파일들만 build해주는 옵션이 존재한다.

이게 build를 더 빠르게 해준다.

![](https://i.imgur.com/9NBxv1E.png)

여기에서 Clean를 하게되면 Clean build처럼 이전에 했던 빌드를 Cache에서 제거하게 된다. 그러므로 시간이 오래 걸리는데 이것을 체크하지 않으면 변화가 생긴 파일만 build해준다.

이로써 성능적으로 더 나을 수 있다.


Xcode cloud API

![](https://i.imgur.com/E9XrbHg.png)

특정 파일이나 변수에 대해 Secret으로 만들 수 있다. 그래서 Build, test를 하지 않게 만들 수 있다.

![](https://i.imgur.com/fIBPPlp.png)

### Configure actions

빌드 시 어떤 액션을 하고 싶은가?

Archive Action

![](https://i.imgur.com/wEMLmT6.png)

Provisioning profiles, code-signing identites를 관리할 필요 없이 Xcode Cloud에서 자동적으로 관리해준다.

Test Action

![](https://i.imgur.com/sPoNFYy.png)

만약 Test Action이 실패하면 Build도 실패하게 된다.

그런데 만약 Build에 영향없는 테스트를 하고 싶으면 해줄 수도 있다.

테스트 하고 싶은 것을 설정해줄 수 있다.

Scheme Settings를 통해 원하는 테스트를 할 수도 있고 전체를 할 수도 있다.

![](https://i.imgur.com/QsLcJAp.png)

Xcode에서 여러 버전, 예전 버전에서도 테스트 하고 싶으면 이를 추가해줄 수 있다.

Analyze Action

컴파일러는 많은 클래스에서 버그나 경고가 생기는 것을 우리에게 알려준다.

이런 것을 런타임에 찾기 전에 이렇게 알려주는 것이 많은 시간을 절약시켜준다.

만약 static analysis를 실행하게 되면 이것은 local의 반복적인 development workflow가 아니기 때문에 이것을 돌리는 것을 까먹기 떄문에 나중에 이슈가 올라온다.

하지만 Xcode Cloud는 변화가 생길떄마다 이것을 해준다.

![](https://i.imgur.com/NOuOa1x.png)

static analysis가 Required to pass할 것인지 말지 결정해줄 수 있다.

만약 결과에 대해 계속 모니터하고싶으면 pass시키지 않도록 하면 된다.

Build action

![](https://i.imgur.com/ZJGv7D2.png)

### configure post-actions

앞에 actions이 끝난 다음 하는 행동이다.

![](https://i.imgur.com/BcaCEmn.png)

notifications

노티에 대해서 두가지 다른 이벤트가 존재한다.

가운데꺼는 PR로 머지되었을 때, 아니면 passing하는 것이 실패하는 조건을 말한다.

![](https://i.imgur.com/L3EmrsE.png)

그리고 Slack, Email를 통해 노티를 설정해줄 수 있다.

기본적으로 Email는 관련된 사람 모두 받을 수 있고 그외에 다른 곳에 추가하고싶을 때 추가하면 된다.

TestFlight deployment

![](https://i.imgur.com/QZ56qCa.png)

TestFlight 내부 테스팅을 하기 위한 조건으로 3가지가 필요하다

![](https://i.imgur.com/5FeZL5i.png)

1번째 Archive에 대한 설정

![](https://i.imgur.com/b1WI0OM.png)

다음 post-Actions에서 설정

![](https://i.imgur.com/6vLGoiQ.png)

QA팀이 main branch로 머지되기전에 미리 성공 실패여부를 확인할 수 있다.

만약 외부 테스팅을 하고 싶으면 그것도 가능하다.

4가지 조건이 있다.

![](https://i.imgur.com/MrO3gzd.png)

clean를 선택해서 derived data를 쓰지않고 처음부터 끝까지 빌드한 것을 가져오게 한다.

App Store Connect에서 관리해줄 수 있다.

### Recommended strategies

만약 외부로 내부내고 release를 하고싶을 때 이러한 흐름이 필요하다.

![](https://i.imgur.com/KcD6vbL.png)

Overnight Test WorkFlow

![](https://i.imgur.com/f2no3g9.png)

시간을 정해서 원할 때 테스트 돌리게 만들 수도 있다.

![](https://i.imgur.com/xWeP7n9.png)

이렇게 테스트를 각각 분리해서 Actions에 만들 수도 있다.

![](https://i.imgur.com/1wvd9v4.png)

### Wrap up

![](https://i.imgur.com/QIVofB4.png)



