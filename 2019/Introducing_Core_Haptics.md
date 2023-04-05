# Introducing Core Haptics

[영상 보러가기](https://developer.apple.com/videos/play/wwdc2019/520)

![](https://i.imgur.com/g6JsAxS.png)

> Core Haptics를 사용하여 햅틱 패턴을 디자인하는 방법을 알아보자.


## Where to use Core Haptics

Core Haptics는 이벤트 기반의 오디오 및 햅틱 렌더링 API이다.

![](https://i.imgur.com/5RzFcQY.jpg)

iPhone 8 부터 지금까지 나온 모든 기기에 Apple에서 자체적으로 설계한 햅틱 엔진이 탑재되어있다.
햅틱 엔진은 Haptic API와 하나의 파일을 통해 액세스할 수 있고 모든 제품에서 동일한 느낌을 제공한다.

![](https://i.imgur.com/qVyLfVj.png)

이미 UIKit이 UIFeedbackGenerator 클래스에서는 햅틱을 제공하고 있는데 Core Haptics는 UIFeedbackGenerator와 다른 용도로 추가되었다.

![](https://i.imgur.com/YlrLgCq.png)

일반적으로 UIKit의 **UIFeedbackgenerator**를 계속 사용하고 거기에 햅틱을 추가하기를 원한다. 
이 API는 이벤트에 대한 디자인 의도를 나타낸다.(선택이든 알림이든)

반면에 **Core Haptics**는 자신만의 햅틱을 개발하고 싶을 때 사용한다.
이 API를 통해 자신만의 패턴을 개발할 수 있고 더 섬세한 제어가 가능해진다.

![](https://i.imgur.com/vdhwskw.png)

또한 Core Haptics는 Audio API이기도 하다. 따라서 오디오를 햅틱과 동기화하여 재생할 수 있다.
실제로 위의 예시들은 햅틱 효과와 오디오를 동기화한 예시이다.

![](https://i.imgur.com/Z0i7Z0O.jpg)

게임이나 대화형 앱, AR 등의 앱에서 햅틱 효과를 주로 사용하는데
이를 통해 얻을 수 있는 것
- 생생함과 몰입감


## Expressing content

Core Haptics에서 제공되는 클래스는 두 종류이다.

![](https://i.imgur.com/jBl1cm5.png)

Classes for Content: 컨텐츠를 표시하는 것
- CHHapticPattern
- CHHapticEvent
- CHHapticParameter

Classes for Playback: 컨텐츠를 재생하는 것
- CHHapticEngine

![](https://i.imgur.com/1kWBewC.png)

가장 기본적인 요소는 **CHHapticEvent**
개별 이벤트는 서로 겹칠 수 있고 이들을 그룹화한 것이 **CHHapticPattern**

![](https://i.imgur.com/ExdndT0.png)

이벤트가 가질 수 있는 타입의 종류에는 3가지가 있다.
1. HapticTransient
2. HapticContinuous, AudioContinuous
3. AudioCustom

![](https://i.imgur.com/nhgwtkp.png)

다음으로 이벤트의 optional한 매개변수인 EventParameter에는 여러 종류가 있다.
첫 번째는 HapticIntensity로 오디오 볼륨을 의미하며 0.0에서 1.0까지의 값을 갖는다.

![](https://i.imgur.com/NhzI7nx.png)

다음 매개변수인 HapticSharpness는 햅틱 효과의 날카로움을 나타낸다.
0.0에 가까울수록 둥글고 유기적인 느낌을 주고 1.0에 가까울수록 선명하고 정확한 느낌을 준다.
마찬가지로 0.0에서 1.0까지의 값을 갖는다.


## Our first haptics

앱에서 햅틱 패턴을 실행할 때 다음 단계를 따라야 한다.

![](https://i.imgur.com/joybSlQ.png)

1. haptic content를 생성한다.
    - NSDictionary를 햅틱 패턴으로 로드
    - 패턴을 재생하기 직전에 생성할 수도 있음.

![](https://i.imgur.com/ZcCJdgA.png)

2. haptic engine 인스턴스를 생성한다.
    - 앱이 햅틱을 사용할 것임을 알게 되는 즉시 수행되어야 한다.

![](https://i.imgur.com/zqwFVo3.png)

3. haptic pattern을 사용할 haptic player를 생성한다.
    - 햅틱 플레이어는 패턴과 엔진을 연결한다.

![](https://i.imgur.com/Kpu13W5.png)

4. haptic engine을 시작한다.
    - 엔진이 시작되면 패턴 재생 요청에 대비하여 오디오 및 햅틱 하드웨어를 초기화하도록 시스템에 알린다.

![](https://i.imgur.com/8g5sFe6.png)

5. 앱이 패턴 재생이 필요한 순간에 haptic player를 시작한다.
    - immediate: 이 패턴이 최소한의 대기 시간으로 실행
    - scheduled: 정확한 timestamp를 함께 전달하여 다른 오디오 플레이어나 게임 이벤트 등 타 시스템과 동기화하는 용도로 사용

![](https://i.imgur.com/VpcVGsb.png)

6. (선택적) player가 종료되는 것을 기다린다.
    - 패턴 재생이 끝난 후 추가적인 작업이 수행되어야 한다면 햅틱 엔진의 콜백을 통해 player가 끝나는 시점을 알려줄 수 있다.

![](https://i.imgur.com/0iQgkpv.png)

7. (선택적) 더 이상 햅틱 이벤트를 사용하지 않는다면 엔진을 종료한다.


### 🪄 Demo

![](https://i.imgur.com/YxQMUww.jpg)

이 앱은 가속도계에 반응하여 화면에서 공이 움직인다.
공이 화면 가장자리에 부딪힐 때 햅틱 및 오디오 피드백이 발생한다.
공이 가장자리에 더 세게 맞을수록 햅틱이 더 강렬해지고 오디오가 더 커진다.
이 예제의 코드를 통해 Core Haptics API를 사용하는 방법에 대해 알아보자.

![](https://i.imgur.com/fL7E9Pe.png)

먼저 CoreHaptics 모듈을 import한다.
haptic engine은 앱의 생명주기 동안 존재해야하기 때문에 view controller의 멤버 변수로 선언된다.

![](https://i.imgur.com/9FXrmnD.png)

햅틱 엔진을 사용하기 전에 미리 설정하고 시작해야 하므로 초기화하는 메서드를 구현한 뒤 viewDidLoad()에서 호출한다.
먼저 인스턴스를 생성한다.

![](https://i.imgur.com/jmD7PqP.png)

이것은 선택사항이지만 가끔 오디오 세션 중단 혹은 앱의 일시 중단 때문에 다른 작업에 의해 엔진이 중단되는 경우가 존재한다. 이 때 호출되는 엔진의 stoppedHandler에 클로저를 할당할 수 있다.

![](https://i.imgur.com/bEuUk1x.png)

생성이 끝났으면 엔진을 시작시킨다.



playerForMagnitude 메서드는 볼이 벽에 충돌함에 대한 응답으로 실행할 패턴을 포함한 player를 만드는 메서드이다. 여기서는 하나의 햅틱 이벤트와 하나의 오디오 이벤트를 생성한다.

![](https://i.imgur.com/2E28akU.png)

햅틱 이벤트를 생성하는 코드.
이벤트 타입을 hapticTransient 타입으로 생성하여 임팩트 있는 효과를 만들고, 공의 속도를 기반으로 햅틱 이벤트의 선명도와 강도를 구성한다.

![](https://i.imgur.com/MRGmw5F.png)

다음은 오디오 이벤트를 생성하는 코드.
여기서 눈여겨볼 점은 sustained 매개변수는 이벤트 기간 동안 서서히 0으로 소멸되도록 하는 옵션이다.

이 두 이벤트를 포함하는 패턴을 생성한 뒤 플레이어를 생성하고 반환한다.

![](https://i.imgur.com/M7F0TCo.png)

collisionBehavior 메서드는 공이 벽에 충돌했을 때 호출되는 메서드로, playerForMagnitude 메서드를 통해 생성한 플레이어를 immediate하게 시작한다. 

이 플레이어의 인스턴스를 앱에서 유지하지 않는다. 즉 이 메서드가 끝날때까지 존재하고 메서드의 실행이 끝나면 인스턴스가 해제된다.
반면에 햅틱 엔진은 이 앱이 지속적인 대화형 앱이기 때문에 수명을 유지한다.

## Dynamic parameters

Demo에서 봤듯이, 이벤트나 패턴을 생성할 때 Dynamic parameters를 사용하고 있다.

![](https://i.imgur.com/2c3i30Y.png)

이를 통해 얻을 수 있는 이점은 한 번에 제어가 가능하다는 것.
예를 들어 일시적으로 모든 햅틱의 강도를 줄이고자 하면, 동적 매개변수를 통해 패턴에 포함되어 있는 모든 이벤트들을 제어할 수 있게 된다.

## Apple Haptic Audio Pattern

![](https://i.imgur.com/Zd4rGno.png)

Apple Haptic Audio Pattern(이하 AHAP)
- Core Haptics 패턴을 텍스트 기반으로 설명한 것
- JSON 파일 형식으로 구성
- Swift Codable 사용 가능
- 쉽게 공유하고 편집할 수 있음
- 외부 AHAP 파일에서 햅틱 패턴을 로드할 수 있기 때문에 앱 코드에서 분리시킬 수 있음

![](https://i.imgur.com/BDYvkq5.png)

가장 기본적인 형태. 버전으로 시작하며 패턴을 갖는다. 패턴은 딕셔너리 형식으로 추가한다.

![](https://i.imgur.com/V16cKm7.png)

패턴에 첫 번째 이벤트를 추가한다.
이벤트는 두 개의 필수 키인 Time과 EventType을 갖고 이 이벤트에만 영향을 미치는 선택적인 키인 EventParameters를 갖는다.

![](https://i.imgur.com/kK5N4wz.png)

같은 방식으로 두 번째 이벤트를 추가한 모습
EventType이 HapticContinuous 또는 AudioContinuous인 경우에는 지속시간이 필요하므로 EventDuration(단위: 초)가 필요하다.

![](https://i.imgur.com/V3B5FJF.png)

AHAP은 JSON 형식의 키-값 쌍으로 이루어져 있기 때문에 다음과 같은 구조를 한 눈에 파악할 수 있다.

## Summary

![](https://i.imgur.com/gbbpxF9.png)

좋은 햅틱 디자인을 설계하려면? 업데이트된 HIG 문서를 확인하라!

![](https://i.imgur.com/BHo7Aw7.png)

오늘 이야기한 것
- 햅틱의 효과: 몰입과 쉬운 상호작용 
- 햅틱 및 오디오 피드백
- AHAP 
- 새로운 고성능 API: Core Haptics
- 가이드라인과 예제
