# Optimizing App Launch

토픽

![](https://i.imgur.com/NREoa0c.jpg)

앱 실행은 UX와 관련있다.

![](https://i.imgur.com/78UPgIA.jpg)

빌드하는데 걸리는 시간 약 2.4초

![](https://i.imgur.com/MMIZjUs.png)

만약 우리가 매번 실행할 때 마다 1 밀리초만큼 앞당길 수 있다면..

![](https://i.imgur.com/X08pq1e.jpg)

162일을 아낄 수 있다.

launch가 왜 중요할까?

![](https://i.imgur.com/pvcFi6E.jpg)


- 유저의 첫번째 앱 경험에서 좋은 영향을 끼칠 수 있다
- 코드의 전반적인 성능을 높일 수 있다.
- CPU, Memory 사용량에 영향을 끼칠 수 있다.

Launch Types

![](https://i.imgur.com/NzYx5wT.jpg)

Cold는 첫 시작에 하는 것이므로 메모리에 없으므로 가장 오래 걸린다.
Warm는 런치한 이후에 다시 relaunch할 때 한다. 더 빠르고 지속적이다
Resume는 다시 재시작하는 것인데 이미 런치했었기 때문에 속도가 더 빠르다.

App Launch 단계

유저가 홈스크린에서 앱을 누른다 -> 100ms 걸린다.
그리고 개발자가 내부에서 view를 만들고 데이터 내용을 가져오는데 300ms 걸린다.
마지막으로는 비동기로 가져온 데이터를 가져와서 뿌려주는 역할로 200ms 걸린다.

![](https://i.imgur.com/GWslBDj.jpg)

이것을 개발적으로 표현할 수 있다.

![](https://i.imgur.com/UHAAB0F.png)

**System Interface**
DYLD3

Dynamic linker로 프레임워크나 라이브러리를 load할 때 사용한다.
3는 새로나오며 성능적 개선이 있다.

![](https://i.imgur.com/GuoeyuL.png)

캐싱을 활용해서 warm launch가 가능해진다.

기능

![](https://i.imgur.com/Y07b0QQ.png)

libSystem init

![](https://i.imgur.com/eEPzkAS.png)

고정 비용과 함께 사용하므로 개발자는 section에 집중할 필요가 없다.

**Static Runtime Initilization**

![](https://i.imgur.com/KmZFEEe.png)

**UIKit init**

UIApplication, UIApplicationDelegate를 초기화 해준다.
시스템에서 이벤트가 실행되고 시작되는 것을 시작하게 만들어준다.

![](https://i.imgur.com/2pFOWos.png)

**Application init**

개발자가 app launch에서 가장 영향을 끼칠 수 있는 부분이다.

iOS 12 이전에는 app lifecycle 콜백 함수를 이것들을 사용했다.

![](https://i.imgur.com/xComs6m.png)

![](https://i.imgur.com/cqVtRDs.png)

SceneDelegate 메서드

![](https://i.imgur.com/k7rEKp4.png)

관련없는 work를 연기시킨다. background로 넘기거나 아니면 나중에 전체적으로 실행할 때 한번에 하는식으로 한다.

화면들의 resource를 공유한다.

**First Frame Render**

화면 로드하는 부분

![](https://i.imgur.com/GMMV2I7.png)

할 수 있는 기능들

![](https://i.imgur.com/VyNWMQq.png)
  
**Extended **

![](https://i.imgur.com/MGCrhYw.png)

지금까지 런치가 무엇인지에 대해 설명했다.

이제 어떻게 적절하게 launch를 측정하는지에 설명할 것이다.

불예측성을 최소한으로 하는 것이 중요하다.

![](https://i.imgur.com/9qBCyYc.jpg)

Clean, consistent한 환경을 만들기 위한 팁이 있다.

![](https://i.imgur.com/7O97hTO.jpg)

다음으로는 예전, 최신 기기를 타겟할 때

![](https://i.imgur.com/WRdxfEx.jpg)

각 RAM, CPU core 다 다르다!

Launch를 XCTest로 측정하는 법

![](https://i.imgur.com/OBERo6Q.jpg)

다음으로는 런치를 측정하기 위해 사용하는 instruments에 대한 이야기다.

앱 런치와 관련해서 3가지 Tips and Tricks가 있다.

![](https://i.imgur.com/2wTTOiW.jpg)

1. Work 최소화

![](https://i.imgur.com/OdLni6M.png)

2. work 우선화

![](https://i.imgur.com/UQ3pAiP.jpg)

3. work 최적화

![](https://i.imgur.com/dPxGVnM.jpg)

첫 번째는 런치때 실행할 것을 결정하고 lazy로 할 것을 정해라

### Demo

![](https://i.imgur.com/Tvd3kpa.jpg)

![](https://i.imgur.com/OCEsYll.jpg)

문제가 하나 있다.

런치하는데 약 2.5초가 걸린다.

Xcode에서 먼저 이걸 해라!

![](https://i.imgur.com/1liENid.png)

재컴파일 할떄 컴파일 타임을 최적화해주기 떄문에 Profile로 한다.

template로 App Launch 가 있다!

![](https://i.imgur.com/B5Q9sIA.jpg)

실행하면 이렇게 App 런치를 보여준다

![](https://i.imgur.com/WMup8qn.png)

그리고 App에서 사용하는 Thread의 사용시간을 보여준다.

![](https://i.imgur.com/9jvVBOH.jpg)

가장 중요한 것은 Main Thread다!!

gray는 쓰레드가 block된 상태로 사용하지 않고 있다는 것이다.
red는 실행할 수 있지만 스케줄링에 따라 CPU 자원의 한계로 사용하지 못하고 있다.
orange는 다른 더 높은 우선순위를 가진 work 때문에 밀려서 대기상태에 있다.
blue는 실행중이다! CPU core를 사용하고 있다.

![](https://i.imgur.com/XMzRANP.png)

여기에서 보면 149ms나 사용하고 있어서 오버헤드를 발생하고 있다는 것을 파악할 수 있다.

이 부분이 370ms를 사용하고 있다.

![](https://i.imgur.com/KoQjrZu.png)

그것을 stack tree에서 어느 곳인지 파악한다.

![](https://i.imgur.com/rwU5Ucv.jpg)

실제 코드는 여기다

![](https://i.imgur.com/EoCjPBk.jpg)

여기서 단순히 log만 알려주는데 왜이렇게 많은 시간을 사용할까?

그래서 찾아보자!

![](https://i.imgur.com/KP3Dk5Q.jpg)

이곳에 하는데 이건 static method이고 실제로 메인 쓰레드가 만들어 지기전에 사용된다.

외부 라이브러리나 프레임워크는 강하지만 그만큼 많은 비용이 요구된다.

하지만 300ms는 너무 과하다

그래서 iOS 내부의 로그로 바꾸며 dependency를 제거한다

![](https://i.imgur.com/JEkKSMt.jpg)

그리고 여기에서 프레임워크를 지운다!

![](https://i.imgur.com/SDDoaGg.png)

UIKit init 같은 경우에는 모든 앱에서 고정적으로 시간을 사용해야한다.

다음으로는 application init이다.

여기에서 main thread가 754ms나 block이 되어있는데 이것은 좋은 것이 아니다.

![](https://i.imgur.com/u3xlaC1.png)

이는 런치를 delay시킨다!

이곳에서 event list를 볼 수 있는 필터다

![](https://i.imgur.com/gotpplx.png)

내역을 보면 754 block 후 실행되고 있다

![](https://i.imgur.com/sJat9XC.png)

실행되는 것을 보면 0x12253에서 되고 있다.

![](https://i.imgur.com/7XxhcDG.png)

이곳에서 많은 일을 하고 있다는 말이다

![](https://i.imgur.com/159DkGW.png)

메인쓰레드에서 보면 priority가 있다.

![](https://i.imgur.com/OXRBNW5.png)

47는 User Intetactive QoS와 관련있다!

여기 보면 0x12253는 오렌지가 많은데 그 이유가 있다

![](https://i.imgur.com/2r2n4Ul.png)

priority가 4다

![](https://i.imgur.com/zYqO6fu.png)

그것은 background QoS이므로 우선순위에서 밀리므로 CPU 자원이 부족한 것이다.

이제 이것을 해결할 것이다!!!

데이터를 로드하는 부분이다. async, sync가 잇다

![](https://i.imgur.com/DHAWsEm.jpg)

AppDelegate 파일

![](https://i.imgur.com/oxLjIST.jpg)

비동기로 사용하고있고 세마포어를 사용하고 있어서 모든 것을 다 가져올때까지 기다린다. 그래서 다 가져온 후 tableview의 first frame를 만들어 준다.

여기 보면 tableview 데이터를 가져올 때 문제점이 있다

![](https://i.imgur.com/Hx712XC.jpg)

보면 처음부터 끝까지 다 가져오고있는데 이렇게 하게되면 많은 데이터를 한번에 가져와야한다.

이것을 20개씩 가져오는 것으로 바꾼다.

![](https://i.imgur.com/1PntbOW.jpg)

그 이후 20개를 런치한 이후에 값을 가져오도록 바꾸는 것이다!!!

이제 여기에서 테이블뷰컨트롤러 본다

![](https://i.imgur.com/ufyGjVB.png)

디테일 스택 trace에서 보면 많은 시간을 사용한다(890ms)

![](https://i.imgur.com/0IPy8jr.png)

그리고 디테일뷰컨에서도 882ms를 사용해서 느리게 만드는 역할을 하고 있다.

![](https://i.imgur.com/kT5R1Xb.png)

이제 코드에서 보면 문제점을 파악할 수 있다.

<img width="1053" alt="스크린샷 2023-03-31 오후 1 04 05" src="https://user-images.githubusercontent.com/52434820/229021943-48fe3e9a-ee4b-4368-a432-21d760295aea.png">

cellForRowAt에서 이렇게 작성했는데 이건 tableview -> detailview로 이동하는데 간략화할 수 있다. 하지만 trace에서 이것이 좋은 방법이 아니라는 것을 알 수 있다.

detailView는 처음 시작할 때 필요없고 유저가 탭했을 때 필요하다.

그래서 위치를 셀을 눌렀을 때의 메서드로 옮긴다.

<img width="1182" alt="스크린샷 2023-03-31 오후 1 05 56" src="https://user-images.githubusercontent.com/52434820/229021960-9768da80-5fe2-41a0-a0be-b0e71b79731d.png">

런치 시간이 줄어들 것이다!!

XCTest로 런치 시간에 대한 성능 테스트한다

<img width="1055" alt="스크린샷 2023-03-31 오후 1 08 31" src="https://user-images.githubusercontent.com/52434820/229021974-324c33db-c190-4498-b148-841ef49c5c8f.png">

다 돌리면 결과와 평균값도 알려준다

<img width="816" alt="스크린샷 2023-03-31 오후 1 09 09" src="https://user-images.githubusercontent.com/52434820/229022032-23253b88-0e6c-460b-9181-4b228e6b72eb.png">

<img width="1277" alt="스크린샷 2023-03-31 오후 1 09 44" src="https://user-images.githubusercontent.com/52434820/229022038-dda18f27-5415-4245-a6e5-5af6ba0d2be6.png">

**시스템 개선 행한 부분**

<img width="1048" alt="스크린샷 2023-03-31 오후 1 11 33" src="https://user-images.githubusercontent.com/52434820/229022200-623fa44f-f25c-42ad-b427-61d714ed0f14.png">

**Track your launch over time**

Xcode Organizer로 이렇게도 볼 수 있다

<img width="1313" alt="스크린샷 2023-03-31 오후 1 13 08" src="https://user-images.githubusercontent.com/52434820/229022119-62c6f159-4235-41b6-9644-8c80557c5823.png">

### Summary

<img width="756" alt="스크린샷 2023-03-31 오후 1 14 52" src="https://user-images.githubusercontent.com/52434820/229022103-13906a34-6046-4075-9d57-941e2b586997.png">
