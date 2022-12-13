# Detect and diagnose memory issues

<img width="712" alt="스크린샷 2022-12-13 오후 7 27 12" src="https://user-images.githubusercontent.com/63997044/207297379-e9d093d5-ae7e-4c41-b6bd-4c2de2e1e330.png">

Xcode로 메모리 성능 문제를 감지하고 진단하는 방법을 알아보자.

## Impact of memory footprint

왜 우리가 애플리케이션의 메모리 사용량에 대해 알아야 할까? 핵심적인 이유는 사용자 경험과 관련있기 때문이다.

<img width="714" alt="스크린샷 2022-12-13 오후 7 27 43" src="https://user-images.githubusercontent.com/63997044/207297423-96732139-401b-4f67-8c9a-3bbe14b3b76f.png">

1. Faster application activations
ARC는 메모리 사용을 모니터링하고 다른 곳에 메모리 할당이 필요한 경우 앱을 종료하여 메모리를 확보한다.
앱이 백그라운드 상태일 때 상태를 보존하여 메모리 사용공간을 작게 유지하면 ARC에 의해 메모리가 회수될 확률이 낮아져 앱의 활성화 속도가 빨라진다.

2. Responsive experience
메모리 사용량을 줄이면 응답성이 향상된다. 또한 사용자가 앱과 상호작용하면서 추가적인 메모리가 필요한 경우 이를 위해 대기하는 비용을 회피할 수 있다.

3. Complex features
메모리를 효과적으로 관리한다면 애니메이션과 같은 더 많은 메모리를 사용하는 기능을 추가할 수 있다.

4. Wider device compatibility
메모리 사용 공간을 줄이면 앱이 구형 디바이스에서도 문제 없이 실행되어 더 많은 사용자에게 제공할 수 있게 된다.

앱이 메모리 공간을 어떻게 구성하는지 알아보자.

<img width="713" alt="스크린샷 2022-12-13 오후 7 28 16" src="https://user-images.githubusercontent.com/63997044/207297441-f55fe331-0080-45d8-9398-fde25e7ee211.png">

앱의 메모리를 분류하면 다음과 같이 나눌 수 있다.
- Dirty
    - 앱에서 사용하는 메모리
    - 이미지 버퍼, 프레임워크 등 할당된 모든 힙 영역의 메모리
- Compressed
    - 최근에 액세스되지 않은 압축된 Dirty 페이지를 의미함
    - 이 페이지에 액세스하면 압축이 해제된다.
- Clean
    - 아직 사용되지 않은 메모리 혹은 페이지 아웃될 수 있는 메모리

보통 **사용 중인 메모리**이라고 하면 **Dirty memory + Compressed memory**를 의미한다.


## Tools for profiling memory

애플에서 제공하고 있는 메모리 사용량을 분석하는 도구들 중 이번 세션에서는 이 중에서 XCTest 프레임워크를 사용할 것이다.

<img width="711" alt="스크린샷 2022-12-13 오후 7 28 54" src="https://user-images.githubusercontent.com/63997044/207297700-19c3d481-9dca-42b5-89ed-f0f4e643073e.png">

XCTest를 통해 다음과 같은 정보를 얻을 수 있다.
- Memory Utilization
- CPU usage
- Disk writes
- Hitch rate
- Wall clock time
- Application launch times

<img width="711" alt="스크린샷 2022-12-13 오후 7 29 12" src="https://user-images.githubusercontent.com/63997044/207297706-fa46480d-7b4c-4268-91d6-e09d229d5463.png">

예제 테스트로 함께 알아보자.
이 앱에서 사용자가 레시피를 다운로드할 수 있는 Save meal 기능의 메모리 사용량을 측정하고 싶다.

<img width="709" alt="스크린샷 2022-12-13 오후 7 29 30" src="https://user-images.githubusercontent.com/63997044/207297886-88c4c455-1640-4cd4-b3d8-470f0b6df02a.png">

