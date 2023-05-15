# Customize your advanced Xcode Cloud workflows

### Environment variables

key- value 페어

![](https://hackmd.io/_uploads/S1AffBeB2.png)

미래에 어떤 행동에 대해 적어둘 수 있다.

![](https://hackmd.io/_uploads/BkZ7MHer2.png)

예를 들어 매번 빌드할 때 마다 이 API를 세팅하고 액션을 하고 싶을 때 이렇게 지정해줄 수 있다.

![](https://hackmd.io/_uploads/SkNQfSlS2.png)

또한 secret하게 가지고 싶은 부분을 유지할 수 있다. 그리하여 밖의 editor에서 이를 보지못하도록 한다.

![](https://hackmd.io/_uploads/ry8QGrxBh.png)

비밀로 하고싶으면 이렇게 해두면 value가 보이지 않게 된다.

### Custom scripts

![](https://hackmd.io/_uploads/rkFQMSerh.png)

각 타입은 이름에서 알 수 있듯이 특정 단계에서 액션을 할 수 있다.

실행되는 순서

![](https://hackmd.io/_uploads/B13VGBlrn.png)

shell scripts를 삽입하면 끝난다!

![](https://hackmd.io/_uploads/HkYNGSgSn.png)

Environment variables의 스크립트들은 많다!

만약 iOS, macOS, watchOS, tvOS 등을 실행할 때 이 변수를 사용할 수 있다

![](https://hackmd.io/_uploads/r1pcGSeH2.png)

코드가 merge되기 전에 PR에서 build - test를 하게된다. 이를 TestFlight에서 팀원들에게 할당할 수 있다

커스텀 scripts를 프로젝트에 넣는 방법

![](https://hackmd.io/_uploads/Hy-uzrgH2.png)

아래 + 버튼을 통해 폴더를 만들어서 이름을 정해준다

빌드 동안 디자이너가 만든 아이콘을 세팅해주는 스크립트를 넣어준다

![](https://hackmd.io/_uploads/r1p6GHeB2.png)

어떤 타겟도 선정하지 않고 완료해준다

![](https://hackmd.io/_uploads/SJQ2MBgH3.png)

마지막으로 pre-Xcodebuild 스크립트를 더해준다.

이것을 여기에서는 미리 만들어뒀기에 ci_scripts 폴더에 넣어준다!

스크립트를 보면..

![](https://hackmd.io/_uploads/r1gpfrgr2.png)

PR 넘버와 archive 액션을 통해 PR 올린거에 대해 testflight에 올리도록 한다.

만약 존재하는 APP Icon이 잇으면 이를 제거해주도록 한다

그리고 새로운 Icon으로 바꿔준다

![](https://hackmd.io/_uploads/r1e1QBxr2.png)

마지막꺼는 스크립트만 가능하고 소스코드로 빌드를 하는 것이 아니다

그리하여 Ci_scripts folder에 모두 포함되어야한다.

### Additional repositories

InvitationKit를 private 레퍼로 활용해서 팀원들과 사용하려고 한다.

![](https://hackmd.io/_uploads/H1T1XrlB2.png)

패키지를 만들어준다.

dependency를 만들고 이를 내 브런치에서 push하려고 한다.

처음으로 이 dependency를 빌드하는 것이므로 Xcode Cloud에서 없으므로 빌드가 fail된다
이를 해결해준다!

App Store Connect에서 실패한 이유를 알려준다

![](https://hackmd.io/_uploads/rkmgQBlBn.png)

들어가면 세팅페이지가 나오고 Grant 여부를결정한다.

![](https://hackmd.io/_uploads/ByybQSlHh.png)

이러면 이제 connect 완성!

![](https://hackmd.io/_uploads/B1HW7SeHn.png)

![](https://hackmd.io/_uploads/BJBz7SeS2.png)

만약 필요하지 않다면 접근을 제한할 수도 있다

![](https://hackmd.io/_uploads/SJFM7SxHh.png)


### Webhooks

새 build가 나왔을 때 tester에게 알려주기 위해 webhooks 를 사용한다

![](https://hackmd.io/_uploads/SkZXXSgBn.png)

Webhooks 프로세스

![](https://hackmd.io/_uploads/SkVQQHxH2.png)

App Store Connect에서 Settings -> Webhooks에 들어서 추가해주면된다

![](https://hackmd.io/_uploads/HyUm7Bxr2.png)

각 webhook이 유니크한 이름을 해서 구별할 수 있도록 한다.

Webhook 내부

![](https://hackmd.io/_uploads/rkcVmBlSn.png)

![](https://hackmd.io/_uploads/ryyBQreB3.png)

JSOn 형식으로 payload되어 있다

AWS Lambda형식으로부터 webhook 다루는 방법

![](https://hackmd.io/_uploads/SJFd7SlBn.png)

![](https://hackmd.io/_uploads/B1A_Xrxrn.png)

각 webhook 눌러보면 상세 내용을 볼 수 있다

![](https://hackmd.io/_uploads/SJmwXrlrh.png)

큰 프로세스를 보면 이렇다

![](https://hackmd.io/_uploads/HkqLmBxrh.png)


