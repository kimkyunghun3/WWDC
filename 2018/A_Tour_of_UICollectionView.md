# A Tour of UICollectionView

<img width="1174" alt="스크린샷 2022-09-08 오후 12 00 05" src="https://user-images.githubusercontent.com/63997044/189024232-af55e96a-c7dc-41a7-bdf2-f451a4d62559.png">

### # Introduction
> `UICollectionView`는 애플리케이션에서 뛰어난 사용자 경험을 달성하는 데 도움이 되는 유연하고 강력한 도구입니다. UICollectionView API를 시작하기부터 고급 업데이트 애니메이션까지 다루는 방법을 알아봅시다.

## # UICollectionView Concepts

<img width="1174" alt="스크린샷 2022-09-15 오후 4 16 11" src="https://user-images.githubusercontent.com/63997044/190339533-c0db0176-a424-47ba-b6d5-623ac2f65d3f.png">

오늘 다룰 것
- 레이아웃
- 업데이트
- 애니메이션

### Key UICollectionView Concepts
<img width="1171" alt="스크린샷 2022-09-15 오후 4 16 51" src="https://user-images.githubusercontent.com/63997044/190339651-f6280b2f-7f99-440b-9243-b0e3443da0ba.png">

코드를 보기 전에 CollectionView에 대해 이해해야 할 세 가지 핵심 개념을 보겠다. 바로 **Layout**, **Data Source**, **Delegate**이다.

### UICollectionViewLayout
<img width="1172" alt="스크린샷 2022-09-15 오후 4 16 59" src="https://user-images.githubusercontent.com/63997044/190339681-828acf92-9690-443b-b5b4-df30cdfd246f.png">

**Layout**
- 컨텐츠들의 시각적 배열.
- UITableView와 유사
- Attribute들: Bounds, center, frame...
- CollectionViewLayout은 추상 클래스이므로 직접 사용하는 것이 아니라 하위 클래스를 사용한다.

### UICollectionViewFlowLayout
<img width="1175" alt="스크린샷 2022-09-15 오후 4 17 24" src="https://user-images.githubusercontent.com/63997044/190339757-9d4ab3d3-dd3d-4ddc-9681-eb63fb154703.png">

- UICollectionViewLayout의 Concrete 하위 클래스(이미 만들어져있는 클래스)
- UICollectionViewDelegate를 확장한 UICollectionViewDelegateFlowLayout사용
- line-based

### Flow Layout
### 1. Line Orientation
<img width="1168" alt="스크린샷 2022-09-15 오후 4 17 59" src="https://user-images.githubusercontent.com/63997044/190339871-50151c63-1aff-4e69-8425-43520d4d3c2d.png">

이것을 따라서 항목을 배치할 수평선의 방향을 의미한다. Horizontal / Vertical 두 가지가 존재한다.

### 2. Line Spacing
<img width="1172" alt="스크린샷 2022-09-15 오후 4 18 26" src="https://user-images.githubusercontent.com/63997044/190339957-12cf71e7-a58f-4422-96e6-c25bc5556453.png">

Line Spacing은 그림에서 수평선 사이의 간격을 의미하는데, 항목들이 배치되는 수평선 사이의 공간을 의미한다.

### 3. Inter-Item Spacing
<img width="1171" alt="스크린샷 2022-09-15 오후 4 18 46" src="https://user-images.githubusercontent.com/63997044/190340033-2d4d16f4-5f3d-4ef3-b811-4fbc48dac2d3.png">

Inner-Item Spacing은 레이아웃 선을 따라서 생긴 항목들 사이의 공간들을 의미한다. 

### UICollectionViewDataSource
<img width="1174" alt="스크린샷 2022-09-15 오후 4 19 15" src="https://user-images.githubusercontent.com/63997044/190340141-ba4d62b5-d2c6-45b9-84d0-fc8c32792eea.png">

**Data Source**
UITableView를 사용할 때의 코드와 매우 유사하다. Layout이 컨텐츠를 어디에 그릴지 결정하는 것이라면, Data Source는 컨텐츠가 무엇인지에 해당한다.
여기서 핵심적인 메서드는 다음 세 가지
- `optional func numberOfSection(in collectionView: UICollectionView) -> Int`: CollectionView의 섹션 수이며, 디폴트 값은 1이다.
- `func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int`: 각각의 개별 섹션에 있는 항목들의 수를 나타낸다.
- `func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell`: 사용자에게 표시할 실제 컨텐츠를 제공하는 메서드이다.

