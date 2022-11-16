# Modern Cell Configuration

<img width="716" alt="스크린샷 2022-11-15 오후 6 01 15" src="https://user-images.githubusercontent.com/63997044/201906124-35a61d47-397a-4dbe-87eb-79ed7763e474.png">


> **OverView**

- Cell의 Content, Style을 구축하는 새로운 방법을 소개한다.
- 다양한 상태에 따라 다른 모양을 쉽게 얻을 수 있도록 하는 Configuration State에 대해 소개한다.
- Background Configuration, Content Configuration에 대해 알아보자.

<img width="715" alt="스크린샷 2022-11-15 오후 6 01 39" src="https://user-images.githubusercontent.com/63997044/201906207-3127491a-29f4-4431-8f7b-c0f09d098a61.png">

이 세션에서 우리가 중점으로 둘 것은 **View Configuration**이다.

## Getting started with configurations

<img width="712" alt="스크린샷 2022-11-15 오후 6 02 21" src="https://user-images.githubusercontent.com/63997044/201906353-c1e1f792-4659-4b49-9cea-b06aa3288595.png">

UITableView를 사용한 적이 있다면 매우 친숙할 코드. 내장된 imageView와 textLabel 속성을 사용하고 있다.

<img width="713" alt="스크린샷 2022-11-15 오후 6 02 54" src="https://user-images.githubusercontent.com/63997044/201906370-78a513d3-6402-400e-90b1-9548e29f2303.png">

이번에 소개할 방법인 **Content Configuration**을 통해 셀을 구성하는 방법.
- `defaultContentConfiguration()`을 통해 설정되지 않은 새 구성을 반환한다.
- default Content Configuration에는 이미지와 텍스트가 포함되어 있다.
- 설정을 다 했으면 contentConfiguration 프로퍼티에 할당하여 셀에 적용한다.

이 방법의 특징은 content라는 변수에 저장된 값을 변경하는 것으로, 실제로 UIImageView 또는 UILabel을 직접 건들지 않는다. 모든 속성은 Configurationd에서 설정되고, 마지막에 이것을 적용시킬 뿐이다.

<img width="715" alt="스크린샷 2022-11-15 오후 6 03 22" src="https://user-images.githubusercontent.com/63997044/201906378-7ea83f02-fd8f-4079-9dec-84fe986ec216.png">

이 특징이 중요한 이유는 바로 셀의 종류에 관계없다는 것이다. 
UITableViewCell, UICollectionViewCell 둘 다 동일하게 사용할 수 있다. 이 뿐만 아니라 header/footer와 같이 '셀이 아니더라도 contentConfiguration을 지원하는 모든 view'에 대해서 동작할 수 있다.

이것이 어떻게 가능할까? 바로 Configuration이 composable하기 때문이라고 한다.

Composable하다는 것은 조각들을 하나로 묶어서 구성할 수 있다는 의미.
예전의 셀 구성은 UITableVieCell이 그랬던 것처럼 셀 클래스 자체에 적용(baked into)되었는데, 이제는 셀 레이아웃이나 모양 등을 지원하는 것들은 셀이나 뷰에 바로 연결되어 적용하는 독립적인 조각으로 취급할 수 있게 되었다.

<img width="716" alt="스크린샷 2022-11-15 오후 6 04 30" src="https://user-images.githubusercontent.com/63997044/201907512-259a4305-dbd7-4226-a5f6-c4029298120e.png">

Configuration은 내용, 스타일 지정 등과 같이 특정 상태에 대한 View의 모양을 설명한다. 단지 속성의 모음일 뿐이고, 뷰 또는 셀에 적용하기 전까지는 아무 작업도 작용하지 않는다. 
또한 Composable하기 때문에, configuration을 지원하는 모든 뷰 또는 셀과 함께 사용할 수 있다.

Configuration에는 두 가지 종류가 있다.
- Background Configuration
- List Content Configuration