이에 대한 테스트 코드
- `measure(metrics:, options:, block:)`: 타겟 앱의 메모리 성능을 측정한다.
- `startMeasuring()`: 측정 시작을 지시한다.
- UI에 대한 동작을 지시하고 실행

<img width="714" alt="스크린샷 2022-12-13 오후 7 30 09" src="https://user-images.githubusercontent.com/63997044/207297911-8d78ffee-0b7d-49e6-a145-f8446214234c.png">

5회 반복의 측정 결과와 평균값이 계산되어 표시된다.
이 결과값을 향후에 기준치(`baseline`)로 사용할 수 있으며 `baseline`보다 결과가 크면 테스트가 실패한다.

<img width="711" alt="스크린샷 2022-12-13 오후 7 29 56" src="https://user-images.githubusercontent.com/63997044/207297916-4eb45211-c711-492f-a302-11cacfab80c5.png">

`regression`: `baseline`으로부터 발생한 편차. regression이 발생했다면 테스트를 통과하도록 코드를 수정할 필요가 있음을 알 수 있다.

<img width="711" alt="스크린샷 2022-12-13 오후 7 30 30" src="https://user-images.githubusercontent.com/63997044/207297939-6ae85e2b-423a-43ce-86fa-d380254221d3.png">

Xcode 13에서 새롭게 추가된 `regression`을 분석하는 방법
- Ktrace file
- Memory graphs

### Memory Graphs

<img width="713" alt="스크린샷 2022-12-13 오후 7 30 52" src="https://user-images.githubusercontent.com/63997044/207297963-7d5f3ac2-8101-4fb1-8fb2-ed5050a49ad5.png">

- 메모리 그래프는 visual debugger 또는 Command line Tool과 함께 사용 가능하다.
- 인스턴스의 프로세스 주소 공간에 대한 스냅샷을 제공
- 메모리 그래프는 가상 메모리 영역과 할당된 malloc 블록의 주소와 크기를 기록한다. 이를 통해 힙 공간의 연결된 데이터 구조를 볼 수 있다.
- XCTest는 새로 할당하는 개체에 대한 malloc 스택 로깅(MSL)을 자동으로 활성화한다.

<img width="716" alt="스크린샷 2022-12-13 오후 7 31 13" src="https://user-images.githubusercontent.com/63997044/207298243-46bd668f-0eba-4a91-aa26-69d124fae360.png">

수집을 활성화하려면 xcodebuild의 `enablePerformanceTestsDiagnostics` 플래그를 사용해야 한다.
이 플래그는 Ktrace와 Memgraph를 수집을 활성화한다.
성능 테스트가 실행되면 콘솔에 결과가 출력되며 테스트 결과와 실패 이유, xcresult 번들 경로를 알 수 있다.

<img width="713" alt="스크린샷 2022-12-13 오후 7 32 05" src="https://user-images.githubusercontent.com/63997044/207298258-f7866767-bb50-4e53-abff-b0dc975edf8f.png">

Xcode에서 xcresult 번들을 열면 메모리 사용량과 memgraphs 파일을 다운받을 수 있다. MSL을 활성화하기 위해 추가 반복을 하므로 두 개의 memgraph 파일이 생성된다.


## Types of memory issues

앱의 일반적인 메모리 문제는 크게 Leak과 Heap 문제로 나눌 수 있다.
- Leaks
- Heap size issues
    - Heap allocation regressions
    - Fragmentation

### Leak(메모리 누수)

<img width="714" alt="스크린샷 2022-12-13 오후 7 33 12" src="https://user-images.githubusercontent.com/63997044/207298271-deb6b490-08ea-4abb-9f57-788950580339.png">

프로세스가 객체에 대한 메모리 할당 해제가 이루어지지 않고 참조를 잃을 때 발생한다.(대표적으로 Retain cycle)

<img width="714" alt="스크린샷 2022-12-13 오후 7 33 37" src="https://user-images.githubusercontent.com/63997044/207298285-3117602e-1203-409b-9914-6e154a564a15.png">