### UICollectionViewDelegate
<img width="1173" alt="스크린샷 2022-09-15 오후 4 19 39" src="https://user-images.githubusercontent.com/63997044/190340226-cb851e3a-debe-4863-8778-6331973a254b.png">

**Delegate**
- Delegate를 사용하는 것은 부가적(optional)인 행동이다. 
- CollectionView는 UIScrollView를 상속받기 때문에 ScrollView에서 제공하는 것과 동일한 Delegate를 사용하지만 확장할 수 있다.
- 스크롤 동작을 수정할 경우, 그리고 콘텐츠와 상호 작용할 때의 제어(강조 표시 혹은 선택에 대한 기능 제공) 등을 수정하는 경우 `UICollectionViewDelegate`의 메서드로 작업한다. 
- 또한 WillDisplayItem(화면에 나타났을 때), DidEndDisplayingItem(화면에 사라졌을 때)와 같은 메서드도 제공한다.
```swift
func collectionView(_ collectionView: UICollectionView, willDisplay cell: UICollectionViewCell, forItemAt indexPath: IndexPath) {
    <#code#>
}

func collectionView(_ collectionView: UICollectionView, didEndDisplaying cell: UICollectionViewCell, forItemAt indexPath: IndexPath) {
    <#code#>
}
```

## Demo(코드로 살펴보기)

<img width="897" alt="스크린샷 2022-09-15 오후 4 27 05" src="https://user-images.githubusercontent.com/63997044/190341832-c2c9172e-afa3-4c43-92d0-05a08d0bc098.png">

미리 준비한 UICollectionViewFlowLayout을 상속받은 ColumnFlowLayout을 준비했음.
이걸로 CollectionView를 생성한다.
그리고 DataSource와 Delegate를 self로 선언한다.

<img width="878" alt="스크린샷 2022-09-15 오후 4 28 03" src="https://user-images.githubusercontent.com/63997044/190342026-9e2c8dd4-2590-419f-b479-0d09f5c690d2.png">

DataSource 관련 필수 메서드 2개를 구현
<img width="884" alt="스크린샷 2022-09-15 오후 4 28 32" src="https://user-images.githubusercontent.com/63997044/190342156-fdf83995-d366-4e4b-a80d-be24b2311213.png">

Delegate 메서드 구현. 클릭했을 때의 동작 구현을 위해 메서드 구현

<img width="865" alt="스크린샷 2022-09-15 오후 4 30 05" src="https://user-images.githubusercontent.com/63997044/190342498-227d6530-39e0-4b14-876d-d7be7a86a4f0.png">

ColumnFlowLayout의 코드 내용. 항목 크기를 설정하고, 간격을 보기좋게 조정했다.

<img width="860" alt="스크린샷 2022-09-15 오후 4 30 34" src="https://user-images.githubusercontent.com/63997044/190342609-8a6346a9-2fe8-45dc-8964-6844fda779d5.png">

시뮬레이터 실행 모습.

<img width="858" alt="스크린샷 2022-09-15 오후 4 31 16" src="https://user-images.githubusercontent.com/63997044/190342752-c8b9da88-f34f-4210-90ca-543aac0060ae.png">

UICollectionViewLayoutsPrepare() 메서드는 레이아웃이 무효화될 때마다 호출되며, FlowLayout의 경우 CollectionView의 bounds가 변경될 때마다 레이아웃이 무효화된다. 예를 들면 디바이스가 회전하거나 실행하는 화면이 조정되는 경우이다.

사진은 가로로 디바이스를 회전시킨 상황인데, 회전하면서 레이아웃이 무효화되고 재설정하는 메서드가 호출되어 레이아웃을 다시 맞춰주는 모습이다. 그러나 가로로 방향을 회전하면서 더 많은 항목들을 표시할 수 있다. 이 때 FlowLayout을 사용하면 쉽게 해결할 수 있다. 앞에서 얘기했던 대로 Flow Layout은 다음 줄로 넘어가기 전에 한 줄에 가능한 한 많은 항목들을 배치하려고 하는 성질이 있다. 따라서 항목의 크기를 변경해주면 된다.

<img width="847" alt="스크린샷 2022-09-15 오후 4 33 01" src="https://user-images.githubusercontent.com/63997044/190343110-92d7cb35-403e-44ba-b2b1-7cd505fc0560.png">

itemSize 설정 부분을 다음과 같이 바꿔주었다. 임의의 너비를 300포인트로 잡고 한 줄에 들어갈 수 있는 열 수를 계산한다. 이를 기반으로 사용 가능한 너비를 구해 itemSize에서 사용한다.

