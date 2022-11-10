# Lists in UICollectionView

![](https://i.imgur.com/pAYJLFG.png)

이 다이어그램의 여러 부분에 대한 개별 비디오가 있으며 이 비디오에서는 List Configuration과 List Cell을 다룬다.

### UITableView-like appearance

iOS 14의 List는 UICollectionView에서 UITableView와 유사한 모양을 제공한다.

### Based on Compositional Layout

우리는 작년에 도입 한 Compositional Layout 위에 다시 빌드한다. 따라서 매우 유연하고 사용자 지정하기 좋다.

### Highly customizable

![](https://i.imgur.com/MjTyQD8.png)
![](https://i.imgur.com/LyCnibN.png)

최근에 사용한 모든 이모지를 맨 위 행에 가로 스크롤하여 표시하는 앱의 예를 볼 수 있다.
아래에는 모든 이모지를 그룹별로 정렬 하고 이 계층 구조가 내장되어 있다.
그리고 맨 아래에는 UITableView와 훨씬 비슷한 모양이 있다. 또한 각 셀을 스와이프하여 좋아하는 이모지를 표시 할 수도 있다. 여기에 표시되는 것은 List와 Compositional Layout의 조합을 사용하는 단일 컬렉션 뷰다.

### Optimized self sizing

특히 List에 대한 self-sizing 지원을 크게 향상시키고, UICollectionView에서 목록을 사용할 때 목록이 '자동 크기 조정'을 새로운 기본 동작으로 만들고 있다.
셀 높이를 수동으로 계산하는 것에 대해 더 이상 걱정할 필요가 없이 자동 레이아웃을 사용하여 셀을 만들면 컬렉션 뷰가 나머지를 처리한다.
수동 크기 조정이 필요한 경우 셀 하위 클래스에서 preferredLayoutAttributesFitting(_:)를 override하여 이를 수행 할 수 있다.

### List configuration

![](https://i.imgur.com/8ypDO9o.png)

콜렉션뷰에서 list를 작성하기 위해 레이아웃 측에서 필요한 유일한 새 유형이다.
UICollectionLayoutListConfiguration은 NSCollectionLayoutSection과 iOS 13에서 도입한 기존 구성 레이아웃 시스템의 일부인 UICollectionViewCompositionalLayout 위에 구축되어있다.
 
iOS 14에서는 UICollectionLayoutListConfiguration이라는 새로운 유형을 제공한다.
list configuration은 테이블뷰의 스타일(.plain, .grouped, .insetGrouped)과 같은 모양을 제공한다.
또한 콜렉션뷰의 List 전용 .sidebar, .sidebarPlain 라는 새로운 스타일을 사용하여 iPadOS 14에서 놀라운 다중 열 앱을 구축 할 수 있다.

![](https://i.imgur.com/8POve5u.png)

### Simple setup

![](https://i.imgur.com/CokQ3dN.png)

list를 작성하는 가장 쉬운 방법은 UICollectionLayoutListConfiguration을 작성하고 appearance를 제공 한 다음 이 구성을 사용하여 UICollectionViewCompositionalLayout을 작성하는 것이다.

### Per-section setup (섹션 별 setup)

![](https://i.imgur.com/xLtoG28.png)

구성은 동일하지만 이 구성을 사용하여 compositional layout 대신에 NSCollectionLayoutSection을 작성한다.
이 코드는 컴포지션 레이아웃의 기존 섹션 공급자 이니셜 라이저 내에서 사용될 수 있으며, 컬렉션 뷰의 모든 섹션에 대해 호출되어 특정 섹션에 고유 한 개별 레이아웃 정의를 반환 할 수 있다.
여기에 표시되는 내용은 앞에서 보여 드린 간단한 설정과 완전히 동일한 디자인으로 표시된다.
섹션별로 레이아웃을 사용자 정의 할 수 있다.

### Headers and Footers

UICollectionView의 머리글과 바닥 글은 명시 적으로 사용 가능해야 하며 이를 수행하는 두 가지 방법이 있습니다.

![](https://i.imgur.com/3LNDqz4.png)

### supplementary views 등록

이 뷰를 제공하는 가장 쉬운 방법은 diffable 데이터 소스에서 SupplementaryViewProvider를 사용하는 것이지만 UICollectionView 델리게이트에서 동등한 메소드를 구현할 수도 있다.

첫 셀 사용
헤더에만 사용할 수 있으며 headerMode를 .firstItemInSection으로 설정하여 사용할 수 있다.
이것은 콜렉션 뷰에서 이 특정 섹션의 첫 번째 셀을 헤더처럼 구성하도록 지정한다.
이 모드는 계층 적 데이터 구조와 새로운 섹션 스냅 샷 API를 사용할 때 권장되는 모드다.

이 작업이 어떻게 진행되는지는 여기에서 확인할 수 있다.

Advances in diffable data sources - WWDC 2020 - Videos - Apple Developer

이 모드를 사용할 때는 데이터 소스의 첫 번째 항목은 더 이상 섹션의 실제 내용을 나타내지 않고 이 섹션의 제목일 수도 있는 헤더를 나타내기 때문에 데이터 소스가 이 점를 알아야 한다.
 
Advances in diffable data sources - WWDC 2020 - Videos - Apple Developer

### List Cell

iOS14에서 UICollectionViewListCell이라는 새로운 UICollectionViewCell 서브 클래스를 소개한다.
컬렉션 뷰의 구성적 특성에 충실하면서, 예상대로 일반 컬렉션 뷰 셀 어디든 리스트 셀을 사용할 수 있고, list section과 함께 UICollectionViewCell을 사용할 수도 있다.
list cell은 셀 내용의 들여 쓰기뿐만 아니라 구분 기호의 삽입을 구성하기 위해보다 세분화 된 지원을 제공한다. UITableView와는 대조적으로, Swipe Actions도 이제 list cell의 기능이 되었다.

또한 액세서리 API가 대폭 향상되었으며 'Modern Cell Configuration'에서 모두 배울 수 있는 기본 시스템 컨텐츠 및 배경 구성에 액세스 할 수 있다.

Modern cell configuration - WWDC 2020 - Videos - Apple Developer
 
### Separators

![](https://i.imgur.com/1a8O06R.png)

separator는 셀의 기본 내용과 일치해야한다.이 예에서는 이미지보기가 아니라 셀 레이블이다.
따라서 separator는 이 레이블의 가장자리와 일치하도록 삽입되어 있어야한다.

UITableView에서 이는 separator inset이라고하는 포인트 기반 값을 제공하여 수행된다.
레이블의 X 오프셋을 수동으로 계산하여 값을 구성하기에는 safe area inset, 레이아웃 여백, 동적 글꼴 크기 및 SF 기호가있는 최신 AutoLayout 세계에서는 더 이상 쉽지 않다.
따라서 레이블이 어디에서 나올지 미리 알기가 어렵다. list cell에서 우리는 Separator Layout Guide라고 부르는 새로운 개념을 소개한다.

![](https://i.imgur.com/YRQEWjr.png)

이 레이아웃 가이드는 UIKit의 기존 레이아웃 가이드와 약간 다르게 작동한다. 컨텐츠를 이 레이아웃 가이드로 제한거는 대신 이 레이아웃 가이드를 컨텐츠로 제한 걸 수 있다.
separator layout guide를 설정하는 가장 쉬운 방법은 먼저 셀의 레이아웃을 구성하고 셀이 원하는 방식으로 표시되면 단일 제약 조건을 추가하는 것이다.
separator layout guide의 leading anchor를 레이블의 leading anchor 또는 무엇이 됐든 셀에 있는 기본 콘텐츠에 제약을 걸어라!!

그런 다음 List section과 함께 List cell을 사용하면 separator가 셀의 기본 콘텐츠와 자동으로 정렬된다.
사용자 정의 셀 레이아웃이 있는 경우, separator가 올바른 위치에 있는지 쉽게 확인할 수 있는 방법이다.

### Swipe Actions

![](https://i.imgur.com/INxH6cO.png)

UITableView와 달리 스와이프 동작은 이제야 List cell의 기능이 되었다.
셀 내용과 함께 구성한다. 따라서 이미지뷰 또는 셀 레이블을 구성 할 때마다 선행 또는 후행 스와이프 동작 구성을 설정할 수도 있다.

셀과 레이아웃 사이의 통신이 필요하므로, 셀이 List configuration을 사용하여 구성된 섹션 내에서 렌더링 된 경우에만 스와이프 동작이 지원된다.
스와이프가 시작되려고 할 때만 스와이프 구성을 생성하는 UITableView에서 우리가 썼던 스와이프 동작 API의 동적 특성이 필요한 경우, 선행 또는 후행 스와이프 동작 구성 getter를 덮어쓴 다음 그 안에 구성을 만들 수 있다.
사용자가 실제로 셀을 스와이프하려고 할 때만 getter를 호출해야한다.
 
스와이프 액션의 액션 핸들러에서 구성중인 셀의 indexPath를 캡처하지 말아라!
indexPath는 안정적인 식별자가 아니다. 이 셀의 indexPath는 컨텐츠를 삽입하거나 삭제할 때마다 변경되므로 이 특정 셀을 반드시 다시 로드 하지않는다.
따라서 사용자가 스와이프 동작을 트리거 할 때 저장된 indexPath를 사용하여 셀의 데이터 모델을 가져 오는 경우 실제로 다른 셀의 데이터를 조작 할 수 있다. 잘못된 데이터를 삭제할 수 있으므로 삭제 작업에 특히 위험하다.

![](https://i.imgur.com/UJ0y0fs.png)

![](https://i.imgur.com/zFiLWE5.png)


대신 데이터 모델을 직접 캡처하거나 이 셀의 컨텐츠를 식별하는 데 사용할 수있는 안정적인 식별자를 캡처해라
Diffable Data Source 및 안정적인 item 식별자와 iOS 14의 새로운 셀 등록 유형은 이에 적합하다.

### Accessories

UITableView에서 액세서리 API는 상당히 제한되어있다. 상호 배타적이며 셀의 후미에만 관련된 액세서리 유형 및 액세서리뷰에 접근했다. List cell은 많은 새로운 액세서리 유형을 제공하며 셀의 뒤와 앞면 모두에 대해 액세서리를 구성 할 수 있으며 같은면에 여러 액세서리를 구성 할 수도 있다.

![](https://i.imgur.com/lxZuFnX.png)

또한, UITableViewCell의 액세서리가 장식 뷰에 가까웠다면 List cell에서는 기능을 활성화할 수 있다.

예를 들어, 재정렬 액세서리를 사용하여 셀을 구성하는 경우 사용자가 이 액세서리를 탭하면 필요한 재정렬 콜백도 구현한다고 가정할 때 컬렉션뷰가 재정렬 모드로 자동 설정된다.
다른 예로는 이전에 삭제 편집 컨트롤이라고 알려진 삭제 액세서리가 있습니다. 사용자가 이 액세서리를 누르면 목록 셀에 셀의 구성된 후행 스와이프 동작이 자동으로 표시된다.

그리고 새로운 액세서리, outline/disclosure 액세서리도 있다.
사용자가 이 액세서리를 누르면 셀이 자동으로 데이터 소스와 통신하고 이 셀의 하위를 확장하거나 축소합니다. 이를 위해서는 새로운 섹션 스냅샷 API를 사용해야하며 'Advances in diffable data sources'에서 이에 대해 배울 수 있다.

Advances in diffable data sources - WWDC 2020 - Videos - Apple Developer
  
이제 API 작동 방식을 살펴 보겠다. 셀의 액세서리를 구성하려면 목록 셀의 단일 액세서리 속성을 UICellAccessories배열로 설정하기 만하면 된다.

![](https://i.imgur.com/0DMrnih.png)

또한, 시스템은 disclosure indicator는 항상 표시되도록 되어 있지만, 삭제 액세서리는 컬렉션 뷰가 편집 모드일 때만 표시되어야 한다는 것을 알고 있다. 편집 모드로 들어가거나 나갈 때 삭제 액세서리를 자동으로 추정한다.

이러한 시스템 기본값을 많이 제공하지만 거의 모든 시스템 기본값을 사용자 정의 할 수 있다.
예를 들어 편집 모드가 아닌 경우에만 disclosure indicator를 표시하려면 표시된 매개 변수를 .whenNotEditing으로 설정하면된다.

### 마치며

이것은 UIKit이 모든 상태를 처리하는 수퍼 선언적 API이다.
Lists는 매우 모듈화되고 유연한 매우 사용자 정의 가능한 레이아웃이다. 기존 테이블 뷰를 대체하고 목록을 사용자 지정 compositional layout과 함께 유연하게 사용할 수 있는 위치를 생각해 보면 좋다.
