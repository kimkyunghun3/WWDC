# Explore the new system architecture of Apple silicon Macs

인텔 based Mac architecture

![](https://i.imgur.com/JvgIa18.jpg)

T2 features는 애플페이, 터치아이디, Hey siri 같은 것이 있다.

구별되는 GPU를 가진 기계는 CPU 와 GPU를 구분하는 메모리를 가지고 있다.

![](https://i.imgur.com/crdcZH7.jpg)

Apple silicon Macs에서는 이제 칩 안에 하나의 시스템으로 많은 구성요소를 가지도록 설계되었다. 이를 SoC라고 한다. (system on chip)

하나로 만드는 것의 장점으로는 시스템에 메모리 구조가 통합된 것을 제공할 수 있다.

이말은 즉, GPU, CPU 동일한 메모리에서 작동한다는 것을 의미한다.

그래서 이미지관련된 Graphics 관련된 것도 동일한 메모리에서 작동하므로 공유가 되기 때문에 효율적으로 동작할 수 있게 된다.

no overhead, PCle bus를 통해 데이터가 복사되는 것이 필요하지 않다.

![](https://i.imgur.com/zt1j0AL.jpg)

비교해보면 asymmetrics core를 통해 더 적은 CPU tasks를 통해 더 강하고 효율적인 코어를 사용한다.

이를 asymmetric multiprocessing, AMP 라고 부른다

이것을 활용해서 적절하게 core를 배치해서 사용할 수 있게 만든다.

ML에서도 computUnits를 결정할 필요가 없다. 왜? 어차피 하나로 통합된 아키텍쳐를 가지고 있기 때문에!

![](https://i.imgur.com/MZgc3I8.jpg)

QoS

![](https://i.imgur.com/zdBMqRY.png)

QoS를 설정해줘서 파워를 효율적으로 사용하는 것에 대해 우선수위를 두게 할 수 있다.

모든 곳에서 중요하지만 특히 AMP에서 더 중요하다.

QoS는 어떤 코어를 테스크에서 실행할지 결정하는 요소이기 떄문이다.

두번쨰로는 GCD다.

AMP에서도 이거 중요하다!

![](https://i.imgur.com/DUyzGOK.jpg)

이렇게 분산해서 하면 더 성능적 우위를 접할 수 있다.

이는 최적화되게 병렬적으로 코드를 실행하게 도와준다.

### Security

![](https://i.imgur.com/TW21JMM.jpg)

4가지 종류가 존재한다.

#### Write XOR execute

![](https://i.imgur.com/q27Mpce.jpg)

애플 실리콘은 제한을 만든다. 이말은 즉, 메모리 페이지들이 실행할수(executable) 있거나 writable 할 수 있는 것을 의미한다.

그리고 동시에 실행되지 못하게 만든다.

JIT는 두가지 작업을 실행하게 만드는 것인데 여기에서 새 API가 소개된다.

![](https://i.imgur.com/00BC56R.jpg)

이 API는 writable, executable에 대한 허용을 결정해준다.

각각 다른 thread에 있는데 동일한 page에 있다. 그래서 각각 다른 허용으로 이루어지지만 위치는 동일하다.

이는 multi threaded한 JITs에서 쉽게 선택할 수 있게 만들어준다.

그리고 JIT compiler가 빠르고 안전하게 만들어준다.

![](https://i.imgur.com/NRlheGN.jpg)

kerenl이 메모리에 올라가면 kernel integrity protections는 커널 코드를 포함하는 페이지를 수정할 수 있는 상태로부터 보호한다..! 그말은 즉, 수정을 못하게 만드는것같다
또한 추가적인 페이지를 실행하지 못하게 한다.

Pointer authentication

![](https://i.imgur.com/F4e3ryk.png)

포인터들의 오용을 방지해준다.

포인터관련.. 알아보고싶으면 해봐라..

![](https://i.imgur.com/gR5zIwJ.png)

Device isolation

PCIe 기기들은 IOMMU를 통해 시스템 메모리에 접근한다.

인텔맥 상황

![](https://i.imgur.com/Js9TkNr.png)

macOS가 모든 기기들에게 시스템 메모리의 공유된 view를 준다.

애플실리콘 상황

![](https://i.imgur.com/5VIAFy7.png)

모든 기기들이 각각 분리된 memory mappings를 가진다.

![](https://i.imgur.com/7tHvbL9.jpg)

위에는 IOMapper를 만들고 이를 초기화할때 넣어주는방식...
아래에는 저 메소드를 호출해서 직접적 ioMemoryDescriptor에서 사용한다.

#### Kernel extensions

커널 익스텐션을 사용하기 위해서는 Pointer authentication를 사용해야한다.

하지만 지난번에 새로운 DriverKit이 나왔다! 카탈리나 OS때!!!

![](https://i.imgur.com/VFzoNxD.png)

이는 시스템을 더 안정적이며 안전성을 개선했다.

Rosetta

![](https://i.imgur.com/NISXacN.jpg)

오로지 실리콘맥에서만 사용가능하다!! 장점은 위에 다 쓰여져있다

![](https://i.imgur.com/IDYHDRe.jpg)

로제타는 내가 실행하는 applicaton에서 모든 실행가능한 코드를 translate한다.

![](https://i.imgur.com/jAbMNg1.jpg)

application이 실행되고 로드가 되면 Rosetta는 a x86_64 process를 따라한다.

그래서 모든 프로세스, 모든 프레임워크를 translate한다!!

만약 로제타가 설치때 생성되지 않는 것을 마주치게 된다면 컴파일때 이것을 설치하게 한다.

디버거같은 것도 지원하므로 Xcode로부터 바로 translated된 앱들을 빌드하고 실행할 수 있다.

여기에서 인텔맥, 실리콘맥의 프로세스 차이가 존재한다.
Page size, memory 순서 규칙, mach_absolute_time의 빈번다른 모든 포인트 행동들이 모두 다르다!!

Native Port와 관련되서 만약 더 알고싶다면 아래의 WWDC를 보면된다.

![](https://i.imgur.com/rSQ9n8w.png)

### Boot Process

실리콘맥은 Boot 시스템을 기반으로 만들어졌다.

![](https://i.imgur.com/bY85NEC.jpg)

Bood-loader를 통해서 다양한 macOS를 내부 외부에서 설치할 수 있게 만들어준다.

![](https://i.imgur.com/e8MTXrY.png)

다음으로는 Start-up에 대해 이야기한다

![](https://i.imgur.com/L647oh6.png)

이전보다 더 간단해졌는데, 모든 start-up key가 통합되었다.

![](https://i.imgur.com/8WKlFaC.png)

이렇게 ToucID Button 하나만 눌러서 해결한다!!!!

새로운 Recovery 기능이 2가지 나왔다

![](https://i.imgur.com/zVhahay.jpg)

Mac Sharing Mode가 있는데 SMB 파일을 사용해서 공유해서 user data에 접근하도록 file-level를 제공한다.

다음으로는 Startup Disk가 있다.

![](https://i.imgur.com/qUif7qh.png)

![](https://i.imgur.com/0WPbQ6e.jpg)

Secuiry에서 full과 redcued 2가지가 존재한다.

Full 버전

![](https://i.imgur.com/xcjPjIo.jpg)

reduced 버전 

![](https://i.imgur.com/IGMOFNC.jpg)

존재하는 보안 configuration tool인 CSRUtil이 있다.

![](https://i.imgur.com/4aYxO3n.jpg)

Per-OS secuiry policy가 인텔과 실리콘 맥이 다르다!

![](https://i.imgur.com/6hl6GwQ.jpg)

만약에 인텔맥에서 macOS에 여러개 volumes를 설치했다면 모든 설치들이 다 공유되어있어서 영향을 끼쳐서 보안이 downgrade가 될 수도 있다.

하지만 실리콘 맥에서는 각각 macOS 설치에 대해 분리된 보안 정책을 가지고 있다.

로그인관련된 이야기!

![](https://i.imgur.com/OZw5tqz.jpg)

실리콘맥은 통합된 로그인 시스템을 제공한다!

데이터 보호에 대해서는 여러가지가 있는데

![](https://i.imgur.com/hVV1wQo.jpg)

![](https://i.imgur.com/lpDjDGL.jpg)

항상 암호화를 유지하고 secure hibernation를 제공한다.

만약 저전력모드일 때 이곳에서 메모리 내용들에 대해 보호하도록 안전장치가 되어있다.

그럼 이제 맥에서 Recovery가 어떻게 동작하는지 볼 것이다.

맥에는 저장내용으로 2가지가 존재한다.

![](https://i.imgur.com/xFUrrKR.jpg)

만약에 MacOS가 이용할 수 없다면 macOS Recovery를 사용해서 다시 설치하도록 한다.

![](https://i.imgur.com/Dw5h3oW.png)

만약 macOS Recovery가 동작안된다면?

![](https://i.imgur.com/wfECIGV.jpg)

인텔맥에서는 Internet Recovery를 사용한다.

실리콘맥에서는 System Recovery라는 것을 사용해서 한다!!


