# ARC in Swift: Basics and beyond

<img width="646" alt="스크린샷 2022-11-30 오후 5 27 10" src="https://user-images.githubusercontent.com/63997044/204752320-4500c299-c21d-47da-a1be-153da0a54caf.png">

> **Overview**

Swift에서 객체의 수명과 ARC에 대해 알아보자.

만약 참조 타입을 사용한다면, Swift는 메모리 관리를 Automatic Reference Counting(이하 ARC)를 통해 진행한다. ARC가 어떻게 동작하는지 알아보자.

## Object lifetimes and ARC

<img width="717" alt="스크린샷 2022-11-30 오후 5 28 12" src="https://user-images.githubusercontent.com/63997044/204752354-1ae1430e-2aab-4581-87f2-f8af5245a77f.png">

(ARC에 의해 관리되는 참조 타입) 객체의 생명 주기는 다음과 같다.
- 객체가 생성될 때 시작하고, 마지막 사용이 끝나는 시점에 종료된다.
- ARC는 수명이 종료된 객체를 할당 해제한다.
- ARC는 reference count를 통해 객체의 수명을 추적한다.
- Swift 컴파일러는 retain/release 연산자를 삽입한다.
- Swift는 런타임에서 reference count가 0인 객체를 할당 해제한다.


<img width="711" alt="스크린샷 2022-11-30 오후 5 28 34" src="https://user-images.githubusercontent.com/63997044/204752409-38953fff-b1fd-4f7d-a523-631533430188.png">

예시를 살펴보자. 위 예제에서 test() 메서드 내부를 살펴보면 순서대로
1. Traveler 인스턴스 생성
2. 인스턴스의 참조 복사
3. 값 업데이트

을 하고 있다. 메모리 관리를 위해 컴파일러는 인스턴스 생성에 retain 연산자를, 사용이 끝나는 시점에 release 연산자를 각각 삽입할 것이다.

<img width="713" alt="스크린샷 2022-11-30 오후 5 28 57" src="https://user-images.githubusercontent.com/63997044/204752430-8bdfb5a7-3890-46a5-b8db-1d5ca9476a28.png">

traveler1 인스턴스만 보겠다. 생성될 때 참조가 시작되고 참조를 traveler2로 복사하면서 더 이상 traveler1은 사용되지 않는다.

<img width="717" alt="스크린샷 2022-11-30 오후 5 29 16" src="https://user-images.githubusercontent.com/63997044/204752436-b99b948e-3818-4342-9ec7-cc9331a7ab44.png">

따라서 컴파일러는 여기에 release 연산자를 삽입한다. 
여기서 **retain 연산자는 따로 삽입하지 않는데**, 그 이유는 객체가 생성되면서 reference count를 1로 설정해주기 때문이다.

<img width="713" alt="스크린샷 2022-11-30 오후 5 29 44" src="https://user-images.githubusercontent.com/63997044/204752439-2b1d2541-f709-4134-8886-131882b35bfc.png">

이제 Traveler 인스턴스의 또 다른 참조인 traveler2를 보자. traveler2는 참조 복사로 시작되고, 값 업데이트로 사용을 마무리한다.

<img width="714" alt="스크린샷 2022-11-30 오후 5 30 02" src="https://user-images.githubusercontent.com/63997044/204752444-21a4f8a0-1175-4c11-a7fa-6776cba92d6c.png">

마찬가지로 컴파일러는 마지막 사용 직후 release 연산자를 삽입하고, 이번엔 retain 연산자를 삽입한다. init()으로 새로운 인스턴스를 생성하는 것이 아닌 참조를 복사하는 것으로 traveler2가 생성되었기 때문이다.

컴파일러의 작업이 끝났으니 코드를 따라 런타임에 어떤 일이 일어나는지 살펴보자.

<img width="711" alt="스크린샷 2022-11-30 오후 5 30 35" src="https://user-images.githubusercontent.com/63997044/204752953-82b62711-a267-47c3-81c2-9a17af187396.png">

우선 Traveler 객체가 힙 영역에 생성된다.
reference count는 1, name은 Lily, destination은 nil로 할당된다.

<img width="709" alt="스크린샷 2022-11-30 오후 5 30 50" src="https://user-images.githubusercontent.com/63997044/204752960-3bec06f3-546e-4418-9a0b-aa7105b6aad8.png">

이제 retain 연산자가 실행된다.
새로운 참조를 준비하는 과정에서 reference count가 2로 증가한다.

