# Meet the UIKit button system

> iOS에서 새로 업데이트 된 버튼에 대해 알아보자!

### Buttons in iOS 15

![스크린샷 2023-01-05 오전 3 10 41](https://user-images.githubusercontent.com/63997044/210724103-332647c0-3c4a-4c58-a758-4a6fe5965acd.png)

버튼은 사용자의 입력을 요청하는 가장 일반적인 방법.
iOS 15에서 UIKit은 기본적으로 네 가지 스타일을 제공한다.(Plain / Gray / Tinted / Filled)
그리고 다음과 같은 기능들이 추가되었다.
- Dynamic Type(supported by default)
- Multiline
- Accessibility
- Easier customization -> UIButtonConfiguration


### UIButtonConfiguration

![스크린샷 2023-01-05 오전 3 11 45](https://user-images.githubusercontent.com/63997044/210724162-f9e209da-ae92-4281-9c8b-52727d587325.png)

`Sign In` 버튼을 구성하는 코드는 다음과 같다. 이 버튼은 매우 중요한 버튼이므로 우리는 이 버튼을 `filled` 스타일로 변경하여 더 눈에 띄게 만들어볼 것이다.

![스크린샷 2023-01-05 오전 3 12 18](https://user-images.githubusercontent.com/63997044/210724202-4c3372a8-97b1-4b76-8432-b9609afea95a.png)

버튼의 configuration 프로퍼티에 `.filled()` 값을 입력하기만 하면 된다. 버튼의 타이틀과 이미지에 대한 코드를 각각 업데이트하지 않고 버튼 스타일을 쉽게 변경할 수 있다.

![스크린샷 2023-01-05 오전 3 13 31](https://user-images.githubusercontent.com/63997044/210724245-9bc8f09c-4580-4486-aef6-e977f3ef9544.png)

UIButtonConfiguration에는 스타일 말고도 다양한 옵션이 있다. `Add to Cart` 버튼은 지금 기본적인 버튼 스타일이지만 UIButtonConfiguration을 활용하여 UX를 강화할 것이다.

![스크린샷 2023-01-05 오전 3 14 03](https://user-images.githubusercontent.com/63997044/210724826-b3d88968-4efc-4fe8-ab1b-f3435047d8cb.png)

`.tinted()` 스타일의 config에 `title`, `image`, `imagePlacement` 설정을 할 수 있다.
버튼에 이미지를 추가하여 버튼이 어떤 역할을 하는지 한 눈에 파악할 수 있게 되었다.

![스크린샷 2023-01-05 오전 3 14 42](https://user-images.githubusercontent.com/63997044/210725400-46902974-fe6d-4811-9ce5-ae5a3100fd7c.png)

여기에 추가적으로 적용할 두 가지 기능이 있다.
1. subtitle 표시
2. 버튼을 누르면 filled로 이미지 전환

![스크린샷 2023-01-05 오전 3 16 17](https://user-images.githubusercontent.com/63997044/210725036-d57956a4-432d-4d35-b0b3-1fcd8696da25.png)

이를 위해 적절한 시점에 이미지 속성을 업데이트해야 하기 떄문에 `configurationUpdateHandler`가 필요하다.
핸들러를 사용하면 좋은 점은 업데이트 코드를 중앙집중할 수 있다는 점이다.

![스크린샷 2023-01-05 오전 3 16 34](https://user-images.githubusercontent.com/63997044/210725048-868d66a4-55b6-486b-81f3-9cc1271e7f18.png)

configurationUpdateHandler를 호출하는 방법: `setNeedsUpdateConfiuration()` 메서드

![스크린샷 2023-01-05 오전 3 16 54](https://user-images.githubusercontent.com/63997044/210725545-21d1e30b-4064-40b6-b34e-a51b65f1362e.png)

`showsActivityIndicator = true` 버튼에 Activity Indicator가 활성화된다.

![스크린샷 2023-01-05 오전 3 17 05](https://user-images.githubusercontent.com/63997044/210725557-0f64708b-d1fc-44d8-bb97-ac7cd9e3264a.png)

버튼의 내부 레이아웃 구조는 다음과 같다. contentInset, imagePadding 및 titlePadding을 통해 버튼 요소 사이의 공간을 조정할 수 있다.

![스크린샷 2023-01-05 오전 3 17 19](https://user-images.githubusercontent.com/63997044/210725560-da37cfc9-23b0-429e-b93c-e4587b0f8db3.png)

이런 식의 구성 방법을 시멘틱 스타일이라고 한다.
시멘틱 스타일을 사용하면 버튼을 간단하게 만들 수 있다.

<img width="1059" alt="스크린샷 2023-01-05 오후 4 33 44" src="https://user-images.githubusercontent.com/63997044/210726052-78179c97-b476-4ed9-8c79-a88a2321d339.png">

Check Out 버튼을 구성한다면 시멘틱 스타일과 configureUpdateHandler를 조합하여 설정하면 된다.


### Toggle Buttons

![스크린샷 2023-01-05 오전 3 19 13](https://user-images.githubusercontent.com/63997044/210726318-5fc840bf-d00a-4312-bd69-e1f6e5b6c527.png)

iOS에서의 toggle 버튼
on/off 상태를 나타냄
UIControl에서 selected 상태를 유지

![스크린샷 2023-01-05 오전 3 19 29](https://user-images.githubusercontent.com/63997044/210726341-5b23e494-0810-406c-b4a9-d282bec535ab.png)

일반 버튼을 toggle 버튼으로 만드는 방법
- button.changesSelectionAsPrimaryAction = true

### Pop-up Button

![스크린샷 2023-01-05 오전 3 20 10](https://user-images.githubusercontent.com/63997044/210726459-fb56c5b8-61b1-42ed-bd2d-2cefc134598e.png)

여기서 버튼은 메뉴에 해당하고 하위 항목을 선택하면 색상과 이미지가 전환되고 버튼 또한 업데이트된다.

<img width="460" alt="스크린샷 2023-01-05 오후 4 37 26" src="https://user-images.githubusercontent.com/63997044/210726646-0b899e51-fa72-428a-bd46-89c6a2d42ef2.png">

Pop-up 버튼을 구현하는 코드를 알아보자.
button의 메뉴에 목록을 등록한다. 특정 아이템을 기본으로 지정하려면 state: .on 옵션을 추가한다.
Pop-up 버튼을 구현하려면 showsMenuAsPrimaryAction을 true로 설정해야 한다.
또한 토글 버튼과 마찬가지로 changesSelectionAsPrimaryAction을 true로 변경해야 한다.




### Mac Catalyst

![스크린샷 2023-01-05 오전 3 22 48](https://user-images.githubusercontent.com/63997044/210726817-62e7ef3b-beed-48cb-b7a9-bdd83d893237.png)

Mac Catalyst로 빌드한 Mac 앱은 iPad 앱과 코드를 공유한다.
iOS식으로 만든 Pull-down, Pop-up, Toggle 버튼들은 자동으로 Mac 버전으로 적용된다.

<img width="459" alt="스크린샷 2023-01-05 오후 4 39 51" src="https://user-images.githubusercontent.com/63997044/210727036-8a43ced6-d718-4ed6-b993-26331a9e2ec6.png">

iPad app button/Mac Catalyst button

<img width="558" alt="스크린샷 2023-01-05 오후 4 40 28" src="https://user-images.githubusercontent.com/63997044/210727132-49bc04df-465c-4d54-9bc0-522612e66002.png">

preferredBehavioralStyle = `.automatic` 혹은 `.pad`으로 지정하여 기본 스타일을 변경할 수 있다.


### UIMenu

![스크린샷 2023-01-05 오전 3 24 51](https://user-images.githubusercontent.com/63997044/210727195-03204a20-ff0c-45f3-91a1-2524661d444b.png)

Pop-up 버튼 예제에서 봤듯이 대부분의 기능은 UIMenu에 구축된다.
UIButtonConfiguration과 결합하여 다양한 스타일의 버튼을 생성할 수 있다.
또한 메뉴 내에서의 계층 구조 등 여러 기능들을 제공한다.

![스크린샷 2023-01-05 오전 3 25 09](https://user-images.githubusercontent.com/63997044/210727199-1fe8c020-b980-48a7-b94b-5d0bd15266fd.png)

하위 메뉴를 구성하는 Pull-down 메뉴 바 코드이다.
메뉴 내 하위 메뉴에서 단일 선택을 지원하기 위해 options: .singleSelection 옵션을 적용한다.
menu.selectedElements 프로퍼티를 통해 선택된 하위 메뉴에 대해 쉽게 액세스할 수 있다.


### 요약

![스크린샷 2023-01-05 오전 3 25 17](https://user-images.githubusercontent.com/63997044/210727204-eeb6064b-f426-4158-86a4-3b57dd521207.png)

App에서 better button을 만드는 방법!
- configuration을 추가하여 풍부한 스타일을 사용한다.
- 기존 Picker 스타일을 Toggle, Pop-up 버튼으로 대체한다.
- UIButton을 상속한 서브클래스를 대체한다.
- Mac Catalyst 자동 변환을 사용한다.
