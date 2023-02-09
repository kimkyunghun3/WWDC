# Compose custom layouts with SwiftUI

https://developer.apple.com/documentation/swiftui/composing_custom_layouts_with_swiftui

![](https://i.imgur.com/5OhrLZv.png)

![](https://i.imgur.com/k5JrYmv.png)

# 목차

Grid
Layout
ViewThatFits
AnyLayout

## Grid

![](https://i.imgur.com/21XXaUE.png)

동물 선호도를 조사하는 앱을 만들고자 한다

![](https://i.imgur.com/x6vV2Ad.png)

먼저 리더보드를 만들껀데, 위와 같은 UI를 만들고 싶음
(name과, score는 가장 긴 값에 맞추고, 각각 좌/우 정렬)


### 1차 시도

```swift
ForEach(pets) { pet in
  HStack {
    Text(pet.type)
    ProgressView(
      value: Double(pet.votes),
      total: Double(totalVotes)
    )
    Text("\(pet.votes)")
  }
}
```

![](https://i.imgur.com/eXGKzqw.png)

가장 먼저 생각할 수 있는 접근법으로 HStack을 활용할 수 있지만
각각의 HStack은 서로를 전혀 모르기 때문에 목표를 달성할 수 없다..!

### 2차 시도

![](https://i.imgur.com/RFNs86X.png)

```swift
Grid(alignment: .leading) {
  ForEach(pets) { pet in
    GridRow {
      Text(pet.type)
      ProgressView(
        value: Double(pet.votes),
        total: Double(totalVotes)
      )
      Text("\(pet.votes)")
        .gridColumnAlignment(.trailing)
    }

    GridRow {
      Divider().gridCellColumns(3)
    }
  }
}
```

Grid(iOS 16.0+)을 사용하면, 목표를 매우 간단히 달성 가능
전체를 Grid로 감싸고, 한줄 한줄을 만들때 HStack 대신 GridRow를 사용하면, 각 열들이 서로 동일한 영역을 가지도록 설정할 수 있다..!!

## Layout

![](https://i.imgur.com/RF003MF.png)
모든 버튼이, 가장 넓은 버튼의 크기와 동일하게끔 하고 싶음!

![](https://i.imgur.com/j4Ykmoc.png)
이런거 x (Text에 맞춰 줄어들기 금지)

![](https://i.imgur.com/syWBGQm.png)
이런것도 x (늘어나기 금지)

![](https://i.imgur.com/xT2HFxW.png)
근데 그냥 구현해 보면, 아래같은 UI를 그려주는걸 확인할 수 있음


### SwiftUI는 어떻게 View Size를 자동으로 해주는걸까?

![](https://i.imgur.com/UG2bZwN.png)

먼저 상위 View가 subviews에 사이즈를 제안한다

Container
-> HStack
-> Buttons
-> Text

이제 반대로 Text가 String의 길이를 사용해서 실제 사이즈를 정하고, 상위 View에 보고한다

Text ->
Buttons ->
HStack ->
Container

### 1차 시도

![](https://i.imgur.com/BxZ3qW6.png)
Text()에 .frame modifier를 사용해서 최대 크기를 주면, 위와같은 UI를 만들 수는 있는데,원하던 바는 아님..!


### 2차시도

Layout 프로토콜(iOS 16+)을 활용한 CustomLayout 만들기


#### sizeThatFits

Layout Container의 크기를 결정하는 매서드

![](https://i.imgur.com/TD40EnB.png)

#### placeSubviews

subview들을 올바른 위치에 배치하는 매서드

![](https://i.imgur.com/e9N2oXz.png)



![](https://i.imgur.com/TZSucuP.png)

![](https://i.imgur.com/bmiJHLZ.png)

![](https://i.imgur.com/GJjArMa.png)

![](https://i.imgur.com/MULwqU6.png)

![](https://i.imgur.com/H7l26Qp.png)


## ViewThatFits

주어진 View중 현재 화면과 가장 잘 맞는 View를 알아서 골라줌!(iOS 16+)


![](https://i.imgur.com/6gdparm.png)
아래와 같이 큰 글씨에서는 버튼이 잘리는 문제가 있음

![](https://i.imgur.com/dn51Ari.png)
이럴땐 시스템이 자동으로 판단해서 세로 형태의 레이아웃을 선택함!

## AnyLayout

**Ranking이 View의 layout에 반영되게 하려면?**

![](https://i.imgur.com/YuoJdai.png)
LayoutValueKey를 통해 데이터를 전달할 수 있음!!

**Layout을 동적으로 바꾸려면?**

![](https://i.imgur.com/mdi3tUu.png)
AnyLayout을 통해 View의 layout을 매우쉽게 바꿀 수 있다!



