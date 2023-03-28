# Power down: Improve battery consumption

[LINK](https://developer.apple.com/videos/play/wwdc2022/10083/)

배터리 타임을 늘리는 방법은 크게 4가지가 있다

> Adpot Dark Mode
Audit frame rates
Limit background time
Defer work

## Adopt Dark Mode

![](https://i.imgur.com/QPnrY3L.jpg)

다크모드를 채택하자!
전력소비에 가장큰 영향을 끼치는것은 디스플레이
OLED화면에서는 픽셀단위로 전력을 소모하는데, 어두운 색상은 훨씬 더 전력을 적게 소모함

### EX) Food Truck App

![](https://i.imgur.com/DXGjmyS.png)

예제 앱과 같은경우 최대 70% 까지 디스플레이 배터리 소비를 줄일수 있음

## Audit frame rates

![](https://i.imgur.com/KcsCqGV.jpg)

주사율을 조정하자!

![](https://i.imgur.com/2P2Ubar.png)

많은 요소들이 모여서 Frame을 구성함
각 요소들은 독립적인 주사율을 가지고 있음
가장 높은 주사율이 화면 전체의 주사율이 됨

### EX) Food Truck App

![](https://i.imgur.com/Cew0hcI.png)

Food Track이라는 Text가 좌우로 움직이는데 60 프레임의 주사율을 가지고 있는 상황
때문에 화면 전체가 60 프레임의 주사율을 가진상태로 refresh된다
60 -> 30 frame으로 낮추면 배터리를 많이 아낄 수 있음

### Debug

![](https://i.imgur.com/YIVeUuV.png)

Instruments에 있는 CoreAnimation FPS을 사용하면 앱의 시간별 프레임률을 확인할 수 있다

### Code

![](https://i.imgur.com/US7XhbH.png)

CADisplayLink 객체를 사용하면, 개발자가 주사율을 직접 컨트롤할 수 있음..!

### 추가

![](https://i.imgur.com/KFuiElK.png)

주사율이 높으면, 배터리 소비가 증가함. 그러니까 불필요하게 높은 주사율을 적용하지 말자
더 자세히 알고 싶으면 Optimize for variable refresh rate displays Session 참고

## Limit background time

![](https://i.imgur.com/fn6tcqA.jpg)

사용자가 앱을 전환하면, 앱은 종료되는것이 아니라 백그라운드에서 계속 실행됨

![](https://i.imgur.com/YhwTFjo.png)

### Location

![](https://i.imgur.com/waUaCM6.png)

#### Tool

![](https://i.imgur.com/dj6TWfT.png)


Xcode Gauges
![](https://i.imgur.com/5FHwleF.png)

MetricKit
![](https://i.imgur.com/txsO2hM.png)

Control Center
![](https://i.imgur.com/HlWYaTw.png)

### Audio

특정 앱에서 음악을 실행중, 사용자가 일시정지 버튼을 눌렀을때
앱은 sound만 멈추는게 아닌, Audio Engine도 같이 멈춰야함
AVAudioEngine의 autoShutdownEnabled 프로퍼티를 통해 가능

해당 모드에선, audio engine이 idle 상태와 재생상태를 탐지하고 자동으로 audio hardware를 on/off 시킨다

## Defer work

![](https://i.imgur.com/yhIbYOA.jpg)

![](https://i.imgur.com/gzDeT4n.png)

하루종일 여러가지 수많은 작업들을 함

![](https://i.imgur.com/glQSQpF.png)

작업들을 분류하면, 당장 수행해야 하는 작업과 나중에 해도 상관없는 작업들이 있음


### API

![](https://i.imgur.com/RB7QByf.jpg)

- BGProcessingTask
- Discretionary URLSession
- Push Priority

### BGProcessingTask

![](https://i.imgur.com/Cfz4D6Y.png)

API의 requiresExternalPower, requiresNetworkConnectivity 프로퍼티를 활용해서 작업을 미룰 수 있다

### Discretionary URLSession

![](https://i.imgur.com/v1hns5q.png)

#### ex)

![](https://i.imgur.com/3GeP3Na.png)

Background URLSession을 만들고, isDiscretionary = true로 설정해서 쉽게 만들 수 있다

![](https://i.imgur.com/P5JPHPM.png)

추가적인 설정 (timeoutInterval, earliestBeginDate, expected workload size)

### Push Priority

![](https://i.imgur.com/1XevYde.png)

우선순위가 높은 Push는 시스템을 자주깨워 배터리 소모량을 늘리기 때문에, 높은 순위를 가진 push alarm만 서버가 디바이스에 즉시 전달한다

우선순위가 낮은 push는 서버가 적절한 시점까지 푸쉬 전달을 미룸
(다른 푸쉬가 올때라던가, 디바이스가 깨어있을때)
간단하게 apns-priority를 5로 설정하면 서버가 알아서 처리함