<img width="715" alt="스크린샷 2022-11-30 오후 5 30 56" src="https://user-images.githubusercontent.com/63997044/204753061-095c499f-e1f3-4afe-b20f-d40870438765.png">

이제 traveler2라는 상수가 Traveler 객체를 참조한다.

<img width="711" alt="스크린샷 2022-11-30 오후 5 31 05" src="https://user-images.githubusercontent.com/63997044/204753082-bc8e3a06-de7b-4e2c-9f68-809d854cc359.png">

release 연산자가 실행되어 사용이 끝난 traveler1을 할당 해제한다. reference count도 1로 감소한다.

<img width="709" alt="스크린샷 2022-11-30 오후 5 31 12" src="https://user-images.githubusercontent.com/63997044/204753168-435d081f-e5e1-45a2-a187-d3d945294660.png">

참조 타입의 내부 프로퍼티 값을 변경해준다.

<img width="713" alt="스크린샷 2022-11-30 오후 5 31 20" src="https://user-images.githubusercontent.com/63997044/204753195-9431a492-daca-40eb-9c90-064622e2de7d.png">

이것이 traveler2의 마지막 사용이었기 때문에 release 연산자가 실행되면서 reference count는 0이 된다.
그리고 reference count가 0이 되면서 객체는 메모리에서 해제된다.

<img width="714" alt="스크린샷 2022-11-30 오후 5 31 41" src="https://user-images.githubusercontent.com/63997044/204755263-0930e9cd-de71-42db-8573-d8073dd73efb.png">

위의 내용을 종합하면 Swift에서 객체의 수명은 **use-based**로 동작한다고볼 수 있다. 객체가 사용되기 위해 생성되면서 시작되고, 객체의 사용이 끝나면서 종료된다.

이러한 Swift의 특징은 C++ 같은 언어와 다르다. 
C++에서 객체의 수명은 우괄호가 닫힐 때까지 보장된다. 

<img width="715" alt="스크린샷 2022-11-30 오후 5 32 11" src="https://user-images.githubusercontent.com/63997044/204755331-2308635f-4995-413f-9868-ff6967b7aa9c.png">

방금 살펴본 예시에서 객체의 수명은 사용의 시작과 끝에서 각각 할당과 해제가 된다는 것을 알았다. 이것을 `minimum lifetime`이라고 한다.
그러나 실전에서는 객체의 수명은 **컴파일러의 release/retain 삽입에 따라 결정**되며 이 작업은 ARC 최적화에 따라 **매번 달라질 수 있다.** 즉 저건 객체가 갖는 최소한의 수명이고, 그보다 더 늦게 해제될 수도 있다. 대부분의 상황에서는 정확히 어떤 시점에 release가 실행되는지 알 필요는 없다.

<img width="716" alt="스크린샷 2022-11-30 오후 5 34 22" src="https://user-images.githubusercontent.com/63997044/204755491-ed655093-d9fe-4dd6-96ca-00bdc27c8c06.png">

그러나 몇몇 상황에서는 객체가 언제 해제되는지 중요하기 때문에 객체의 수명을 관찰하기도 한다. 또한 객체의 수명을 관찰하며 이에 의존하여 동작하는 프로그램의 경우 버그를 유발할 수 있으며 이러한 버그들은 찾기 쉽지 않고 전혀 엉뚱한 때와 장소에서 발견될 수도 있다.

객체의 수명을 관찰하는 Swift 언어의 특징들을 몇 가지 알아보고, 객체의 수명에 의존하는 코드가 어떤 위험이 있는지, 그렇다면 안전하게 사용하는 방법은 무엇인지 알아보도록 하자.

## Observable object lifetimes

### Weak and unowned references

일반적인 참조인 강한 참조와 다르게, 약한 참조와 미소유 참조는 reference count를 증가시키지 않는다. 또한 참조 순환을 끝내기 위해 사용한다. 먼저 Reference Cycle에 대해 살펴보자.

<img width="712" alt="스크린샷 2022-11-30 오후 5 35 15" src="https://user-images.githubusercontent.com/63997044/204755519-8f26859d-73a0-4308-8dc8-ea94002aae14.png">

Account 클래스가 추가되었다. Account 클래스는 멤버 프로퍼티로 Traveler 타입을 가지고, 반대로 Traveler 클래스는 멤버 프로퍼티로 Account 타입의 프로퍼티를 갖는다.

ARC에서 어떤 일이 일어나는지 확인해보자.

<img width="715" alt="스크린샷 2022-11-30 오후 5 35 34" src="https://user-images.githubusercontent.com/63997044/204755784-5b7c0c99-5ebd-4eae-b210-e0d868c63fff.png">

