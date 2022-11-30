## Protocol and Value Oriented Programming in UIKit Apps

Model - View - Controller의 기본구조
- Model은 데이터를 담당
- View는 화면을 보여줌
- Controller는 Model과 View를 중계

![](https://i.imgur.com/VEkDvMh.png)

Lucid Dreams이라는 간단한 앱을 통해서 알아보겠다.

![](https://i.imgur.com/DhrMqUZ.png)

Value types와 protocols에 대해 알아보겠다.

![](https://i.imgur.com/ZYjcTOk.png)

Dream이라는 모델을 정의한다.

![](https://i.imgur.com/G6shMQs.png)

Class로 만들면 공유문제를 피할 수 없다.

![](https://i.imgur.com/1jDXgGI.png)

이렇게 복잡한 관계속에서 관리를 제대로 할 수 있을까?
어디서 변경되는지 그 side effect를 감당할 수 없을 것이다.

![](https://i.imgur.com/fQGBjXv.png)

그러니깐 구조체로 하는게 좋다.

![](https://i.imgur.com/75NskbV.png)

value는 model types만 쓰는게 아님! 다른곳에도 사용가능하다.

![](https://i.imgur.com/seRZ39T.png)

View의 Cell Layout에 관해서 얘기해보자.

![](https://i.imgur.com/kZy9Aza.png)

일반적인 경우, 아래와 같은 3가지를 만들려면
- UIView를 상속받은 DreamDetailView
- UITableViewCell을 상속받은 DecoratingLayoutCell
- DecoratingLayoutCell을 상속받은 DreamCell

을 만들어야한다.

![](https://i.imgur.com/kb52fpr.png)

보통은 이런식으로 Cell을 만든다. 그리고 내부에서 layout을 잡는다.

![](https://i.imgur.com/LAUSWKw.png)

구조체로 바꿔보자. 이제 UIView를 상속받는 어느곳에서나 사용가능하다.

![](https://i.imgur.com/5FJ8u4O.png)
![](https://i.imgur.com/7FlV2ED.png)

테스트도 매우 쉽다.

![](https://i.imgur.com/YVK85qm.png)

![](https://i.imgur.com/dLyUVTa.png)

좋긴한데 더 개선점을 찾아볼 수 있을것 같다.

UIView와 SKNode에서 동시에 쓸수는 없을까?

![](https://i.imgur.com/ThVaC6t.png)

프로토콜을 도입하자. 이제 content와 decoration에 Layout 타입이 올 수 있다.

![](https://i.imgur.com/LimRl5q.png)

Generic과 함께 사용할 수도 있다. 이 상황에는 content, decoration이 동일해야한다는 제약을 줄 수도 있다.

![](https://i.imgur.com/ECwaOxY.png)

Generics Types에 대해서 더 알아보자.

- static한 다형성을 줄 수 있다
- 컴파일 타임에 최적화가 가능하다

![](https://i.imgur.com/8K6YUdl.png)

### Composition

작은것들을 모아서 하나의 큰 조각으로 만드는것
합치더라도 하나하나의 조각들에 대해 고립적으로 어떤것인지 아는것
추상화할 때 Subclass, superclass에 대해 생각할 필요없이 각각 다룰 수 있다
클래스 상속으로 할 수도 있다

하지만 클래스 자체가 비싸다.
추가로 인스턴스를 만들면 Heap allocation를 사용하고 views에서도 더 최악이다.
그래서 클래스 활용해서 view를 만드는 것은 좋지 못하다.
Swift에서는 하지만 좋은 방법으로 Composition하는 것이 있다.
Value type인 Struct를 활용해서 하면 비용도 적게 들고 가볍다

캡슐화도 되어있다
이제 이것을 Layout에 적용해보자!

![](https://i.imgur.com/iWNnl8d.jpg)

이렇게 구조체로 만들 수 있다.
이것에서는 UIView, SKNode가 필요할 것이라고 예측한다.
그래서 layout, compose를 한번에 하도록 해보자!

![](https://i.imgur.com/GUBG8zE.png)

프로토콜에 정의를 해두고 각 구조체에서도 아까처럼 동일하게 넣어준다.

이제 DecoratingLayout에서는 조금 다르게 뒤에 … 넣어서 children의 type를 조금 더 확장성있게 만들어 줄 것이다.

이에 대한 자세한 것은 추후에 말해준다.
이제 이를 사용하려고 한다.

![](https://i.imgur.com/7sY5Q5N.png)

이렇게 사용하면 된다.

### Contents

레이아웃에 내용을 넣는다.

프로토콜활용해서 여러 내용을 넣는 것을 만들어보자!

![](https://i.imgur.com/oDeAA6F.png)

하나의 컨텐츠에 UIView, SKNode가 모두 들어있다.

여기에 이제 associated Type를 넣어본다.

이것은 하나의 placeholder로 이용된다.

![](https://i.imgur.com/AorR5Ro.png)

연관 타입을 넣어서 어떻게 각각 내부를 구현하는지 아래와같이 볼 수 있다.

![](https://i.imgur.com/mUTN22m.jpg)

하지만 이를 더 개선시킬 수 있다.

아까했던 제네릭 타입을 함께 이용한다면 두개의 코드로 나눌 필요 없이 하나의 코드로 해결할 수 있다.

![](https://i.imgur.com/2Hgu82B.png)

연관타입을 이용하면 프로토콜을 더 파워풀하게 만들어준다.

이제 CascadingLayout이 존재하는데 두개의 다른 레이아웃을 만들어야한다.

![](https://i.imgur.com/Bqnya0V.jpg)

이 그림을 보면 이제 Children의 내용이 동일한 타입이여야 하는 것을 제한을 걸 수 있다.

![](https://i.imgur.com/3y0ac7e.png)

결론적으로 이렇게 프로토콜을 만들면 더 나아진다

![](https://i.imgur.com/7iVI6Wl.png)

테스트에서도 용이하다!!!!

이거 보면 실제 사용하는 UIView와 관련없이 테스트에서만 사용하는 구조체를 만들어서 사용할 수 있다.

![](https://i.imgur.com/mvJwp20.jpg)

Summary

![](https://i.imgur.com/EZUa5o0.jpg)

### Controller

여기에서 작업을 하고 폰을 흔들어서 undo 기능을 수행하려고 한다.

![](https://i.imgur.com/qduDnnd.jpg)

하지만 잘안되는데 그 이유를 찾아보도록 하자!

코드를 보면 이렇게 되어있다.

![](https://i.imgur.com/Wq2JrLZ.png)

우리는 이렇게 만들기를 원하지않는다.

![](https://i.imgur.com/tadWdXR.jpg)

해결하기 위해서 model 프로퍼티를 만들어서 하나의 모델 타입을 사용하길 원한다.

2개의 모델 프로퍼티을 하나의 code path으로 보고 해결하려고 한다.

여기에서 하나의 모델 프로퍼티를 더 추가하더라도 동일하다.

![](https://i.imgur.com/FvjdQ2h.jpg)

이제 코드를 보도록 하자

원래 ViewController에 있던 모델 프로퍼티들을 모델에 넣고 모델 path를 이용한 타입을 ViewController에 넣어서 해결한다.

![](https://i.imgur.com/B9lzQcM.png)

이제 Undo code를 구현하도록 하자!

![](https://i.imgur.com/DQ0qjWL.jpg)

이렇게 dream[1]이 사라지면 removeLast를 통해 스택에서 하나씩 지워지도록 한다.

이렇게 하면 모델에서 하나씩 지운다.

개인 모델 프로퍼티가 업데이트하게 되면 뷰에 대해서도 업데이트 해줘야하는데 각각이 독립적으로 이루어져있다.

그렇게 하다보니 match시키는 것에 어려움이 존재하며 이는 불일치를 유발한다.

그에 대한 버그는 아래처럼 보인다.

![](https://i.imgur.com/GJrV4Bn.jpg)

이렇게 하게되면 매우 복잡하다.

![](https://i.imgur.com/G3sieCa.png)

undo할 수 있는 변화에 대해서는 순서가 중요하다.

앱이 이런 요소들을 넣으면 실수를 유발할 수 있는 기회가 늘어난다.

하지만 이렇게 복잡하게 되어있으면 뷰와 모델의 일치성을 유지하기가 매우 어렵다.

이제 ViewController에서 모델이 변할 때 어떻게 처리하는지 볼수 있다

![](https://i.imgur.com/6WFww79.png)

old, new를 통해 새로운 것과 예전거를 비교해서 tableview에 reload해주고 있다.

그 다음으로 undo에 대해서 old model를 넣어둔다.

![](https://i.imgur.com/Yy4n1Cw.png)

## 이점

![](https://i.imgur.com/Wam2IVx.png)

### Controller (UI State)

![](https://i.imgur.com/95c7ZTd.png)

여기에 ViewController에서 UI State 관련되어 있는 것이 있다.

UI State와 관련된 다이어그램이 있다.

![](https://i.imgur.com/QtQmNWJ.jpg)

viewing에서 공유 아이콘을 누르면 selecting으로 가게 된다.

![](https://i.imgur.com/kANqzzN.png)

그래서 몇개 선택하고 공유하기를 누르면 sharing state르 넘어가게된다

![](https://i.imgur.com/JUGouDv.png)

만약 중간에 selecting에서 cancel 버튼을 누르게 되면 다시 뒤로 가게 된다. (viewing state)

![](https://i.imgur.com/CwSElEU.jpg)

하지만 UI 관련 불일치가 일어난다!

![](https://i.imgur.com/Z9aj6KQ.png)

네비바를 보면 Cancel를 했는데 여전히 Duplicate가 있다..! 그리고 공유하기 눌렀을 떄 나온 동그라미 들이 있다

몇개의 state 프로퍼티가 이상하게 나오고 있다. selecting state가 남아있다.

![](https://i.imgur.com/OEyksK6.png)

그림에서 보듯이 여러 프로퍼티가 존재하는데 하나의 프로퍼티가 변하면 다른 프로퍼티도 clear해주어야한다!
하나라도 해주지 않으면 불일치가 일어난다.

이 문제는 enum를 활용해서 해결해볼 수 있다.

![](https://i.imgur.com/IPDQh7T.png)

state를 받아서 사용할 수 있다.

### Recap

결론적으로 이렇게 되어있다..!

![](https://i.imgur.com/snRP5Qx.jpg)