<img width="843" alt="스크린샷 2022-09-15 오후 4 33 35" src="https://user-images.githubusercontent.com/63997044/190343246-9856676e-f839-4175-a8b6-382e1a0448ac.png">

원하는 대로 결과가 출력된 모습.

___

## When to Go Custom?
<img width="1171" alt="스크린샷 2022-09-15 오후 4 20 45" src="https://user-images.githubusercontent.com/63997044/190340430-b2057502-d10d-4e6f-979a-1a83ad3817b3.png">

이것은 Flow Layout으로 작동하지 않을 것이다. 왜냐하면 라인 기반 레이아웃의 모습이 아니기 때문이다.

### Creating a Custom UICollectionViewLayout
<img width="1171" alt="스크린샷 2022-09-15 오후 4 21 07" src="https://user-images.githubusercontent.com/63997044/190340499-4dee2e1a-e36b-4afa-8372-86bd1667f376.png">

이런 레이아웃을 짜려면 당연하게도 커스텀 레이아웃을 만들어야 한다. 이럴 때 사용하는 유용한 4+1개의 메서드를 소개하겠다.

### Providing Content Size
<img width="1176" alt="스크린샷 2022-09-15 오후 4 21 28" src="https://user-images.githubusercontent.com/63997044/190340563-66172f37-a007-464a-a8ed-c2893f866de3.png">

첫 번째 메서드는 `collectionViewContentSize`.
UICollectionView가 UIScrollView의 하위 클래스인 것을 기억해야 한다.
UIScrollView의 기능 중 하나는 커다란 컨텐츠 영역이 있고 그 안에서 컨텐츠가 이동한다는 것이다.
이 메서드(프로퍼티)는 모든 항목들을 포함하는 사각형 모양의 크기를 반환한다.

### Providing Layout Attributes
<img width="1171" alt="스크린샷 2022-09-15 오후 4 21 41" src="https://user-images.githubusercontent.com/63997044/190340605-a5a92208-9d46-4399-bcab-eccb07f3e761.png">

다음 두 메서드는 레이아웃의 속성들을 제공하는 메서드이다.
먼저, `layoutAttributesForElements(in rect: CGRect)`는 항목을 처음 표시하거나 사용자의 스크롤로 인해서 화면에 표시해야 하는 항목을 알아야할 때 주기적으로 호출된다. 기하학적인 영역에 대한 것 
다음은 `layoutAttributesForItem(at indexPath: IndexPath)`이다. indexPath의 단일 항목에 대한 속성을 알려주는 메서드이다.

### Preparing the Layout
<img width="1172" alt="스크린샷 2022-09-15 오후 4 21 49" src="https://user-images.githubusercontent.com/63997044/190340626-a3fdd140-fa74-423f-93df-c1419a5189df.png">

네 번째 메서드는 `prepare()`.
레이아웃이 무효화될 때마다 호출된다.

### Handling Bounds Changes in Your Custom Layout
<img width="1175" alt="스크린샷 2022-09-15 오후 4 22 14" src="https://user-images.githubusercontent.com/63997044/190340716-a1d619b2-58bd-45af-a686-8163d98a5ea4.png">

CollectionView의 bounds가 변경될 때마다 호출되는 메서드.
bounds의 변경이란 앱의 크기가 변경되거나, CollectionView의 크기가 변경되거나 혹은 스크롤을 통해 원점이 변경되는 상황을 가리킨다. 즉 이 메서드는 스크롤할 때마다 호출될 것인데 기본적으로는 false를 반환한다. 

## Demo(코드로 살펴보기)
<img width="861" alt="스크린샷 2022-09-15 오후 4 34 40" src="https://user-images.githubusercontent.com/63997044/190343500-d100024a-9808-4229-9183-3f0b1aa19a51.png">

MosaicLayout 생성. UICollectionViewLayout의 하위 클래스
contentBounds: 모든 항목들의 대표 범위
cachedAttributes: 성능이 중요할 떄 빠르게 참조할 수 있도록 캐시된 속성 배열

<img width="849" alt="스크린샷 2022-09-15 오후 4 34 59" src="https://user-images.githubusercontent.com/63997044/190343564-774291ca-1440-464e-bb61-f8ac57e82b03.png">

prepare는 레이아웃이 무효화될 때마다 한번씩 호출되기 때문에 설정을 잡아주기 적합하다.
createAttributes 메서드에서 할 동작
- 나중에 빠르게 가져올 수 있도록 속성 배열에 캐싱
- contentBounds와 attributes.frame의 결합(contentBounds의 최신화)