<img width="712" alt="스크린샷 2022-11-15 오후 6 04 47" src="https://user-images.githubusercontent.com/63997044/201907611-811bbc98-7895-4aae-b246-f45f6298e56a.png">



Background Configuration에 해당하는 속성들. 대체로 외형에 대한 속성
- 배경색
- 시각적 효과
- 테두리
- 모서리
- 기타 사용자 정의 작업

<img width="716" alt="스크린샷 2022-11-15 오후 6 05 02" src="https://user-images.githubusercontent.com/63997044/201907617-bb785408-49f4-4526-bd7b-3ae62a73d568.png">

List Content Configuration에 해당하는 속성들. 대체로 Content에 관련된 속성
- 이미지
- 텍스트
- 보조 텍스트
- 각각에 대한 사용자 정의 속성들(글자 크기, 이미지 모드 등)

<img width="709" alt="스크린샷 2022-11-15 오후 6 05 22" src="https://user-images.githubusercontent.com/63997044/201907777-b9e19eda-f7a1-41c7-8c60-59496888e97b.png">

### Configuration을 사용하여 얻을 수 있는 장점들!
- configuration은 value type이다. 즉 복사하여 값 타입 변수를 만들기 때문에 적용하기 전까지는 원본에 전혀 영향을 미치지 않는다.
- 따라서 항상 `defaultContentConfiguration()`을 통해 새로운 설정을 요청하고 사용하는 것을 권장한다.(기존에 어떤 설정이 적용되었는지 생각할 필요 없기 때문에 추천한다)
- 고급 동작에 대한 액세스 제공(클릭 시 하이라이트 등 상호작용에 대한 애니메이션을 적용하는 것)이 매우 간단한데, Background Configuration의 속성에 값을 입력해주면 된다.
- Configuration은 사용하기 쉬울 뿐만 아니라, 복잡한 상태 전환을 처리할 때 발생할 수 있는 여러 버그들을 없애준다. Configuration을 적용할 때 현재 적용할 것으로 덮어쓰기 때문에 한 번에 적용된다.
- 마지막으로 Configuration은 내부적으로 성능 최적화 처리를 해줌.
___

## Configuration state

<img width="716" alt="스크린샷 2022-11-15 오후 6 07 09" src="https://user-images.githubusercontent.com/63997044/201909503-b7e3d210-78e3-44cb-b27f-0c25d5ace59a.png">

**상태**란 뷰를 구성하는 데 사용하는 일종의 **Input**이다.
마찬가지로, 셀의 모양을 구성하기 위해 일반적인 UIKit Item들의 상태 뿐만 아니라 사용자 지정 상태까지 모두 포함한 것이 **Configuration State**이다.

- Configuration: Traits + States + Custom States

<img width="716" alt="스크린샷 2022-11-15 오후 6 07 34" src="https://user-images.githubusercontent.com/63997044/201909618-1d243530-9071-4505-8132-a74192f7150e.png">

Configuration State는 크게 **View Configuration State**와 **Cell Configuration State**로 나눌 수 있다.

<img width="714" alt="스크린샷 2022-11-15 오후 6 09 14" src="https://user-images.githubusercontent.com/63997044/201909670-88c31ab5-e0e1-4ea9-9165-32be3f1e99ee.png">

View Configuration State는 다음과 같다. 기본적으로 제공된 4가지(Highlighted, Selected, Disabled, Focused)와 사용자 지정 상태(Custom)가 있다.

<img width="715" alt="스크린샷 2022-11-15 오후 6 09 33" src="https://user-images.githubusercontent.com/63997044/201909687-a0455407-b9e2-4c69-b778-55071bd5db1a.png">

Cell Configuration State는 View Configuration State의 속성을 모두 가져옴 + 몇 가지(Editing, Swiped, Expanded, Drag and Drop) 더 추가됨

<img width="716" alt="스크린샷 2022-11-15 오후 6 10 01" src="https://user-images.githubusercontent.com/63997044/201910045-64a2c432-60ec-4c5a-87e7-92ed7417d5ec.png">

