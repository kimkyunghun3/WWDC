# Advances in UI Data Sources

[wwdc Advances in UI Data Sources](https://developer.apple.com/videos/play/wwdc2019/220/)

순서는 아래와 같다

![](https://i.imgur.com/Nt6oxNC.png)

현재 UI Data 어떻게 상호작용하는지에 대해 알아보자

컬렉션뷰 데이터소스

![](https://i.imgur.com/8qF7ipD.jpg)

섹션 수, 아이템 수.. 뻔한거 알려준다

simple, flexible하다

UI  업데이트 하는법

![](https://i.imgur.com/NXGpQr2.jpg)

Web service response → didChange 알려준다

![](https://i.imgur.com/UXhIH3L.jpg)

어떻게 이렇게 값이 변하고 이런 부분을 업데이트하는데 이렇게 업데이트 시 에러가 난다

![](https://i.imgur.com/psrlfMV.jpg)

reloadData하게되면 애니메이션이 이상해서 UX를 혼란스럽게 만든다

이제 새 접근한다!

DiffableDataSource

![](https://i.imgur.com/Qivkyj6.jpg)

snapshots도 사용한다

![](https://i.imgur.com/vxl6qtG.png)

Snapshot에 대한 설명

컨트롤러 바뀌었을 때 어떻게 하는지에 대하 ㄴ상황

![](https://i.imgur.com/mbiAUdm.jpg)

현재와 새 snapshot를 알게되고 apply통해 새것을 바꾼다

이곳들에서 사용한다

![](https://i.imgur.com/N0fWIsV.jpg)

### Demo

스냅샷을 만들고 이것을 활용해서 UI 업데이트한다.

그래서 자동적으로 UI 요소들으 바꾸게 만들어준다.

![](https://i.imgur.com/W2SZFBp.jpg)

매칭되는 것을 검색어에 할 때마다 자동적으로 아래 추천들 UI가 업데이트되도록한다.

스냅킷 사용하는법 알려준다

![](https://i.imgur.com/UmcZRdt.jpg)

구조체나 enum사용할 때 Hashable해야한다. 객체들을 그렇게 만들어야한다.

만약 이전과 현재 바뀐게 없다면 자동적으로 DiffableDataSource에서 알게된다. 

그리고 identifier를 활용해서 변하는 것이 있는지 없는지 확인한다.

그리고 제네릭으로 NSDiffableDataSourceSnapshots에 SectionType, ItemIdentifierType를 받는다.

![](https://i.imgur.com/Wtr4nLf.jpg)

그리고 세션도 여러개 받을 수 있다 enum를 활용해서

구조체에 Hasable로 받고

identifer 만들어서 유니크하게 가지고 있는다.

새로운 데이터 복사하면 카피하는게 아니라 해시어블로 가지고 있는다.

![](https://i.imgur.com/0F6fJN9.jpg)

이제 데이터소스 만들어줘야한다.

![](https://i.imgur.com/U6D9cN1.jpg)

후행클로저로 만들어준다.

셀에 원하는 것으로 만들어주는 것을 정하고 원하는 데이터들을 적어둔다.

다음으로는 

와이파이 설정관련( 세션이 2개인 것 )

![](https://i.imgur.com/byHwbsl.jpg)

다이나믹하게 업데이트되도록 한다.

UI update

![](https://i.imgur.com/jo3is91.jpg)

스냅샷도만들어주고.. 세션넣고 아이템 넣고한다.

이제 Item 모델에대해 해시어블 체택 해주고 사용해서 아이템들을 유니크하게 만든다

![](https://i.imgur.com/kjeqN2b.png)

이제 와이파이 데이터소스 만드는 곳

![](https://i.imgur.com/70XGTxm.jpg)

요런거 정렬만드는 컬렉션뷰 만들기

![](https://i.imgur.com/lXebB2J.jpg)

![](https://i.imgur.com/1ytGWIG.jpg)

현재 스냅상태의 스냅샷을 부른다

매번 처음부터 시작할 필요없이 업데이트된 부분부터 시작할 수 있따

나머지부분은 뭐 동일해서.. 바꿀게 없다!

### Considerations

항상 apply() 를 호출해라! 이전꺼로 더하고 업데이트 적용 메서드 호출 노놋!

스냅샷 부르는 방법 2가지 있다

![](https://i.imgur.com/MeMWv7B.jpg)

identifier에 대한 것

![](https://i.imgur.com/uoa1VS1.png)

### 예시

![](https://i.imgur.com/kMN6bgY.jpg)

indexPath based API는?

![](https://i.imgur.com/yjzugTA.jpg)


### 성능

![](https://i.imgur.com/yAvZhcw.png)

백그라운드에서 어플라이 한다음 완료되면 메인 큐로 옮겨서 적용한다

아닌가..?

![](https://i.imgur.com/gEb2whb.jpg)

믹스하지말고 하나에서 정하라고 하네!

Share Sheet

![](https://i.imgur.com/MSmrzA3.png)


에어드랍에 대해서도 알려준다

### 결론

![](https://i.imgur.com/o69U6Ik.jpg)