먼저 Traveler 객체가 힙 영역에서 생성된다. 이 때 reference count는 1이다.

<img width="710" alt="스크린샷 2022-11-30 오후 5 35 47" src="https://user-images.githubusercontent.com/63997044/204755805-342d6cf0-4893-4daf-9a7c-3d1d6cf0ed1e.png">

그리고 Account 객체가 reference count를 1로 갖고 생성된다. Account 객체가 Traveler 객체를 참조하고 있기 때문에, Traveler 객체의 reference count가 2로 증가한다.

<img width="715" alt="스크린샷 2022-11-30 오후 5 36 38" src="https://user-images.githubusercontent.com/63997044/204756032-1e87ca5f-522a-4b6e-a660-d6dcb2224771.png">

다음으로 Traveler 객체가 Account 객체를 참조하기 시작한다. Account 객체의 reference count가 순간 2로 증가했다가 이 라인이 상수 account의 마지막 사용이므로, ARC는 account를 할당 해제한다. 결국 Account의 reference count가 1이 된다.

<img width="712" alt="스크린샷 2022-11-30 오후 5 37 01" src="https://user-images.githubusercontent.com/63997044/204756068-62ed6f33-e1f1-421a-afcb-3471128827a8.png">

마지막으로 Traveler 객체의 printSummary() 메서드를 호출한다. 마찬가지로 이 부분이 Traveler 객체의 마지막 사용이기 때문에 reference count가 1 감소한다.

모든 객체의 사용이 마무리되었고 ++더 이상 참조를 유지할 필요가 없는데도 객체의 reference count는 0이 아닌 1로 유지되고 있다.++ 이 상황이 바로 Reference Cycle이다. **결국 객체들은 할당 해제되지 않고 메모리 누수의 원인이 된다.**

<img width="711" alt="스크린샷 2022-11-30 오후 5 38 12" src="https://user-images.githubusercontent.com/63997044/204756414-f6408d74-3630-4ec4-8bdb-10eb92d38ba2.png">

이런 현상을 해결할 수 있는 방법이 약한 참조와 미소유 참조이다.
약한 참조와 미소유 참조는 reference count와 무관하기 때문에, 약한 참조와 미소유 참조가 사용 중이더라도 참조된 객체가 해제될 수 있다. 
- 만약 런타임에서 이런 상황이 발생한다면 약한 참조에 대한 접근은 안전하게 nil을 반환한다. 
- 반면 미소유 참조는 위험에 빠지게(traps) 된다.

<img width="713" alt="스크린샷 2022-11-30 오후 5 38 39" src="https://user-images.githubusercontent.com/63997044/204756525-9e8240f9-c7c1-4adf-99f9-ca6ae71bdce9.png">

다시 Traveler와 Account 예제로 돌아와서 Traveler에 대한 Account의 참조를 weak으로 변경해보자.
printSummary() 메서드가 Traveler 객체의 마지막 사용이기 때문에 Traveler의 reference count는 0가 된다.

<img width="716" alt="스크린샷 2022-11-30 오후 5 39 00" src="https://user-images.githubusercontent.com/63997044/204756622-190f33ec-c86d-456b-bbd8-63ae9cbcd2a4.png">

reference count가 0이 되었기 때문에 Traveler 객체는 해제된다. Traveler 객체가 사라지면서 Account 객체의 reference count도 0이 되므로, Account 객체도 할당 해제된다.

<img width="714" alt="스크린샷 2022-11-30 오후 5 41 02" src="https://user-images.githubusercontent.com/63997044/204756772-3a2455b7-1f6e-4068-b0c4-d528a9a4fd46.png">

방금은 Reference Cycle을 해결하기 위한 사용 예시를 보여준다. 그렇지만 약한 참조로 접근한 객체가 이미 수명이 끝나 할당 해제된 상태라면 어떻게 될까? 약한 참조는 nil을 반환하기 때문에 강제 옵셔널 추출 코드가 오류를 발생시킬 것이다.


<img width="712" alt="스크린샷 2022-11-30 오후 5 41 32" src="https://user-images.githubusercontent.com/63997044/204756943-173b45b6-9ca8-44f2-aa15-73403df34e04.png">

그러나 이 조치(옵셔널 바인딩)는 비록 오류를 발생시키지는 않지만, **Silent Bug**의 원인이 된다. 그 이유는 바로 명백한 오류를 표시하지 않고 조용하게 있으면서(겉으로는 문제없는 듯 보이면서), 객체의 수명에 따라 의도한 동작을 하지 않을 수도 있기 때문이다.

