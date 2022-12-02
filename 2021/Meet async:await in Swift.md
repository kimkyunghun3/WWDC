# Meet async/await in Swift

UIkit 에서 이미지 썸네일을 가져오는 코드에서 비동기, 동기적으로 가져온다.

![](https://i.imgur.com/hioN7sp.png)

쓰레드에서 sync하게 동작하면 하나 하게되면 멈추고 그 다음 작업은 이전 것이 끝날 때 까지 기다려야한다.

반대로 비동기는 다른 동작을 할 수 있다

![](https://i.imgur.com/CoLiU2V.jpg)

비동기 장점 

- 다른 작업 막지 않고 바로 시작할 수 있다.

이제 예시를 보여준다.

![](https://i.imgur.com/Hd34CNu.png)

서버에서 이미지를 가져와서 썸네일을 뿌려준다

그 과정은 아래와 같다

![](https://i.imgur.com/kc58tNK.png)

각 과정은 위에 것이 끝나면 다음 것으로 진행되는 프로세스를 가지고 있다.

여기에서 어떤 작업들은 결과값을 빠르게 내보낸다.

![](https://i.imgur.com/4apwh4q.png)

일부는 시간ㄴ이 더 걸린다. 다운로드 하고 이미지를 그리고 랜더링해서 원하는 것으로 할 때에 오래걸린다

![](https://i.imgur.com/RFRz8b7.png)

이럴때 SDK에서 비동기로 처리하도록 한다.

이제 completion handler 동작 방식에 대한 설명

![](https://i.imgur.com/BHQDAHf.jpg)

순서대로 하나씩 호출되지만 dataTask는 비동기돌 작동한다.

그 다음 이렇게 순서대로 호출된다

![](https://i.imgur.com/me5kTuI.jpg)

만약 이미지가 제대로 안된다면 error로 가지게 된다.

하지만 이미지를 제대로 가져오게 된다면 prepareThumbnail를 호출한다

![](https://i.imgur.com/O6iiC5K.jpg)

그래서 비동기로 다른 작업도 하며 이미지를 원하는 섬네일 스탈로 변경하게 한다.

썸네일 준비되면 이제 클로저에서 completion를 호출한다.

하지만 이렇게 하면 문제가 있다.

guard let return에서 단순히 리턴만 해주고 있어서 이미지가 제대로 다운안되거나 썸네일이 제대로 완성되지 않아도 이것에 대해 알려주지도 않고 업데이트도 되지 않는다.

이것을 completion 으로 에러를 또 보낼 수 있다.

![](https://i.imgur.com/3s28h7j.jpg)

일반적인 에러 핸들링 사용할 수 없다.

completion handler에서 이를 체크할 수 없다.

리턴으로 에러 completion를 가지는게 이상하다.

스위프트에서 이렇게 하면 리턴으로 돌아오지 않는다고 한다.

이것을 더 쉽게 할 수 있다. Result type으로 안전하게 해줄 수 있다.

![](https://i.imgur.com/wBP6eRE.jpg)

이렇게 하면 조금 안전하게 하지만 더 코드가 더러워지고 못생겨진다고 한다.

그래서 이를 해결하기 위해서 Async/await 가 나왔다!!

async를 적으면 간단하게 표현해줄수 잇다. 성공하게 되면 썸네일을 리턴하고 그게 아니라면 throws로 에러를 내보낸다는 의미다.

![](https://i.imgur.com/PvSScLi.png)

await는 이제 바로 리턴하는 것이 아니라 비동기로 오는 것을 기다린다는 의미다.

await는 async keyword와 같이 사용해야 한다

이 코드를 보면 사실 이전 코드와 동일하게 동작한다. value, error를 받아서 오는건데 차이점은 좀 더 보기 편하고 심플하다는 것이 있다.

그리고 이제 마지막에 썸네일을 만든다

![](https://i.imgur.com/ovwnhbq.png)

이곳에서도 썸네일을 만들어서 성공하면 리턴을 하는것이고 실패하면 throw를 통해 에러를 내보내준다.

이는 단순히 조용하게 처리하는 것이 아니라 에러 처리를 해주므로 이를 확인할 수 있다.

그리고 코드만 보아도 의도 파악이 쉽다.

비동기 프로퍼티

<img width="985" alt="스크린샷 2022-12-02 오후 12 38 57" src="https://user-images.githubusercontent.com/52434820/205214053-0d44bbf9-5618-4ba8-b268-0bd42ab82696.png">

Swift5.에서는 getter에서도 throw할 수 있다. 그래서 이처럼 비동기 코드를 작성할 수 있다.

그리고 오직 read-only 프로퍼티에서만 async를 사용할 수 있다.

#### 일반적인 함수 호출 방식

<img width="1410" alt="스크린샷 2022-12-02 오후 12 42 04" src="https://user-images.githubusercontent.com/52434820/205214057-179c85df-3e06-46a3-a518-45b24d77e753.png">

썸네일을 가져오도록 메서드 호출하면 내부 메서드에서 이를 실행하고 거기에서 나온 리턴값을 이 썸네일에 넣어준다.

#### 비동기 함수 호출 방식

<img width="1470" alt="스크린샷 2022-12-02 오후 12 46 13" src="https://user-images.githubusercontent.com/52434820/205214087-80ed346f-9208-4f38-8d4b-5093c72a3ead.png">

일반적으로는 control를 호출한 메서드에게 넘기는데 이곳은 그것과 다르다.

컨트롤을 넘기지 않고 시스템에게 넘긴다음 suspended가 되어 있는다.

그리고 시스템보고 어떤 것이 더 우선인지 결정하라고 한다.

그리고 이제 리턴으로 오게되면 resume를 부르고 이에 대한 통제권을 다시 가지게 된다.

그리고 만약 suspended 상태가 필요하다면 언제든지 여러번 다시 갈 수도 있다.

그렇게 지나고 마지막으로 suspended가 이루어진 다음에는 이제 쓰레드에서 이를 종료하게 되면 값이나 error를 처음 호출한 곳으로 보낸다.

메인 스레드에서 버튼 눌러서 썸네일 가져오도록 요청 → fetchThumbnail 메서드 호출 → data 비동기 호출

이 방식으로 이루어진다.

### Async/await facts

<img width="673" alt="스크린샷 2022-12-02 오후 12 50 19" src="https://user-images.githubusercontent.com/52434820/205214092-db1ab183-dc1c-4efc-9ae1-8e848651bc46.png">

### 프로젝트 적용

테스팅에 적용한다!

#### 기존 방식

<img width="1360" alt="스크린샷 2022-12-02 오후 12 51 39" src="https://user-images.githubusercontent.com/52434820/205214111-6b431448-8a2b-4505-9438-1a018f9e1a12.png">

#### 이후 변경 방식
<img width="1263" alt="스크린샷 2022-12-02 오후 12 52 32" src="https://user-images.githubusercontent.com/52434820/205214120-4b8b7954-31f7-4515-9385-e4986bfb381b.png">

스유에서 이미지에 대해 post.id를 통해 썸네일을 가져오는 것을 보여준다.

<img width="1229" alt="스크린샷 2022-12-02 오후 12 53 00" src="https://user-images.githubusercontent.com/52434820/205214124-77bbbb9d-2ce6-4ae3-a1ed-ada629c40344.png">

하지만 이렇게 하더라도 실행이 안된다.

왜냐하면 비동기 코드 - 동기 코드가 이어져 있기 때문에 이를 연결해주는 브릿지가 필요하다

<img width="1350" alt="스크린샷 2022-12-02 오후 12 54 08" src="https://user-images.githubusercontent.com/52434820/205214172-f30c09ac-35fc-4fbd-82cb-6ffeb7157737.png">

이를 Task가 해준다. 비동기 코드를 패키지에 넣어서 이를 다음 가능한 쓰레드에 보내는 것을 해주고 있다.

이렇게 하면 동기 코드 내에서 비동기 코드를 실행할 수 있다.

### SDK에서 비동기 API

<img width="1407" alt="스크린샷 2022-12-02 오후 12 56 59" src="https://user-images.githubusercontent.com/52434820/205214184-b849f009-49a1-4552-94d7-9bf62ef89f15.png">

이렇게 변하면 얼마나 편한가? 얼마나 간단해 보이나?

<img width="1418" alt="스크린샷 2022-12-02 오후 12 57 22" src="https://user-images.githubusercontent.com/52434820/205214216-d7001f15-1b7b-4fbb-a654-5410a8d425b0.png">

원래 이곳 메서드에서 get~ 메서드 명이엿는데 이렇게 리턴해오지 않으므로 네이밍에 제거할 수 잇다.

<img width="1271" alt="스크린샷 2022-12-02 오후 12 59 08" src="https://user-images.githubusercontent.com/52434820/205214223-4848744d-f14e-4a0e-aced-54dabb609968.png">

비동기로 하는 것을 보여준다

<img width="1418" alt="스크린샷 2022-12-02 오후 1 00 51" src="https://user-images.githubusercontent.com/52434820/205214229-bbf2a2fe-694d-4b89-9b09-1ff81dfb8b1e.png">

중간에 비어져있는데 이때 다른 작업을 하게 되면 되고 필요할 때 suspended에서 가져와서 이 데이터를 쓰면된다

코어데이터 사용해서 하는 법인데 앞에서 보인 시스템으로하는 것과 동일하다

<img width="1441" alt="스크린샷 2022-12-02 오후 1 02 07" src="https://user-images.githubusercontent.com/52434820/205214235-7b85c99c-f996-4894-860c-5b9641714ea5.png">

### Async/await 있는 경우

<img width="1467" alt="스크린샷 2022-12-02 오후 1 03 03" src="https://user-images.githubusercontent.com/52434820/205214455-7b6e75ac-af5e-4138-9de3-033a1c990655.png">

continuation이 어떻게 도와주고 async 대체제를 잘 사용하게 하는지 보여준다

withCheckedThrowingContinuation이 async 스위프트 함수에서 throwing하는 것을 도와준다.

어떤 함수가 절대 error를 thorw하지 않는 곳에 사용한다?

suspended async 함수에서 resume할 때 사용할 수 있다.

![](https://i.imgur.com/lMd5hvz.jpg)

continuation에서는 resume를 제공하고 있다.

하지만 조심해야하는 부분이 있다.

![](https://i.imgur.com/KlgpnWw.jpg)

모든 path에서 정확하게 한번 불러야 한다.

하지만 이를 안적어도 문제가 되지 않는게 스위프트에서 알아서 warning 로그를 내보내준다.

이렇게 하게되면 data가 이상하게 나올 것이다.. 조심!

![](https://i.imgur.com/y08kHJK.png)

다음으로 한번만 호출되어야하는 것이 여기 있다

![](https://i.imgur.com/zQ1zfP4.jpg)

### Summary

![](https://i.imgur.com/zg0N19R.png)