- unsafe type과 같이 ARC에서 관리하지 않는 경우 참조를 잃기 전에 할당을 해제 해주어야 한다.
- ARC에 의해 관리되는 객체라 하더라도 강한 참조 사이클이 발생할 수 있으므로 Reference cycle이 발생할 위험이 있다면 약한 참조를 사용해야 한다.

<img width="717" alt="스크린샷 2022-12-13 오후 7 34 29" src="https://user-images.githubusercontent.com/63997044/207298503-fe202bc5-9f40-4a8a-b52b-708c956e73a3.png">

memgraph 파일로부터 누수를 살펴보자.
`leaks`로 memgraph 파일에 대한 누수 결과를 분석할 수 있다. (`leaks ~.memgraph`)
결과 출력에서 누수가 있었음이 표시된다.

<img width="717" alt="스크린샷 2022-12-13 오후 7 35 06" src="https://user-images.githubusercontent.com/63997044/207298539-620ca50e-62f3-46c5-bf0d-0122f0a53b5b.png">

- 4 leaks for 240 bytes
- retain cycle을 다루는 ROOT CYCLE이 표시되며 어떤 객체와 참조가 문제인지 알 수 있다.
- 누수에 대한 할당 호출 스택이 표시되어 있으므로 어떤 객체로부터 누수가 발생했는지 알 수 있다.

### Heap allocation regressions(힙 할당 문제)

<img width="719" alt="스크린샷 2022-12-13 오후 7 36 17" src="https://user-images.githubusercontent.com/63997044/207298557-5856e902-c543-49e3-b1ea-149dbd919e4a.png">

힙은 동적으로 할당된 객체가 저장되는 프로세스 주소 공간이다. 
힙에 많은 객체를 할당하면 따라서 사용하는 메모리 공간이 증가하게 된다. 이로 인해 Heap allocation regression이 발생하게 된다.

<img width="718" alt="스크린샷 2022-12-13 오후 7 36 33" src="https://user-images.githubusercontent.com/63997044/207298562-1485e932-8a07-4fc3-8a6e-1c516f32ec4a.png">


- 힙 사용량을 줄이기 위해 사용하지 않는 메모리 할당을 제거해야 한다.
- 또한 불필요하게 큰 메모리 할당을 줄여야 한다.
- 더 이상 사용하지 않는 메모리를 할당 해제해야 한다.
- 필요한 시점에만 메모리를 할당해야 한다.

<img width="717" alt="스크린샷 2022-12-13 오후 7 37 20" src="https://user-images.githubusercontent.com/63997044/207298888-aeb0251b-3b30-4e52-8975-63b0fe4bb4e5.png">

실패한 XCTest에서 Heap regression을 확인해보자.
`vmmap -summary`로 memgraph 파일에서 어떤 메모리 위치가 사용되는지 살펴볼 수 있다. (`vmmap -summary ~.memgraph`)

<img width="716" alt="스크린샷 2022-12-13 오후 7 37 06" src="https://user-images.githubusercontent.com/63997044/207298874-1b149157-486d-4385-af66-9362c0912b7f.png">

Pre memgraph의 물리적인 사용량은 약 112MB.
Post memgraph의 물리적인 사용량은 125MB로 약 `13MB`의 차이가 발생했다.

<img width="718" alt="스크린샷 2022-12-13 오후 7 38 08" src="https://user-images.githubusercontent.com/63997044/207298897-cd4b890b-316d-44d0-ba7b-e8c2fb149946.png">

프로세스의 메모리 사용량이 지역별로 분류되어 표시된다. 힙 할당 문제라면 `MALLOC_`으로 시작되는 영역을 살펴보아야 한다.
`MALLOC_LARGE` 영역이 13MB의 Dirty Memory를 보유하고 있는데, 이에 해당하는 값이 증가한 크기와 유사하기 때문에 어떤 객체가 연관되어 있는지 확인해보아야 한다.