<img width="711" alt="스크린샷 2022-11-30 오후 5 42 27" src="https://user-images.githubusercontent.com/63997044/204757165-85b53a76-12f6-48ed-bd1d-1d46fde3ec8e.png">

Safe techniques 중 하나는 `withExtendedLifetime()` 메서드를 사용하는 것이다.
`withExtendedLifetime()` 메서드를 사용하면 객체의 수명을 안전하게 연장할 수 있다. 원래대로라면 Traveler 객체는 이미 할당 해제 되어야겠지만 account.printSummary()가 호출될 때까지 잠재적인 버그를 예방하면서 안전하게 그 수명을 연장하는 것이다.

<img width="714" alt="스크린샷 2022-11-30 오후 5 42 53" src="https://user-images.githubusercontent.com/63997044/204757187-75a506f4-c418-4a77-bbd3-e5bebd357ea8.png">

이 코드는 바로 앞과 동일한 동작을 하는데, 꼭 어떤 동작이 끝날때까지라고 지정할 필요 없이 빈 클로저를 사용해서 명시적으로 이 코드 라인까지 연장할 수 있다.

<img width="714" alt="스크린샷 2022-11-30 오후 5 43 14" src="https://user-images.githubusercontent.com/63997044/204757208-bb6d63ce-cfa1-4bd9-aa21-8941b09709a5.png">

다른 방법으로 `defer` 를 사용해서 코드를 중간에 삽입할 수도 있다.
`withExtendedLifetime()` 메서드를 통해 객체의 수명과 관련된 버그들을 손쉽게 잡아낼 수 있을 것 같지만 직접 코드를 입력한다는 면에서 깨지기 쉬우며(만병통치약이 아니라는 의미 같음) 전적으로 개발자에게 책임이 있다. 무차별적으로 사용 시 애플리케이션의 유지 비용을 증가시킬 수 있으므로 잠재적으로 버그가 일어날 수 있는 부분에만 적용해야 한다.

<img width="716" alt="스크린샷 2022-11-30 오후 5 43 39" src="https://user-images.githubusercontent.com/63997044/204757219-9b9ba5d9-f00c-4de6-97e9-fd576a0969df.png">

어떤 상황에서는 재설계하는 것이 더 좋은 방법일 수 있다. 코드를 이와 같이 재설계하여 동일한 동작을 하면서도 잠재적인 위험을 덜어내었다.

따라서 무조건적인 정답이 정해진 것이 아니라 여러 방법 중에서 신중하게 선택해야 한다. 구조적인 설계를 신중하게 하지 않으면 성능 비용을 증가시키는 것 뿐만 아니라 버그에 노출될 수도 있기 때문이다. 왜 약한 참조와 미소유 참조를 사용해야 하는지, 어떤 목적을 위해 사용하는 것인지 늘 생각해야 한다.

<img width="715" alt="스크린샷 2022-11-30 오후 5 44 33" src="https://user-images.githubusercontent.com/63997044/204757422-ccec2b20-6319-42ec-a42b-57376ba15d77.png">

다시 돌아와서 두 클래스의 관계에 대해 확인해보자. Traveler 클래스는 Account 클래스를 참조하고 있어야 한다. Account 클래스는 Traveler 클래스의 정보가 필요한 것이지, Traveler 클래스를 꼭 참조할 필요는 없다.

실제 필요한 정보를 별도의 타입으로 분리해보자.

<img width="712" alt="스크린샷 2022-11-30 오후 5 44 54" src="https://user-images.githubusercontent.com/63997044/204757472-24beec45-75e1-4158-a251-5bb360603c79.png">

Traveler의 정보를 나타낼 새로운 클래스 PersonalInfo 클래스를 만들어 Traveler 클래스로부터 이를 분리하였다.

물론 구현을 위해 추가적인 비용이 들긴 했지만, 약한 참조 또는 미소유 참조를 사용하지 않고도 Reference Cycle을 벗어날 수 있게 되었고 객체의 수명으로 인한 모든 버그들을 해결할 수 있게 되었다.

\# 결론: Reference Cycle을 해결하기 위해 Weak, Unowned 참조를 사용해야 한다면 Weak, Unowned는 또다른 잠재적인 버그의 가능성이 있기 때문에 구조의 재설계로 해결할 수는 없는지 확인해보는 것이 좋다!


## Deinitializer Side-Effects

Deinitializer(소멸자)는 할당 해제 전에 실행된다.