업데이트된 Configuration State를 얻는 방법: `.update(for:_)` 메서드
값 타입이므로 업데이트된 Configuration의 복사본이 반환한다.
update를 통해 state를 요청한다고 해서 구성이 변경되는 것은 아니다.


<img width="717" alt="스크린샷 2022-11-15 오후 6 10 32" src="https://user-images.githubusercontent.com/63997044/201911244-b5632112-7e22-4522-8d41-faf4f8d6c964.png">

실제로 셀의 상태가 변화하면 자동으로 업데이트하게 되는데 그건 위에 있는 `automaticallyUpdatesContentConfiguration`와 `automaticallyUpdatesBackgroundConfiguration` 때문이다. 
상태에 변화가 있다면 자동으로 상태를 업데이트하고 Configuration을 재적용한다.


<img width="714" alt="스크린샷 2022-11-15 오후 6 11 14" src="https://user-images.githubusercontent.com/63997044/201911294-7d8c1aaa-7dba-425c-97ee-623aa42ab2e7.png">

그렇다면 Configuration을 업데이트하는 코드를 어디에 넣어야 할까?

바로 `updateConfiguration(using state: UICellConfigurationState)` 메서드를 오버라이드하여 내부 구현에서 작성한다.

이 메서드는 셀이 화면에 표시되기 전에 항상 호출되며 state가 변경될 때마다 다시 호출되므로 새로운 상태에 맞게 구성될 것이다.


이 메서드는 또한 화면에 표시되기 전에 항상 호출되므로 업데이트를 중앙집중화할 수 있는 위치이기도 하다. setNeedsUpdateConfiguration()을 호출하여 업데이트를 요청하면 된다.
- 예시
```swift
class CustomConfigurationCell: UICollectionViewCell {
    var image: UIImage? {
        didSet {
            setNeedsUpdateConfiguration()
        }
    }
    
    override func updateConfiguration(using state: UICellConfigurationState) {
        backgroundConfiguration = CustomBackgroundConfiguration.configuration(for: state)
        
        var content = CustomContentConfiguration().updated(for: state)
        content.image = image
        contentConfiguration = content
    }
}
```


<img width="713" alt="스크린샷 2022-11-15 오후 6 11 35" src="https://user-images.githubusercontent.com/63997044/201911444-ef9c992b-db68-42f4-a89d-317a80bb4b08.png">

코드 예시를 통해 업데이트 예를 살펴보자.
1. 먼저 defaultContentConfiguration()을 통해 기본값을 얻고, 상태를 업데이트한다. 이 state에 대한 기본 configuration이 된다.
2. 셀이 표시하는 항목인 image와 text를 업데이트한다.
3. 사용자 정의를 적용한다. 
4. 마지막으로 Configuration을 셀에 설정한다.

이 메서드는 상태가 변경될 때마다 호출되며 매번 새로운 상태에 대한 Configuration을 적용한다.

<img width="714" alt="스크린샷 2022-11-15 오후 6 12 11" src="https://user-images.githubusercontent.com/63997044/201911535-2a7664f6-1ac0-4b41-b155-0a90455714b8.png">

Color Transformer: Input Color에 대한 적절한 Resolved Color를 반환해주는 구조체(iOS 14 신규)
- 예시
```swift
struct CustomBackgroundConfiguration {
    static func configuration(for state: UICellConfigurationState) -> UIBackgroundConfiguration {
        var background = UIBackgroundConfiguration.clear()
        background.cornerRadius = 10
        if state.isHighlighted || state.isSelected {
            // Set nil to use the inherited tint color of the cell when highlighted or selected
            background.backgroundColor = nil
            
            if state.isHighlighted {
                // Reduce the alpha of the tint color to 30% when highlighted
                background.backgroundColorTransformer = .init { $0.withAlphaComponent(0.3) }
            }
        }
        return background
    }
}
```
___

