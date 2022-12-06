# Use asnyc/await with URLSession

Swift의 concurrency 특징

![](https://i.imgur.com/rcIAp0B.png)

네트워크는 비동기로 되어있다

이 화면에 대한 코드를 설명 할 것이다

![](https://i.imgur.com/XYiRBzO.jpg)

일반적인 Completion handler를 통해 사진 이미지를 가져오는 것

![](https://i.imgur.com/IGhTCXl.jpg)

하지만 3가지의 문제점이 존재한다.

1. 흐름

![](https://i.imgur.com/TSitpG7.jpg)

흐름을 보면 이곳 저곳으로 jump하고 있다

다음으로는 threading 

3개의 다른 실행이 존재한다.

![](https://i.imgur.com/oLZBkHU.jpg)

이렇게 3개의 completion handler 실행이 되는데 이 부분이 지속적으로 모두 main thread에서 동작하는 것이 아니다.

또 하나로는 error는 early return 를 되기도 하고 불필요하게 두번 호출된다. ( if let 의 error, else의 error)

UIImage가 제대로 가져오지 않거나 포맷이 적절하지 않으면 nil이 떨어지는데 이럴 경우 nil image, nill error 두개가 한번에 내려오게 된다.

이제 이것을 새 버전인 async/await로 했을 경우 어떻게 되는가?

![](https://i.imgur.com/M37NgPN.jpg)

훨씬 보기 쉽고 linear하게 흐름이 흘러간다

![](https://i.imgur.com/Rr3gz09.png)

또한 결과가 적절하지 않으면 throw를 던진다. 이렇게 함으로써 스위프트에서 제공하는 기본적인 error handling를 사용하고 있다.

업로드 할 때에도 쓰인다 

![](https://i.imgur.com/M5oZ3y5.jpg)

download에서도 사용한다

![](https://i.imgur.com/GXgW0kr.jpg)

스위프트에서 Cancellation를 Task를 활용해서 하고 있다.

![](https://i.imgur.com/OSTV8SV.png)

Task안에 담아서 넣어두고 한번에 cancel 한다.

### 데모 

이미지가 있는 컬렉션뷰에 좋아요 표시를 실시간 업데이트가 되도록 만들겠다.

![](https://i.imgur.com/JHe2sj2.jpg)

AsyncBytes를 활용해서 서버에서 오는 것의 데이티를 확인한다.
또한 response를 통해서 서버에서 성공적으로 response가 오는지 확신할 수 있다.

![](https://i.imgur.com/10WIzRr.jpg)

데이터를 받게되면 각 라인마다 response를 가져오게된다.
![](https://i.imgur.com/34JOowr.jpg)

그 다음 Json Data를 파싱하고 UI에 업데이트하도록 한다.
그리고 메인쓰레드에서 호출되도록 await를 붙여준다.

AsyncSequence

![](https://i.imgur.com/hx6opdx.png)

File 다루는 것도 이것을 통해 사용한다.

URLSession는 델리게이터 메서드로 되어있다.

새 async method에서는 인증 이슈를 어떻게 해결하냐?

![](https://i.imgur.com/yS86qGg.jpg)

delegate를 통해 데이터를 업데이트, 업로드, 다운로드 등을 한다.

![](https://i.imgur.com/x1AOP0g.png)

델리게이터를 통해 성공하거나 실패할 때 까지 관리를 한다.

session delegate, task delegate가 있는데

task delegate를 통해 인증 오류 해결하는 법을 알려주겠다.

![](https://i.imgur.com/eXLCoih.png)

favorite 마킹하는 과정에서 나는 인증 에러 해결법

URLSessionTaskDeleate인 AuthenticationDelegate 사용한다.

URLSessionTaskDeleate 프로토콜을 채택해서 사용한다.

![](https://i.imgur.com/k1xsNO8.png)

이것을 사용해서 유저에게 인증서관련된것을 처리하려고 한다.

그 다음으로는 urlsession didReceive method 이용

![](https://i.imgur.com/ovoQWUh.jpg)

여기에서 에러 핸들링도 잘해줘야한다.

![](https://i.imgur.com/pC11irN.jpg)

여기에서 이렇게 해주게 함으로써 필요한 부분에서만 delegate method가 사용되고 다른 곳에서는 사용되지 않도록 이곳에서만 인스턴스를 생성해준다.

이러면 인증관련곳으로 넘어가고 로그인 후 favorite가 된다.

### 마무리

![](https://i.imgur.com/PRQz0bc.png)