<img width="852" alt="스크린샷 2022-09-15 오후 4 36 03" src="https://user-images.githubusercontent.com/63997044/190343746-5e026c3c-15fe-408d-bc4c-7f25c8cad0a4.png">

따라서 위에서 얘기했던 메서드를 구현한다. collectionView의 bounds가 변경될 때만 true를 반환.

<img width="841" alt="스크린샷 2022-09-15 오후 4 36 58" src="https://user-images.githubusercontent.com/63997044/190343922-541256d0-e219-4d69-ae65-d48a37ad61d1.png">

시뮬레이터로 확인한 레이아웃 모양. 모자이크 구성으로 멋지게 표현되었으며 회전을 통해 bounds가 변경되었기 때문에 레이아웃이 무효화되고 새로 업데이트하는 것을 볼 수 있다.
그러나 스크롤 성능은 매우 좋지 않다. 코드로 돌아가서 (스크롤할 때) 무슨 일이 일어나고 있는지 확인해보자.

<img width="848" alt="스크린샷 2022-09-15 오후 4 37 37" src="https://user-images.githubusercontent.com/63997044/190344038-add4bfcc-5262-41dd-b95b-2056d6f18386.png">

이 메서드가 스크롤하는 동안 너무 자주 호출되고 있다. 전체 배열을 필터링하기 때문에 CollectionView의 항목 수가 많은 경우 비용이 많이 든다.

<img width="847" alt="스크린샷 2022-09-15 오후 4 38 36" src="https://user-images.githubusercontent.com/63997044/190344315-620eaf5a-eefd-4beb-9370-4c1f28fe0af8.png">

layoutAttributesForElements 메서드를 효율적으로 개선해보자. 먼저 (미리 준비한) 이진 탐색 메서드를 사용하여 인덱스를 찾고, 그 위치에서 시작하여 위아래로 반복문을 돌려 나머지 속성 집합을 구축하는 방법으로 구현하였다. 간단하게 설명하면, 배열을 반복하지 않기 때문에 메서드 호출에 대한 비용이 줄어들어 스크롤이 빨라졌다.
___

