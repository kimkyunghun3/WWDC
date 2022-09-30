# iOS Memory Deep Dive

순서는 아래와 같다

![](https://i.imgur.com/1xq9qfu.jpg)

UX에 좋다!

Pages 앱의 메모리 말한다.

힙에 여러 객체들을 가지고 있따

![](https://i.imgur.com/7TOqaeM.jpg)

일부 객체들은 여러 페이지에서 사용되고 있다

메모리 사이즈 보통 이렇다

![](https://i.imgur.com/wNjTUv9.jpg)

메모리 클린으로 할당해주고 안에 채우는 dirty를 하고 있다

![](https://i.imgur.com/6CixaCa.jpg)

보통 메모리는 아래처럼 되어있다

![](https://i.imgur.com/LziIBeA.jpg)

클린 메모리에 대한 설명

![](https://i.imgur.com/FcUPCJz.jpg)

Memory mapped files → jpg, data, model 타입

framework → UIKIt 같은 것들

dirty 메모리에 대한 설명

![](https://i.imgur.com/0TdIQD0.jpg)

data, array 등등 힙에 할당되는 컬렉션들

decoded된 image buffer → ImageIO, CGRasterData 등

Framework

프레임워크도 dirty memory를 사용한다.

![](https://i.imgur.com/4EUmwG1.jpg)

compressed memory

전통적인 disk swap아니다.

![](https://i.imgur.com/cFv9NRc.jpg)

접근되지 않는 페이지들을 압축해서 불필요한 공간을 차지하지 않도록 한다

![](https://i.imgur.com/0zccB8C.jpg)

이렇게 딕셔너리에서 3칸을 사용하고 있는데 만약 딕셔너리에서 계속 사용되지않고 있을 때 시스템에서 만약 추가 공간이 필요할때 squeeze해서 압축시킨 다음 이곳을 하나의 칸으로 바꾸고 나머지 두칸을 비게 만들어서 사용한다

![](https://i.imgur.com/KdVGFPO.jpg)

다음으로 memory warnings이다

![](https://i.imgur.com/1RuaFD2.jpg)

앱은 항상 cause를 체크하지 않는다.

그래서 캐시 정책을 보통 바꾸어서 사용한다

예를 들면 아래와 같다

![](https://i.imgur.com/bLBjqhV.jpg)

위에 compress처럼 여러개 차지하고 있다가 하나의 칸으로 compress하도록 만드는 것과 동일

다음으로 Caching 이다

![](https://i.imgur.com/TbSAjQa.jpg)

캐시를 사용하면 반복적으로 작업하는 것을 일일이 불러올 필요없이 바로 사용할 수 있게 만든다.

NSCache를 사용하면 thread-safe하게 캐시된 객체들을 저장할 수 있다

메모리 FootPrint에 대해 알아보는데 Clean 메모리는 중요하지 않다

![](https://i.imgur.com/7vm3cNy.png)

그래서 Dirty, Compressed에 대해 알아볼 것이다

디바이스에 따라 Footprint의 한계가 다르다


Extension를 제한이 대부분 낮다..

![](https://i.imgur.com/UbOxOZE.png)


이제 이 profile에 대해 제대로 알아보자

### Tools for Profiling Footprint

Xcode10부터 메모리 사용을 이처럼 볼 수 있다

![](https://i.imgur.com/c6HwdOX.jpg)

Virtual Memory Trace

![](https://i.imgur.com/A9dB1eC.jpg)

메모리 사용 증거를 보여준다..

Xcode Debugger

디바이스의 메모리 초과해서 사용하면 아래처럼 경고가 뜬다

![](https://i.imgur.com/YlBfd72.jpg)

다음으로 Xcode Memory Debugger

![](https://i.imgur.com/AtYpg7i.jpg)

leaks 에 대한 설명

![](https://i.imgur.com/LPokNpa.png)

Memgraph 에서 Malloc allocation 확인하기 위해서 이것을 체크해서 사용하면 된다

![](https://i.imgur.com/lgTvuNi.jpg)

malloc history를 아래 명령어를 통해 볼 수 있따

![](https://i.imgur.com/kOQwpW2.jpg)

명령어를 하면 아래처럼 trace 를 확인 가능하다

![](https://i.imgur.com/Uj8iQps.jpg)

여기에서 보면 이곳이 메모리 할당을 + 620 엄청 많이 받고 있는데 여기에서 문제가 있지 않을까 확인할 수 있다

![](https://i.imgur.com/1HZnOas.jpg)

툴을 선택하는 기준은 아래와 같다

![](https://i.imgur.com/QHo2UAF.jpg)

### Images

![](https://i.imgur.com/GJZaExa.jpg)

![](https://i.imgur.com/JK9foZL.jpg)

파일 사이즈가 이정도인데 실제로 얼마나 ㄱ쓰일까?

![](https://i.imgur.com/gpb2WPh.jpg)

이미지 로드에 Load → Decode → Render 형식으로 이루어져있다

![](https://i.imgur.com/xaW7jP2.png)

로드할 떄 compressed 되어있어서 작게 되어있는데 이를 decode해서 GPU가 읽을 수 있도록 포맷을 변환한다.

이렇게 할 때 uncompressed하게되어서 이때 이제 사이즈가 커지는 것이다

이미지 랜더링 포맷은 아래와 같다

![](https://i.imgur.com/bFCt6Eu.png)

그리고 wide format에서는 아래와 같다

![](https://i.imgur.com/mNgEcnx.jpg)

이외에도 여러 포맷이 있는데 적절한 포맷을 어떻게 고를 수 있을까?

![](https://i.imgur.com/dufouKj.jpg)

픽셀마다 4바이트로 그리고 있따

![](https://i.imgur.com/jPVBkgW.jpg)

픽셀마다 1바이트로 그리고 있따

![](https://i.imgur.com/8X6KZ2o.jpg)

이렇게하면 75%나 메모리 사용을 줄일 수 있다

또 하나의 팁은 아래처럼 tintColor를 바꾸더라도 메모리 사용이 늘어나지 않고 1로 고정되어있다

![](https://i.imgur.com/MhPXxKe.png)

또 다른 방식으로는 Downsampling이 있다

ㅁㄴㅇ
![](https://i.imgur.com/3ALQPdX.jpg)

ImageIO 프레임워크를 사용하면 메모리를 더럽히지 않고 사용할 수 있다

ImageIO 사용하기전

![](https://i.imgur.com/NWASEbW.jpg)

사용 후 

![](https://i.imgur.com/vgxPtw0.jpg)

API에 미리 이미지 사이즈가 크다는 거승ㄹ 알려주고 이를 적절한 사이즈로 바꿔서 create해주는 작업을 한다

이전 코드보다 50% 더 빠르게 동작한다

### Optimizing when in the background

내가 볼 수 없는 것의 큰 자원들을 unload해서 사용해라

App life cycle 사용해서 가능하다

UIViewConttrolelr apperenr cycle에서도 가능

![](https://i.imgur.com/nQUInJe.jpg)

예를 들면 아래와 같다

![](https://i.imgur.com/If0skoo.jpg)

백그라운드, 포그라운드에서 unload할 수 있또록 만들 수 있다

View Life cycle에서도 동일하게 사용가능하다

![](https://i.imgur.com/LhdIJIr.png)

### Demo

![](https://i.imgur.com/r4B6WMw.jpg)

데모에서 사용되는 것이 

메모리 사용이 엄청나다는 것을 볼 수 있따

Memgraph 파일을 본다

![](https://i.imgur.com/ESpzTtC.png)

여기에서 Size를 볼 수도 있다

또 Leaks 여부 파악도 가능하다

아래의 오른쪽에서 두번쨰 버튼 누르면 확인가능하다 어떤게 leak이 있는지

![](https://i.imgur.com/iZnvF3T.png)

터미널에서 vmmap를 확인하느 법

![](https://i.imgur.com/d6VPoSQ.png)

이제 보여지면 아래와 같이 보여진다

![](https://i.imgur.com/flZ8XEx.jpg)

이제 vmmap에서 특정한 CG image가 사이즈를 많이 차지하고 있다고 판단해서 이에 대해 확인해본다

![](https://i.imgur.com/G5bgSr8.png)

다음으로는 이제 연관성을 보기 위해서 메모리주소가 연속적으로 되어있는 것을 확인할 수 있는 방법이다

![](https://i.imgur.com/7aSpRX0.jpg)

이제 Virtual Memory Region에서 가장 더티한 것을 찾는 게 아니라 마지막 메모리 주소를 통해서 찾는 법을 알려준다

![](https://i.imgur.com/XDfg3SF.png)

터미널에서 이 그림을 트리형식으로 보여주고 있다

<img width="956" alt="스크린샷 2022-09-30 오후 4 51 38" src="https://user-images.githubusercontent.com/52434820/193226090-20128147-efdd-43e1-bab1-cd0cffcf7883.png">


모습은 아래처럼 보인다

<img width="1507" alt="스크린샷 2022-09-30 오후 4 53 05" src="https://user-images.githubusercontent.com/52434820/193226101-c4fea759-4ee9-47f8-af18-962d64068553.png">


터미널로 malloc_history 보는법

<img width="1710" alt="스크린샷 2022-09-30 오후 4 55 30" src="https://user-images.githubusercontent.com/52434820/193226113-6595fdde-aafb-4782-87e9-37aeb69f2f3b.png">

근데 이거 툴에서 볼수있어서 굳이?

요약

<img width="1227" alt="스크린샷 2022-09-30 오후 5 14 01" src="https://user-images.githubusercontent.com/52434820/193226120-63da4365-906d-4cec-8a49-264dde88ae10.png">