<img width="716" alt="스크린샷 2022-12-13 오후 7 38 28" src="https://user-images.githubusercontent.com/63997044/207298913-94bdc59f-782e-402b-a356-beb7bf335683.png">

`heap -diffFrom` 명령어를 통해 pre memgraph와 post memgraph를 열어본다.(`heap -diffFrom pre_~.memgraph post_~.memgraph`)
이것은 post에는 존재하지만 pre에는 없는 객체를 보여준다.

<img width="718" alt="스크린샷 2022-12-13 오후 7 39 07" src="https://user-images.githubusercontent.com/63997044/207298937-236faf8c-514b-44ca-b2cd-d5bdc0acebf7.png">

출력에서 힙 메모리가 클래스별로 분류된 부분을 볼 수 있다. 여기에서 13MB에 해당하는 `non-object`를 발견할 수 있다.

<img width="714" alt="스크린샷 2022-12-13 오후 7 40 18" src="https://user-images.githubusercontent.com/63997044/207298949-f247e2a0-b70c-4c5e-9338-384c46b8c193.png">

이곳의 힙 주소를 얻기 위해 `heap -address=` 명령어를 사용한다. address 옵션에 값을 넣어 크기가 500KB 이상인 non-object만 가져온다. (`-address=non-object[500K-] post_~.memgraph`)
`0x113800000` 주소에 있는 non-object가 13MB를 점유하고 있는 용의자라는 것을 알아내었다.


알아낸 주소를 어떻게 활용할 수 있을까?

1. leaks --traceTree=0x113800000
<img width="717" alt="스크린샷 2022-12-13 오후 7 40 38" src="https://user-images.githubusercontent.com/63997044/207298957-09f65ff5-7ad9-47e3-99ca-8b96be67cf9e.png">

    이 주소를 참조하는 객체들에 대한 트리를 제공한다.
    자세한 정보를 얻고 싶은 특정 객체가 있을 때, MSL이 활성화되어있지 않을 때 유용하다.


2. leaks --referenceTree
<img width="713" alt="스크린샷 2022-12-13 오후 7 41 34" src="https://user-images.githubusercontent.com/63997044/207299437-109f9603-c1c1-4d14-b0d9-7282f6d6da4d.png">

    프로세스의 모든 메모리에 대한 하향식 참조 트리를 제공한다.
    앱에 regression이 발생했지만 어떤 특정 객체가 원인인지 알 수 없는 경우에 유용하다.


3. malloc_history -fullStack ~.memgraph 0x113800000
<img width="715" alt="스크린샷 2022-12-13 오후 7 41 58" src="https://user-images.githubusercontent.com/63997044/207299445-2858d548-49d3-45d2-aff0-0586b3592443.png">

    객체가 어떻게 할당되었는지 알아내는 방법이다. 해당 주소에 대한 할당 호출 스택들을 볼 수 있다.


### Fragmentation(단편화)

<img width="717" alt="스크린샷 2022-12-13 오후 7 42 33" src="https://user-images.githubusercontent.com/63997044/207299453-24105ca9-8698-46d7-9835-5db9fe650515.png">

- 페이지: 시스템이 프로세스에 부여하는 고정된 크기의 분할할 수 없는 메모리 청크
- 페이지는 나눌 수 없기 때문에 페이지의 일부를 사용할 땐 전체 페이지가 Dirty 메모리로 간주됨. 
- 따라서 아주 극히 일부만 사용하더라도 페이지 전체에 대한 비용이 든다.

<img width="716" alt="스크린샷 2022-12-13 오후 7 42 56" src="https://user-images.githubusercontent.com/63997044/207299458-7c90fdb8-f070-4f72-a4fc-7f425d6ac46c.png">

Fragmentation은 100% 활용되지 않는 Dirty 페이지가 있을 때 발생한다.

<img width="717" alt="스크린샷 2022-12-13 오후 7 43 39" src="https://user-images.githubusercontent.com/63997044/207299463-e63478b1-95d3-4218-bcb0-e9d1d51b7d56.png">

