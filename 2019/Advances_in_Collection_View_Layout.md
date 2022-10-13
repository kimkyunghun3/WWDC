# Advances in Collection View Layout

## 목차
<img width="1162" alt="스크린샷 2022-10-13 오후 5 10 09" src="https://user-images.githubusercontent.com/63997044/195539993-8293a625-7c90-4be7-83cc-a303fc4a0618.png">

## Current State-of-the-Art
CollectionView가 iOS 6에서 다시 도입되었을 때, 레이아웃을 정의하기 위한 별도의 추상화가 있었고 이는 그당시 새로운 개념이었다.
따라서 하나는 렌더링을 수행하고, 다른 하나는 레이아웃을 담당하도록 두 클래스가 협력하는 모양이 되었는데 `CollectionViewFlowLayout`이라는, 구체적으로 정의된 클래스를 출시한 바 있다.

![스크린샷 2022-10-13 오후 3 36 57](https://user-images.githubusercontent.com/63997044/195520297-5579123e-5e0a-4505-92cb-9936323443cb.png)

물론 iOS 6가 사용되던 당시의 디자인이 단순했던 면도 있지만 Flow Layout은 다양한 디자인에 매우 유용하게 사용되었다.
지난 WWDC에서 다루었듯 이는 라인 기반 시스템을 사용했는데, 그러나 라인 기반 시스템은 한 줄의 공간을 모두 채우고 나서야 다음 줄에 항목들을 배치할 수 있다.

![스크린샷 2022-10-13 오후 3 37 25](https://user-images.githubusercontent.com/63997044/195520361-2968dcb6-adcf-4de1-9e10-35d31158294d.png)

그러나 오늘날의 앱은? 장치의 종류와 화면의 크기가 다양해지면서 앱의 디자인이 더욱 다양해졌다. 
iOS 13의 앱스토어를 예로 들어보겠다. 만약 디자이너가 이런 디자인을 건네준다면, Custom Layout이 필요할 것이다.

### Building Custom Layouts
![스크린샷 2022-10-13 오후 3 37 45](https://user-images.githubusercontent.com/63997044/195520411-ccd2389a-5bfc-4b45-b1f6-26fc7996abb7.png)

우리가 살펴봤던 Custom Layout에 대한 내용들
- Boilerplate code: 코드 재사용
- Performance considerations: 성능 관련 고려사항
- Supplementary and Decoration: View의 두 가지 타입
- Self-sizing: 자체 크기 조정 문제

따라서 이번에 새로운 콘크리트 클래스인 **Compositional Layout** 를 소개할 것이다.
___

## A new approach
### Compositional Layout
![스크린샷 2022-10-13 오후 3 38 45](https://user-images.githubusercontent.com/63997044/195520621-38f7d489-1980-4346-b85f-cf1c40a22330.png)

Compositional Layout을 기반으로 한 세 가지 개념
1. Composable(구성 가능한)
단순한 것에서 복잡한 것을 구성하는 방식이다.
2. Flexible(유연한)
Compositional Layout은 모든 레이아웃을 작성할 수 있도록 유연하게 설계되었다.
3. Fast(빠른)
프레임워크에서 자체적으로 최적화를 수행하기 때문에 매우 빠르다.

![스크린샷 2022-10-13 오후 3 39 00](https://user-images.githubusercontent.com/63997044/195520669-5d896c2c-54eb-493a-9ab7-af7d8d85299c.png)

Compositional Layout은 레이아웃의 작은 구성 요소들을 모두 연결하여 큰 레이아웃을 구성하는 것.
Flow Layout으로 Custom Layout을 만들 때처럼 서브클래싱하지 않는다.

![스크린샷 2022-10-13 오후 3 41 17](https://user-images.githubusercontent.com/63997044/195521114-ce9493cb-116f-4f95-8e82-e00a2c3705cb.png)

![스크린샷 2022-10-13 오후 3 40 05](https://user-images.githubusercontent.com/63997044/195520904-800115a9-fd01-476c-9c26-06f713d18ff2.png)

Item -> Group -> Setion -> Layout 으로, 사다리를 타고 올라가는 것처럼 자연스러운 추상화 과정으로 되어 있다.

![스크린샷 2022-10-13 오후 3 40 36](https://user-images.githubusercontent.com/63997044/195520990-ea98e0b0-e6df-42c2-965f-49ca318a38fb.png)

### NSCollectionLayoutSize
![스크린샷 2022-10-13 오후 3 40 51](https://user-images.githubusercontent.com/63997044/195521040-149b01bc-e02f-4ade-a13e-59fcbc6dd4bf.png)

Compositional Layout의 내부 크기를 조정하는 방법.
모든 것들이 명백한 크기를 가지고 있으며, widthDemension + heightDemension으로 결정된다.
이 두 값은 float와 같은 scalar값이 아니라, NSCollectionLayoutDimension이라는 새로운 타입의 값이다.

### NSCollectionLayoutDimension
![스크린샷 2022-10-13 오후 3 42 33](https://user-images.githubusercontent.com/63997044/195521337-41a0ca7f-c884-4f7c-bb4c-842f6e71ab04.png)

Axis-Independent(축 독립적인) 방법.

![스크린샷 2022-10-13 오후 3 42 46](https://user-images.githubusercontent.com/63997044/195521378-c1163d21-a7d9-42a3-88aa-ab1d145c6140.png)

fractionalWidth/fractionalHeight. 컨테이너 내에서 차지할 비율의 크기


![스크린샷 2022-10-13 오후 3 43 43](https://user-images.githubusercontent.com/63997044/195521533-e606e18f-7b94-4c53-a69f-b0890de48aa9.png)

이런 경우라면 너비 1/4, 높이 1/4의 사이즈를 갖는다.

![스크린샷 2022-10-13 오후 3 44 02](https://user-images.githubusercontent.com/63997044/195521581-62e883cf-7c36-4acb-bd5f-446b67fa928d.png)

absolute: 고정적인 크기를 지정함

![스크린샷 2022-10-13 오후 3 44 24](https://user-images.githubusercontent.com/63997044/195521648-18f4dbfa-91c8-4391-841c-06c1564ec354.png)

estimated: 추정치로 크기를 지정함

![스크린샷 2022-10-13 오후 3 44 34](https://user-images.githubusercontent.com/63997044/195521700-d6cd36c2-e1f0-4485-9654-26ef955383cf.png)

estimated로 선언한 경우 고정크기가 아니기 때문에 변화할 수 있다.


### NSCollectionLayoutItem
![스크린샷 2022-10-13 오후 3 44 54](https://user-images.githubusercontent.com/63997044/195521759-832f526c-656f-44f6-9d5c-6811c830859f.png)

Cell 또는 Supplementary, 즉 화면에 표현되는 것.

### NSCollectionLayoutGroup
![스크린샷 2022-10-13 오후 3 45 24](https://user-images.githubusercontent.com/63997044/195521852-553c4e64-61b6-453b-811b-322d0aa868c5.png)

레이아웃의 기본 단위.
Horizontal, Vertical, Custom 세 가지 타입이 있다.
선을 따라서 배치하지 않는 레이아웃일 경우에 Custom을 사용하는데, 사용자 정의 방식으로 Item의 절대 크기와 위치를 지정할 수 있다.
또한 방사형으로 배치하는 레이아웃의 경우에도 Custom을 사용.
Horizontal, Vertical과 나란히 Custom 그룹을 배치할 수도 있다.


### NSCollectionLayoutSection
![스크린샷 2022-10-13 오후 3 46 19](https://user-images.githubusercontent.com/63997044/195521996-e170c31e-4a6b-4add-90a7-f84167d529af.png)

이름 그대로 CollectionView의 섹션에 적용되는 레이아웃 정의이며, 해당 섹션에 얼마나 많은 Item이 있는지 데이터 소스에 직접 매핑되는 곳이다.

레이아웃을 구성하는 간단한 방법
1. section의 정의를 지정하는 것
- 이는 FlowLayout과 유사함
2. section별로 콜백되는 클로저를 지정하고 각 section별로 해당 정의를 요청하는 것
- Compositional Layout에서 확장시킨 방법
- 레이아웃이 section들 간에 완전히 구별되기 때문에 많은 가능성이 열리게 됨

### NSCollectionViewCompositionalLayout, UICollectionViewCompositionalLayout
![스크린샷 2022-10-13 오후 3 46 47](https://user-images.githubusercontent.com/63997044/195522069-fe144160-05e7-4a38-a05b-5855ad43b91f.png)

최상위 레이아웃 클래스
NSCollectionViewCompositionalLayout: Mac OS용
UICollectionViewCompositionalLayout: iOS, tvOS


___

## Demos

1. 기본적인 List
![스크린샷 2022-10-13 오후 4 35 10](https://user-images.githubusercontent.com/63997044/195531889-696821f4-3e20-4dbd-875a-b56001f211e9.png)

2. Grid(한 줄에 5개)
![스크린샷 2022-10-13 오후 4 36 00](https://user-images.githubusercontent.com/63997044/195532051-03cb1998-2c14-49ee-a506-08ac6cde2004.png)

itemSize의 Width를 0.2만큼, groupSize의 Height를 0.2만큼 제한하여 정사각형 모양으로 Item을 출력하는 모습이다.

3. Inset Grid
![스크린샷 2022-10-13 오후 4 37 32](https://user-images.githubusercontent.com/63997044/195532437-e853174f-e462-4382-888e-fcea6e5bca18.png)

item.contentInsets의 값 지정으로 Inset이 생긴 모습.

4. Two-Column Grid
![스크린샷 2022-10-13 오후 4 38 36](https://user-images.githubusercontent.com/63997044/195532614-7f401c43-4e30-4028-b7f8-b21e01fb9b6d.png)

group의 생성자에서 count의 값을 입력해서 하나의 Group에 몇 개의 Item을 넣을 것인지 지정할 수 있다. 이 방법을 사용하면 ItemSize의 크기는 무시된다.

5. Distinct Sections
![스크린샷 2022-10-13 오후 4 40 15](https://user-images.githubusercontent.com/63997044/195533051-de64aff3-7b90-45d0-abf3-ec051f542133.png)

Collection View의 Section별로 다른 레이아웃을 사용하는 코드이다.

6. Adaptive Sections
![스크린샷 2022-10-13 오후 4 41 49](https://user-images.githubusercontent.com/63997044/195533431-69eaf9b6-ae40-47f0-a825-b65cb65d72e3.png)

Distinct Sections과 동일해보이지만 화면을 회전하여 가로 세로 길이가 늘어난 경우, 레이아웃을 재설정하면서 한 Group 내 Item의 수를 변경할 수 있다.

![스크린샷 2022-10-13 오후 4 42 35](https://user-images.githubusercontent.com/63997044/195533584-a5860a40-7c03-4bc0-a175-5e056b33c3a4.png)

원래 이렇게 나와야 하는데 시뮬레이터 오류인지 실습 코드에서는 적용되지 않는다.
___

## Advanced layouts

### NSCollectionLayoutSupplementaryItem
![스크린샷 2022-10-13 오후 4 25 20](https://user-images.githubusercontent.com/63997044/195529770-37f76bad-a497-4844-840c-aaebded96c07.png)

Badges, Headers, Footers
Compositional Layout으로 더 쉽게 만들 수 있다.

### NSCollectionLayoutAnchor
![스크린샷 2022-10-13 오후 4 25 44](https://user-images.githubusercontent.com/63997044/195529934-12e5e953-782b-4151-9f18-be4af0a5b120.png)

Supplementary를 어느 곳에 위치시킬 지 관계를 통해 지정하는 방법.

### Badges
![스크린샷 2022-10-13 오후 4 55 31](https://user-images.githubusercontent.com/63997044/195536537-1135302e-2bdb-45c3-8571-113e6a339c6b.png)
Cell의 내부에 위치하는 것이 아니라 컨텐츠 위에 떠 있음.

![스크린샷 2022-10-13 오후 4 57 24](https://user-images.githubusercontent.com/63997044/195536835-c8ff12ff-c562-411c-b768-6f9ebd8fea42.png)

NSCollectionLayoutAnchor를 사용해 Badge의 위치를 top, trailing으로 지정한 모습.

### Header and Footer
![스크린샷 2022-10-13 오후 4 26 34](https://user-images.githubusercontent.com/63997044/195530115-af2be33a-a66f-40fc-ad5f-e774f35e6694.png)

경계 보충 항목이라고도 한다.
Header와 Footer는 콘텐츠를 가리지 않고 콘텐츠의 위 혹은 아래에 해당 콘텐츠 영역을 확장

![스크린샷 2022-10-13 오후 5 00 46](https://user-images.githubusercontent.com/63997044/195537557-a84b29f7-e01f-47bd-ba51-2f6e85d2c8f3.png)
고정할 수 있음 (pinToVisibleBounds = true)

### Estimated Self-Sizing
<img width="1178" alt="스크린샷 2022-10-13 오후 5 04 05" src="https://user-images.githubusercontent.com/63997044/195538254-0a1b9d42-c734-4108-bda9-b00a9a6a3bf8.png">

1. 목표: 빠르고 정확한 Self-Sizing
2. 축(가로축, 세로축) 별로 Cell 크기 조정을 한다.
3. Supplementary Items에도 적용된다.
4. Dynamic Text에도 대응된다.

### Nested NSCollectionLayoutGroup
![스크린샷 2022-10-13 오후 4 27 32](https://user-images.githubusercontent.com/63997044/195530322-939df814-c4a2-4f13-8987-8a5dd81cbf86.png)

- Compositional Layout의 핵심: Group
- 중첩 단계에는 제한이 없다(No Limit to nesting depth)
- 중첩 그룹을 통해 자유로운 새로운 디자인을 만들어낼 수 있다.



### Nested CollectionViews
![스크린샷 2022-10-13 오후 4 28 12](https://user-images.githubusercontent.com/63997044/195530467-1105c2c9-3186-4458-80f8-804238ec475b.png)

- iOS 13의 앱스토어 디자인을 보면서 중첩 CollectionView에 대해 알아볼 수 있다.
- 복잡해보이지만 Compositional Layout은 이것을 한 줄로 끝낸다. 
- 바로 section.orthogonalScrollingBehavior = .continuous

### UICollectionViewLayoutSectionOrthogonalScrollingBehavior
![스크린샷 2022-10-13 오후 4 28 31](https://user-images.githubusercontent.com/63997044/195530521-7374e93c-04ba-498b-8ed6-04a43d71daaa.png)

- none
- continuous
- continuousGroupLeadingBoundary
- paging
- groupPaging
- groupPagingCentered

___

## Case Study - App Store

<img width="1171" alt="스크린샷 2022-10-13 오후 3 29 14" src="https://user-images.githubusercontent.com/63997044/195518924-0d103cc3-e052-4092-935d-36e4edb5846f.png">

Flow Layout만을 사용하는 경우, 수직 방향으로 스크롤하는 CollectionView를 먼저 놓고, 가로 축으로 스크롤하는 모든 섹션에 대해 추가적인 CollectionView가 필요할 것임.
이렇게 구현해도 되지만 코드가 길어진다는 문제점이 있음.
우리는 Compositional Layout을 사용해서 단일 CollectionView로 해결함

<img width="1171" alt="스크린샷 2022-10-13 오후 3 29 14" src="https://user-images.githubusercontent.com/63997044/195520070-ab803d80-3e1e-4329-8519-60da7de6cf5f.png">

iOS 13을 업데이트하면서 App Store 또한 재설계되었다. 
앱 하나를 가리키는 컨테이너 너비 100%를 차지하는 단일 레이아웃 Item이 있다. 해당 레이아웃 Item은 컨테이너 너비의 절반을 차지하는 그룹 내부에 있다. 이 그룹들은 UI를 구성하는 기본 빌딩 블록이 된다. 
그리고 이 그룹을 레이아웃 section에 고정하고 스크롤 동작을 설정한다.

<img width="1172" alt="스크린샷 2022-10-13 오후 3 29 00" src="https://user-images.githubusercontent.com/63997044/195518883-ef775dfd-7758-43a0-b586-270b3afebfb8.png">

iOS 13의 App Store는 읽는 방향이 다른 언어에 대한 지원을 하고 있다. 이 또한 Compositional Layout으로 가능하다. 이를 해결하기 위해 단 한줄의 코드도 작성할 필요가 없다.

### Compositional Layout on the App Store
![스크린샷 2022-10-13 오후 4 44 54](https://user-images.githubusercontent.com/63997044/195534071-325f0b26-9443-4d6a-a9b0-46ee9d62ed2e.png)

따라서 미세하게 관리했던 영역들을 단일 CollectionView로 병합할 수 있다.
또한 코드가 더 간결해지고 추론하기 쉬워졌으며, 앞으로의 유지보수 측면에서도 이점이 많아졌다.
___

### Summary

- iOS, tvOS, macOS 모두 적용 가능하다. 다른 플랫폼 간 코드 공유는 정말 대단한 일
- 다양한 레이아웃을 구성하게 해주는 다재다능한 도구이다
- 유연하고 빠르면서 필요한 코드도 줄어들었다.
