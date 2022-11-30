# WWDC 2015 Performance on iOS and watchOS

# Introduction
![](https://i.imgur.com/Bj3zoUB.png)
앱의 성능에 대한 범주는 너무 광범위하다.
그렇기 때문에 몇가지 범주로 나누어 해당 영역에서 앱의 성능을 개선하기 위한 구체적인 전략을 제공하려 한다.

## 1. Why should i think about performance?
**성능에 대해 생각해야 하는 이유는 무엇 일까?**

![](https://i.imgur.com/pxPBIbd.png)
성능을 다른말로 요약하자면 하나의 기능이다.

성능은 사용자에게 앱에서 뛰어난 경험을 제공하기 위한 핵심 요소이며, 앱의 코드를 작성하는동안 항상 염두에 두어야한다.

앱의 반응성이 좋아 유저의 입력에 기다리지 않고 즉시 반응 하고 이를통해 유저가 정보에 액세스 하거나 상호 작용을 한다면 유저와 긍정적 신뢰 관계를 구축 할수 있게 된다.
(음..대충 불쾌감 없이 앱을 이용할수 있게된다? 랙걸리면 불편하니까...)

iOS 9 iPad에서 멀티태스킹을 채택할경우 앱이 화면 전체를 실행하지 않으며 시스템 리소스도 완전히 실행하지 않는다.
그렇기 때문에 멀티태스킹 환경에서 good neighbor(다른 앱의 성능에 영향을 주지 않는 앱)가 될 수 있다. 

CPU, 메모리 같은 시스템 리소스를 효율적으로 사용하도록 설계된 앱은 사용할 때만 좋은것이 아니다. 이는 실제로 배터리 전원을 절약할수 있도록 한다.

iOS 9는 광범위한 하드웨어를 지원하며 성능은 앱을 사용하는 유저에게 휼룡한 기능을 제공하기 위한 전제 조건이다.

## 2. How should you think about performance?
**성능에 대해 어떻게 생각합니까?**

![](https://i.imgur.com/LOMfJwC.png)

앱을 구축할 때 가장 첫 번째 단계는 **기술을 선택하는 것** 이다.
사용자에게 앱에서 제공할 기술을 선택해야 하며 이는 매우 중요하다.
앱을 구축하는 과정이 끝나면 **성능 측정** 을 시작하고 앱과 유저의 상호작용을 통해 어떤 사용자 경험을 만들어내는지 알수있게 된다.
(성능이 좋은지 반응이 좋은지) 

(측정 결과는 현재 앱이 어떻게 작동하는지 알려준다.)

측정 결과를 가지고 있다면 앱스토어에 제출 하기전에 앱이 어떤 성능인지 어느정도의 성능을 원하는지 **목표를 설정** 할수 있게 된다.

### Use the Right Tool for the job
**기술을 선택해보자**

![](https://i.imgur.com/ieEbCta.png)

작업에 적합한 도구를 선택하는 것은 뛰어난 성능을 위해 앱을 사전에 설계하는 데 있어 초기에 매우 중요하다.

기술을 선택하는 첫 번째 단계는 기술을 아는 것 이다.

사용 가능한 기술을 알게 되면 앱 작동 방식과 수행해야 할 작업에 대한 지식을 가져와 앱에 가장 적합한 기술을 선택할 수 있다.

좋은 예시중 하나는 앱에서 3개의 String 값을 사용 하는 경우 Info.plist에 작성하거나 UserDefaults를 사용하는것이 좋다.

반면 앱에서 3,000개의 String값을 사용해야 한다면 CoreData를 고려할수 있다.

기술을 선택할때 AppleAPI와 Frameworks를 고려하는것이 좋다.
(Apple이 개발자들이 사용하기 좋은것을 만들기위해 많은 시간을보내고 제품을 만들기 때문이다. 우리꺼 기술 먼저 고려해줘잉~)

AppleAPI, Frameworks를 채택할경우 이점이 생기는데 유저가 앱을 설치한후 iOS업데이트를 설치할 수 있으며 이때 API,Frameworks 업데이트도 같이 포함된다는 것 이다.
(iOS업데이트하면 앱에 사용된 API, Frameworks도 같이 업데이트되니까 성능 up! 한다는 소리인것 같다.)

그렇기 때문에 iOS 업데이트를 설치하게 되면 앱의 성능도 향상된다.

### Measuring Performance

**몇가지 기술을 선택하고 앱을 빌드 해보자, 성능을 측정해보자**

![](https://i.imgur.com/JjNtBHz.png)

**성능 측정에 대해 생각할수 있는 몇가지 종류가 있다.**
#### 1. 애니메이션(Animation)
애니메이션은 앱이 생생하고 유동적으로 느껴지도록 하고 유저에게 애니메이션을통해 무슨일이 일어나고 있는지 알려 줄 수 있다.

애니메이션의 성능을 측정하는 가장 쉬운 방법은 CoreAnimation 을 사용하는 것이다.

#### 2. 응답성(Responsiveness)
Responsiveness(응답성) 은 유저의 입력에 얼마나 빠르게 반응 하는지에 관한 것이며, Responsiveness을 측정하는 가장 간단한 방법은 로우하게 보일수 있지만 효과적이며 이는 단순히 실제 코드를 계측 하는것이다.

여러 스레드, 많은 시스템 상호 작용을 측정하기위해 SystemTrace 라는 도구를 이용할수 있다.

### 3. 메모리(Memory)
메모리는 모바일 장치에서 가장 소중한 리소스이며 앱이 필요한 만큼 메모리를 사용하고 더이상 사용하지 않도록 하는것이 정말 중요하다.

앱의 메모리를 측정하는 효과적인 방법은 Xcode 디버거를 사용하는것 이다.
더 자세히 측정하기 위해서는 Allocations 라는 도구가 있다.
메모리 누출(Memory Leak)이 있다고 의심될경우 이를 추척하는데 도움이 되는 Leaks 라는 도구도 있다.

### 코드 계측 예제를 살펴보자.
![](https://i.imgur.com/X6yJiIQ.png)

유저가 버튼을 탭할 경우 이미지를 로드하고 imageView에 띄워줄것이다.
이때 시간이 얼마나 오래걸리는지 알고싶을수 있다.

![](https://i.imgur.com/eRYR9oY.png)
CFAbsoluteTimeGetCurrent API 를 사용해 측정할수있다.
(현재시간의 절대값을 Double값으로 반환하는 API)

![](https://i.imgur.com/YLexbiA.png)
측정된 두개의 시간을 - 연산만 하더라도 버튼이 상호작용하는 시간에 대해 알수 있지만 좀더 정밀한 측정을 하기위해 결과에 1000을 곱하고 밀리초(milliesSeconds) 단위로 측정하도록 할수 있다.

이처럼 앱이 실제로 어떻게 작동 하는지 이해할 수 있도록 앱의 릴리스 구성을 프로파일링하는 것은 중요하다.
그러나 성능 측정을 AppStore에 제출하지 않는것도 중요하다.

그렇기 떄문에 Xcod에서 복사본을 만들고 성능 측정을 할수 있도록 하는것을 제안한다.

![](https://i.imgur.com/66tot6j.png)
요렇게 말이다.

![](https://i.imgur.com/Voy59s9.png)
위와같이 측정할수 있는 종류에는 다음과 같은것들이 있다.

가장 일반적으로 IBAction 에서 이러한 작업을 수행하며, UIView의 터치 처리 코드에서도 수행할수 있다.
UIGestureRecognizer target에 대해도 수행할 수 있다.

또한 tap 이벤트를 통해 modal present를 사용하거나 화면전환 될 경우 
viewWillAppear와 viewDidAppear 사이의 시간에 대한것도 측정할수 있다.

### Setting Performance Goals

**앱스토어에 제출하기전 성능 목표를 어떻게 설정해야할까?**

![](https://i.imgur.com/obWDfVp.png)

애니메이션은 초당 60프레임으로 실행될 때 현실적이고 유동적이며 생생하게 느껴진다.

애니메이션 구현에 대한 설명은 아래의 영상을 시청하도록 하자.
`WWDC 2014 Advanced Graphics and Animation in iOS Apps` 

![](https://i.imgur.com/lo77bzS.png)

반응성(유저입력에 반응 하는것)에 관한것에 얘기해보자.
유저는 100밀리초 보다 오래걸릴 경우 반응이 늦다고 느끼기 시작한다.

따라서 목표 반응 속도는 100밀리초 이내여야 한다.

![](https://i.imgur.com/gXpzkdJ.png)

코드를 변경하여 성능을 개선해보자.

어떻게 성능을 개선할수 있을까?
1. 추측하지마라.
- 도구를 사용해 프로파일링하고 앱의 성능 문제를 일으키는 원인에 따라 조치를 취해야한다.

2. 가장 간단한 구현이 성능에 충분하지 않을때만 코드를 복잡하게 만들어라.
- 성능 문제를 사전에 방지하기 위해 도입한 메커니즘이 성능 문제를 일으키는 경우가 많다.

3. 한번에 하나씩만 변경해라.
- 앱 성능 향상에 접근하는 방법과 향상시킨 결과를 이해하는것은 매우 어려우니 한번에 하나씩 수정해야 한다.

4. 디버깅 할때와 동일하게 엄격하고 논리적으로 접근해라.

### 앱의 성능을 향상 시키기 위해 코드를 변경하는 방법
![](https://i.imgur.com/fOjzTb0.png)

1. 처음 해야 할 것은 문제를 재현(Reproduce)하는 것이다.
2. 코드의 어떤 부분이 성능문제에 기여하는지 이해하기위해 도구를 사용하고 프로파일링 한다.
3. 성능 문제를 유발하는 코드의 특정 부분에 코드를 실행하는 시간을 측정한다.
4. 문제가 되는 코드를 변경한다.

종종 한번의 코드 수정으로 목표에 도달할수 없으며 여러가지 변경 사항이 결합되어 목표에 도달하게되는 경우가 있을수 있다.

그렇기 때문에 이미지의 사이클대로 시행하다보면 코드를 수정후 더 나아졌지만 원하는 목표에 도달하지 않았다는것을 알수있게 된다.

#### Profiling VS Measuring

![](https://i.imgur.com/B3FUpeA.png)

프로파일링(Profiling)및 측정(Measuring)은 비슷해 보일 수 있지만 실제로는 **앱 성능을 향상 시키는 두개의 개별 단계**이다.

**프로파일링**은 Xcode 디버거 및 Time Profiler 와 같은 도구를 사용하여 **성능 문제에 기여하는 코드의 모든것을 가져오는 단계**이다.

**측정**이란 **코드의 특정 영역을 계측**하여 유저가 해당 코드가 실행되기를 **어느정도의 시간**을 기다리는지 이해하는것을 의미한다.

## Responsiveness(반응성)

![](https://i.imgur.com/w6lLR2x.png)

반응성은 유저의 입력에 반응하는것을 말한다.
앱의 메인 스레드가 유저의 모든 입력을 소비하는 곳 이기 때문에 메인스레드에 대해 이야기 해보자.

![](https://i.imgur.com/nKXXNo5.png)

메인스레드는 터치스크린의 모든것(tap, scroll 등등), 방향변경, 멀티태스킹, 크기조정 및 기타 시스템 상태 이벤트와 같이 기기의 다른 센서에서 방생하는 모든것을 다룬다.

메인스레드가 유저입력에 응답하는 작업에 전념하는 경우 훌륭하지만, 메인스레드가 하는 일에 대해 조금 덜 주의 하거나 메인 스레드가 모든 작업을 수행한다면 앱이 정지되거나 정지된 것 처럼 보일수 있다.

그렇다면 메인스레드에서 어떤 작업을 하지않도록 해야할까??
![](https://i.imgur.com/w6UJ3uo.png)

특정 사항에는 CPU-intensive 작업이 포함된다. 이는 네트워크에서 다운로드한 긴 문자열을 분석하고, 이미지에 필터를 적용하고, 외부 리소스에 의존하는 작업을 의미할 수 있다.
이에대한 설명은 `Profiling in Depth`를 시청하라.

![](https://i.imgur.com/qKXqUNi.png)

외부 리소스에 의존(네트워크 통신 등등)하는 작업으로 돌아가 보자.
이들의 또 다른 이름은 `blocking call`이다.
- 스레드가 진행되는 것을 방해하기 떄문이다.

Syscall을 만드는 모든 경로는 차단 호출로 간주된다.

일반적으로 현재 메모리에 없는 리소스가 포함되며, 디스크에서 로드하거나 네트워크를 통해 항목을 가져오는것과 같다.

메인 스레드는 한번에 하나의 클라이언트만 허용하기 때문에 다른 사람이 해당 리소스 사용을 마칠때까지 기다리게 된다.

그렇다면 blocking call을 어떻게 발견할까??

![](https://i.imgur.com/FhRimKl.png)

동기 라는 단어는 차단과 동의어 이다.
동기라는 단어가 발견된다면 blocking call 을 일으킬수 있는 단서라고 볼수 있다.

위의 코드 `NSURLConnection.sendSynchronousRequest` 에서 blocking call을 발견했다.
코드를 작성하며 종종 비동기 API를 보게되는데 이름에 동기적(Synchronous)이라고 구체적으로 호출하는 API의 경우 쉽게 전환이 가능하다.

![](https://i.imgur.com/Kb1raZ8.png)

요렇게 A가붙은 비동기(Asynchronous) API도 지원하기 때문에 변경해주면 된다.

그러나 코드가 실행되는 순서를 변경하고 있으며 이작업의 결과에 영향을 받는 다른 코드가 있을 수 있다.

따라서 약간의 구조적인 수정이 필요하다.

![](https://i.imgur.com/MUq3DAw.png)

동기에서 비동기로 쉽게 전환할 수 없거나 한번의 작업으로 메인 스레드에서 전체 코드를 이동 하려는 경우를 가정해 보자.

이러한 경우 `GCD(Grand Central Dispatch)`를 사용할수 있다.
GCD는 스레드 실행과 스레드의 상태를 관리하고 원하는 작업을 수행할수 있도록 도와주며 closures(혹은 blocks)으로 실행할수 있다.

클로저를 GCD에 제출하면 프로세스의 임의 스레드에서 실행된다.

임의의 스레드는 얼마나 많은 스레드가 있는지 생각할 필요가 없기 때문에 편리하지만 주의해야할 점이 있다.

코드가 실행되는 스레드를 제어하지 않기 때문에 클로저 또는 block의 작업은 모든 스레드에서 수행하기전에 안전 해야 한다.

![](https://i.imgur.com/FYyog31.png)

일부 객체는 메인 스레드에서만 액세스하도록 제한 된 것들이 있는데 

예를 들어 UIKit의 View, Controller 는 메인 스레드 에서만 생성, 수정 및 소멸되어야 한다.

Foundation및 CoreGraphics 개체와 같은 일부 개체는 모든 스레드에서 사용할수 있다.
- 그러나 내부적으로 자신을 보호하지 않기 때문에 다중 스레드에서 사용하려는 경우 보호 로직을 직접 구현해야 한다.
- Serial GCD queue를 통해 구현하는 것이 일반적

![](https://i.imgur.com/aQn2ken.png)

위의 코드를 살펴보자.
1. 파일에서 일부 데이터를 가져온다.
2. 이미지를 처리하고 필터링 한다.
3. 이미지뷰에 필터링된 이미지를 할당한다.

유저가 위의코드를 실행할경우(버튼) 다음과 같이 스레드가 동작한다.

![](https://i.imgur.com/n8Zu1VJ.png)

메인스레드 에서 차례대로 작업을 수행한다.

하지만 해당 작업을 수행하는 동안 유저가 스크롤 하거나 회전 하려고 하면 해당 입력을 처리할수 없게 되고 blocking call에 걸리는 시간을 알 수 없게 된다.

유저는 의도한 동작이 실행되기를 기다리게 되며 이는 유저의 사용자 경험을 낮추게된다.

이문제를 해결하기 위해 GCD의 `dispatch async` 라는 API를 사용할수 있다.

![](https://i.imgur.com/ZcIjf1U.png)

`dispatch async`는 두개의 인자를 요구한다.
그중 첫번째는 Queue(대기열)를 의미한다. 다음은 실행하려는 코드가 포함된 클로저이다. 

![스크린샷 2022-08-30 오후 9 47 24](https://user-images.githubusercontent.com/63997044/187440377-9180d55b-008f-4a52-a7ae-84c171f0dd76.png)

이렇게 하고 나면 메인 스레드에서 벗어나게 된다.


![스크린샷 2022-08-30 오후 9 48 09](https://user-images.githubusercontent.com/63997044/187440529-6703cbfb-3f98-406f-867e-b6ab8682f6ea.png)


위에서 언급했듯이 UIKit의 view와 controller는 메인 스레드에서만 사용해야 하므로 해당 코드를 밖으로 옮겨주자. 그럼 끝일까?

![스크린샷 2022-08-30 오후 9 44 53](https://user-images.githubusercontent.com/63997044/187439894-4bde0a2c-6b5c-4b2e-9e16-8515f7283a2c.png)

이 클로저는 dispatch async가 반환되었을 때까지 실행이 완료되었다는 것을 보장할 수 없다. 

![](https://i.imgur.com/8QXorzZ.png)


블록 내부에서 메인 큐로 imageview를 업데이트하는 코드를 이동시킨다. 

![스크린샷 2022-08-30 오후 9 44 08](https://user-images.githubusercontent.com/63997044/187439734-681e838b-884b-4f9d-bfc1-ba8ade64e77a.png)

이렇게 하면 아까 문제였던, 작업을 수행하는 동안 스크롤하거나 회전을 하려고 해도 이벤트를 처리할 수 있게 된다.


### Common Blocking Calls
![](https://i.imgur.com/oiBux2s.png)

- Networking: `NSURLConnection` 및 관련 코드들
    - 비동기 API를 사용하도록 변경하라.
- Foundation 생성자: `UIImage(contentsOfFile: filePath)`
    - 코드가 위협적으로 보이지 않지만 외부 리소스에 의존적임.
- Core data
    - 개발자의 예상보다 많은 입출력을 수행한다.
    - Core data 작업을 다른곳에서 concurrency하게 처리.
    - 이에대한 설명은 `What's New in Core Data`를 시청하라.

따라서 이러한 Blocking Call들을 방지하려면 비동기 API로 바꾸거나 GCD를 사용하라. 
- 관련 내용은 `Building Responsive and Efficient Apps with GCD`을 시청하라.

## Memory

### iOS Memory System
![](https://i.imgur.com/b7jKKWp.png)

앱에서 멀티태스킹을 채택한다면 시스템 리소스를 온전히 사용할 수 없게 된다. 따라서 메모리에 대한 수요가 높을 것이고 가능한 한 적게 사용해야 한다.

모든 iOS 기기는 일시 중단된 모든 앱을 RAM에 동시에 유지할만큼 충분한 물리적인 메모리를 갖고 있지 않다. 따라서 메모리 압박(메모리가 부족한 상황)이 오면 포그라운드 앱을 위한 항목 정리가 일어난다. OS X 또는 PC에서는 디스크에 기록할 수 있지만 모바일에서는 그냥 앱이 종료된다.

### Memory is time
![](https://i.imgur.com/Bpge3Gj.png)

- 메모리를 회수하려면 시간이 걸린다. 
    - 사용 가능한 모든 메모리를 사용하여 추가 메모리가 필요한 상황이라서 대기한다.
    - 종료할 대상을 찾는다.
    - 앱을 종료시키고 메모리를 회수한다.
- 갑자기 많은 양의 메모리를 요청하면 응답성에 영향을 줄 수 있다.
- 백그라운드에서 상태를 보존한다.
    - 백그라운드 상태인 앱이 메모리를 작게 차지하고 있으면 종료 대상이 될 확률이 낮아진다.
    - 다시 되돌아갔을 때, 새로 시작하는 대신(initiate) 다시 시작하여(resume) 훨씬 빠르게 사용 가능

### Rationalize Your App’s Memory Footprint: 앱의 메모리 공간 합리화
![](https://i.imgur.com/DUzv7OO.png)

사용하는 자원의 유형에 대해 생각하는 것.
blob과 같은 긴 문자열, 네트워크로 다운받거나 카메라에서 가져온 이미지, 코어 데이터 관리 객체 등
이러한 리소스를 사용자와의 상호 작용에 따라 그룹화하여 멘탈 모델을 설정 
-> Xcode debugger로 확인 가능

### Example

<img width="1070" alt="스크린샷 2022-08-30 오후 9 39 58" src="https://user-images.githubusercontent.com/63997044/187438841-07930daf-4cce-4fa3-843d-742165f8d3ca.png">

Apple Developer 웹 사이트에서 다운받은 Photos framework 예제를 통해 보여준다.

<img width="590" alt="스크린샷 2022-08-30 오후 9 39 39" src="https://user-images.githubusercontent.com/63997044/187438781-fc3ea294-8644-4510-9bfc-3d2aa54e5af0.png">

디버거에서 Xcode 창의 왼쪽 상단을 살펴보자. 빌드하자마자 10MB의 메모리를 사용하고 있다는 것을 확인할 수 있다.

<img width="589" alt="스크린샷 2022-08-30 오후 9 39 24" src="https://user-images.githubusercontent.com/63997044/187438727-523f9414-1b31-4c8a-b7f8-0685d0092920.png">

사진을 열었더니 메모리 사용량이 2.5MB 늘어났다. 같은 동작을 계속 반복했을 때, 메모리 사용량이 계속 증가한다면 메모리 관리에 문제가 있다는 의미이다.

<img width="592" alt="스크린샷 2022-08-30 오후 9 39 10" src="https://user-images.githubusercontent.com/63997044/187438668-331a0c83-95d4-4d53-b836-61d34a44c50f.png">

마지막으로, 홈 버튼을 눌러 앱을 일시정지하고 앱이 백그라운드로 전환될 때 어떤 일이 발생하는지 확인해본다. 앱이 시작한 직후보다 (메모리 사용량이) 조금 줄어들었다.


### Application LifeCycle

백그라운드 상태의 앱을 재개하면 시작할 때 수행했던 작업들을 반복하지 않고 재개했다는 느낌을 받을 수 있도록 백그라운드에서 상태를 유지한다.
이런 동작을 달성하기 위해 특별한 무언가를 수행할 필요가 없다. Apple 기술에는 이런 동작이 내장되어 있으며 앱 생명주기 이벤트에 대한 응답으로 이런 기본 리소스를 자동으로 관리한다.

<img width="1209" alt="스크린샷 2022-08-30 오후 9 38 54" src="https://user-images.githubusercontent.com/63997044/187438607-16cdbb32-d394-487d-99ea-365203da84db.png">

앱 생명주기 이벤트에 대한 응답으로 복구하고자 하는 객체가 있는 경우 NSCache를 사용한다.
혹은 NSCache로 대응이 안되는 경우에는 시스템 생명주기 알림에 대응하는 커스텀 코드 작성
    - 예시: `UIApplicationDidEnterBackgroundNotification` 혹은 `UIApplicationDidReceiveMemoryWarningNotification`

### Memory Strategies
<img width="1209" alt="스크린샷 2022-08-30 오후 9 38 40" src="https://user-images.githubusercontent.com/63997044/187438564-c8660b3d-a58a-47a7-986d-bf6fe5358720.png">

- 앱이 사용하는 자원의 유형과 그에 접근하는 패턴
- 시스템 메모리 상태에 응답하도록 하는 방법에 대한 자세한 정보를 제공.
- 자세한 내용은 `Optimizing Your App for Multitasking on iPad in iOS 9` 를 시청해라.

## Summary
<img width="1211" alt="스크린샷 2022-08-30 오후 9 38 15" src="https://user-images.githubusercontent.com/63997044/187438482-fa7f6b4b-53ba-4f38-bc3c-dbe950458efe.png">

- 성능도 기능이다.
- 효율적인 앱은 `feel great`, `build trust`, and `save power`을 제공한다.
- Apple의 기술들을 이해하고 앱에 가장 적합한 것을 선택할 수 있어야 한다.
- 메인 스레드가 항상 사용자의 input을 받을 수 있게 준비 상태를 유지해야 한다.
- 앱의 메모리 사용에 대한 이해가 필요하다(언제, 왜 사용하는지).
