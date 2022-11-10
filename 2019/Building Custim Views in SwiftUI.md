## Building Custom Views in SwiftUI
#### Graphic effects and layout

---

## ✏️ Layout Basics

> **1. Parent Proposes Size for Child
2. Child Chooses Its Own Size ⭐️
3. Parent Places Child in Parent's Coordinate Space
4. SwiftUI rounds coordinates to nearest pixel**

### 1️⃣ Parent Proposes Size for Child
- 부모 뷰는 자식 뷰에게 사이즈를 제안한다

![](https://velog.velcdn.com/images/marisol/post/e72d1a90-e968-4cf8-936e-0334abafb661/image.png)

### 2️⃣ Child Chooses Its Own Size
- 자식 뷰는 자신의 사이즈를 정하고, 부모 뷰에게 알려준다

![](https://velog.velcdn.com/images/marisol/post/837f2349-0c3e-4529-b182-2de2a582b0de/image.png)

### 3️⃣ Parent Places Child in Parent's Coordinate Space
- 부모 뷰는 자식뷰가 결정한 사이즈대로 자식 뷰를 자신의 좌표 공간에 놓는다. 
- default는 center

![](https://velog.velcdn.com/images/marisol/post/a519e24e-57dc-4775-be50-3cce406f5da1/image.png)

### 4️⃣ SwiftUI rounds coordinates to nearest pixel
- SwiftUI가 좌표를 가장 가까운 픽셀로 반올림하기 때문에 안티앨리어싱 대신 clear edge를 얻을 수 있다.

![](https://velog.velcdn.com/images/marisol/post/5a7d4aff-8896-4499-b5aa-de9e3b2657a0/image.png)![](https://velog.velcdn.com/images/marisol/post/a9ce0cd3-24ee-4ea8-8fa9-76c7b3b8553f/image.png)


> RootView는 Text에 size를 제안하며, RootView이기 때문에 전체 safe area 사이즈를 제공한다.
size를 제안하지만, SwiftUI에서 자식 뷰에게 크기를 강요할 수 있는 방법은 없다.
자식 뷰가 결정한 사이즈에 따라 기본으로 부모 뷰의 center에 위치시킨다.

#### ⚠️ Layout Neutral

body가 있는 View의 최상위 layer는 Layout Neutral이라고 부르는 뷰이다.
Layout Neutral의 bounds는 그 Layout Neutral의 body bounds에 의해 정의된다.
(부모 뷰에게서 제안 받은 사이즈를 그대로 자식 뷰에게 전달하고,
자식 뷰가 결정한 사이즈를 그대로 감싸는 사이즈로 자신의 bounds를 결정함)

![](https://velog.velcdn.com/images/marisol/post/42a388e4-eded-426c-97cf-b9e184624ca6/image.png)

위와 같은 코드가 있다면, 
1️⃣ Text가 모든 edges에 10씩 패딩을 주고
2️⃣ 패딩 준 만큼 Background를 Green Color로 칠하고
3️⃣ 부모 뷰가 가운데에 위치시키게 될 것이다.

만약 Background -> Padding 순서였다면 Green Color가 패딩이 없는 상태로 칠해지게 될 것!

![](https://velog.velcdn.com/images/marisol/post/eea695f2-9c8e-45a2-a1d1-32b7eb0996dd/image.png)

이 경우는 view의 body가 20x20의 고정된 이미지인데,
image를 resizable로 표시하지 않는 이상, 크기가 고정되어 있다.
그래서 만약에 frame을 30x30으로 주더라도, 해당 view의 frame 영역만 30x30으로 늘어날 뿐, 이미지 크기 자체는 변하지 않는다.
frame은 제약 조건 (constraint)이 아니라, 그저 액자 같은 뷰이다.

---

## ✏️ HStack and VStack

Stack은 자식뷰의 linear chain이 아니라, 동등한 입장에서 공간을 차지하기 위해 경쟁한다.

### 1️⃣ 부모 뷰가 제공한 공간이 충분한 경우
![](https://velog.velcdn.com/images/marisol/post/55bdc1f3-57e7-40a3-b83b-ef40bf551648/image.png)

Stack은 내부 간격 요구 사항을 계산하고, 제안된 width에서 이를 빼서 할당되지 않은 공간을 제공한다.
크기를 알 수 없는 3개의 자식 뷰가 있는데, 남은 공간을 3등분 하고, 그 중 하나를 가장 flexible하지 않은 자식 뷰를 위한 크기로 제안한다. 
(이미지가 고정된 size이기 때문에 가장 flexible하지 않다)
이미지 한테 공간을 할당하고 남은 사이즈를 둘로 나누고, 덜 flexible한 "Delicious"에게 공간을 준다. 
그 공간은 Delicious가 들어가고도 남기 때문에, Avocado Toast를 위한 공간은 충분하다.

### 2️⃣ 부모 뷰가 제공한 공간이 충분하지 않은 경우
![](https://velog.velcdn.com/images/marisol/post/338a4954-c798-4a18-8ee8-9da8d9df65d5/image.png)

"Delicious" 보다 "Avocado Toast"를 보여주고 싶은 경우, LayoutPriority를 설정할 수 있다.
Stack의 하위 항목들이 서로 다른 레이아웃 우선 순위를 가질 때, Stack은 할당되지 않은 공간을 가져가고,
우선순위가 낮은 모든 하위 하옴ㄱ들의 최소 너비를 확보한 다음,
나머지는 가장 높은 우선 순위 값을 가진 하위 항목이 나눠 가진다.

이 경우, 이미지의 폭과 최소로 축소된 "..."를 제외한 모든 가용 공간이 Avocado Toast에게 제공된다.
![](https://velog.velcdn.com/images/marisol/post/a8337a97-e184-41a7-84ac-e1cfd0b1fa44/image.png)

### Alignments

Stack에는 Alignments를 설정할 수 있으며, 
HStack의 경우는 요소들의 vertical alignment를 설정할 수 있고,
VStack의 경우는 요소들의 horizontal alignment를 설정할 수 있다.
따로 설정하지 않으면 기본으로 center 정렬이다.

그런데 만약에 Delicious의 font를 Caption으로 줄이고 alignment를 bottom으로 줬다면
세 가지 뷰들의 bottom이 모두 달라진다.

![](https://velog.velcdn.com/images/marisol/post/48f268c8-c045-4109-afa5-d3642c67c628/image.png)

alignment를 .lastTextBaseline으로 설정하면, 마지막 요소의 base line을 기준으로 정렬된다.
이미지만 살짝 더 아래로 내리고 싶을때, 커스텀해줄 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/2aca3c80-f873-4767-a0f1-652066ff9928/image.png)

#### 🧐 만약 새롭게 Alignment를 정의하고 싶다면?

![](https://velog.velcdn.com/images/marisol/post/21639539-4d05-47dd-9d3a-a8625cded7e3/image.png)![](https://velog.velcdn.com/images/marisol/post/0bb95a81-881f-4b60-8852-c9546a8054e5/image.png)

---

## ✏️ Graphics in SwiftUI

![](https://velog.velcdn.com/images/marisol/post/40875e0f-9e09-4ae3-9443-651de8fa827a/image.png)

![](https://velog.velcdn.com/images/marisol/post/47b99326-329c-4400-80b9-26c90b6547c1/image.png)

![](https://velog.velcdn.com/images/marisol/post/598248d1-8e44-465f-8c65-e7a7a9e0db4d/image.png)

