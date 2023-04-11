# What's new in the Photos picker

[영상 보러가기](https://developer.apple.com/videos/play/wwdc2022/10023)

![](https://i.imgur.com/tYcHD73.png)

> System Photo Picker의 개선사항에 대해 알아보자!

![](https://i.imgur.com/10tZcDw.png)

(System) Photo Picker는 앱이 iOS에서 사진과 영상, 앨범에 접근하기 위한 도구이다. 앱의 프로세스 밖에서 작업이 이루어지므로, 앱에서 라이브러리 접근을 요청할 필요가 없다.

![](https://i.imgur.com/XyA7blt.png)

Photo Picker의 장점은, 직관적인 UI와 사용하기 쉬운 API(PHPicker API를 가리킨다.)

### New features

#### 1. Filters

![](https://i.imgur.com/3Zig1oz.png)

기존 PHPickerFilter는 세 가지 필터를 지원했었음.
- images / videos / livePhotos

![](https://i.imgur.com/ILKkHAI.png)

기존 세 가지 필터 외에도 다양한 필터가 추가되었다.
또한 `playbackStyle(_:)`은 새로운 필터로 커스터마이징할 수 있다.
`cinematicVideos`, `depthEffectPhotos`, `bursts`를 제외한 모든 필터들이 자동으로 백포팅을 지원한다.

![](https://i.imgur.com/59XYruK.png)

복합 필터의 경우 기존의 any 외에도 all과 not이 추가되었다.
- all: 모든 조건이 true인 결과만 출력
- not: 모든 조건이 false인 결과만 출력

![](https://i.imgur.com/aw4guAp.png)

필터 사용 예


#### 2. SheetPresentaion

![](https://i.imgur.com/KcIuB1I.png)

iOS 15에서는 UISheetPresentationController가 발표되었고 이를 사용하여 Picker를 위와 같이 half-height mode로 띄울 수 있었다.

그러나 어떤 경우에는 선택된 사진이나 영상의 변화가 Picker에도 반영되어야 할 필요가 있다.

![](https://i.imgur.com/PdGMcAQ.png)

새롭게 추가된 API중 일부를 소개하면, 
- deselectAssets(withIdentifiers:)
    - 해당 Asset을 Picker 상에서 선택 해제
- moveAssets(withIdentifier:, afterAssetWithIdentifier:)
    - 위치 이동 등 재정렬 기능
등이 있다.

### Platform support

![](https://i.imgur.com/ELi9kdC.png)

이번 업데이트로 iOS와 iPadOS 뿐만 아니라 macOS와 watchOS에서도 Photo Picker를 지원하기 시작했다.

#### 1. iPadOS

![](https://i.imgur.com/REphBhJ.png)

새로운 iPad UI 디자인을 도입했다.
비교적 큰 iPad 화면을 활용하기 위해 사이드바가 추가되었고, 만약 Split View를 사용하여 공간이 부족해지면 기존의 소형 Picker UI로 되돌아간다.

#### 2. macOS

![](https://i.imgur.com/TKIAFEe.jpg)

macOS 스타일과 어울리는 사이드바가 있고 다중 선택과 유동적인 줌인 기능, 그리고 사람 장소 등을 활용하는 고성능의 검색 기능을 지원한다.

또한 NSOpenPanel에서도 이용이 가능하다.

#### 3. watchOS

![](https://i.imgur.com/y8MxbnQ.png)

iOS에서의 Picker와 유사하지만 더 작은 화면에 최적화되어 있다.
Grid와 Collection 두 가지 모드를 지원하며 Picker의 선택 순서와 선택 한계도 명시할 수 있다.

그러나 iOS나 macOS와 다른 점은
- watchOS에서는 only Images만 표시한다.
- 이미지가 500개를 초과하면 최근 순으로 500개까지만 표시한다.


### Frameworks

![](https://i.imgur.com/pzHh20r.png)

PHPickerViewController는 UIKit 기반의 API이다.
이 새로운 Picker API는 이제 AppKit과 SwiftUI에서도 사용이 가능하다. 

![](https://i.imgur.com/4J8czKx.png)

SwiftUI - PhotosPicker API를 사용한다.

![](https://i.imgur.com/SMwVpGR.png)

만약 사진이나 비디오가 iCloud와 같이 인터넷에서 다운로드를 받아야 하는 경우, 일부 데이터가 즉시 로드되지 않는 문제가 발생할 수 있다.
이 때 각각 로딩 UI를 보여주는 것을 권장한다.

PhotosPicker는 **Transferable**이라는 프로토콜을 사용한다.

![](https://i.imgur.com/McJzqU4.png)

Transferable
> 채택한 타입이 전송 API와 상호 작용하기 위해 드래그 앤 드롭 또는 복사, 붙여넣기와 같은 기능을 포함하는 프로토콜


(👉 [Transferable](https://developer.apple.com/documentation/coretransferable/transferable))

![](https://i.imgur.com/penvCR8.png)

Transferable을 통해 직접 SwiftUI Image를 불러올 수 있는데 보통 Transferable을 준수하는 모델 객체를 정의해서 해당 기능들을 재정의하여 불러오는 데이터 타입을 제어하는 형태로 사용한다.

![](https://i.imgur.com/HarAYxT.png)

용량이 큰 사진이나 동영상 여러 개를 한 번에 불러오는 화면을 가정해보자. 메모리에서 모든 걸 동시에 로딩하는 것은 적절한 방법이 아니다. 
이런 오류를 피하기 위해, FileTransferRepresentation을 사용하여 선택한 것을 파일로 로딩하여 메모리 사용량을 줄일 수 있다.

주의할 것은 파일 로딩 방법을 사용했을 때 해당 파일의 수명은 앱이 담당해야 한다는 것으로, 앱 디렉토리에 파일들을 복사하여 사용하고 사용이 끝나면 해당 파일들을 삭제해야 한다.

### 📱 Demo

![](https://i.imgur.com/DJYYvJd.png)

오늘의 데모 앱은 계정의 프로필을 표시하는 앱
편집 버튼을 통해 프로필 이미지를 변경하는 기능을 구현할 것이다.

![](https://i.imgur.com/co3YrFe.png)

ProfileImage View는 imageState에 반응한다.
따라서 Picker에서 이미지를 선택하면 imageState를 업데이트하도록 만들어주면 된다.

![](https://i.imgur.com/QOIiTFH.jpg)

View Model에서 새로운 상태인 imageSelection을 추가한다.
```swift
@Published var imageSelection: PhotosPickerItem?
```

그 후 ProfileImage View로 돌아가 Picker를 띄우는 오버레이 버튼을 만들어준다.
해당 코드를 살펴보면,
```swift
.overlay(alignment: .bottomTrailing) {
    PhotosPicker(selection: $viewModel.imageSelection, matching: .images, photoLibrary: .shared()) {
        ...
    }
    .buttonStyle(.borderless)
}
```
- PhotosPicker에 imageSelection으로 selection 바인딩을 부여했다.
- 이미지만 보여주기 위해 matching에 PHPickerFilter를 사용하였다.

![](https://i.imgur.com/7kcCUW5.jpg)

이미지를 탭 하면 Picker는 자동으로 닫히지만, 프로필 이미지는 아직 업데이트되지 않는다. 그 이유는 imageSelection을 imageState와 연결짓지 않았기 때문.

![](https://i.imgur.com/BVK8v2e.png)

프로퍼티 옵저버를 추가하여 imageSelection에 따라 imageState의 값이 결정되도록 만들어 준 모습이다.
현재 상태에서는 아직 loadTransferable을 구현하지 않았기 때문에 컴파일러 오류가 발생한다.

![](https://i.imgur.com/joa2eT3.png)

loadTransferable의 구현은 imageSelection의 completion Handler에 imageState를 업데이트하는 코드를 작성한다.

![](https://i.imgur.com/2t3eido.jpg)

예상한 대로 잘 돌아가는 모습.

![](https://i.imgur.com/FfOQABp.png)

이 코드는 macOS에서도 똑같이 돌아간다. 

watchOS에서도 마찬가지로 동일한 코드로 실행이 가능하지만, 만약 모든 플랫폼에서 실행되는 앱을 만든다면 watchOS 환경에 대해 몇 가지 주의해야 한다.

![](https://i.imgur.com/womaXs9.png)

watchOS를 위한 특별한 고려사항
- watchOS는 간단한 flow와 가벼운 상호 작용을 위해 만들어졌다.
- 이미지는 기기의 크기에 맞게 스케일링된다.

