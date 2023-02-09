# Diagnose Power and Performance regressions in your app

![](https://velog.velcdn.com/images/marisol/post/3b9debd6-953a-4161-a4b6-83c7d3b3b175/image.png)

![](https://velog.velcdn.com/images/marisol/post/94d31e07-5778-475a-abe1-c64365a6ba37/image.png)

각 릴리즈를 이전 릴리즈보다 향상 시켜야 하는데, 이때 Xcode Organizer를 성능을 이해하기 위한 훌륭한 도구로 사용할 수 있다.

Xcode Organizer는 배터리, 앱 런치, 중단, 메모리, disk writes, 스크롤 히치, 종료를 포함한 7가지 측정 카테고리의 데이터를 수집한다.

## 1️⃣ Key performance metrics

![](https://velog.velcdn.com/images/marisol/post/a3d0dbf9-5735-448c-a8b4-8c0122cc9537/image.png)

Xcode Organizer에 대해 살펴보고 성능 퇴보 분석을 진단해보자.
왼쪽 navigation bar에서 7가지 측정 카테고리 중에서 선택할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/ee4d5b0a-da30-4203-a217-b83395a79d94/image.png)

배터리 및 종료와 같은 일부 카테고리에는 여러 섹션과 다양한 이벤트 유형을 자세히 설명하는 하위 카테고리가 있을 수 있다.

![](https://velog.velcdn.com/images/marisol/post/c05e14c1-fb8e-4ba2-99da-2a3e5d4bb81e/image.png)

![](https://velog.velcdn.com/images/marisol/post/486b07b9-b641-4769-a553-8cd9429ef9c3/image.png)

Organizer 중앙에는 최근 16개 릴리즈에 대한 데이터가 포함된 차트가 표시된다.

![](https://velog.velcdn.com/images/marisol/post/0598fe68-fefc-45c7-8244-43d7eb1700d9/image.png)

화면 차트는 최신 버전에서 앱 종료가 크게 증가했음을 보여주며, 특히 파란색으로 강조된 illegal instruction exits으로 인해 릴리즈에 문제가 있을 수 있음을 나타낸다.

성능을 이해할 수 있는 여러 측정도구들이 있지만, device option, metric category, subcategory 등 고려할 수 있는 요소가 너무 많기 때문에 성능 최적화를 어디서부터 시작해야할지 확실치 않다.

이러한 이유로 Organizer에는 모든 성능 우선 순위를 강조하고, 워크플로우를 간소화할 수 있는 ```insight```섹션이 있다. 

## 2️⃣ Discovering regressions

먼저 성능 퇴보가 무엇인지, 그리고 어떻게 감지되는지에 대해 알아보려고 한다.

성능 퇴보는 앱이 최근 릴리즈에 비해 전력 또는 성능 영역이 저하될 때 발생한다.

![](https://velog.velcdn.com/images/marisol/post/78649b12-33d0-449c-9a67-ab61588f28aa/image.png)

예를 들어, 릴리즈 후 시작하는 데에 시간이 더 오래 걸리는 경우인데, 이 예시에서는 최근 릴리즈에 대한 측정을 분석하여 값이 점차 증가하는지 확인한다.

그런 다음 메트릭이 최근 몇 릴리즈에 걸쳐 평균화되고, 최신 값과 비교된다. 메트릭이 증가 추세이고, 최신 값이 최근 몇 릴리즈의 평균보다 높은 경우에는 퇴보로 flag가 지정되고, insight 섹션에 요약된다.

이 예에서는 앱을 실행하는데 평균 1.1초가 걸렸고, 마지막 릴리즈에서 갑자기 2.5초로 증가하여 regression flag가 지정되었다.

![](https://velog.velcdn.com/images/marisol/post/0f0745e5-2aff-47d7-b744-cf4eff448ee4/image.png)

Xcode Organizer에 ```regression``` 탭이 있는데, 성능 우선 순위 (top performance priorities)를 확인할 수 있는 one-stop shop이라고 표현하고 있다.

앱이 최근 릴리즈 후에 갑자기 크래시되는가?
새로운 카메라 기능이 디바이스 배터리를 방전시키는가?

이러한 질문에 대한 답과 자세한 내용을 ```insights``` 섹션에서 확인할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/f485a91f-c039-4f35-b6ea-e98a9af23c66/image.png)

왼쪽에는 더 나은 앱을 만들기 위해 무엇에 집중해야하는지 정확히 강조하여 어떤 측정 카테고리가 얼마나 퇴보되었는지, 백분위가 요약된다.

여기서 내 앱에서는 종료, 메모리, disk writes에 걸쳐 여러 regression이 있다. 다음으로, 각각의 예와 어떻게 해석해야하는지를 알아보려고 한다.

각 regression 분석에서는, 최신 앱 릴리즈의 모든 주요 문제를 요약한다. 이 섹션의 맨 위에는 잘못된 함수 포인터에 액세스할 때 발생할 수 있는 충돌을 유발하는 illegal instruction이 표시되어 있다.

더 아래에는 내 앱의 마지막 5개 릴리즈에 대한 illegal instruction exit 차트가 있다.

차트의 맨 위에는 영향을 받은 백분위수와 디바이스가 표시된다. 이 경우 모든 iPhone의 상위 백분위수 사용자가 최신 릴리즈에서 더 많은 충돌을 경험하고 있다.

illegal instruction exit의 평균과 최신 수는 차트 오른 쪽에 있다. 이 경우 내 앱은 최신 버전에서 10일마다 크래시가 발생하기 시작했다.

사용자가 홈 화면으로 돌아가서 앱을 자주 다시 시작해야하기 때문에 Onscreen termination은 매우 좋지 않은 경험이다. 이러한 충돌을 유발하는 잘못된 함수 포인터에 대한 참조를 제거하고, crash diagnostics를 살펴봄으로써 더 자세히 알아볼 수 있다.

regression과 crash diagnostics를 통해 다음 앱 릴리즈에서 onscreen termination을 줄여야 한다는 것을 알게 되었다.

Onscreen termination뿐만 아니라, regression을 통해 내 앱에서 개선해야할 다른 영역을 찾으려고 한다.

![](https://velog.velcdn.com/images/marisol/post/0e0da4b9-e9e0-4e1b-9b21-3ec82ea206c2/image.png)

이 경우 task timeout은 일반 백분위와 상위 백분위 모두에서 모든 아이폰에 걸쳐 증가했다. 오른쪽의 각 regression 을 스크롤하여 메트릭의 최신 및 평균 값 뿐만 아니라 최근 릴리즈의 데이터를 볼 수 있다.

![](https://velog.velcdn.com/images/marisol/post/d0e61cac-9543-497a-a004-469cf4c8df30/image.png)

앱이 백그라운드로 전환되면, 시스템이 작업을 종료하기 전에 30초 동안 작업을 실행할 수 있다. 즉, 백그라운드 작업을 적절하게 종료하지 않으면 앱이 더 자주 종료되어 다음 번에 사용자가 다시 돌아왔을 때 시작 속도가 느려질 수 있다.

이 앱은 백그라운드에서 3일마다 종료되는데, Onscreen termination 만큼 사용성에 치명적이지는 않지만, 더 자주 발생한다.

![](https://velog.velcdn.com/images/marisol/post/ba57842d-455f-43e3-a93a-576bed695ac7/image.png)

마지막으로, 앱의 regression으로 돌아가서, 집중해야할 성능 영역이 하나 더 있다.
disk write가 지난 릴리즈에서 28% 증가했다.

## 3️⃣ Disk write insights

![](https://velog.velcdn.com/images/marisol/post/65c668d4-80cd-42c6-a417-fdb8f9982f7c/image.png)

Disk write이 왜 중요할까? CPU 및 메모리와 유사하게, Storage는 한정된 자원이다.

disk write을 잘 관리하지 않으면 짜증나는 onscreen hang과 UI hitches의 흔한 원인이 되고, 배터리 수명을 빠르게 소모할 수도 있다.

그렇게 때문에 disk write 최적화는 뛰어난 사용자 환경을 보장하는데 매우 중요한 요소가 된다. 

![](https://velog.velcdn.com/images/marisol/post/10945b92-dcfe-4f1a-8f53-322ccfdf3dfe/image.png)

Xcode Organizer의 disk write 리포트는 앱 분석 공유에 동의한 디바이스에서 수집되며, 여기에는 write로 이어진 stack trace에 대한 정보가 포함된다.

각 리포트의 stack trace는 여러 개의 서명으로 분류되며, 모든 리포트에서 하나의 서명에 대해 write가 추적된다. 이 리포트 목록에는 총 write 수를 기준으로 정렬된 서명이 표시된다.

각 서명에 대해 write의 원인을 보여주는 상세 stack trace를 찾을 수 있으며, 수신된 로그 수, OS 버전 및 하드웨어 모델 분석 등에 대한 통계도 찾을 수 있다.

문제 영역을 찾기 위해 top signature를 살펴보면, 이 경우에는 top signature가 write의 67%를 차지하고 있다.

우리는 callStack에서 이것이 SQLite 함수 때문이라고 추론할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/1854fc10-9ee0-4bf1-a345-546a4b006bf7/image.png)

문제를 찾기 위한 노력을 줄이기 위해, Xcode 13에서 Organizer는 ```insight``` 라는 새로운 필드를 지원한다. 문제를 highlight하고, 문제를 해결하기 위한 최적화 제안도 표시된다. 이렇게 하면 앱의 write를 줄이고, 성능을 향상시키는 동시에 엄청난 디버깅 작업을 줄일 수 있다.

지금 insight를 보면 index를 추가하는 최적화 제안이 있다. 또한 documentation 페이지에 대한 링크가 있어 문제를 자세히 설명하고, 문제를 디버깅하고, 해결하는 단계를 제공한다.

![](https://velog.velcdn.com/images/marisol/post/5a7bb586-1373-4017-b209-c5f126239857/image.png)

이제 그 충고를 따르는 것이 도움이 되는지 확인해보려고 한다. file activicies instruments를 작동시킨다. File activities instruments는 모든 스토리지 관련 문제를 디버깅할 수 있다. 또 앱이 수행하는 모든 읽기 및 쓰기에 대한 세부 정보를 제공한다.

이 전에는 디바이스가 파일에 약 180mb의 쓰기를 보였다. 이로 인해 780 밀리세컨드의 지연시간이 발생했다.

![](https://velog.velcdn.com/images/marisol/post/2ba15ed4-bb73-4034-a115-5334985ae4c1/image.png)

그리고 이제 index 이후, 함수로 인한 쓰기가 0으로 줄어들었다. 이제 SQLite가 더 효율적이기 때문이다. 즉, 더 이상 780 밀리세컨드의 대기 시간으로 인해 작업 속도가 느려지지 않는다.

![](https://velog.velcdn.com/images/marisol/post/f7e27880-bbcd-4750-8bd5-41fcfecabfc5/image.png)

다시 insights 창으로 돌아와보면, 우리 앱에 3번의 regression이 있었다. 각각을 면밀히 조사했고, 각각 모두를 해결할 수 있는 리소스를 발견했다.

Xcode Organizer에는 수많은 전력 및 성능 분석 도구가 있으며, ```insights```는 앱의 성능 우선 순위를 확인하는데 유용한 창이 되므로, 최대한 활용해라!

Xcode Organizer 외에도 현재 다루는 모든 분석에 액세스할 수 있는 또 다른 방법이 있는데, 바로 App Store Connect API이다.

## 4️⃣ App Store Connect APIs

App Store Connect API는 앱스토어 커넥트에서 수행하는 작업을 자동화할 수 있는 REST API를 말한다.
App Store Connect API를 사용하여 변경한 내용은 개발 및 배포에 사용하는 프로덕션 데이터에 영향을 미친다.
App Store Connect API는 앱스토어 커넥트의 다음 영역을 자동화하기 위한 리소스를 제공한다.
- In-App Purchases, and Subscriptions (인앱 구매, 구독)
- TestFlight
- Xcode Cloud
- Users and Roles (팀에 초대 전송)
- Provisioning
- App Metadata
- App Clip Experience
- Reporting
- Power and Performance Metrics (전력/성능 측정)
- Customer Reviews and Review Responses

![](https://velog.velcdn.com/images/marisol/post/4c0453d9-ec7d-42b0-b7a2-6b59081650f8/image.png)

데이터에 프로그래밍 방식으로 액세스하고, 커스텀 솔루션을 구축하려는 경우에 유용하다.

![](https://velog.velcdn.com/images/marisol/post/5e41b258-3c9d-423e-a71d-bc6d54d63b94/image.png)

몇가지 API 응답에 대해 간략하게 설명하려고 한다. metric의 경우, ```perfPowerMetric``` endpoint에 GET 요청을 할 수 있다.
이것은 최신 버전에 대한 앱의 metric과 insights가 있는 JSON 응답을 리턴한다.

```perfPowerMetric```: 앱의 전력/성능 측정과 진단을 얻을 수 있는 읽기 전용 리소스. 유저가 디바이스에서 이 정보를 공유하도록 선택할 수 있다.

insights는 측정 데이터 집합을 기반으로 추세를 보여주는 자동으로 생성된 분석이다. insights는 가장 최근 앱 버전의 성능을 이전 버전과 비교한다. insights 섹션에서 앱에 대한 식별된 모든 퇴보을 발견할 수 있다. 각각의 퇴보 분석에 대해 측정 카테고리와 식별된 퇴보 분석의 요약을 확인할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/b597e747-e3f4-4ce1-85da-1c10d5fe4b2f/image.png)

Xcode Organizer에서 이전에 발견한 illegal instruction으로 인한 onscreen termination이다.

그리고 다음으로 population 섹션을 찾을 수 있다. 이를 통해 영향을 받는 백분위수 및 디바이스의 상세하고 체계적인 리스트를 확인할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/989cbcda-1984-4cad-815d-ec5e96fa402b/image.png)

마찬가지로, 진단 보고서의 경우 DiagnosticSignature endpoint에 GET 요청을 작성할 수 있다. 

응답에는 top signatures 목록이 있고, 각 signature에는 해당 signature와 관련된 세부 정보에 대한 링크가 있다.

![](https://velog.velcdn.com/images/marisol/post/0b978ec5-776f-4729-b0b3-cf372a539ec7/image.png)

이 링크에 대한 GET 요청은 자세한 진단 로그와 signature와 관련된 insights를 제공한다.

분석 파이프라인에서 이러한 API를 사용하고 insights를 발견하면, 즉시 조치를 취해야 한다.

![](https://velog.velcdn.com/images/marisol/post/b7802d40-c336-497f-81d5-76c8f2c66532/image.png)

---
참고자료
- https://developer.apple.com/videos/play/wwdc2021/10087/?time=841
- https://developer.apple.com/documentation/appstoreconnectapi/power_and_performance_metrics_and_logs
