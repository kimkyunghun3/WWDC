# Hello Swift Charts

[WWDC Link](https://developer.apple.com/videos/play/wwdc2022/10136/)

![](https://i.imgur.com/BKB4HQi.jpg)

현재 우리는 수많은 데이터에 둘러쌓여 살고있음
단순데이터는 쓸모가 없고, 데이터를 시각화해서 이해하는게 중요함
Apple 생태계에서는 이미 훌륭한 예시들이 있다

![](https://i.imgur.com/OXURAQv.jpg)

그동안 차트를 그릴려면?

- UIBezierPath 이용하기
- 서드파티 라이브러리 `Charts` 사용

### 이제 Apple의 FirstParty Framework인 Charts를 쓰자! (iOS 16.0 이상)

![](https://i.imgur.com/pGKL5Xs.jpg)

## EX) Food Truch App

트럭이 판매한 펜케이크를 추적하는 앱을 만들어 봤다 (진짜?)

![](https://i.imgur.com/Gx8V28t.jpg)

#### 1) 매우 간단한 차트 만들기

```swift
import SwiftUI
import Charts

struct StylesDetailsChart: View {
    var body: some View {
        Chart {
            BarMark(
                x: .value("Name", "Cachpa"),
                y: .value("Sales", 916)
            )
            
            BarMark(
                x: .value("Name", "Injera"),
                y: .value("Sales", 850)
            )
        }
    }
}

struct StylesDetailsChart_Previews: PreviewProvider {
    static var previews: some View {
        StylesDetailsChart()
    }
}

```

![](https://i.imgur.com/hlnNf3L.png)

#### 2) 모델을 활용한 차트 만들기

```swift
import SwiftUI
import Charts

struct Pancakes: Identifiable {
    let name: String
    let sales: Int
    
    var id: String { name }
}

let sales: [Pancakes] = [
    .init(name: "Cachapa", sales: 916),
    .init(name: "Injera", sales: 850),
    .init(name: "Crepe", sales: 802),
    .init(name: "Jian Bing", sales: 753),
    .init(name: "Dosa", sales: 654),
    .init(name: "American", sales: 618),
]

struct StylesDetailsChart: View {
    var body: some View {
        Chart {
            ForEach(sales) { element in
                BarMark(
                    x: .value("Name", element.name),
                    y: .value("Sales", element.sales)
                )
            }
        }
    }
}

struct StylesDetailsChart_Previews: PreviewProvider {
    static var previews: some View {
        StylesDetailsChart()
    }
}

```

![](https://i.imgur.com/z4frsZ3.png)

### EX) Food Truck App Detail

푸드트럭은 쿠퍼티노, 샌프란시스코를 찾아감
두 도시의 일일 판매량을 차트를 이용해서 시각화 해보자

![](https://i.imgur.com/esppZOh.png)

#### 1) 두 도시를 차트로 비교하기 (애니메이션)

```swift
func date(_ year: Int, _ month: Int, _ day: Int) -> Date {
    Calendar.current.date(from: DateComponents(year: year, month: month, day: day))!
}

struct SalesSummary: Identifiable {
    let weekday: Date
    let sales: Int
    
    var id: Date { weekday }
}

let cupertinoData: [SalesSummary] = [
    .init(weekday: date(2023, 4, 6), sales: 54),
    .init(weekday: date(2023, 4, 7), sales: 42),
    .init(weekday: date(2023, 4, 8), sales: 88),
    .init(weekday: date(2023, 4, 9), sales: 49),
    .init(weekday: date(2023, 4, 10), sales: 42),
    .init(weekday: date(2023, 4, 11), sales: 125),
    .init(weekday: date(2023, 4, 12), sales: 67)
]

let sfData: [SalesSummary] = [
    .init(weekday: date(2023, 4, 6), sales: 81),
    .init(weekday: date(2023, 4, 7), sales: 90),
    .init(weekday: date(2023, 4, 8), sales: 52),
    .init(weekday: date(2023, 4, 9), sales: 72),
    .init(weekday: date(2023, 4, 10), sales: 84),
    .init(weekday: date(2023, 4, 11), sales: 84),
    .init(weekday: date(2023, 4, 12), sales: 137)
]

enum City: String {
    case cupertino
    case sanFrancisco
}

struct CupertinoDetailsChart: View {
    @State var city: City = .cupertino
    
    var data: [SalesSummary] {
        switch city {
        case .cupertino: return cupertinoData
        case .sanFrancisco: return sfData
        }
    }
    
    var body: some View {
        VStack {
            Picker("City", selection: $city.animation(.easeIn)) {
                Text("Cupertino").tag(City.cupertino)
                Text("San Francisco").tag(City.sanFrancisco)
            }
            .pickerStyle(.segmented)
            Chart(data) { element in
                BarMark(
                    x: .value("Day", element.weekday, unit: .day),
                    y: .value("Sales", element.sales)
                )
            }
        }
    }
}

struct CupertinoDetailsChart_Previews: PreviewProvider {
    static var previews: some View {
        CupertinoDetailsChart()
    }
}

```

![](https://i.imgur.com/CAaQC5a.gif)

#### 2) 두 도시를 한 차트에서 같이 비교하기

```swift
import SwiftUI
import Charts

struct Series: Identifiable {
    let city: String
    let sales: [SalesSummary]
    
    var id: String { city }
}

let seriesData: [Series] = [
    .init(city: "Cupertino", sales: cupertinoData),
    .init(city: "San Francisco", sales: sfData)
]

struct LocationDetailsChart: View {
    var body: some View {
        Chart(seriesData) { series in
            ForEach(series.sales) { element in
                BarMark(
                    x: .value("Day", element.weekday, unit: .day),
                    y: .value("Sales", element.sales)
                )
                .foregroundStyle(by: .value("City", series.city))
            }
        }
        .padding()
    }
}

struct LocationDetailsChart_Previews: PreviewProvider {
    static var previews: some View {
        LocationDetailsChart()
    }
}
```

![](https://i.imgur.com/czhCzaP.png)

#### 4) LineMark

```swift
LineMark(
    x: .value("Day", element.weekday, unit: .day),
    y: .value("Sales", element.sales)
)
.foregroundStyle(by: .value("City", series.city))
```

![](https://i.imgur.com/5V868Bv.png)

#### 4) PointMark

```swift
PointMark(
    x: .value("Day", element.weekday, unit: .day),
    y: .value("Sales", element.sales)
)
.foregroundStyle(by: .value("City", series.city))
```

![](https://i.imgur.com/bBD6p78.png)

#### 5) Symbol

```swift
PointMark(
    x: .value("Day", element.weekday, unit: .day),
    y: .value("Sales", element.sales)
)
.foregroundStyle(by: .value("City", series.city))
.symbol(by: .value("City", series.city))
```

![](https://i.imgur.com/prKsea9.png)

### 정리

많은 Marks와 Properties 그 밖에 Style을 조합하여 어느 차트튼 만들 수 있다!

![](https://i.imgur.com/SVZkJDQ.jpg)

![](https://i.imgur.com/jd5eYH9.jpg)
