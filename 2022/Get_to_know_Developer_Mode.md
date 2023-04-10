# Get to know Developer Mode

[WWDC Link](https://developer.apple.com/videos/play/wwdc2022/110344/)

![](https://i.imgur.com/APPw9sK.jpg)

## 목차
- What is Developer Mode
- Using Developer Mode
- Automation flows

## What is Developer Mode?

![](https://i.imgur.com/69nAB0X.png)

- 개발자의 workflows를 활성화 시킬 수 있는 모드 (iOS 16, watchOS9 이상)
- 기본적으로는 비활성화 되어 있고, 명시적 등록이 필요하다
- 재부팅이나 시스템 업데이트 후에도 설정이 유지된다
- 설정은 알아서 해준다

### Why Developer Mode?

개발자가 아닌 유저들에게 보안을 더 향상시키기 위해서 도입

- 개발자 기능은 Target Attack(표적공격)에 악용되고 있다
- 개발자가 아닌 사람들은 개발자 기능이 필요 없다

### Developer Mode and distribution

대부분의 배포와 테스트 과정에서는 개발자 모드가 필요 없음
- Test Flight
- Enterprise (In - House) distribution
- App Store

오직 local 개발(Xcode 직접 빌드)에서만 개발자 모드가 필요함

## Using Developer Mode

### When to turn on Developer Mode

- singed applications을 실행해야 할 때
- application을 debug & instrument 할 때
- automate testing을 수행할 때

### How to turn on Developer Mode

- 수동으로: 설정 -> 개인정보 보호 
- 자동으로: devmodectl 이용

## Automation flows

비밀번호가 없는 기기만 자동으로 개발자 모드를 등록할 수 있다
여러 기기를 한꺼번에 등록하는 것도 가능하다

> devmodectl streaming

![](https://i.imgur.com/bNMocZI.png)

> 개발자 모드가 켜지면, 디바이스에 noti가 날라온다

![](https://i.imgur.com/f2DbMys.png)

## Warp-up

- iOS 16 & watchOS 9 에서는 Developer Mode를 켜야한다
- 자동화를 위해선 devmodectl을 사용해라
- Mac apps에 관해서 궁굼하다면, What's new in notarization for Mac apps 세션을 참고해라
