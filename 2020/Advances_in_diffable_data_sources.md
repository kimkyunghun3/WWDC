# Advances in diffable data sources
<img width="714" alt="스크린샷 2022-11-10 오전 12 07 04" src="https://user-images.githubusercontent.com/63997044/200890676-f1d4bb0d-48c8-4ddd-9ca0-011e7ddb4b73.png">
> **OverView**

Diffable Data Source는 CollectionView 및 TableView를 관리하고 업데이트하는 작업을 크게 단순화했다. 그로 인해 앱에서 동적이고 반응성이 좋은 경험을 제공할 수 있게 되었다. 

- Section Snapshot을 사용하여 목록 및 개요를 구성하는 Collection View를 작성하는 법과 iPad 앱에서 사이드바 구현을 지원하는 방법을 알아본다. 
- UICollectionViewDiffableDataSource를 사용하여 셀 재정렬을 단순화하여 코드를 간소화하고 앱 인터페이스를 더 빠르게 구축하는 방법도 알아본다.

이 세션은 2019년의 "Advances in UI Data Sources"를 기반으로 한다.

## Sample

iOS 14에서의 Diffable Data Source의 개선점에 대해 이야기할 것이다.
샘플 프로젝트([Implementing Modern Collection Views](https://developer.apple.com/documentation/uikit/views_and_controls/collection_views/implementing_modern_collection_views))의 "Emoji Explorer"를 통해 설명한다.

<img width="715" alt="스크린샷 2022-11-10 오전 12 14 14" src="https://user-images.githubusercontent.com/63997044/200890710-05e2248d-f09d-4f0d-b9ae-bb3b073fe421.png">

첫 번째 Section은 수평으로 스크롤할 수 있는 이모티콘 **Grid Section**이다.

<img width="714" alt="스크린샷 2022-11-10 오전 12 14 36" src="https://user-images.githubusercontent.com/63997044/200890919-6105adc3-ced5-4ab9-aa07-5c34b0033a96.png">

중간의 Section. 여기는 iOS 14에 새로 추가된 접을 수 있는 **Outline Section**이다.

<img width="714" alt="스크린샷 2022-11-10 오전 12 15 00" src="https://user-images.githubusercontent.com/63997044/200891083-ffbb6cac-c392-410b-8105-b847bc0188ab.png">

마지막 Section은 표랑 비슷한 **List Section**이다.

## Diffable data source

> **Review**

<img width="718" alt="스크린샷 2022-11-10 오전 12 21 03" src="https://user-images.githubusercontent.com/63997044/200891470-b74b31eb-2edb-4909-b366-ed2528a8fb14.png">


\# Diffable Data Source
- iOS 13에 도입
- (새로운 Snapshot data type을 추가하여) UI 상태 관리를 단순화함
    - Snapshot은 고유 섹션 및 항목 식별자를 사용하여 전체 UI 상태를 캡슐화한다.
    - UICollectionView를 업데이트할 때, 먼저 새로운 Snapshot을 만들고, 현재 UI 상태로 채워넣고, 데이터 소스에 이를 적용한다.
- 개발자가 직접 추가적인 작업을 할 필요 없이 차이를 계산하여 움직이는 작업을 처리함
- 자세한 내용은 지난 WWDC 2019 "Advances in UI Data Sources"

<img width="718" alt="스크린샷 2022-11-10 오전 12 28 51" src="https://user-images.githubusercontent.com/63997044/200891655-3308fe9b-5c68-4aa1-9e3a-794134f4ace6.png">

iOS 14에서 새롭게 추가된 기능: Section Snapshots 그리고 Reordering Support

## Section Snpshots
<img width="714" alt="스크린샷 2022-11-10 오전 12 29 54" src="https://user-images.githubusercontent.com/63997044/200891758-0f7e43d1-c8c5-4297-b945-162ce9173718.png">

기존 Snapshot 타입 옆에 새로 추가하는 것으로 이를 **Section Snapshot** 이라고 칭하기로 함
이 Snapshot은 UICollectionView의 **단일 섹션에 대한 데이터**를 캡슐화한다.
이렇게 하는 이유
1. 데이터 소스를 섹션 크기의 단위로 구성할 수 있게 됨(Composable data sources)
2. 아웃라인 스타일의 UI의 랜더링을 지원하도록 계층적 데이터 모델링(Hierarchical data)

Emoji Explorer로 돌아가서 Section Snapshot의 쓰임을 알아보자!

<img width="718" alt="스크린샷 2022-11-10 오전 12 35 15" src="https://user-images.githubusercontent.com/63997044/200892050-7ca7f846-e89e-4037-8022-13202210d2c7.png">

Grid Section에서는 단일 Section Snapshot 사용. 여기에 있는 콘텐츠를 전부 모델링한다.

<img width="715" alt="스크린샷 2022-11-10 오전 12 36 29" src="https://user-images.githubusercontent.com/63997044/200892389-f23c82a3-210d-46ec-a3e3-23b9cb233d88.png">

Outline Section에서는 이렇게 계층적 데이터를 모델링한다.

<img width="712" alt="스크린샷 2022-11-10 오전 1 56 42" src="https://user-images.githubusercontent.com/63997044/200892629-29e23a86-2972-43d0-9b61-effc8a3284f1.png">

마지막으로 List Section에서 이 콘텐츠를 세 번째 Section Snapshot으로 모델링한다.
___
<img width="716" alt="스크린샷 2022-11-10 오전 12 39 14" src="https://user-images.githubusercontent.com/63997044/200892715-fa4fb174-4bf8-496f-a7a2-3a7c4cc79644.png">

NSDiffableDataSourceSectionSnapshot의 스니펫

이제부터 Snapshot과 Section Snapshot 두 용어를 구분해야 함.

여기서 주목할 것은 **섹션 식별 타입이 존재하지 않는다는 것**. Section Snapshot은 본질적으로 어떤 섹션을 나타내는지 모르는 상태이다.

Section Snapshot에 콘텐츠를 추가하려면 append를 사용한다.
옵셔널 매개변수로 **parent**가 있는데, 이를 통해 계층 관계를 생성한다.


<img width="716" alt="스크린샷 2022-11-10 오전 12 44 54" src="https://user-images.githubusercontent.com/63997044/200892984-c12d30ab-15b7-4ec3-9a6f-2ca617487ee5.png">

이에 맞춰 UICollectionViewDiffableDataSource에도 변경이 있었다.
- apply
- snapshot(for section:_)


<img width="720" alt="스크린샷 2022-11-10 오전 12 50 02" src="https://user-images.githubusercontent.com/63997044/200893775-c4d4ab8d-0f84-4bac-89b7-01d3444cb85e.png">

코드 스니펫을 구체화해보면서 Snapshot과 Section Snapshot을 함께 사용하는 방법을 알아보자.
먼저 섹션을 추가한다.(recent/top/suggested)
이제 Section Snapshot을 각 섹션에 apply하여 항목을 채운다.

<img width="716" alt="스크린샷 2022-11-10 오전 12 49 05" src="https://user-images.githubusercontent.com/63997044/200893667-eaf94ee1-d9b7-43f2-befb-5adac17a5601.png">

먼저 "Smileys", "Nature", "Food" 등 Root Item들을 append 메서드를 통해 생성한다.
옵셔널 매개변수인 parent가 제공되지 않으면 = Root Item이다.

<img width="717" alt="스크린샷 2022-11-10 오전 12 56 26" src="https://user-images.githubusercontent.com/63997044/200893865-8918e3fd-6b46-4e9c-8735-7aa4f3b86693.png">

이번엔 상위 항목이 "Food"인 하위 항목을 추가한다. 마찬가지로 append 메서드를 사용하지만, 매개변수에 섹션의 이름을 입력해준다.
이렇게 하면 계층적 데이터가 완성된다. 

<img width="716" alt="스크린샷 2022-11-10 오전 12 57 44" src="https://user-images.githubusercontent.com/63997044/200893989-1fd68369-a161-4f67-ae98-a9e99fa27201.png">

이 코드 스니펫은 선택적으로 상위 항목을 포함하면서 특정 상위 항목에 대한 모든 하위 항목들을 가져오는 방법이다.

<img width="716" alt="스크린샷 2022-11-10 오전 1 00 43" src="https://user-images.githubusercontent.com/63997044/200894009-30d7e7d7-8678-4220-a162-8a9c0d3931cc.png">

다음은 확장 상태(열고 접는 상태)이다.
- expand
- collapse
- isExpanded


<img width="716" alt="스크린샷 2022-11-10 오전 1 02 14" src="https://user-images.githubusercontent.com/63997044/200894021-fe9a9d5c-e57f-4fe6-a182-f7e90eb1d188.png">

Section Snapshot의 확장 상태를 변경하면 실제로 Diffable Data Source에 적용할 때까지는 적용되지 않는다.
이게 무슨 말이냐면, 사용자가 Disclosure Accessory로 상호 작용을 하면, 프레임워크는 Section Snapshot을 새로운 확장 상태로 업데이트하고 그 이후에 해당 Section Snapshot을 데이터 소스에 적용한다.

이렇게 구성된 이유는 **유연한 디자인 적용**과 **섬세하게 앱을 제어하기 위함**이다.
- 사용자 상호 작용으로 인한 확장 상태 변경에 대해 알림을 받고 이를 처리하는 기능을 구현할 수 있게 된다.
- 예를 들면 이런 계층적 데이터 구조를 통한 뷰를 구성하는데, 특정 부모가 절대 축소되지 않아야 한다거나, 두 개의 Outline Section에서 최소한 하나의 섹션은 확장 상태가 되어야 하는 디자인이 있을 수 있다.

<img width="714" alt="스크린샷 2022-11-10 오전 1 08 10" src="https://user-images.githubusercontent.com/63997044/200894696-69f8f901-a800-4070-9bd6-553dea08f653.png">

이런 다양한 디자인을 지원하기 위해 상호 작용으로 인한 확장 상태 변경에 따른 프로그램 제어를 할 수 있는 새로운 API가 추가되었다.
- shouldExpandItem
- willExpandItem
- shouldCollapseItem
- willCollapseItem
- snapshotForExpandingParent: lazy loading. 콘텐츠를 가져오는 데 비용이 많이 들 때 초기 Section Snapshot에 로드되는 콘텐츠의 양을 최소화할 때 사용


## Reordering Support

Diffable Data Source가 제공하는 기능 중 하나는 **Collection View의 데이터를 고유한 식별자로 모델링하는 기능**이다.

각각의 항목에 고유한 식별자를 사용한다면 사용자 상호 작용에 대해 재정렬 변경 사항을 자동으로 적용할 수 있지만 이것만으로는 충분하지 않다.

우리의 앱은 사용자로 인한 재정렬 상호 작용이 발생했음을 알려야 한다. 그래야 최종적인 source of truth인 앱의 백업 저장소에 새로운 시각적 순서를 유지할 수 있다. 
(상호작용으로 인한 View의 변경이 Data의 변경까지 전달되어야 한다는 이야기인듯)

<img width="716" alt="스크린샷 2022-11-10 오전 1 25 22" src="https://user-images.githubusercontent.com/63997044/200894828-848f2440-79c5-4eb1-baae-faf3b20a009a.png">

그래서 재정렬을 지원하기 위해 UICollectionViewDiffableDataSource에는 이제 ReorderingHandler라는 새 프로퍼티가 존재한다.
이 ReorderingHandler는 3개의 옵셔널 클로저를 포함하는 구조체이다.
- canReorderItem: 재정렬을 활성화. 사용자가 재정렬을 시작하려고 할 때 호출되며 true를 반환하면 재정렬 상호 작용이 시작될 수 있다.
- didReorder: 사용자가 재정렬 상호작용을 완료하면 애플리케이션이 새로운 순서를 source of truth에 커밋하도록 이 클로저가 호출됨.
    - 이 때 전달되는 타입을 보면 `NSDiffableDataSourceTransaction`

<img width="717" alt="스크린샷 2022-11-10 오전 1 31 01" src="https://user-images.githubusercontent.com/63997044/200894846-bcd34099-63ba-47cb-9b6b-3136b1957f8b.png">

Transaction은 Data Source를 업데이트하는데 추론하는 모든 정보들을 제공한다.
먼저 `NSDiffableDataSourceTransaction`부터 살펴보면
- initialSnapshot: 업데이트가 적용되기 전의 Diffable Data Source
- finalSnapshot: 업데이트가 적용된 상태의 Diffable Data Source
- difference: Swift 표준 라이브러리인 CollectionDifference. 
    - 데이터 타입이 배열과 같은 Collection Type인 경우 항목 식별자로 확인할 필요 없이 CollectionDifference를 직접 적용
- sectionTransactions: 재정렬 업데이트와 관련된 모든 섹션에 대한 세부 정보를 제공

다음으로 `NSDiffableDataSourceSectionTransaction`
- sectionIdentifier: 이 트랜잭션이 적용된 섹션의 식별자
- 나머지는 동일

<img width="717" alt="스크린샷 2022-11-10 오전 1 43 40" src="https://user-images.githubusercontent.com/63997044/200894861-702cbe6f-6dc5-4b6e-abb4-8b9ecfe1a556.png">

예제를 통해 확인해보자. 여기에서 backingStore가 곧 CollectionView의 단일 섹션에 대한 source of truth인 배열이다.
CollectionDifference를 통해 새 백업 저장소를 만들고, source of truth를 업데이트했다.
___
## 요약
- iOS 14에서는 Section Snapshot과 Reordering Support가 추가됨
- Section Snapshot을 통해 하나의 섹션을 묶고 이를 계층적으로 구성하여 접고 펼 수 있는 아웃라인 스타일의 UI를 구성할 수 있음
- Reordering Handler를 통해 사용자 상호 작용으로 인한 데이터 재정렬을 더욱 간단하게 지원하게 되었음
