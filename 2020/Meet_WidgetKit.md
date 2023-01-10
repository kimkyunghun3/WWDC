# Meet WidgetKit

[Meet WidgetKit - WWDC20 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2020/10028/)

## Great Widget의 세가지 조건

- Glanceable
- Relevant
- Personalized

![](https://i.imgur.com/zoYNupc.png)

### Glanceable

- 위젯은 미니 앱이 아니다
- 정보를 간결하고 눈에 띄게 보여주는걸로 충분함

![](https://i.imgur.com/7N14c9A.jpg)

### Relevant

- 모바일에서 공간은 비싸고, 그래서 스마트 스택이 도입됨
- WidgetKit API를 사용하면 더 Relevant한 위젯을 만들 수 있음

![](https://i.imgur.com/Daex1iW.png)

### Personalized

- 날씨앱을 예시로 들면 여러 사이즈를 지원하거나, 사용자 지역의 날씨를 보여주는 등, 개인화가 되어야함

![](https://i.imgur.com/OVRMD6X.jpg)

![](https://i.imgur.com/VKx7xRQ.jpg)

### iOS, iPad OS, MacOS with SwiftUI

![](https://i.imgur.com/9PShQeR.jpg)

### How WidgetKit works

- WidgetKit의 동작방식을 캘린더 앱의 예시를 통해 알아봅시다

![](https://i.imgur.com/JQ8EqWZ.jpg)

### Timeline allows views to be ready up front

- Timeline은 View들이 미리 준비될 수 있도록 도와줌, 즉 필요한 뷰를 바로 return할 수 있다

![](https://i.imgur.com/irUKHYg.jpg)

### Can refresh from you main app

- Main app에서 일정을 수정하면, 새로운 Timeline을 return 하게 됨!

![](https://i.imgur.com/cr0Z3G2.png)

## Widget의 구성요소

![](https://i.imgur.com/jF0jlML.jpg)

### Kind

- 하나의 App Extension으로 여러 Kind의 Widget을 만들수 있게 하는게 목표

![](https://i.imgur.com/mfi2j6N.png)

- 주식 앱을 예시로 들어보면, 같은 Widget Extension을 사용하고, 같은 사이즈의 위젯인데도, 표시되는 내용이 다름

![](https://i.imgur.com/OlwxBz9.png)

### Configuration

- 위젯의 Configuration은 Static, Intent로 나뉜다

![](https://i.imgur.com/6ZXxoav.png)

- Static: 사용자의 개인화와 전혀 상관없는, 정해진 내용을 단순히 보여주는 형태

![](https://i.imgur.com/qkKv50C.png)

- Intent: 좀 더 사용자가 보여질 내용을 선택하고, 커스텀 할 수 있는 형태

![](https://i.imgur.com/94Fj9sD.png)

### supportedFamilies

- 위젯은 systemSmall, systemMedium, systemLarge 세가지 형태의 사이즈를 지원

![](https://i.imgur.com/dNr9CMz.png)

### PlaceHolder

- 유저데이터가 없는 기본적인 형태의 PlaceHolder를 가지고 있음

![](https://i.imgur.com/Wvwl1Cr.png)

- 아래 애플 기본앱들을 보면, 잘 디자인 된 placeholder를 확인할 수 있음

### Code

- 코드는 이런식인데, 다른 세션을 통해서 더 자세히 설명하겠음

![](https://i.imgur.com/YaveHub.jpg)

![](https://i.imgur.com/oIgJotI.jpg)

- 이런 위젯들이 바로  Galanceable Experience다!

![](https://i.imgur.com/x1Obbki.png)

- 위젯은 StatelessUI를 가진다
- 실제로 스크롤, 비디오재생, User와의 상호작용등은 지원하지 않음, 오직 Tap만 지원한다

![](https://i.imgur.com/xRv1jqY.jpg)

- Depp link를 이용해서, 그냥 원하는 화면을 띄워라, 위젯의 여러부분을 쪼개는건 가능

![](https://i.imgur.com/OWqu7tW.jpg)

![](https://i.imgur.com/cRdeMHJ.jpg)

## Views, timelines, and reloads

![](https://i.imgur.com/moSSCfo.jpg)

## Snapshot

- Image / ScreenShot이 아님! 실제 특정시간에 유저에게 표시되는 화면을 의미

![](https://i.imgur.com/UJCzm5j.jpg)

## Timeline

- SnapShot은 Single Entry, Timeline은 단순히 multiple entires를 의미
- 어떤 시간에, 어떤 뷰가 보여져야하는지를 결정함

![](https://i.imgur.com/I9nDkIG.png)

- iOS System은 이런 다양한 Timeline을 통해 수많은 위젯들을 동작시킴

![](https://i.imgur.com/5atq2eM.png)

![](https://i.imgur.com/VCInqI6.jpg)

- 위젯이 업데이트 되어야 할때도 있는데, 이때는 Reload라는 개념이 필요함
- Reload는 시스템이 Extension에게 새로운 Timeline을 달라고 요구하는것
- Realod 덕분에, Widget은 항상 최신의 UserData를 표시할 수 있다

![](https://i.imgur.com/Cdufzq8.png)

### Timeline Protocol

![](https://i.imgur.com/YnPxwze.png)

- snapshot, timeline을 completion을 통해서 반환해야함!

![](https://i.imgur.com/wVdalCE.jpg)

- Reload 정책도 직접 정해줄 수 있다

![](https://i.imgur.com/5kTP03h.png)

![](https://i.imgur.com/cBxUZzQ.png)

- Background Noti를 받거나, 유저가 데이터를 바꾸면 Reload를 요청하게 됨

![](https://i.imgur.com/TDBiVV9.png)

- 이때 WidgetCenter를 통해서 timeline을 reload하도록 요청할 수 있다

![](https://i.imgur.com/8AhvYEX.png)

- 서버 통시이 필요할때, URLSession도 사용가능

![](https://i.imgur.com/73gYRu1.png)
