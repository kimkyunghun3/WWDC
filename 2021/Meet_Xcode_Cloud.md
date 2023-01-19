# Meet Xcode Cloud

Apple Platform에서 CI/CD를 쉽게 사용할 수 있다!

![](https://i.imgur.com/ZFFlulC.png)

깃 레퍼지토리에 push하면 CI에서 Build - Test가 이루어진 다음 이것이 성공하게 되면 Artifacts를 만들고 이메일로 알림 해준다.

![](https://i.imgur.com/bSjIWNS.png)

Build and Test를 오른쪽으로 쭉 이어지는 프로세스가 진행된다. 이렇게 기능을 만들거나 리팩토링을 하는 등 반복적으로 이루어진다.

![](https://i.imgur.com/ZDP4Lj2.png)

자동화로 pipeline를 만들어서 build, test, distribute, collect feedback, iterate on project한다.

![](https://i.imgur.com/sIHjRee.jpg)

Xcode Cloud를 이곳에서 찾을 수 있다.

workflow에서 빌드한 것을 확인할 수 있고 이슈가 나면 그 부분도 확인할 수 있다.
또한 팀에서 하는 모든 것들을 확인할 수 있다.

### workflow란?
configuration으로 Xcode cloud에서 어떤 행동을 수행할 것인지 결정하고 언제 수행할 것인지도 결정한다.
여러개의 workflow를 만들 수 있어서 첫 번째 workflow를 만들고 이를 수정해도 되고 다른 기능의 workflow를 만들 수도 있다.

workflow를 실행하는 것을 build라고 이야기한다.

![](https://i.imgur.com/E8ejdDO.png)

여기에서 모든 workflows의 상태를 확인할 수 있다.

![](https://i.imgur.com/RUKelVo.png)

앱 내에서 각 브런치별로 하나의 workflow를 여러곳에서 사용하는 것을 확인할 수 있다!

![](https://i.imgur.com/ONBAsN4.png)

또한 각 Build마다 test reports, logs 등을 모두 확인할 수 있다.

그리고 Xcode이외에도 App Store Connect에서도 이를 볼 수 있다.

workflow를 관리할 수도 있다.

![](https://i.imgur.com/4VR0KGK.png)

또한 artifacts를 보고 다운로드할 수도 있다.

![](https://i.imgur.com/Qf86gQj.png)

그리고 TestFlight를 쓰면 Xcode Cloud에서도 이렇게 확인할 수 있다.

![](https://i.imgur.com/kfEnzm4.jpg)

알림에 대한 설정도 해줄 수 있다.

![](https://i.imgur.com/O6OuW7a.png)

### Privacy

![](https://i.imgur.com/Wh5TTI3.png)

Build 환경도 일시적이라 지속적으로 유지되지 않는다.

소스는 절대 저장되지 않고 데이터는 암호화되어 있다.

그리고 데이터를 지우고 싶으면 언제든지 가능하다!

### Demo

![](https://i.imgur.com/zjAc24I.jpg)

Workflow를 먼저 생성해준다.

다음으로 어떤 App를 할 지 선택해주면 된다.

![](https://i.imgur.com/09a5RO5.png)

이렇게 하고 default workflow를 만들면 프로젝트 팀에서 존재하는 configuration를 통해 기본 설정을 해준다.

그리고 4가지 조건을 커스텀해줄 수 있다.

![](https://i.imgur.com/lvyYMvv.png)

그 다음으로 이제 git를 사용하기 때문에 사용하는 레퍼지토리를 선택해주면 된다.

그러면 이제 다음 가이드라인이 제공된다.

![](https://i.imgur.com/nXlV3Ub.png)

내용을 보면 이제 내부의 데이터들은 암호화되어있고 가능한 사용자만 접근이 가능한 것을 볼 수 있다.

![](https://i.imgur.com/9cxDAev.png)

![](https://i.imgur.com/GgTwJJW.png)

성공~

그럼 이제 Xcode Cloud는 내 application와 bundle ID를 App Store Connect에 등록하라고 제안한다.

근데 이미 App Store Connect에 만들어진 앱이면 바로 완성을 진행하면된다!

이러면 첫 번째 스텝이 끝난다!!!!

이제 이것을 보면 Overview를 확인할 수 있다.

![](https://i.imgur.com/6aN7bd9.jpg)

Rebuild 버튼을 통해 다시 실행하도록 할 수 있고 Check Out 버튼을 통해 local 환경으로 바꿀 수도 있다.

그리고 Actions에서 Logs를 보면 artifacts와 로그를 확인할 수 있다. 또한 binary file에도 접근할 수 있다.

![](https://i.imgur.com/Dy9lT5P.jpg)

![](https://i.imgur.com/JogjvqZ.png)

에러가 난 부분을 > 버튼을 통해 코드로 확인할 수 있다.

![](https://i.imgur.com/HGOBUXO.png)

![](https://i.imgur.com/nTsxht7.png)

코드를 이렇게 수정한 다음 commit하고 push 해본다

push하게되면 이제 이렇게 빌드가 돌고 있는 것을 확인할 수 있다.

![](https://i.imgur.com/uHjqELs.jpg)

이제 이게 성공한 것인지 아닌지 확인 할 수 있다.

![](https://i.imgur.com/MrCbkIc.png)

이제 Workflow를 수정하려면 workflow에서 오른쪽 눌러서 edit를 통해 들어간다.

이름을 변경해줄 수도 있고

![](https://i.imgur.com/hkW0xZu.jpg)

start condition의 의미는 어떤 trigger를 통해 이 CI 가 동작할 지를 정할 수 있다.

여기에서는 main branch에 push를 하면 진행되는 것이 default로 되어있다.

![](https://i.imgur.com/Y0qq95v.png)

Test에 대해서도 설정해줄 수 있다.

Actions에 Test를 추가하고 Destination에서는 iPhone, iPad 등 원하는 것을 설정해줄 수 있다.
또한 시뮬레이터의 기기를 원하는 것으로 설정해서 타겟에 맞는 테스트가 가능하다.

![](https://i.imgur.com/jTKJz5C.jpg)

만약 Code change없이 build를 하고 싶으면 이처럼 실행해줄 수도 있다.

![](https://i.imgur.com/CG81Ehr.png)

그리고 원하는 브런치를 선택해서 그것을 build 해줄 수 있다.

![](https://i.imgur.com/92P7doR.png)

이제 설정한 브런치에 대해 build가 돌아가는 것을 확인할 수 있다.

![](https://i.imgur.com/8njj027.png)

### 결론!

fastlane, github action같은 3rd party를 안쓰고 애플에서 제공하는 것을 쓸 수 있기 때문에 장점이 존재한다!!

![](https://i.imgur.com/jJ7XjMo.png)
