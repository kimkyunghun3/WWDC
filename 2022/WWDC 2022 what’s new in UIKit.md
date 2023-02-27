# WWDC 2022 what’s new in UIKit

![](https://i.imgur.com/sPfvtBH.png)

# iOS 16의 UIKit
SwiftUI를 출시?했음에도 UIKit은 앱의 프레임워크이며 iOS 16 서에 새로운 기능을 지원하도록 업데이트 되었다.

해당 영상에는 UI를 개선하고, API를 개선하고, UIKit과 SwiftUI를 함께 사용하는 방법에대해 이야기 하고자 한다.

## ㄱ. iOS 16의 새로운 기능들

### 1. NavigationBar 
![](https://i.imgur.com/tybOMoQ.png)


데스크탑 수준의 ToolBar 기능을 지원하도록 업데이트 되었다.

Browser, Editor 라는 두가지 스타일을 도입했다.

- Browser 는 웹과 문서 브라우저와 같은 히스토리나 폴더 구조를 이용하여 탐색하기 적합하다.

- Editor 는 문서 편집에 중점을둔 인터페이스다.

![](https://i.imgur.com/whFgX72.png)

네비게이션바에 버튼을 추가할 수 있으며, 버튼들의 집합은 중앙에 배치된다.
(네비게이션바 타이틀 부분에 버튼들을 넣을수 있는것 같아요!)

![](https://i.imgur.com/4VAJxBP.png)

우측의 메뉴(... 요렇게 생긴거)를 탭하면 popup창을 띄워 중앙의 버튼들을 제거하거나, 추가, 위치를 변경 할수 있도록 했다.

![](https://i.imgur.com/IjS3NJH.png)

뷰의 사이즈가 작아져 버튼들을 모두 표시할수 없는경우 메뉴 popup에 해당 버튼들의 기능들이 추가되게 된다.

![](https://i.imgur.com/RYF2x10.png)

새로운 네비게이션 스타일에서는 타이틀을 탭할경우 다음과 같은 몇가지 기능을 지원한다.
1. Duplicate
2. Move
3. Rename
4. export
5. print

이런 기능들은 delegate 메서드를 구현하면 자동으로 표시되어 사용할 수 있게된다.(델리게이트를 준수하는 클래스가 해당 기능을 구현하면 알아서 사용할수 있게끔 표시해주나봅니다!)
또한 delegate에 의해 구현된 기능 말고도 커스텀기능을 추가하는것이 가능하다.

![](https://i.imgur.com/cO0mu5N.png)

또한 Mac Catalyst로 빌드한 앱은 추가로 코드를 작성하지 않아도 NSToolbar와 원할하게 통합되어 업데이트된 ToolBar를 활용할 수 있게되었다.

### 2. New ways to manipulate text
텍스트를 조작하는 새로운 방법

iOS16은 여러앱에 걸쳐 텍스트를 편집하는 방법을 도입했다.

![](https://i.imgur.com/HSlJcVX.png)

1. 찾기, 바꾸기(Find and replace)
- 사진이나 캘린더 등등 인앱 에서 에서 사용되는 Search와는 다른것.(개발자가 만든 search 기능과는 다른것?)
- 찾기와 바꾸기는 Text작업을 하기위해 설계되었다.
- UITextView, WKWebView UIKit으로 구현된 뷰에서 플래그를 설정 하면 해당 기능을 활성화 할 수 있다.

![](https://i.imgur.com/5yd7lKK.png)

2. 편집 메뉴
- 입력 방법에 따라 다르게 보인다.
- 터치 상호작용 에서는 훨씬 상호작용적으로 재디자인된 Menu를 갖게 되었다.
- 포인터를 사용하면 모든 기능이 들어있는 context Menu가 등장한다.
- 이러한 기능을 제공하기위해 UIMenuController를 대체하는 UIEditMenuInteraction을 도입했다.

![](https://i.imgur.com/mDIaCYK.png)

- TextView의 메뉴에 기능(action)을 삽입 하는 새로운 API도 생겼다.(해당 내용은 Adopt desktop class editing interactions 세션을 확인 하세요!)

### 3. Materials in Sidebar(아이패드에 있는 기능인가 봅니다!)

![](https://i.imgur.com/EquBDKE.png)

iOS 16 에서 Slide over 모드의 사이드바는 추가 코드를 작성하지 않아도 활성화 된다.

이와 같은 목표를 달성하기위해 유저대신 몇가지 private views를 관리하도록 했다.(공개 안하고 내부적으로 뭔가 건들였다는거 같아요!)

### 4. iOS 16의 새로운 기능들 요약

![](https://i.imgur.com/f5SISsb.png)

지금까지는 새로운 기능들의 기본적인 내용들만 다루었으며 해당 부분들을 더 알아보기위해서는 다음의 세션들을 통해 확인할 수 있다.

1. Meet desktop class iPad
2. Build a desktop class iPad app

## ㄴ. Control enhancements
새롭게 추가한 control을 소개하고, UIPageControl의 개선사항에 대해 얘기해보자.

### 1. UICalendarView
![](https://i.imgur.com/RWpIfZt.png)

UIDatePicker의 inline calendar style을 독립적인 요소로 UICalendarView의 형태로 사용할 수 있다.

UICalendarView는 다양한 선택 동작을 지원하며, 단일 날짜(한개만 선택?)와 여러날짜를 선택하는것이 가능하며, 일부 날짜를 제한하는 기능도 있다.
각 날짜를 장식(예쁘게?)할 수도있다.

UICalendarView와 UIDatePicker의 중요 차이점중 하나는 날짜를 표현할때 NSDate가 아닌 NSDateComponents로 나타낸다.

NSDateComponents는 매우 유연하므로 어떤 달력을 사용할것인지 명시해야한다.

![](https://i.imgur.com/DkNdOts.png)

달력을 그레고리력으로 설정해야한다면 gregorian이라고 명시 해주어야한다.

1. Configure calendar view(UICalendarView 구현하기)

![](https://i.imgur.com/9X8lOFB.png)

위에서 설명한 캘린더뷰 처럼 설정하려면 UICalendarView 를 생성하고 delegate를 설정한다.
UICalendarView의 calendar 프로퍼티에 설정하고자 하는 Calendar를 할당해준다!(예시에서는 그레고리력 달력을 넣었읍니다.)

다음으로는 multi Date(날짜 여러개선택하기)를 설정하기 위한 방법이다.
![](https://i.imgur.com/nVdIrMf.png)

UICalendarSelectionMultiDate객체를 만들고 selectedDates 프로퍼티를 유저(개발자)의 data model에 있는 기존 날짜를 할당하여 캘린더 뷰에 표시되도록 할 수 있다.

![](https://i.imgur.com/yE4P9nB.png)

캘린더의 단일 날짜 선택을 제한하기 위해 calendar의 delegate 메서드 multiDateSelection을 구현하여 날짜를 제어 할 수 있다.

선택할 수 없는 날짜는 CalendarView에서 회색으로 표기된다.

2. Configure decorations(날짜 장식하기)

![](https://i.imgur.com/F6FjweL.png)


개별 날짜에 decorations(장식)을 추가하고싶다면 Calendar delegate 메서드를 구현하면 된다.

색상을 커스텀 할 수 있으며, 필요하다면 커스텀 뷰를 사용 할 수 도 있다.

커스텀뷰를 사용할 경우 상호작용을 허용하지 않으며 공간에 고정되게 된다.

### 2. UIPageControl

개선된 UIPageControl.

![](https://i.imgur.com/CJldcHt.png)

현재 페이지에 커스텀 Indicator이미지를 추가하여 어떤 페이지를 보여주고 있는지 나타낼수 있게 되었다.(기존에 사용해본적은 없지만 해당 기능 넣으려면 만들어서 넣어줬어야 했나보네요!?)

![](https://i.imgur.com/wuEoLFe.png)

다음은 vertical PageControl을 설정하는 예시 코드입니다.

현재선택된 것과 선택되지 않은 페이지에서 이를 알려주는 인디케이터의 이미지가 다른것을 확인할 수 있다.

해당 기능을 사용하기위해 direction 속성을 top to bottom 으로 설정하면 된다.(간편!)

### 3. UIPasteControl

Apple은 사용자의 개인정보를 지키기위해 노력하며 iOS 15에서는 paste interfaces를 통하지 않고 pasteBoard에 접근할경우 이를 배너를통해 알려주었습니다.(상단의 토스트뷰?)

![](https://i.imgur.com/xXl4pZM.png)

iOS 16 에서는 배너대신 pasteBoard의 사용허가를 묻는 Alert을 띄워 묻는 방식으로 변경되었습니다.

![](https://i.imgur.com/umtWKpl.png)
기존 custom paste control을 사용했다면 새로운 UIPasteControl로 대체할 수 있으며, 이것은 UIButton과 모습, 동작이 비슷합니다.

![](https://i.imgur.com/YkMufMB.png)

## ㄷ. API refinements(API 개선사항)

### 1. Customizing Sheets

iOS 15 에서 Sheet에 detent를 추가하여 유연하고 역동적인 UI를 구축할 수 있었습니다.
iOS 16 에서는 커스텀 detents를 지원하여 sheet를 마음대로 조절할 수 있습니다.

![](https://i.imgur.com/AyD1jIV.png)

![](https://i.imgur.com/YYWYq6L.png)

해당 기능을 사용하려면 .custom detent 를 사용하여 관련된 block에서 sheet의 높이를 지정할 수 있습니다.

![](https://i.imgur.com/P8BlUJs.png)

고정값을 사용해도되고, detent의 최대 높이값을 사용해도됩니다!

![](https://i.imgur.com/Z24579d.png)

다른 API를 참조해야할 경우 custom detent에 식별자를 주어 활용할 수 있습니다.

![](https://i.imgur.com/OH1t8nB.png)

custom detent를 사용할경우 bottom safe area inset을 고려하지 않습니다.

![](https://i.imgur.com/syzgG76.png)

해당 기능들을 자세히 알고자 하는경우는 아래의 다음의 세션을 참고해주세요!

![](https://i.imgur.com/0ouRnDR.png)

### 2. SF Symbol

SF Symbol에서 4가지 랜더링 모드를 지원합니다.
1. Monochrome
2. Multicolor
3. Hierarchical
4. Palette

![](https://i.imgur.com/nNdsYj5.png)

기존 System Symbol이 렌더링 모드 지정이 안된경우 Monochrome을 기본값으로 지정되었었는데, 이제는 자체적으로 가지고 있는 기본값으로 렌더링 된다.

![](https://i.imgur.com/Lwfx1wS.png)

Monochrome을 명시적으로 지정하기위해선 다음 API를 사용하면 된다.

```swift
UIImage.SymbolConfiguration.preferringMonochorme()
```

![](https://i.imgur.com/bkvbthn.png)

variable symbol을 지원하는 경우 0과 1사이의 값을 지정해, 해당 값을 기반으로 다양한 variation을 제공한다.

다음은 variable symbol 사용 예시이다.

![](https://i.imgur.com/ygoev5m.png)

![](https://i.imgur.com/yFRcLft.png)

이는 다른 렌더링 모드와 같이 사용가능하며 palette 모드로 꾸며줄 수 있다.

![](https://i.imgur.com/PkEIvGY.png)

Custom variable symbol이 궁금하다면 다음의 세션을 통해 알아 볼 수 있다.

![](https://i.imgur.com/tllScIp.png)

### 3. Swift Concurrency and Sendable

![](https://i.imgur.com/oEUQMOI.png)

UIKit은 새로운 Concurrency 기능에 맞춰 개선되었다.
UIImage와 UIColor같은 immutable(불변) type을 MainActor와 customActor 사이에서 컴파일러의 경고 없이 전송이 가능하다.

(Sendable 가능한것이 추가되었다 라고 표현하네요!)

![](https://i.imgur.com/zJIHx1S.png)

위의 예시를 보면 Processor라는 CostomActor와 ImageViewer 라는 ViewController가 있는데 이는 MainActor에 작성되어 있다.

![](https://i.imgur.com/N7Cr12O.png)

sendImageForProcessing 메서드 내부에 ImageViewer 가 Processor Actor에 이미지를 전달해 가공(반짝이나 무지개 추가)할 수 있게 된다.

![](https://i.imgur.com/gz6F3CL.png)

UIImage는 Immutable type으로 안전하며 Processor가 새로운 복사본을 만들어 반짝이나 무지개 같은 효과를 추가할 수 있다.

반면에 UIBezierPath는 mutable 하기 떄문에 Sendable 할 수 없다.

iOS 15 까지는 이를 컴파일러가 확인할 수 없어 Warning으로 표시되지 않았지만 

iOS 16에서는 다음과같이 컴파일러가 확인해 Warning으로 알려줄 수 있게 되었다.

![](https://i.imgur.com/H6nEnDc.png)

Sendable 과 Swift Concurrency 에 관해 더 알고 싶다면 아래의 세션을 통해 알 수 있다.

![](https://i.imgur.com/L1qRyIM.png)

### 4. Stage Manager

![](https://i.imgur.com/EAuebx5.png)

iOS 16 은 외부 디스플레이를 위한 기능을 지원한다.
해당 기능을 사용하기 위해 업데이트 하지않아도 된다.(UIScreen API를 사용하는 경우는 제외)

Stage Manager가 생겨남으로 앱이 더이상 main screen에 있다고 가정할 수 없기때문에(외부 디스플레이에 앱의 화면이 띄워져있을수 있기 때문에) UIScreen.main, UIScreen 업데이트 관련 노티피케이션은 이제 deprecated 된다.

대신 traitCollection, UIScene API 를 사용하길 권장한다.

### 5. Self-resizing cells
UICollectionView와 UITableVIew를 위해 셀이 self resizing 되도록 업데이트 되었다.(기본값으로 바꼈데요!)

iOS 16에서는 Cell의 내부 콘텐츠가 변경될 때 자동으로 새로운 콘텐츠에 맞춰 셀의 크기를 조정하게 된다.

![](https://i.imgur.com/9eMtr2Z.png)

해당 기능은 기본값으로 활성화 되어있으며 UICollectionView혹은 UITableView 의 selfSizingInvalidation 속성을 제어할 수 있다.

![](https://i.imgur.com/9mjhDo1.png)

UIListContentConfiguration을 쓰는 경우 Configuration 변화에 따라 자동으로 요청한다.

![](https://i.imgur.com/25uN1tt.png)

그 외의 경우는 cell이나 contentView에 invalidateIntrinsicContentSize() 호출하면 된다.

고유의 크기를 무효화 하고 다음 레이아웃을 반영할 때 새로운 고유 크기를 반영하는 원리!

기본적으로 애니메이션이 적용되며 애니메이션을 끄고 싶은 경우 UIView.performWithoutAnimation 메서드 내부에서 invalidateIntrinsicContentSize 를 호출 하면 된다.(호출시 바로바로 하는건 아니고 모았다가 적절한 타임에 한대요!(coaleasing))

![](https://i.imgur.com/aVZSFy9.png)

오토레이아웃을 사용한 경우 .enabledIncludingConstraints 값을 사용하면 더 포괄적인 행위로 사용할 수 있다.
contentView 내부에서의 레이아웃 변화에 따라 자동으로 invalidateIntrinsicContentSize 호출하여 collectionView 혹은 tableView가 필요에 따라 resize할 수 있다.

## ㄹ. UIKit and SwiftUI
UIKit은 강력하고 유연하며 SwiftUI는 UI를 구현할 때 더풍성하게 표현할 수 있다.

하나의 앱에서 두개의 프레임 워크를 쉽게 적용할 수 있도록 업데이트 하였다.

### 1. UIHostingConfiguration
SwiftUI를 사용해 collectionView와 tableView의 셀 안에서 SwiftUI뷰를 그릴 수 있게 되었다.

![](https://i.imgur.com/4NbMY2A.png)

이것이 가능한 이유는 새로운 content configuration type 인 UIHostingConfiguration때문이다.

위의 코드한줄을 작성하면 셀 안에서 SwiftUI로 작성 할 수 있게된다.

![](https://i.imgur.com/OY484Ed.png)

## A Couple of Smal but Important Changes (중요 변경사항)

### UIDevice deprecations
UIDevice.name이 더 이상 유저의 커스텀 디바이스 명이 아닌 모델만을 반환한다.
커스텀 디바이스 명을 얻기 위해선 entitlement(권한)을 요구한다.

UIDevice.orientation을 설정하는 것은 더이상 지원하지 않는다.
UIViewController의 PreferredInterfaceOrientation API를 대신 써서 원하는 방향을 지정 해야한다.
