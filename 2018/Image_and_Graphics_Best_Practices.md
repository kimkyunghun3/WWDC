# Image and Graphics Best Practices
[영상 보러가기](https://developer.apple.com/videos/play/wwdc2018/219)

![](https://i.imgur.com/SQNWjjX.png)


> Image와 Graphics를 사용할 때 메모리와 CPU의 자원 사용에 대해 이야기한다.
> 앱의 CPU 사용량과 메모리 사용량은 서로 관련이 있고, 더 많은 CPU를 사용할수록 배터리 수명과 앱의 응답성에 부정적인 영향을 미친다는 것은 명백하다. 따라서 메모리와 CPU의 자원 사용을 개선하는 방법에 대해 알아보자.



## iOS Memory Deep Dive

[WWDC18 - iOS Memory Deep Dive](https://github.com/kimkyunghun3/WWDC/blob/main/2018/iOS_Memory_Deep_Dive.md)의 이미지 관련 내용과 관련있는 부분이기에 그 내용을 간단하게 요약해서 설명한다.

이 이미지 파일은 590KB이지만, 실제로 쓰이는 메모리는 무려 10MB나 된다.
(2048 x 1526 x 4바이트)
그 이유는 OS에서 이미지를 불러올 때 Load → Decode → Render 과정을 거치기 때문



Load 단계에서 압축된 이미지를 불러온다.
Decode 단계에서 GPU가 읽을 수 있도록 포맷을 변환한다. => 이 때 사이즈가 커진다.
Render 단계에서 이미지 데이터를 시각적 이미지로 표시한다.


## UIImage and UIImageView

![](https://i.imgur.com/6cXQo4E.png)

이 화면을 구성한다면 어떻게 만들 수 있을까?

![](https://i.imgur.com/5hth4h2.png)

UIImageView와 UIImage를 사용하여 이렇게 만들 수 있을 것이다.
MVC 스타일에서 UIImage는 모델 객체, UIImageView는 View에 해당한다. 둘의 역할은 각각 다음과 같다.
- UIImage: 이미지 컨텐츠의 로드를 담당
- UIImageView: UIImage를 표시하고 렌더링하는 역할

![](https://i.imgur.com/zZLHEvw.png)

그러나 실제 이미지를 표시하는 과정은 둘 사이에 디코딩 과정이 추가된다.
디코딩에 대해 알아보기 전에 먼저 버퍼가 무엇인지 알아야 한다.

![](https://i.imgur.com/G8VFFFY.png)

버퍼란, 연속적인 메모리의 영역을 의미한다. 특히 동일한 일련의 요소로 구성된 메모리를 가리킬 때 버퍼라는 용어를 사용한다.

![](https://i.imgur.com/AIGvkmO.png)

그리고 이미지 버퍼는, 메모리의 버퍼 중에서 이미지의 일부를 나타내고 있는 버퍼를 의미한다. 이미지 버퍼가 따로 중요한 이유는 버퍼의 각 요소들은 단일 픽셀의 색상과 투명도를 나타내기 때문이다.

즉 버퍼의 크기는 이미지의 크기에 비례한다.

![](https://i.imgur.com/duCOW2q.png)

프레임 버퍼는 애플리케이션에서 실제 렌더링된 출력을 보유한다. 앱이 뷰의 계층 구조를 업데이트하면 UIKit은 앱의 윈도우와 모든 뷰를 프레임 버퍼로 렌더링한다. 

그리고 프레임 버퍼는 디스플레이에 픽셀을 출력하기 위해 하드웨어가 읽을 픽셀별 색상 정보를 제공한다. 이 동작은 1/60초 또는 1/120초와 같은 고정된 간격으로 발생한다.
변경 사항이 없으면 디스플레이 하드웨어는 이전에 보았던 것과 동일한 데이터를 프레임 버퍼에서 가져온다. 

![](https://i.imgur.com/kSlpS9Y.png)

그러나 변경 사항이 있다면 앱의 창을 프레임 버퍼로 다시 렌더링하고, 디스플레이 하드웨어가 프레임 버퍼에서 새 컨텐츠를 가져온다.

![](https://i.imgur.com/94694ZW.png)

이미지를 비교 및 대조할 때 쓰이는 버퍼: 데이터 버퍼
데이터 버퍼 또한 다음과 같은 일련의 바이트를 포함하고 있다.
- 해당 버퍼에 저장된 이미지의 크기를 설명하는 메타데이터
- JPEG, PNG 등 인코딩된 이미지 데이터 자체

그러나 바이트는 직접적으로 픽셀 정보를 제공하지 못한다.

![](https://i.imgur.com/gek2gBn.png)

예를 들어 여기에 UIImageView가 있고, 이미지 파일의 내용을 나타내는 데이터 버퍼가 있다. 이미지를 화면에 표시하려면 UIImageView의 프레임 버퍼를 한 픽셀 당 나타내야 할 데이터(색상, 투명도 등)로 채워야 한다.

![](https://i.imgur.com/7AWozh6.png)

따라서 이를 위해 UIImage는 데이터 버퍼의 이미지와 동일한 크기의 이미지 버퍼를 할당하고 이미지 데이터를 픽셀당 정보로 변환하는 작업을 수행한다. 
이것이 바로 Decoding 작업이다.

![](https://i.imgur.com/h7ncwL0.png)

그러나 디코딩 작업은 CPU를 많이 사용하고, 메모리 할당과 해제가 지속적으로 발생하는 작업이다.
따라서 UIKit이 ImageView에 렌더링을 요청할 때마다 매번 디코딩을 수행하는 것이 아니라 UIImage가 해당 이미지 버퍼를 가지고 있어 한 번만 작업이 수행된다.

![](https://i.imgur.com/1ACcrLg.png)

결과적으로 크고 영구적인 메모리가 앱의 모든 이미지마다 할당된다. 이 할당은 이미지의 크기에 비례하고 성능 저하 및 단편화의 원인이 된다.

애플리케이션이 허용 한도를 초과하여 메모리 사용량을 축적하면 OS가 물리적 메모리 영역을 압축하기 시작한다. 이 작업 또한 CPU를 사용하기 때문에 CPU 사용량이 증가하고, 결국 OS가 프로세스를 하나씩 종료하게 되며 심지어 현재 앱 자체가 종료될 수 있다.

따라서 앱이 짧은 시간 동안만 메모리를 사용하더라도 장기적으로는 악영향을 미칠 수 있으므로 우리는 메모리를 효율적으로 사용해야 한다.

### Downsampling

다운샘플링은 메모리 사용량 자체를 줄이는 테크닉이다.

![](https://i.imgur.com/z9gD76Y.png)

앞서 보았던 이미지 렌더링 파이프라인을 다시 보면, 일반적으로 이미지를 표시하는 ImageView가 실제 내부에 표시할 이미지의 크기보다 작다.
다운샘플링을 사용하면 메모리를 절약할 수 있다.

![](https://i.imgur.com/wmRhJ11.png)


축소된 이미지를 썸네일이라는 객체로 저장한다.
썸네일은 더 작은 디코딩된 이미지 버퍼를 갖기 때문에 결국 총 메모리 사용량은 줄어든다.
썸네일의 이미지 버퍼를 통해 만든 UIImage를 ImageView에 할당한다. 그러면 원본 이미지 버퍼를 버릴 수 있게 된다.


다운샘플링을 수행하기 위해 구현한 코드를 보면서 몇 가지 중요한 부분을 살펴보자.

![](https://i.imgur.com/gzCgBmK.png)

1. CGImageSource 객체 생성
```swift
let imageSourceOptions = [kCGImageSourceShouldCache: false] as CFDictionary
let imageSource = CGImageSourceCreateWithURL(imageURL as CFURL, imageSourceOptions)!
```

`CGImageSourceCreateWithURL()` 메서드를 통해 객체를 생성한다. 
Core Graphics 프레임워크에 해당 URL의 파일에 저장된 데이터를 나타내는 객체를 생성하고 있음을 알려준다.
옵션에 대한 값을 딕셔너리 형태로 취할 수 있다.
- kCGImageSourceShouldCache: false 
    - 디코딩된 형식으로 캐싱할 것인지 설정하는 옵션. 다운샘플링 후 원본 데이터 버퍼를 버릴 것이므로 false로 설정 


2. 썸네일의 크기 및 옵션 설정
```swift
let maxDimensionInPixels = max(pointSize.width, pointSize.height) * scale
let downsampleOptions = [
    kCGImageSourceCreateThumbnailFromImageAlways: true,
    kCGImageSourceShouldCacheImmediately: true,
    kCGImageSourceCreateThumbnailWithTransform: true,
    kCGImageSourceThumbnailMaxPixelSize: maxDimensionInPixels] as CFDictionary
```

썸네일의 최대 크기(픽셀 수)를 설정하고 다운샘플링 옵션을 딕셔너리로 생성한다.

- kCGImageSourceCreateThumbnailFromImageAlways: true
    - 원본 이미지가 썸네일을 포함하고 있더라도 전체 이미지로 썸네일을 만들 것인지
- kCGImageSourceShouldCacheImmediately: true
    - 썸네일을 생성할 때 이미지 버퍼를 생성할 것인지
- kCGImageSourceCreateThumbnailWithTransform: true
    - 원본 이미지의 방향이나 비율에 맞춰 썸네일을 조정할 것인지
- kCGImageSourceThumbnailMaxPixelSize: true
    - 썸네일 이미지의 최대 가로, 세로 크기(픽셀 단위)

여기서 중요한 옵션은 `kCGImageSourceShouldCacheImmediately`.
Core Graphics에게 썸네일 생성을 요청할 때 디코딩된 이미지 버퍼를 생성해야 한다고 알려주는 옵션이다. 따라서 디코딩에 따른 CPU를 사용하는 시점을 정확히 제어할 수 있게 된다.


3. 썸네일 생성

```swift
let downsampledImage = CGImageSourceCreateThumbnailAtIndex(imageSource, 0, downsampleOptions)!
return UIImage(cgImage: downsampledImage)
```

반환되는 CGImage인 썸네일을 생성한다. 결과를 UIImage로 래핑하여 반환한다.


4. 결과

![](https://i.imgur.com/5SVuF3J.png)

3,000 x 2,000 픽셀의 이미지를 최적화를 거치지 않고 UIImageView에 할당하면 31.5MB를 사용한다. 다운샘플링을 사용하여 이미지를 최적화하면 18.4MB로 줄일 수 있다.

![](https://i.imgur.com/KjTsGMH.png)

특히 화면의 작은 공간에서 큰 이미지를 많이 로드해야 하는 앨범 앱 같은 경우 이러한 메모리 문제가 더욱 복잡하다. 

![](https://i.imgur.com/SLVnKLl.png)

CollectionView의 셀 내부에서 이미지를 표현한다고 가정해보자. 보여질 이미지의 크기가 작기 때문에 셀 내부에서 다운샘플링을 통해 이미지 썸네일을 만들고 이것을 표시할 것이다.

![](https://i.imgur.com/MG8rcer.png)

그러나 이 방법은 CPU 관점에서 좋지 못하다. 새로운 행이 화면에 보여질 때 CPU의 사용량이 급증하는 것을 볼 수 있다.

셀의 ImageView에 프레임 버퍼가 업데이트되고 디스플레이 하드웨어가 곧 새 프레임을 얻어 화면을 업데이트한다면 깔끔한 화면을 볼 수 있지만, CollectionView에 셀이 전달되기 전에 이미지의 디코딩이 오래걸리거나 이미지가 더 이상 쓸모없게 되어버리는 경우가 발생할 수 있다.

![](https://i.imgur.com/WZ5WWOz.png)

이런 불규칙적인 CPU 사용량은 사용자 경험과 배터리 수명에도 좋지 못하다.
따라서 균일한 CPU 사용량을 유지하는 방법은 두 가지가 있다.

1. 첫 번째는 Prefetching.
필요한 이미지를 미리 디코딩하여 CPU 사용량을 분산시킬 수 있다.
2. 두 번째는 백그라운드에서 디코딩/다운샘플링을 시도하는 것


![](https://i.imgur.com/wk5WFdG.png)

prefetch 메서드를 구현하였다. 글로벌 큐를 이용해 다운샘플링 함수를 호출할 수 있지만 이 방법은 Thread Explosion을 유발하는 원인이 될 수 있기 때문에 글로벌 큐 대신 직렬 큐를 사용한다.

![](https://i.imgur.com/X0JeFLH.png)

이미지 소스는 네트워킹 외에도 가져오는 방법이 다양한데, 앱에서 어떤 이미지를 지원한다면 에셋을. 사용하는 것을 권장하고 있다.
이미지 에셋은 이름/특성 기반으로 조회하기 때문에 매우 빠르고 버퍼 캐싱 관리와 장치별 thinning과 같은 기능들을 포함하고 있다.

![](https://i.imgur.com/JF3QHoW.png)

벡터 아트워크는 iOS 11에서 도입된 기능으로, 벡터 데이터를 보존하여 이미지가 기본 크기보다 크거나 작은 ImageView에서 렌더링될 때 흐려지지 않는다.
이미지 에셋 인스펙터에서 Vector Artwork 체크박스를 체크하여 사용할 수 있다.

![](https://i.imgur.com/YqgnorE.png)

벡터 아트워크는 디코딩 대신에 래스터화 라는 과정을 거친다. 벡터 데이터를 불러와 프레임 버퍼에 복사할 수 있는 비트맵 데이터로 변환한다.

![](https://i.imgur.com/07mmi94.png)

만약 모든 벡터 아트워크에 대해 이 작업을 수행한다면 또 다시 CPU와 메모리 사용량에 대한 고민을 마주하게 될 것이다. 
Xcode에는 이러한 작업에 대해 적당한 크기로 래스터화된 과정을 생성하고 이것을 에셋 카탈로그에 저장한다. 따라서 매번 벡터 아트워크를 비트맵으로 변환하는 대신 에셋에 저장된 이미지를 디코딩하고 프레임 버퍼에 렌더링하여 사용한다.

이런 최적화를 통해 이미지가 그려질 때마다가 아닌 컴파일 시간에 아트워크를 래스터화하여 CPU 히트를 최적화한다.

## Custom drawing with UIKit

경우에 따라서 애플리케이션이 런타임에 컨텐츠를 그리기도 한다.

![](https://i.imgur.com/CQKGYBn.png)

아까와 마찬가지로 이번에는 이 화면을 구성한다고 가정하자.
UIButton은 Live Photo를 활성화/비활성화 하는 이 버튼 스타일을 지원하지 않는다.

![](https://i.imgur.com/Sy4L9SV.png)

UIView를 하위 클래스로 만들고 draw 메서드를 재정의하여 노란색 roundRect와 일부 텍스트 및 아이콘 이미지를 그려 Custom View를 구현할 수 있지만 이 방식을 권장하지 않는다.



![](https://i.imgur.com/PpYpXQl.png)

그 이유를 UIImageView와 비교하면서 알아보자.
ImageView는 디코딩된 이미지 버퍼를 생성하도록 이미지에 요청하면 디코딩된 이미지를 CALayer로 넘겨 해당 레이어의 컨텐츠로 사용한다.

Custom View는 이미지 버퍼를 생성해야 할 레이어는 draw 메서드의 내용을 갖고 있는다. 그리고 뷰는 draw 메서드를 실행하고 이미지 버퍼의 내용을 채운다. 그런 다음 디스플레이 하드웨어의 요청에 따라 프레임 버퍼에 복사된다.

Custom View의 비용이 UIImageView보다 더 큰데, 그 이유는 백업 저장소(Backing Store)에 있다.

![](https://i.imgur.com/THGYs96.png)

CALayer에 연결된 이미지 버퍼의 백업 저장소의 크기는 우리가 표시해야 하는 View의 사이즈에 비례한다.
iOS 12에서 추가된 새로운 최적화 기능은 백업 저장소의 요소 크기가 색상 컨텐츠 사용 여부(표준 색상, SRGB 색상)에 따라 동적으로 증가한다는 것이다.

![](https://i.imgur.com/SoBsQtS.png)

백업 저장소의 사용을 줄이는 방법은 다음과 같다.
- 큰 뷰를 더 작은 하위 뷰로 리팩토링한다.
- draw 메서드의 오버라이딩을 줄이거나 제거한다.
- 이미지 데이터의 중복 복사본을 제거한다.
- 백업 저장소가 필요하지 않은 UIView의 최적화된 속성을 사용한다.

![](https://i.imgur.com/GpW0Q7H.png)

draw 메서드를 재정의하면 CALayer와 함께 사용할 백업 저장소를 생성해야 하므로 커스텀 드로잉의 대안으로 다음을 선택할 수 있다.
- UIView의 일부 속성들은 여전히 사용할 수 있다.
- 예를 들어 패턴이 없는 단색의 backgroundColor는 백업 저장소를 만들 필요 없이 적용된다.
- 배경색으로 패턴을 만드는 대신 UIImageView를 사용한다.

![](https://i.imgur.com/FLZsKCb.png)

또한 Masking Views를 사용할 때 백업 저장소를 유의해야 한다.
- UIView.maskView, CALayer.maskLayer는 해당 mask를 저장하기 위해 추가 할당이 발생한다.
- 반면 CALayer의 cornerRadius는 추가 메모리 할당 없이 잘린 모서리를 얻을 수 있다.

![](https://i.imgur.com/mxyxTTK.png)

마지막으로 중복된 이미지 데이터를 제거하기 위한 방법으로 다음이 있다.
- UIImageView는 렌더링 과정에서 직접 프레임 버퍼에 단색으로 채색할 수 있다.
- `UIImage.withRenderingMode(_:)`
- UIImageView의 tintColor를 사용하면 이미지를 렌더링하려는 색상으로 설정할 수 있다.

![](https://i.imgur.com/qIALFXU.png)

UILabel에도 마찬가지로 동일한 기법의 최적화가 적용되어 있다. 단색화를 통해 컬러 텍스트나 이모지를 표시할 때보다 75% 적게 사용할 수 있다.

![](https://i.imgur.com/Gz3dYXr.png)

메모리의 이미지 버퍼에 저장된 아트워크를 오프스크린으로 렌더링하고 싶다면 UIKit에서 제공하는 `UIGraphicsImageRenderer`를 사용한다.

## Advanced CPU and GPU techniques

iOS에서 제공하는 고급 CPU 및 GPU 기술을 앱에서 사용하는 방법

![](https://i.imgur.com/sJjMKtM.png)

1. 이미지에 대해 실시간으로 많은 고급 처리가 필요한 경우 **Core Image**를 사용한다.
    - Core Image는 이미지 처리를 위한 레시피를 생성하고 처리 동작을 CPU 또는 GPU에서 처리하는 프레임워크이다.

2. CIImage에서 UIImage를 만들고 이를 UIImageView에 전달하면 GPU에서 실행한다. 
    - 이것으로 얻을 수 있는 효과는 효율성과 앱에서 다른 작업을 수행할 수 있도록 CPU를 자유롭게 유지한다.
    
![](https://i.imgur.com/gnZrj8P.png)

3. 그래픽 컨텐츠를 처리하고 렌더링하는 고급 프레임워크
    - Metal, Vision, Accelerate 등
    - 여기에서 공통적으로 사용하는 데이터 타입: CVPixelBuffer (CPU나 GPU에서 사용할 수 있는 버퍼)

4. CPU와 GPU 사이에서 데이터를 옮길 때 작업을 교환하지 않아야 한다.
    - 두 작업이 병렬로 실행될 수 있다.

