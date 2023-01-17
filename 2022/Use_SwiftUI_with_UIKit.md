# Use SwiftUI with UIKit

[WWDC Video](https://developer.apple.com/videos/play/wwdc2022/10072/)

# 목차

- **UIHostingController**
- **Bridging data**
- **SwiftUI in cells**
- **flow for cells**

## UIHostingController

![](https://i.imgur.com/6ZJAGZ2.png)

UIHostingController는 단순히 SwiftUI View를 ViewControlelr로 감싼것

```swift
  @IBAction func presentDidTap(_ sender: Any) {
    let heartRateView = HeartRateView()
    let hostingController = UIHostingController(rootView: heartRateView)
    
    self.present(hostingController, animated: true)
  }
```

![](https://i.imgur.com/P6ytsWE.png)

popOver는 preferredContentSize를 사용하여 명시적인 크기를 지정한다

```swift
  @IBAction func popoverDidTap(_ sender: Any) {
    let heartRateView = HeartRateView()
    let hostingController = UIHostingController(rootView: heartRateView)
    
    hostingController.modalPresentationStyle = .popover
    hostingController.preferredContentSize = .init(width: 100, height: 100)
    
    hostingController.popoverPresentationController?.sourceView = sender as! UIButton
    
    self.present(hostingController, animated: true)
  }
```

iOS16 부터 도입된 sizingOptions를 사용해보면, OS가 Size를 알아서 잘! 구해줌

```swift
    ...
    //hostingController.modalPresentationStyle = .popover
    hostingController.sizingOptions = .preferredContentSize
    ...
```

![]()

<img src = "https://i.imgur.com/9fHttrl.png" width = 300>
<img src = "https://i.imgur.com/rbycMnH.png" width = 300>

**요약정리하면, UIKit -> SwiftUI의 화면전환이 필요할때 사용!**


## Bridging data

![](https://i.imgur.com/U3quBZ4.png)

어떤식으로 AppData를 SwiftUI View에 전달해야 하는지를 알려줌!

### 방법1: @State로 전달받기
![](https://i.imgur.com/nD1Hl9u.png)

SwiftUI에선 @State로 데이터를 관리하지만, 좋은 방법이 아님
이유는 Data Essentials in SwiftUI 세션을 참고

### 방법2: argument를 전달하기
![](https://i.imgur.com/mBycH75.png)

```swift
class MainViewController: UIViewController {
  let hostingController: UIHostingController<HeartRateView>
  
  var beatsPerMinute: Int {
    didSet { update() }
  }
  
  required init?(coder: NSCoder) {
    self.beatsPerMinute = 124
    self.hostingController = UIHostingController(rootView: HeartRateView(beatsPerMinute: beatsPerMinute))
    super.init(coder: coder)
  }
  
  @IBAction func presentDidTap(_ sender: Any) {
    DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
      self.beatsPerMinute += 100
    }
    
    present(hostingController, animated: true)
  }
  
  func update() {
    hostingController.rootView = HeartRateView(beatsPerMinute: beatsPerMinute)
  }
}
```

SwiftUI View에는 프로퍼티 값 자체를 전달하고, 나중에 값이 업데이트 되었을때 SwiftUI View 전체를 다시 그리는 방법

![](https://i.imgur.com/ZGxWcuM.gif)

### 방법3: @ObservedObject를 전달하기
![](https://i.imgur.com/5foCgPZ.png)

1. ObservableObject를 채택한 DataModel 만들기
2. SwiftUI에 @ObservedObject로 선언하기

#### DATA Model
```swift
class HeartData: ObservableObject {
  @Published var beats: Int = 124
}
```

#### UIKIT VC

```swift
class MainViewController: UIViewController {
  let data: HeartData
  let hostingController: UIHostingController<HeartRateView>
  
  required init?(coder: NSCoder) {
    self.data = HeartData()
    self.hostingController = UIHostingController(rootView: HeartRateView(data: data))
    super.init(coder: coder)
  }
  
  @IBAction func presentDidTap(_ sender: Any) {
    DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
      self.data.beats += 100
    }
    
    present(hostingController, animated: true)
  }
}
```

#### SwiftUI View

```swift
struct HeartRateView: View {
  
  @ObservedObject var data: HeartData
  
  var body: some View {
    VStack(alignment: .leading) {
      HStack {
        Image(systemName: "heart.fill")
        Text("심박수")
      }.foregroundColor(.red)
      
      HStack {
        Text("\(data.beats)")
          .font(.largeTitle)
        Text("BPM")
          .foregroundColor(.secondary)
        
        Spacer()
      }
    }
    .padding()
  }
}
```


Observable 프로토콜이 채택된 클래스의 내부에 있는 @Published 변수의 값이 변하게 되면, SwiftUI가 변화를 알아차리고, 직접 View를 업데이트 한다.
최종 결과는 위와 동일하지만, 애플에서 가장 추천하는 방법임

![](https://i.imgur.com/SZHeKTx.png)

## SwiftUI in cells

### UIHostingConfiguration

![](https://i.imgur.com/3KY8Bg3.png)

UIView나, UIViewController의 도움 없이, Cell을 SwiftUI를 이용해서 구성할 수 있음


#### Cell Configuration에 대한 자세한 내용은 다음 세션 참고 :)
![](https://i.imgur.com/kuGMEYJ.png)


```swift
  func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCell(withIdentifier: "cell", for: indexPath)
    
    cell.contentConfiguration = UIHostingConfiguration{
      HStack {
        Label("Heart Rate", systemImage: "heart.fill")
          .foregroundColor(.pink)
          .font(.system(.subheadline, weight: .semibold))

        Spacer()
        
        Text(Date(), style: .time)
          .font(.footnote)
      }
    }
    
    return cell
  }
```

![](https://i.imgur.com/dQ7IA1g.jpg)

#### margins modifier를 사용해서, Cell 여백을 줄 수도 있음!

```swift
.margins([.horizontal, .vertical], 100)
```

![](https://i.imgur.com/Bl1rWeN.jpg)

#### swipeAction modifier를 이용해서, swipe Action을 정의할 수 있음

- indexPath 사용하지 말것!

![](https://i.imgur.com/H9CoBvE.png)

```swift
.swipeActions(edge: .trailing) {
  Button("WWDC") { // do someting }
}
```

![](https://i.imgur.com/cKQftPy.jpg)

## Data flow for cells

![](https://i.imgur.com/1I1IQJD.png)

Diffable Data Source & SnapShot을 이용하면, snapshot이 변했을때,
UIKit이 자동으로 Cell을 업데이트 해줬음

#### UIKit Cell의 경우

```swift
struct Model: Hashable {
    let id = UUID()
    let value: Int
}

var items: [Model]
```

#### SwiftUI Cell의 경우

![](https://i.imgur.com/daJx75S.png)

model이 바뀌면, Snapshot과 상관없이 SwiftUI가 직접 데이터를 업데이트 함!
(당연히 @ObservableObject로 연결해 놨을때만..!)

그런데 UIKit을 거치지 않고 Cell의 데이터를 업데이트 할때,
만약 Cell의 높이가 변경되어야 한다면,
UIKit은 도대체 어떻게 CollectionViewCell의 높이를 업데이트 하는걸까?

#### What's new in UIKit 세션을 보면 알 수 있다
- Self-resizing cells
![](https://i.imgur.com/g60tQgf.png)


### UIkit -> SwiftUI, SwiftUI -> UIKit

![](https://i.imgur.com/Ewom2ug.png)

#### UIKit -> SwiftUI

UIKit은 ObservableObject 데이터 객체를 SwiftUI View에 주입시켜서 데이터를 전달
SWiftUI는 ObservableObject로 해당 객체를 구독해서 데이터를 전달받음

#### SwiftUI -> UIKit

전달받은 객체는 클래스기 때문에, SwiftUI에서의 변경사항이 객체에도 고스란히 반영됨
이런 매커니즘으로 SwiftUI와 UIKit은 부드러운 데이터 공유가 가능

### Two way to add SwiftUI

- UIHostingController
- UIHostingConfiguration

![](https://i.imgur.com/Eyo7rlz.png)
