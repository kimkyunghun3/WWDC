# I Have This Idea For An App...

[영상 보러가기](https://developer.apple.com/videos/play/wwdc2018/203/)

> 앱 개발에 관심은 있지만 아이디어를 어떻게 구현할 지 모르는 사람들을 위한 팁
> 
![](https://i.imgur.com/sLZFzjo.png)

발표자는 줄을 서서 기다리는 동안 아이디어를 생각해냈다.
- 짧은 시간 동안 무언가에 몰입시켜 사람들을 바쁘게 만드는 것

-> 갖고 싶어하는 것(유니콘)과 피하고 싶은 것(똥)을 통해 점수를 내고 플레이어들끼리 경쟁하는 게임


## Creating Views with the Storyboard
![](https://i.imgur.com/MDgnYFs.png)


## Game Logic

![](https://i.imgur.com/XcbcuBH.png)

데모 게임의 로직은 다음과 같다.
- 시작 화면에서는 버튼이 존재하고 버튼을 누르면 게임이 시작된다.
- 유니콘 혹은 똥을 나타내는 이모지와 입력을 기다리는 1초 타이머가 함께 실행된다.
- 1초 동안 유니콘을 클릭하지 않으면 게임이 종료된다.
- 똥을 피하면 다시 유니콘 혹은 똥 이모지를 나타낸다.
- 타이머 시간 내에 유니콘을 터치하면 다음 라운드를 시작한다.

게임의 로직을 먼저 생각하고, 이를 따라서 하나씩 구현해본다.

**시작 화면**

![](https://i.imgur.com/1De0WmK.png)

**게임 라운드**
![](https://i.imgur.com/e8AE7Ne.png)

**버튼 클릭 이벤트 처리(good/bad button pressed)**
![](https://i.imgur.com/VfbSeoL.png)


**실행 결과**
![](https://i.imgur.com/wbYsNbp.png)


여기까지 앱을 구현하기 위해 한 것은..

![](https://i.imgur.com/tlMXKnA.png)

- Xcode 탐색
- Stoyboard를 통한 간단한 UI 생성
- UI와 View Controller 간 코드 연결
- Swift를 사용하여 게임 로직 작성

![](https://i.imgur.com/53G6ayF.png)

여기서 더 해볼 수 있는 것
- SpareKit 프레임워크를 사용해서 중력이나 물리적인 요소가 연결된 버튼 만들기
- MusicKit을 사용해서 게임 플레이 중에 Apple Music에서 음악 실행하기
- 사용자의 움직임에 따라 게임 속도 조절하기

## Enhancing Your App

![](https://i.imgur.com/cugKgU2.png)
![](https://i.imgur.com/FGGrkTd.png)

간단한 게임에 기능을 추가해보자.
이 게임을 플레이한 사람의 각 이름과 점수를 함께 보여주는 순위표를 구현하려고 한다.
순위표에서 이름을 누르면, 더 많은 정보를 보여주는 화면으로 이동할 것이다. 이 기능을 구현하기 위해 고려해볼 측면은 세 가지이다.

![](https://i.imgur.com/vyTev55.png)

첫 번째는 **데이터**다. 사용자에 대한 정보들을 어디에 저장할 것인지, 이 데이터를 어떻게 검색할 것인지 고려해야 한다.
두 번째는 **사용자 인터페이스**이다. 순위표는 어떻게 생겼는지, 어떤 용도로 쓸 것인지 고려해야 한다.
마지막은 **로직**이다. 데이터 전달을 관리하고 앱 내에서 어떻게 사용할 것인지에 대해 고려해야 한다.

![](https://i.imgur.com/uccAq7a.png)

앱을 세 가지 범주로 분류한다면 흔히 'MVC'이라고 하는 **Model-View-Controller** 구조 패턴을 따르게 된다.
이 패턴은 작업을 그룹화하는데 도움이 되고 다른 부분에 영향을 주지 않으면서 앱의 한 부분을 쉽게 변경할 수 있다.


![](https://i.imgur.com/5tuD3DI.png)

새 파일을 추가하고 playerData 모델 클래스를 추가하였다.

![](https://i.imgur.com/o6bQYRw.png)

계속해서 이번에는 gameData 클래스를 추가한다.
gameData 클래스는 게임의 데이터를 관리하는 메서드들로 이루어져 있다.

이 부분에서 중요한 것은 데이터를 보관하고 사용하는 별도의 파일 세트를 만들었다는 점이다. 데이터를 별도의 엔티티로 생각하고 사용자 인터페이스와 독립적으로 유지하는 것이 좋다.

![](https://i.imgur.com/lOBSc2G.png)

백엔드에서 데이터를 관리하기 위한 다양한 방법이 있다.
먼저 **Core Data**를 통해 디스크에 개체를 저장하고 관리할 수 있다. Core Data는 개체의 유효성을 검사하고 쿼리 및 필터링 등의 솔루션을 제공한다.
다음으로 데이터가 웹 서버 어딘가에 저장되는 경우 **NSURLSession**을 사용한다.
클라우드에 데이터를 저장한다면 **CloudKit**의 사용을 고려해볼 수 있다.

![](https://i.imgur.com/DlTqYIg.png)

다음은 UI를 구성하는 방법이다. 순위표 화면을 구성하는 UI 요소들을 위처럼 나타낼 수 있다. 디바이스의 종류나 상태에 보여지는 화면이 달라지지 않기 위해 **Auto Layout** 기술을 사용하여 화면을 구성하는 UIView끼리 Relative하게 배치한다.

![](https://i.imgur.com/i1sBBVP.png)

이번에는 단일 화면이 아닌 여러 뷰 컨트롤러 간의 구조에 대해 이야기한다. 
게임의 전체 화면을 관리하는 단일 뷰 컨트롤러로 시작하여 게임을 구성하는 여러 뷰 컨트롤러들 끼리 서로 이동할 수 있도록 연결하기 위해 **Navigation Controller**를 사용한다.

![](https://i.imgur.com/om5XiLP.png)

마지막으로 데이터 모델과 UI를 연결하고 로직을 작성하여 이들을 하나로 통합할 수 있다. View Controller에서 변수와 함수를 통해 데이터 모델을 호출하고 뷰를 업데이트하여 이를 통합한다.

![](https://i.imgur.com/57QBJPO.png)

앱 빌드가 끝났으면 다음 단계를 알아보자.
- 앱을 출시하기 전에 테스트를 거쳐야 한다.
- App Store Review Guidelines를 확인해야 한다.
- Apple Developer에 등록해야 한다.
- 검토를 위해 앱을 제출해야 한다.



## Summary

![](https://i.imgur.com/kiUtySU.png)

