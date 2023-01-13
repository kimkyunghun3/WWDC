# [2020] Why Is My App Getting Killed?
백그라운드에서 앱이 종료되는 6가지 주요 이유를 설명하고, MetricKit을 사용하여 앱이 종료되는 비율을 낮출 수 있는 방법을 소개한다.

앱이 foreground에 있을 때 크래시가 발생하는 것을 원하지 않듯, 앱이 백그라운드에 있을 때 종료되는 것도 사용자 경험에 치명적일 수 있다.

예를 들어서, to do list 앱에서 파인애플을 사야한다는 리마인더를 만들다가 잠시 앱에서 나갔다가 다시 들어왔을 때, 앱이 처음부터 다시 시작되는 경우를 말한다. 파인애플을 사야한다는 리마인더를 작성하고 있던 화면은 사라졌다.
그 이유는 앱이 백그라운드에서 종료되었기 때문이다.

![](https://velog.velcdn.com/images/marisol/post/d023db5d-aa90-49e4-932e-01437ac13771/image.png)

![](https://velog.velcdn.com/images/marisol/post/b90ddb3d-9284-4b31-be97-bcbfecf815ca/image.png)

![](https://velog.velcdn.com/images/marisol/post/dcaef92d-1844-4b0d-a84f-04481fc32f81/image.png)

![](https://velog.velcdn.com/images/marisol/post/9cf73068-d969-40ed-a40f-ce2f2c8b33f9/image.png)

iOS 14부터는 이러한 앱의 종료가 얼마나 자주 발생하는지 구체적인 이유와 함께 확인할 수 있는 새로운 API를 사용할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/de4977da-f2da-484f-a1cf-6bfd5e16db58/image.png)

```MXBackgroundExitData```는 앱의 각 종료 타입의 횟수(count)를 제공한다.
비정상적인 종료 뿐만 아니라, 유저가 app switcher에서 당신의 앱을 명시적으로 종료한 정상적 종료에 대한 횟수도 제공한다.

## Common Termination

### 1️⃣ Crash

![](https://velog.velcdn.com/images/marisol/post/b824940c-1c64-4c6d-b356-40545cfd063b/image.png)

크래시는 가장 간단한 타입의 앱 종료이다.

잘못된 동작의 이벤트들은 모두 crash log를 생성하며, Xcode Organizer에 자동으로 표시된다.
Xcode Organizer 외에도 MetricKit에서 디바이스의 crash 정보를 얻을 수 있는 기능이 추가되었다.

![](https://velog.velcdn.com/images/marisol/post/db44fa8d-3be1-428d-9475-44220703cf1a/image.png)

Xcode Organizer 이외에도 MetricKit을 통해 디바이스에서 직접 정보를 가져올 수도 있다.
MXCrashDiagnostic 객체에는 crash 및 기타 종료를 추적하는데 필요한 모든 정보가 포함되어 있다.

### 2️⃣ Watchdog

![](https://velog.velcdn.com/images/marisol/post/0903e6ba-b130-4f9e-8c43-653cffec3fe7/image.png)

Watchdog는 실행 / 백그라운드 진입 / foreground 진입 등과 같이 주요 앱 전환 시에 시간이 오래 지연되는 것을 말한다.
이러한 전환에는 약 20초의 시간 제한이 있는데, Watchdog는 디버거가 연결되어 있는 동안에는 발생하지 않는다.

Watchdog는 일반적으로 deadlock, 무한 루프, 또는 메인스레드에서 끝나지 않는 동기화 작업과 같은 심각한 문제를 나타낸다.

이런 경우 MXCrashDiagnostic을 통해 진단 보고서를 얻을 수 있다.

### 3️⃣ CPU resource limit

![](https://velog.velcdn.com/images/marisol/post/9a2cc631-2360-4339-82b8-6eb276a560b9/image.png)

또한 앱이 백그라운드 실행에 의존하는 경우, CPU 사용 제한이 매우 엄격하다. 백그라운드에서 지속적으로 높은 CPU 부하가 발생하면, 시스템이 energy exception report를 생성한다. 작업이 오래 지속되면, 시스템이 앱을 종료할 수 있다.

CPU resource exception 로그는 Xcode Organizer 뿐만 아니라 MXCPUExceptionDiagnostic을 통해서도 확인할 수 있다.
앱 종료 시점에 앱이 정확히 무엇을 하고 있었는지 식별하기 위한 call stack을 알 수 있다.

만약 코드에 버그가 있을 경우 간단하게 수정해서 해결할 수도 있지만, 
백그라운드에서 꼭 집중적인 작업을 해야 한다면 background processing task로 작업을 이동시키는 것도 고려해볼 수 있다.

background processing task를 통해 CPU 리소스 제한 없이 디바이스가 밤새 충전되는 동안 몇 분간의 런타임을 얻을 수 있다.

### 4️⃣ Memory footprint exceeded

![](https://velog.velcdn.com/images/marisol/post/04596e86-97bf-4089-bf18-63a183c1384c/image.png)

과도한 CPU 사용을 방지하는 것과 마찬가지로, 메모리 사용을 제어하는 것도 중요하다.

초록색 사각형을 앱 메모리라고 할 때, 앱의 메모리가 너무 커지면 시스템은 footprint를 초과할 때 바로 종료시켜버린다. footprint limit은 foreground와 background에서 동일하다.

![](https://velog.velcdn.com/images/marisol/post/51cf188d-62f5-4c56-b21d-86337f72d837/image.png)

Instruments와 Xcode의 Memory Debugger는 앱에서 과도한 메모리 사용의 원인을 추적하는데에 도움이 된다.
limit은 디바이스마다 다르고, 오래된 디바이스는 일반적으로 limit이 낮다.
만약 아이폰 6s 이전의 디바이스를 타겟으로 한다면, 항상 앱이 200MB 이하를 유지하도록 해야 안전하게 사용할 수 있다.

### 5️⃣ Memory pressure exit (jetsam)

![](https://velog.velcdn.com/images/marisol/post/e4cbb61b-b69f-40a2-b948-a076cdccbfee/image.png)

jetsam은 가장 일반적인 백그라운드 종료의 한 종류이다.
jetsam이 발생하는 경우 무엇인가를 잘못했다기 보다는, 시스템이 foreground 앱과 음악, 네비게이션 앱과 같은 다른 실행중인 애플리케이션을 위해 메모리를 확보하기 위함일 수 있다.

![](https://velog.velcdn.com/images/marisol/post/88679cca-a3f7-4323-834a-b43b49b93d3d/image.png)

foreground앱이 너무 커지면 우리의 초록색 앱이 종료될 수 있다. 
백그라운드로 이동할 때 메모리 공간을 최대한 작게 해서 종료되는 비율을 줄일 수 있다. 그래서 백그라운드로 이동할 때는 50MB 미만을 목표로 하며, 작으면 작을 수록 좋다. 캐시나 디스크에서 읽을 수 있는 리소스는 삭제하는 것을 고려해볼 수 있다. 

하지만 용량을 50MB 이하로 유지하더라도, jetsam의 위험을 완벽하게 제거할 수는 없고, 또 일어나는 시기를 예측할 수도 없다.
예를 들어 카메라를 실행하고 수십 장의 사진을 한꺼번에 찍는 경우처럼 갑자기 많은 양의 메모리가 필요한 경우, 몇 초만에 발생할 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/0d51991c-1fa8-4e8e-aa3f-417d3db41266/image.png)

그래서 background으로 갈 때 반드시 상태를 저장해두어야 한다. 그렇게 함으로써 다음에 앱이 실행되면 사용자가 중단했던 동일한 위치로 되돌아갈 수 있다. 사용자가 텍스트필드 편집을 하고 있었다면 돌아갔을 때 텍스트가 남아있고, 미디어 재생과 관련된 경우 재생 위치가 복원되어야 한다.

이 작업은 UIKit의 built-in State Restoration API를 통해 수행할 수 있다. 이 API를 통해 앱 전체의 상태 복원을 구현한다면, 앱이 백그라운드에서 종료되었다는 것을 사용자가 눈치채지 못할 수도 있다.

### 6️⃣ Background task timeout

![](https://velog.velcdn.com/images/marisol/post/0a575ccb-c3d9-448b-b5d8-66a12879752e/image.png)

jetsam 다음으로 일반적인 앱 종료 이유는 백그라운드 작업 시간 초과이다. foreground에서 background로 이동할 때, ```UIApplication.beginBackgroundTask```를 호출하여 중요한 작업을 완료할 수 있는 런타임을 추가로 얻을 수 있다.

작업을 마치면 ```endBackgroundTask```를 호출하는데, 이 메서드를 명시적으로 호출하지 않으면
시스템이 timeout된 앱을 종료시킨다.
그리고 그 종료는 앱을 suspend시킨지 30초만에 발생하는데, 앱이 만약 상태 복원을 구현하지 않았다면...🥲

그래서 각각의 작업을 30초 퓨즈가 있는 다이너마이트라고 생각하라고 한다.

앱이 백그라운드로 들어가면 퓨즈에 불이 붙는다. 30초 이내에 모든 작업을 종료하는 한, 앱은 시스템이 종료하는 대신 정상적으로 종료된다.

이런 종류의 시스템에 의한 종료가 발생할 때 crash log를 표시하지는 않지만, MXBackgroundExitData를 통해 빈도를 기준으로 statistics를 알려준다.

백그라운드 작업을 잘 핸들링하면 시스템에 의한 종료를 방지할 수 있는데, 먼저 UIKit API의 named variant로 전환해야 한다.

![](https://velog.velcdn.com/images/marisol/post/c8f13dff-3db7-4ea4-8d4a-ca92e30d39ea/image.png)

named variant는 앱에서 잠재적으로 많은 백그라운드 작업들 중, 종료되지 않은 작업을 분리할 수 있기 때문에 유용하다. 

![](https://velog.velcdn.com/images/marisol/post/d7d78d3b-57bc-40f4-a4fe-d21978bb56bc/image.png)

이러한 종류의 종료는 Debugger에서 재현되지 않는다.
그리고 iOS 13.4부터는 task가 너무 오래 보류되었을 때 인쇄되는 새로운 콘솔 메시지가 있다. 이러한 콘솔 메시지는 앱이 foreground에 있는 경우에도 발생한다.

따라서 앱을 디버깅하는 동안 이러한 콘솔 메시지가 표시되면, beginBackgroundTask 호출에 대해 확인하고, endBackgroundTask에 일치하는 호출이 있는지 확인해야 한다.

![](https://velog.velcdn.com/images/marisol/post/38dab38d-bb9e-4f93-b7b0-ffeb8a599ebe/image.png)

expiration handler도 도움이 될 수 있는데, expiration handler는 작업의 백그라운드 시간이 만료되기 직전에 호출되는 핸들러이다.
expiration handler를 사용하기 위해 ```beginBackgroundTask```를 호출하는 것이 좋다. 또한 expiration handler 내부에서 ```endBackgroundTask```를 호출하는 것이 안전하다.

하지만 expiration handler에서 짧은 시간에 expensive한 작업을 하지 않도록 주의해야 한다.
expiration handler를 termination에 대한 안전망으로 생각해야 한다고 한다. expiration handler를 설정하지 않으면, 시스템에서 경고를 보내는 대신 task가 완료되고 실패한 것으로 표시되기 때문이다.

실제로 작업이 끝나면 ```endBackgroundTask```를 호출해서 디바이스를 더 빨리 절전 모드로 전환하고 배터리 수명을 보존해야 한다.
앱에서 이러한 종료가 발생하고 있다는 것을 MXBackgroundExitData를 통해 확인했지만 Debugger에서 문제를 재현할 수 없는 경우, ```telemetry```를 추가해 어떤 expiration handler가 호출되는지 확인하는 것이 유용할 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/1181a74e-9419-4d66-873e-4f274613f00e/image.png)

먼저 log handle을 생성한다.
그런다음 event signpost를 통해 expiration handler에 진입했음을 알린다.
그런 다음 앱을 안전하게 suspend할 수 있도록 필요한 cleanup 작업을 수행한다.
그리고 마지막으로 handler에서 나갈 때 signpost를 drop한다.

![](https://velog.velcdn.com/images/marisol/post/749d644a-6485-4666-823f-71f0f098b3c3/image.png)

signpost가 얼마나 자주 방출되는지 확인하려면, MXMetricsPayload 내의 signpost 수를 확인하면 된다.
또한 앱이 expiration handler를 통과하지 못하고 있는지 여부를 나타낼 수 있는데, 이 예시에서는 불균형이 있다.

입력한 signpost 수(2)가 종료된 signpost 수(1)보다 큰데, 이것은 DatabaseExpirationHandler가 중단되었거나 crashing되었다는 것을 의미한다.

![](https://velog.velcdn.com/images/marisol/post/d76a9d49-cae0-4a46-b8d1-8269fe6cd566/image.png)

⚠️ 앱이 이미 백그라운드에 있는 동안 작업을 시작할 때는, 5초 미만이 남아 있으면 expiration handler가 호출되지 않으므로, 작업을 시작할 때 특히 주의해야 한다.

이를 방지하기 위해 작업을 완료하는데 걸리는 시간을 추정하고, background time이 얼마나 남았는지를 비교해보았을때
백그라운드 작업 시간이 충분하면 ```beginBackgroundTask```를 호출하는 것이 안전함을 보장할 수 있다.
만약 남은 백그라운드 시간이 충분하지 않으면, 디바이스가 충전 중일 때와 같이 나중에 발생할 백그라운드 처리 작업으로 queue에 추가할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/c5cda88b-db9d-4977-8bef-d20c06059cf4/image.png)

다음으로 주의해야할 점은 UIBackgroundTaskIdentifier를 저장하는 방법이다. 예를 들어 인스턴스 변수에 저장하는 경우, 문제가 발생하기 쉽다.

이 예에서는 사용자가 앱에서 beginDataExport 버튼을 누를 때 백그라운드 작업을 시작한다.

몇 초 후 데이터 export가 완료되고 completion handler가 실행된다.
여기서 beginDataExport 버튼을 여러 번 누르면, 여러 개의 미결 백그라운드 작업이 생성된다.

인스턴스 변수는 한 번에 하나의 작업만 포함할 수 있기 때문에, 가장 최근의 작업을 제외한 모든 identifier의 leak이 발생한다. 

![](https://velog.velcdn.com/images/marisol/post/ade5e2e3-8980-4bb0-b043-eb0423060c4b/image.png)

다행히 이런 종류의 버그는 쉽게 해결할 수 있는데, 인스턴스 변수 대신 로컬 변수를 사용해서 UIBackgroundTaskIdentifier를 유지할 수 있다.
Swift에서는 이 로컬 변수가 클로저에 의해 캡쳐되기 때문에, completion handler에서 액세스 할 수 있으며, expiration handler에서도 액세스 할 수 있다.

이처럼 beginDataExport를 호출할 때마다 별도의 기본 메모리 위치에서 task identifier를 추적하여 leak을 방지할 수 있다.

앱에서 ```beginBackgroundTask```와 ```endBackgroundTask```의 모든 사용을 주의 깊게 살펴보면, Background task timeout 종료를 방지할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/e12023f4-4edc-410f-aa97-5700e3c0745a/image.png)

crash, watchdogs, resource exception, background task timeout 등을 방지하면, 앱이 더 빠르게 실행될 수 있다.
위 3가지를 통해 원활한 멀티태스킹 경험을 보장할 수 있다.😇

---
참고자료
- https://developer.apple.com/videos/play/wwdc2020/10078/