## Background and content configurations

<img width="712" alt="스크린샷 2022-11-15 오후 6 14 07" src="https://user-images.githubusercontent.com/63997044/201911550-23f8c930-df0f-4741-a373-913fd63c79eb.png">

UITableView와 UICollectionView의 Cell, Header, Footer 모두 스타일을 기반으로 Background Configuration을 자동으로 설정한다.
앞선 예처럼 `defaultContentConfiguration()`를 통해 기본값을 요청하고 속성들을 변화시키면서 원하는 Configuration을 구성한다.
그러나 이미 지정되어 있는 기본 구성을 요청할 수도 있다. (`listSidebarCell()`, `sidebarCell()`, `valueCell()` 등)

<img width="715" alt="스크린샷 2022-11-15 오후 6 17 10" src="https://user-images.githubusercontent.com/63997044/201911680-2b0aad5b-2c60-4d94-bece-76e1889f2a3a.png">

Content Configuration은 동적 레이아웃을 지원한다.

<img width="713" alt="스크린샷 2022-11-15 오후 6 17 30" src="https://user-images.githubusercontent.com/63997044/201911693-e15e2495-d436-4d71-8e5b-0065cf4d896f.png">

파란색: layout margin
노란색: padding
고정된 크기를 사용하는 대신 실제 콘텐츠의 크기와 margin, padding을 사용하여 고유 높이(Intrinsic Size)를 계산하고 동적으로 높이를 조정할 수 있게 구현되어 있다.

<img width="712" alt="스크린샷 2022-11-15 오후 6 18 00" src="https://user-images.githubusercontent.com/63997044/201911697-9ec2761e-d789-43e3-8bc0-2c7e452de8a8.png">

셀에 사용되는 이미지 크기가 항상 같지 않기 때문에, 만약 빨간 선을 기준으로 leading을 잡고 이미지와 텍스트가 배치된다면 그림과 같이 padding에 의해 어색하게 정렬이 이루어질 것이다.

이런 문제를 해결하기 위해 각 이미지에 대해 **예약 크기**(reserved layout size)를 지정해야 한다.

<img width="713" alt="스크린샷 2022-11-15 오후 6 18 20" src="https://user-images.githubusercontent.com/63997044/201911703-ca3730bd-9403-455f-8c71-8e5c42dfad98.png">

이미지에 대해 동일한 예약 너비를 설정하면 예약된 공간 내에서 이미지를 중앙에 배치한다. 예약 레이아웃 크기는 이미지의 실제 크기에 영향을 주지 않고 항목을 배치하는 데에만 사용된다.
Symbol 이미지를 사용하는 경우 UIKit은 예약된 표준 크기를 자동으로 적용해준다.

<img width="713" alt="스크린샷 2022-11-15 오후 6 18 42" src="https://user-images.githubusercontent.com/63997044/201911708-58ffdf62-29dc-4793-9ce0-07303b6722d2.png">

Background Configuration은 기존 속성과 상호배타적이다.
Configuration을 사용하면, backgroundColor 또는 backgroundView와 같은 __기존 속성들은 nil로 재설정__ 되며 그 반대도 마찬가지이다.

특히 주의해야 할 것이, UITableView에서 Content Configuration을 사용하면 기본 프로퍼티인 imageView, textLabel, detailTextLabel, header, footer 등 모든 기본 제공 subview들을 대체한다.
> (향후 릴리즈에서 이 속성들이 deprecated될 예정이니, Configuration을 활용하는 것을 추천한다고 한다.)
___
## 결론
<img width="717" alt="스크린샷 2022-11-15 오후 6 21 25" src="https://user-images.githubusercontent.com/63997044/201911714-fc8bf982-e96a-426d-80a4-67be9711eb5b.png">

### Configuration
- 가볍고 강력한 방법.
- 동일한 코드로 다양한 곳에 적용 가능하다.
- 셀의 상태를 통해 상태별로 적용해줄 수 있다.
