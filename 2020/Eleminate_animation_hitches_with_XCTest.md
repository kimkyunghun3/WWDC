# Eleminate animation hitches with XCTest

![](https://i.imgur.com/Ye1YkEJ.png)

XCTest를 사용하여 hitch를 감지하고 regression을 포착하는 방법에 대해 알아보자!

![](https://i.imgur.com/1eFA8uN.png)

애니메이션은 User Experience의 중요한 부분이다.
특정 동작을 하는 상황에서 애니메이션이 흐트러지거나 부드럽지 않게 응답하는 경우, 사용자의 눈에 띄고 결국 부정적인 사용자 경험의 원인이 된다.


### Hitch

이러한 사용자가 인지할 수 있을 만큼의 부정적인 반응을 **hitch**라고 한다. hitch는 사용자의 주의를 분산시키고 품질에 악영향을 준다.
hitch는 프레임이 예상보다 화면에 늦게 나타난 시간을 의미한다.

![](https://i.imgur.com/Fsh8glw.png)


hitch가 발생하는 원인은 주어진 시간 안에 프레임이 제대로 그려지지 않았기 때문이다.
앱의 프레임 별로 진행 상황을 보자. 그 전까지는 점진적인 스크롤이 발생하고 프레임 3에서 프레임 4로 넘어갈 때 갑자기 크게 점프한다.

앱의 프레임에 대해서 알아보자.

![](https://i.imgur.com/q5Scf0h.png)

각 기기별로 프레임이 교체되는 성능이 다르다. iPhone 및 iPad는 60Hz의 주사율을 갖는데, 이는 즉 프레임 당 1/60인 16.67ms의 주기를 가진다는 뜻이다.
이 주기마다 화면이 디스플레이의 프레임을 교체할 것인지 결정하는 것이 VSYNC인데, 프레임이 이를 놓치게 되면 hitch가 발생한다.

위의 상황에서는 16.67ms만큼의 hitch가 발생하였다.

![](https://i.imgur.com/ZdJIEj6.png)

hitch를 측정하는 방법은 두 가지가 있다.
Hitch time: 프레임이 늦게 표시된 시간(ms)
Hitch ratio: Hitch time per 1 second(ms/s)

![](https://i.imgur.com/IrPSht9.png)

fps(Frame per secends)를 사용하지 않고 hitch time을 사용하는 이유
- 기기별 성능이 절대적인 척도가 될 수는 없음.
- 애니메이션을 사용하지 않는 화면의 경우 업데이트하지 않는 쉬는 시간이므로 굳이 프레임을 업데이트할 필요가 없음.
- 성능 및 배터리를 위해 기능 별로 원하는 fps가 다를 수 있기 때문에 기기의 fps를 기준으로 적용하는 것은 무리가 있음.
    - 예: 게임의 경우 30fps, 비디오 24fps, 시계 앱 10fps 등

![](https://i.imgur.com/dSQn4wd.png)

Hitch ratio의 기준
Hitch time의 궁극적인 목표는 당연히 0
- 0 ~ 5 : Good
- 5 ~ 10: Warning
- 10 ~ : Critical


### Performance XCTest

![](https://i.imgur.com/2S5rE8v.png)


iOS 14에서 도구 모음을 사용하면 개발, 프로덕션 단계 모두에서 hitch를 추적할 수 있다. XCTest, MetricKit, Xcode Organizer를 사용하지만 이번 세션에서는 XCTest만 사용하여 개발 단계에서 hitch를 추적하고 이를 수정하는 내용만 다룰 것이다.

![](https://i.imgur.com/tYLpPxB.png)


새로 도입된 XCTMetrics. 테스트에서 측정하려는 시스템적인 부분을 지정한다. 이 중에서 이번 세션은 애니메이션에 대한 성능 테스트를 수행하는 데 사용되는 `XCTOSSignpostMetric`에 중점을 둘 것이다.

![](https://i.imgur.com/M5lwvrF.png)


XCTOSSignpostMetric으로 측정할 수 있는 것은 다음과 같다.
- Duration: os_signpost 사이의 간격
- 장애 관련 메트릭(count, duration, ratio of hitches)
- Frame count, Frame rate

![](https://i.imgur.com/gYTm1vM.png)


이러한 metrics를 수집하려면 os_signpost 간격을 내보내야 한다.

![](https://i.imgur.com/IuXVCmh.png)

Non-animation의 경우(duration만 반환) .begin, .end를 사용


![](https://i.imgur.com/060WQJQ.png)

Animation Interval인 경우 .animationBegin을 사용한다.

![](https://i.imgur.com/lCxV88D.png)

혹은 미리 정의된 animation interval을 사용한다.


간단한 Test에 이를 적용시켜보자.

![](https://i.imgur.com/eDOL9R7.png)

Meal Plannner를 탭하고 foodCollection을 위로 스와이프하여 아래로 스크롤한다.
측정할 대상은 `scrollDecelerationMetric`이다.


measure 블록은 기본적으로 성능 측정을 위해 5번 실행된다. 따라서 이대로 테스트를 진행하게 되면 다른 콘텐츠를 스와이프하게 되므로 테스트가 의미 없어진다. 따라서 테스트를 다음과 같이 수정하여 실행 사이에 앱의 상태를 재설정해준다. 

![](https://i.imgur.com/GLQQUS1.png)


측정 상태를 수동으로 제어할 것임을 옵션으로 설정하고, measure 블록에서 stopMeasuring()을 호출한다. 그러고 나서 테스트 환경을 재설정한다.


#### ✨ Tips 테스트를 하기 전에, 불필요한 설정을 제거하고 빠르게 실행하여 성능 측정에 미치는 영향을 제거하는 것이 좋다.

![](https://i.imgur.com/fCerqf4.png)

1. Performance XCTest에 대해 별도의 test scheme을 사용

![](https://i.imgur.com/ZClM2vk.png)

2. Build configuration -> Release, Debugger 비활성화

![](https://i.imgur.com/kGnJouZ.png)

3. 자동 스크린샷 수집과 코드 커버리지 해제

![](https://i.imgur.com/te7QrUd.png)

4. 모든 진단 옵션 해제


이제 Performance Test를 실행하고 나서 테스트 결과를 보는 방법이다. 다섯 번의 반복에 대한 

![](https://i.imgur.com/K2sBrxe.png)

![](https://i.imgur.com/Nz7KBaU.png)

![](https://i.imgur.com/t4Wmwn9.png)

측정값을 수집한 것을 볼 수 있다. 5회 동안 Hitch ratio로 약 1.2ms/s를 얻었다. 이 값을 Baseline으로 설정하여 regression을 잡으면 된다.


![](https://i.imgur.com/dXGc7Yc.jpg)

여기서 새로운 기능을 추가해보자. 먼저 UIImageView에 Image를 설정하고, 이미지의 크기를 조정하는 코드를 추가했다. 이 상태로 성능 테스트를 다시 실행하면

![](https://i.imgur.com/O2inXgu.png)


hitch가 증가했다. 
scaleAspectFit() 호출에 문제가 있는 것 같다. 현재 UI 렌더링을 담당하는 메인 스레드에서 이미지를 다시 그리고 있는데, 새로운 픽셀을 생성하고 메모리를 할당하는 데 CPU를 사용하고 있다.

![](https://i.imgur.com/aiQWS0d.png)


 Core Animation의 setContentMode를 사용하여 이 이미지를 GPU로 다시 그리는 것으로 CPU의 사용을 줄일 수 있다. 이를 통해 이미 존재하는 이미지 픽셀을 사용할 수 있으므로 결국 메인 스레드에서 수행하는 작업의 양이 줄어들었다.

![](https://i.imgur.com/qg33lnN.png)

수정 후 테스트 결과: 정상

### Recap

Hitch: 프레임이 예상보다 늦게 화면에 나타나서 발생하는 버벅거림
Performance XCTest를 통하여 개발 단계에서 hitch를 포착할 수 있다.
- 이미 선언되었거나 커스텀된 `os_signpost`의 주기를 측정한다.
- measure마다 상태 초기화를 시켜 동일한 환경을 유지해야 한다.