## Our Totally Cool Update Animation
![Sep-15-2022 05-32-27](https://user-images.githubusercontent.com/63997044/190340832-825de873-cad6-43f0-9d09-12fc2a6f250f.gif)

이런 애니메이션
UICollectionView가 제공하는 도구인 Perform Batch Updates API를 사용할 것임.
이 API를 사용하면 애니메이션과 동시에 수행하는 일련의 업데이트를 Collection View에 전달할 수 있다.
<img width="854" alt="스크린샷 2022-09-15 오후 4 39 31" src="https://user-images.githubusercontent.com/63997044/190344712-d68ede3e-4914-4cc0-adde-1ba1d2bffaed.png">

PerformBatchUpdates에 대한 호출을 추가. Data Source 업데이트와 Collection View 업데이트를 모두 수행하고 있는데 이는 동기화를 깔끔하게 유지하고 불일치를 피하는 좋은 방법이다.

<img width="837" alt="스크린샷 2022-09-15 오후 4 40 31" src="https://user-images.githubusercontent.com/63997044/190344890-ed10eaa1-3c5d-4f4b-8ab8-bc42577efe78.png">

<img width="1171" alt="스크린샷 2022-09-15 오후 4 40 45" src="https://user-images.githubusercontent.com/63997044/190344935-33d71848-b0f4-48bb-b170-cbd476aad781.png">

에러 발생!


### performBatchUpdates()
<img width="1171" alt="스크린샷 2022-09-15 오후 4 23 14" src="https://user-images.githubusercontent.com/63997044/190340941-26084acf-75d4-4575-81c8-725132f2c57e.png">

동시에 여러 업데이트를 진행하고 애니메이션 제공
Data Source 업데이트와 동시에 CollectionView 업데이트 진행
Data Source 업데이트의 순서는 중요하지만 CollectionView 업데이트는 순서가 중요하지 않다.

### Data Source Updates: Order Matters
<img width="1171" alt="스크린샷 2022-09-15 오후 4 23 32" src="https://user-images.githubusercontent.com/63997044/190341017-3bf82630-9590-41b2-a9a0-8d779a146028.png">

3개의 요소가 있는 2개의 배열로 예시를 들 것이다. 
<img width="1159" alt="스크린샷 2022-09-15 오후 4 24 30" src="https://user-images.githubusercontent.com/63997044/190341236-ae144dce-dcf5-4abb-ac44-636e86dd532c.png">

우리의 직관대로라면 순서에 의해 결과가 다르게 나타날 것이다. 이는 우리가 원하는 결과가 아니다.
<img width="1170" alt="스크린샷 2022-09-15 오후 4 24 26" src="https://user-images.githubusercontent.com/63997044/190341223-734d4868-815e-41ab-8661-ef98c0151654.png">

그런데 코드와 같이 클로저로 업데이트 동작을 지정하고 `performBatchUpdates()`를 수행하면 결과가 동일하게 나올 것이다.

### Collection View Updates Coalescing
<img width="1177" alt="스크린샷 2022-09-15 오후 4 24 58" src="https://user-images.githubusercontent.com/63997044/190341323-e7b405e7-464a-4753-b401-0b3460a7c91f.png">

CollectionView로 전송되는 업데이트의 순서가 중요하지 않은 이유는 무엇일까?
업데이트 작업들을 각각 살펴보겠다.
1. Delete
- IndexPath 내림차순으로 처리된다.
- 항상 이전 상태의 indexPath를 참조한다.
(Data Source 업데이트 이전과 이후 상태를 각각 이전/이후 상태라고 칭함)
2. Insert
- IndexPath 오름차순으로 처리된다.
- 삽입 작업에서 참조하는 IndexPath는 항상 최종 상태 혹은 업데이트 이후 상태르 참조한다.
3. Move
- Delete와 Insert의 혼합 작업
- From은 이전 상태, to는 이후 상태
4. Reload
- 실제로 삽입과 삭제로 분리할 수 있다.
- reload에서 지정된 IndexPath는 이전 상태를 참조한다.

<img width="1175" alt="스크린샷 2022-09-15 오후 4 25 07" src="https://user-images.githubusercontent.com/63997044/190341355-1e6c1705-f423-4f2a-8384-e9ae0a521525.png">

따라서 다음과 같은 작업들의 결합이 일어나게 되면, Collection View는 충돌이 일어날 것이다.
- 같은 위치에 이동과 삭제를 시도할 때
- 같은 위치에 이동과 삽입을 시도할 때
- 둘 이상의 위치를 동일한 위치로 이동을 시도할 때
- 유효하지 않은 IndexPath를 참조할 때

<img width="1175" alt="스크린샷 2022-09-15 오후 4 25 27" src="https://user-images.githubusercontent.com/63997044/190341425-c7e25308-fb6f-46de-bc0e-ea35a8e9101d.png">

오류를 범하지 않으려면 이 네 가지 규칙을 지키면서 항상 Data Source 업데이트를 적용하고 동기화되었는지 확인해야 한다.
- Move와 Delete 및 Insert를 분리한다.
- 모든 삭제 작업과 삽입 작업을 두 개의 개별 목록으로 분리한다.
- 먼저 내림차순으로 삭제를 처리한 다음 오름차순으로 삽입을 적용한다.


### What About reloadData()?
<img width="1175" alt="스크린샷 2022-09-15 오후 4 25 47" src="https://user-images.githubusercontent.com/63997044/190341519-7dc2ac5d-4670-4422-8ecb-e02723aa0169.png">

- 위의 업데이트 작업 없이 Data Source와 Collection View의 동기화 작업을 하는 메서드
- 애니메이션이 제공되지 않음.
- "The Sledgehammer"
- 애니메이션이 없기 때문에 사용자에게 좋은 경험을 주지 않으므로 꼭 필요한 경우에만 사용해야 함.


## Demo(코드로 살펴보기)
<img width="844" alt="스크린샷 2022-09-15 오후 4 42 52" src="https://user-images.githubusercontent.com/63997044/190345342-976c6ec1-c294-42ba-bad5-f4e0cf189ca4.png">

같은 IndexPath를 사용하는 것에서 충돌이 시작함.
따라서 이를 분리하는 작업을 먼저 시작하겠다.

<img width="847" alt="스크린샷 2022-09-15 오후 4 43 31" src="https://user-images.githubusercontent.com/63997044/190345456-130ac19e-5c86-42df-8749-1b1e226c851e.png">

Remove와 Move, Insert를 각각 분리한다.
먼저 내림차순으로 삭제를 수행한다.
그 후 삽입을 오름차순으로 처리한다.
마지막으로 CollectionView를 업데이트하여 애니메이션을 재생한다.

<img width="1169" alt="스크린샷 2022-09-15 오후 4 26 08" src="https://user-images.githubusercontent.com/63997044/190341596-67b0e999-1c27-40a4-a773-973ea3c2415c.png">