<img width="714" alt="스크린샷 2022-11-30 오후 5 47 55" src="https://user-images.githubusercontent.com/63997044/204757599-2dc969ac-5d1f-4537-a1f4-1db5a5593b27.png">

예제를 보자. 클래스 내부에 `deinit`을 통해 소멸자의 동작을 구현해주었다. 일반적인 경우 소멸자는 할당 해제 전에 실행되므로 객체의 수명이 끝나는 순간인 이 부분에서 실행될 것이다. 하지만 이 또한 ARC 최적화로 정확한 시점을 알 수 없다. "Done traveling" 이전에 호출될 수도 있고, 이후에 호출될 수도 있다.
지금 상황은 소멸자의 부작용이 관찰되고는 있지만 무언가에 종속적이지는 않다.

<img width="713" alt="스크린샷 2022-11-30 오후 5 48 43" src="https://user-images.githubusercontent.com/63997044/204757651-21948103-db7f-4dc1-a7e5-85cbdf24a765.png">

새로운 클래스인 TravelMetrics와 test 메서드이다. Traveler의 destination이 업데이트될 때마다 TravelMatrix에 기록된다. 
computeTravelInterest() 메서드는 destinations를 기반으로 category를 추출하는 메서드이고, publish() 메서드는 id, destination의 수, category를 출력하는 메서드

<img width="714" alt="스크린샷 2022-11-30 오후 5 49 43" src="https://user-images.githubusercontent.com/63997044/204757744-91af074a-3fba-49c3-bd8c-7a015fa9e2b9.png">

test() 메서드의 예상 동작은 다음과 같다.
1. Traveler 객체 생성
2. Traveler 객체의 TravelMetrics에 대한 참조가 metrics에 복사됨
3. Traveler의 destination이 "Big Sur", "Catalina"로 업데이트됨
4. TravelMetrics 객체의 computeTravelInterest() 실행

<img width="713" alt="스크린샷 2022-11-30 오후 5 50 07" src="https://user-images.githubusercontent.com/63997044/204757787-ac88c682-705c-4970-9f4b-c6d14c3ae28d.png">

그러나 Traveler 객체의 마지막 사용은 destination을 "Catalina"로 업데이트 하는 부분이기 때문에 이 부분에서 소멸자가 호출되어버린다.
따라서 category를 계산하기도 전에 publish()가 호출되고 category는 nil인 채로 그대로 출력된다.
이 문제를 어떻게 해결할 수 있을지 앞에서 본 방법들을 적용시키면서 알아보자.

<img width="714" alt="스크린샷 2022-11-30 오후 5 50 40" src="https://user-images.githubusercontent.com/63997044/204757924-86f30446-ce22-4fa5-8e28-afe1b769e84f.png">

먼저 withExtendedLifetime() 메서드를 사용하여 `computeTravelInterest()`가 호출될 때까지 Traveler 객체의 수명을 연장시켜 해결했다.
그러나 이 방법을 사용하겠다면, 불일치에 대해 잠재적 위험이 있는 코드라면 모두 해당 메서드를 통해 수명을 조절해주어야 하는 책임이 있다. 책임은 전적으로 개발자에게 있으니까.

<img width="717" alt="스크린샷 2022-11-30 오후 5 51 13" src="https://user-images.githubusercontent.com/63997044/204757946-113ffd6a-3795-418a-9d61-ff0ca0c91a76.png">

이번엔 재설계 측면으로 문제를 해결해보자. 생명주기가 다른 외부(metrics)에서 객체의 프로퍼티(Traveler.travelMetrics)에 직접 접근했기 때문에 문제가 발생했으므로 프로퍼티를 private하게 만들어주고 기능을 옮겨온다면 조금 더 원칙적으로 문제를 해결할 수 있다.

<img width="715" alt="스크린샷 2022-11-30 오후 5 51 29" src="https://user-images.githubusercontent.com/63997044/204757953-3af4c9da-7dd3-452c-a9aa-906836547e53.png">

혹은 소멸자의 동작 구현을 최소화하고 defer를 사용하여 메서드를 호출하게 해결할 수 있다. 블록의 마지막에 메서드를 호출하고, deinit에서는 published되었는지 확인한다.


## Optimize Object Lifetimes

<img width="715" alt="스크린샷 2022-11-30 오후 5 51 44" src="https://user-images.githubusercontent.com/63997044/204757963-19d26020-0eea-4d38-ae39-2f0ed914c158.png">

Xcode 13에서 추가된 Build Setting. 컴파일러가 객체 사용이 종료된 즉시 할당 해제를 하여 객체가 최대한 `minimum lifetime`을 갖도록 설정할 수 있다.
