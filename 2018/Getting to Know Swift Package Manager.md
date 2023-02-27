
# Getting to Know Swift Package Manager

목차

![](https://i.imgur.com/IMwX5GB.png)

Swift는 크로스 플랫폼 빌드 시스템을 가지고 있다.

![](https://i.imgur.com/vs6iRpE.png)

그래서 스위프트가 지원하는 모든 플랫폼을 활용할 수 있다.

코어 라이브러릴를 재사용하게 된다.

![](https://i.imgur.com/mM43Q7L.jpg)

SPM는 이런 것들을 쉽게 distribute하게 해주고 각각 core library에 넣는 것이 아니라 패키지로 담게 해준다.

SPM는 스위프트의 오픈 소스 프로젝트 중 하나다

https://www.swift.org/package-manager/
이곳에서 어떻게 되어있는지 확인할 수 있다.

### How to use it

4개의 command가 존재한다.

swift Build, swift Run, swift Test, swift package
이름그대로 사용..!

**Demo**

![](https://i.imgur.com/rSJtPkR.png)

helloworld 라는 파일을 만들고 이곳에서 swift package 공간을 만들어 준다.
그래서 finder에 가서 보면 이렇게 생성되어 있다.

![](https://i.imgur.com/XuZjFAv.png)

swift run 하면 실행된다.

Package에 내부는 Targets, Products, Dependencies로 되어있다.

![](https://i.imgur.com/TcVJY8y.png)

Dependencies는 기능을 구현하기 위해 사용한다.

각 dependencys는 하나 이상의 라이브러리같은 products를 제공한다.

![](https://i.imgur.com/Ej4sDKK.png)

코드에서 이처럼 되어있다.

![](https://i.imgur.com/2JZcEMl.jpg)

Targets
소스파일을 모듈이나 테스트에서 어떻게 빌드할지에 대해 정하는 것이다.

![](https://i.imgur.com/mmfYA0T.png)

타켓은 다른 타켓을 의존할 수도 있고 다른 프로덕트를 의존할 수도 있다.

Products

라이브러리를 실행할 수 있고 하나 이상의 artifacts들을 합쳐줄 수 있다.

![](https://i.imgur.com/igMxhs2.png)

패키지는 다른 패키지들에게 product를 제공하여 product를 정의내린다.

우리는 Linkage를 선택할 필요 없다. 대신에 SPM이 알아서 우리가 사용하는 것에 따라 자동적으로 선택해준다.

![](https://i.imgur.com/40n8uW1.jpg)

만약 필요하다면 이를 임의로 설정해줄 수도 있다.

코드에서 보면 이렇게 쓰여져 있다.

![](https://i.imgur.com/dtSaOYs.png)

products에 대한 코드도 되어있다.

![](https://i.imgur.com/8Sbm1Th.png)

두번째 보면 dealer라는 dependencies를 가지고 있으며 터미널에서 실행할 수 있다.

이제 패키지 파일에서 우리는 SPM를 추가할 것이다.

![](https://i.imgur.com/lJHpyoa.png)

dependencies에서 이를 추가하고 타겟에도 Utility라는 것을 추가한다.

![](https://i.imgur.com/GlEkdYz.png)

이제 실행해주면 Utility라는 칼라가 들어간 카드들이 나오게 된다.

![](https://i.imgur.com/nCuvllA.png)

또 테스트에 대해서도 병렬로 실행해줄 수 있다.

![](https://i.imgur.com/pSMsFi5.png)

SPM는 빠르고 안전하고 expressive하다.

![](https://i.imgur.com/0p0EwVW.jpg)

SPM의 디자인

![](https://i.imgur.com/7bZeyxd.jpg)

Configuration

![](https://i.imgur.com/svTM0bJ.jpg)

이해기 쉽게 가이드라인도 있어서 따라하기 쉽고 그런 의미같다!

오른쪽 처럼 선언적으로 표현하는 것이 더 좋다

![](https://i.imgur.com/RApxiRN.jpg)

왜냐하면 SPM는 우리 코드가 언제 어떻게 변하고 개선되는지 알 수 없기 때문에 이렇게 선언적으로 표현하는 것이 이를 대응하기 쉽게 해준다.

소스 파일도 이렇게 구조적으로 되어있어서 우리가 이해하기 쉽고 빠르게 찾을 수 있다.

![](https://i.imgur.com/PwVEZHT.png)

![](https://i.imgur.com/H2GUUyD.png)

둘다 보기쉽고 빠르게 삭제 추가 가능하다

SPM는 C, C++, Objective-C 를 모두 지원한다.

![](https://i.imgur.com/JfdLZm4.jpg)

하지만 스위프트와 다른 언어를 동시에 접근해서 사용하는 것은 불가능하다.

![](https://i.imgur.com/BLm5Ttu.jpg)

Dependecies

SPM는 빌드 하기 전에 모든 패키지에 대해 정확한 버전을 파악하고 시작한다.
그래서 가장 최신 중에 모든 것이 호환이 될 수 있는 것을 찾는다.

이제 아까 봤던 dealer라는 것을 통해 확인해볼 수 있다.

여기에서는 2가지 버전이 존재하는데 SPM는 정확한 버전과 태그를 가지고 있다. deck.git같은 경우네느 from이라는 문법을 사용해서 최소 버전만을 정해주고 있다. 그래서 정확한 버전을 가지고 있지 않다.

deck같은 경우에 하단에 다른 것을 사용하므로 이에 대한 버전도 필요하다.

![](https://i.imgur.com/4hNDa4H.png)

SPM에서는 이를 정확한 버전으로 바꾸도록 처리해준다. 

![](https://i.imgur.com/RzPXJnT.png)

이렇게 SPM는 정확한 버전과 함께 태그로 만들어서 실행하도록 변경해준다.

그래서 이것의 장점

![](https://i.imgur.com/GJ0LpNe.png)

building

SPM는 llbuild라는 빌드를 사용하고 있다.

![](https://i.imgur.com/qBQunL4.jpg)

빌드 시스템을 빌드하는 엔진이다.

재사용가능하고 일반적인 빌드이며 속도도 빠르다.

![](https://i.imgur.com/xp5OELI.jpg)

빌드 환경이 고립되어 있어서 안전하다.

전역으로 모든 곳에 접근할 수 있는 게 아니라 내가 사용하는 곳의 라이브러리만 접근하도록 만들어져있다.

sandbox가 되어있다는 것은 파일시슽메에서 빌드를 하는 동안 임의적인 위치에 아무것도 쓰여지지 않도록 한다. 맘대로 내부적으로 처리하지 않도록 한다는 의미같다!

SPM는 빌드에서 임의적으로 빌드, shell 스크립트 사용을 못하게 한다.

그래서 우리는 build graph, input-output를 정확하게 파악할 수 있다.

Workflow Features

Edit mode

![](https://i.imgur.com/cFz2MNt.jpg)

만약에 어떤 것을 수정하려고 branch를 파서 사용하면 이는 특정 버전이 필요하지 않고 아무 버전으로 할 수 있다. 하지만 이를 publish할 때에는 정확한 버전으로 바꾸어야 한다!
이는 여러개의 packages를 개발하는데 유용하다.

로컬 package는 깃이나 레퍼지스토리를 대신에서 내부의 직접적으로 파일 시스템을 통해 가져오도록 한다.

Tools Evaultion

각 Swift에는 새로운 버전의 manifest이 필요하고 이를 업데이트해야한다.

![](https://i.imgur.com/jSFL0Bs.jpg)

하지만 SPM를 사용하면 임의로 업데이트해줄 필요도 없고 기존의 패키지를 잃을 일도 없다.

새 버전이 나오면 이것을 독립적으로 새로 받아서 사용한다.

![](https://i.imgur.com/t9eZQXS.jpg)

각각 소스코드에서 어떤 버전으로 할지 정할 수 있다.

### Evolution ideas

![](https://i.imgur.com/4WphPly.png)

SPM는 개발 환경, 자동화 등에 활용될 수 있다.

SPM는 라이브러리 베이스 아키텍쳐다.

포럼에서 SPM에 대해 피드백 환영!!

![](https://i.imgur.com/ntJuagG.jpg)



