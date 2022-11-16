# Advances in UICollectionView

![스크린샷 2022-11-15 오후 11 05 05](https://user-images.githubusercontent.com/63997044/201956223-a2d5fe88-3eff-4ec8-b72d-04b1c27df01f.png)

> **Overview**

iOS 14에서 추가된 UICollectionView의 새 기능들. 
- 확장 및 축소가 가능한 아웃라인을 생성하기 위해 Diffable Data Source와 함께 Section Snapshot을 사용하는 방법을 보여준다.
- Compositional Layout으로 List를 작성하는 방법을 소개한다.
- 셀을 대기열에서 빼내고 콘텐츠 및 스타일을 구성하는 최신 기술을 소개한다.

___

UICollectionView를 구성하는 API는 데이터, 레이아웃, 프레젠테이션의 세 범주로 구분할 수 있다. UICollectionView의 참신한 개념 중 하나는 "어디"와 "무엇"의 분리이다.
즉 레이아웃에서 어디서 렌더링될 것인지와 어떤 데이터인가를 분리하는 개념은 UICollectionView를 매우 유연하게 만드는 핵심 개념이었다.

### iOS 6

![스크린샷 2022-11-15 오후 11 05 43](https://user-images.githubusercontent.com/63997044/201956254-246660fe-fcd8-459c-a896-8f3639362865.png)

iOS 6에서 UICollectionView가 처음 공개되었을 때 
- 데이터: indexPath 기반의 프로토콜인 UICollectionViewDataSource에 의해 관리
- 레이아웃: UICollectionViewLayout과 콘크리트 클래스인 UICollectionViewFlowLayout만 존재
- 프레젠테이션: UICollectionViewCell, UICollectionReusableView을 제공


### iOS 13
![스크린샷 2022-11-15 오후 11 05 43](https://user-images.githubusercontent.com/63997044/201956254-246660fe-fcd8-459c-a896-8f3639362865.png)

iOS 13에서는 Diffable Data Source 그리고 Compositional Layout이 각각 데이터, 레이아웃 영역에 새로운 요소로 도입되었다.


### iOS 14
![스크린샷 2022-11-15 오후 11 15 32](https://user-images.githubusercontent.com/63997044/201956833-699f2289-dfd7-464f-9788-03425df6c014.png)

iOS 14에서는 세 영역 모두에 새로운 요소를 도입했다.


## Section Snapshot
![스크린샷 2022-11-15 오후 11 20 47](https://user-images.githubusercontent.com/63997044/201956846-511694bf-6d89-4282-8cbc-22b507bd936b.png)

iOS 14에서는 Section Snapshot이라는 새로운 snapshot 타입이 추가되었다. 이름 그대로 UICollectionView의 단일 섹션에 대한 데이터를 캡슐화한다. 이로 인해 얻을 수 있는 장점은 다음과 같다.
- Data Source를 섹션 크기의 청크로 구성할 수 있다.
- 계층적으로 데이터 모델링을 할 수 있다.

## Lists

UITableView와 동일한 스타일의 섹션을 UICollectionView에 추가할 수 있다.

![스크린샷 2022-11-15 오후 11 58 31](https://user-images.githubusercontent.com/63997044/201956854-fa403d97-54ce-4977-bfca-3685fe089aae.png)
Compositional Layout으로 List-style 레이아웃을 만들기 위해 필요한 코드는 간단하다.

![스크린샷 2022-11-16 오전 12 00 54](https://user-images.githubusercontent.com/63997044/201957399-85096672-8994-47c0-9894-99e90116074d.png)
지금 예시로 보여주는 것은 `.insetGroup`이지만, 더 많은 종류가 있고 자세한 내용은 "Lists in UICollectionView"에서 다룰 것이다.

## Modern Cells
![스크린샷 2022-11-16 오전 12 05 41](https://user-images.githubusercontent.com/63997044/201957557-652d1f7a-afc3-4296-b271-8945cc36d689.png)

iOS 14에서는 셀을 구성하는 새로운 방법인 **Cell Registration**이 도입되었다.
- View Model에서 셀을 설정하는 방법
- 셀 클래스와 재사용 식별자를 연결하는 단계를 제거함

기존의 셀 등록 방법
```swift
collectionView.register(ListCollectionViewCell.classForCoder(), forCellWithReuseIdentifier: "listCell")
```

새로운 셀 등록 방법
```swift
struct Emoji: Hashable {
    enum Category: CaseIterable, CustomStringConvertible {
        case recents, smileys, nature, food, activities, travel, objects, symbols
    }
    
    let text: String
    let title: String
    let category: Category
    private let identifier = UUID()
}
```

```swift
// list cell
let cellRegistration = UICollectionView.CellRegistration<UICollectionViewListCell, Emoji> { (cell, indexPath, emoji) in
    var contentConfiguration = UIListContentConfiguration.valueCell()
    contentConfiguration.text = emoji.text
    contentConfiguration.secondaryText = String(describing: emoji.category)
    cell.contentConfiguration = contentConfiguration

    cell.accessories = [.disclosureIndicator()]
}
        
// data source
dataSource = UICollectionViewDiffableDataSource<Section, Item>(collectionView: collectionView) {
    (collectionView, indexPath, item) -> UICollectionViewCell? in
    return collectionView.dequeueConfiguredReusableCell(using: cellRegistration, for: indexPath, item: item.emoji)
}
```

- 새로운 셀이 생성될 때마다 클로저를 호출한다. 클로저 내부에서는 ViewModel의 content를 구성하고 `dequeueConfiguredReusableCell`을 통해 셀 등록을 사용한다.


### Cell Content Configuration
![스크린샷 2022-11-16 오전 12 13 51](https://user-images.githubusercontent.com/63997044/201957581-fcabb81c-4fb6-40f3-b55b-a2ce8bec1737.png)

UITableView의 표준 셀 타입들(`valueCell()`, `subtitleCell()` 등)에 해당하는 레이아웃을 제공한다.
이러한 Content Configuration은 아주 가볍고 콘텐츠의 모양을 간단히 설명할 수 있게 해준다. 또한 프레임워크는 모든 레이아웃 고려 사항들을 처리하고 성능 최적화한다.

### Background Configuration

Content Configuration과 유사하지만, 셀의 색상, 테두리, 배경 등과 같은 속성을 조절하여 적용하는 방법이다. 이 방법을 통해 기존의 방법인 셀의 textLabel과 같은 속성을 더 이상 사용하지 않게 되었다.