- 최대한 수명이 비슷한 객체들을 서로 가까운 공간에 할당할 것. 
    - 이렇게 하면 객체가 함께 해제되며 작업에 필요한 연속된 메모리 청크를 제공할 수 있게 된다.
- 일반적으로 fragmentation는 불가피한 문제이기 때문에, 목표는 최대 25%의 fragmentation를 목표로 한다.

<img width="711" alt="스크린샷 2022-12-13 오후 7 43 56" src="https://user-images.githubusercontent.com/63997044/207299469-79bcb6d7-0b0b-4e06-8a7c-a31b374ddf02.png">

- 또한 Autorelease Pool을 사용하여 Fragmentation을 줄일 수 있다.
    (autorelease pool: 범위를 벗어나는 즉시 내부에 할당된 모든 객체를 해제)
- fragmentation은 특히 오랜 시간 실행되는 프로세스일 경우에 취약하다.

<img width="717" alt="스크린샷 2022-12-13 오후 7 44 19" src="https://user-images.githubusercontent.com/63997044/207299721-2dcfc7b8-19ae-42a5-8990-a92cabeefeff.png">

`vmmap -summary`를 실행하여 단편화 분석을 살펴볼 수 있다. 출력 결과의 맨 아래로 내려가보자. `MALLOC ZONE`에는 각 영역이 어떤 할당으로 나뉘었는지 보여준다. 
일반적으로는 `DefaultMallocZone`에 대해서만 관심을 가지면 된다. 보통 힙 할당이 끝나는 곳이기 때문이다.
그러나 MSL이 활성화되어 있기 때문에 지금은 `MallocStackLoggingLiteZone`을 봐야 한다. 이곳은 MSL이 활성화되어 있는 동안 힙 할당이 끝나는 곳이다. 

<img width="717" alt="스크린샷 2022-12-13 오후 7 44 48" src="https://user-images.githubusercontent.com/63997044/207299752-e0383177-9dfb-4d30-92b5-7024f783e517.png">

`% FRAG` 열은 각 malloc 영역이 단편화로 인해 발생한 메모리 낭비 비율을 보여준다. 사이즈가 가장 큰 `MallocStackLoggingLiteZone`이 최대 상한선인 25%보다 작은 19%이기 때문에, 현재 앱은 단편화 문제로부터 안전하다고 볼 수 있다.

<img width="717" alt="스크린샷 2022-12-13 오후 7 45 12" src="https://user-images.githubusercontent.com/63997044/207299763-36ecd313-9745-4c22-8b1f-fce6aa874380.png">

만약 단편화 문제가 발생했다면, Xcode 도구 중 Allocations 트랙을 사용하여 자세히 진단할 수 있다.


## Detect and Diagnose

<img width="713" alt="스크린샷 2022-12-13 오후 7 45 41" src="https://user-images.githubusercontent.com/63997044/207299769-88b8a555-22e0-4b21-9d8c-f2977fe6c2d7.png">

메모리 문제 감지 과정
- 새 기능을 추가할 때마다 성능 테스트를 작성하여 메모리 지표를 모니터링한다.
- 각 테스트에 대한 기준선을 정한다.
- 테스트를 통해 regression을 파악한다.
- 수집된 ktrace, memgraph 파일을 사용하여 진단한다.

<img width="713" alt="스크린샷 2022-12-13 오후 7 45 53" src="https://user-images.githubusercontent.com/63997044/207299784-fb00296c-4ce5-4b1e-b92c-18bece8442e5.png">

진단 절차
- 가장 먼저 누수 문제를 확인한다.
- 누수가 발견되지 않았으면 힙을 진단한다.
- 어떤 객체에서 문제가 발생했는지 파악한다.
- 문제가 있는 객체를 찾아냈다면 그 주소를, 아직 찾아내지 못했다면 더 많은 단서를 통해 정보를 수집한다.
- 마지막으로 해당 주소를 조사한다.
