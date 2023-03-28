# Building Faster in Xcode

빌드 빨리하는 것들에 대한 요소들

![](https://i.imgur.com/ur5LB1N.jpg)

### Parallelizing Your Build

빌드를 효율적으로 증가한다.

Xcode의 타겟과 dependencies

![](https://i.imgur.com/bK3f2EF.jpg)

Target는 3가지를 빌드할 때 사용한다
- iOS App
- Framework
- Unit Tests

Target dependency는 또다른 타겟을 요구한다.
함축적, 암시적으로 말할 수 있다.

Dependency Graph

![](https://i.imgur.com/1irFRE5.jpg)

여러개의 타겟이 있으며 타겟 사이에 dependencies가 있다.

Xcode가 이 derived data를 순서화한다.

![](https://i.imgur.com/vCOnwB2.png)

Serilized Build Timeline

![](https://i.imgur.com/u4hRoYF.png)

여러개를 한번에 하나씩 직렬로 빌드하게 된다.
이는 시간을 낭비하고 잇는 것이다!

병렬의 장점

![](https://i.imgur.com/ITFdWc2.png)

그러면 이렇게 병렬하는건 어떨까?

![](https://i.imgur.com/0p3Dm3h.png)

Error가 생긴다! 왜? dependencies에 따라 진행되어야하는데 다같이 해버리기 떄문이다.

Xcode 내에서 알아서 병렬로 해결해주고 있다

![](https://i.imgur.com/BsblCGv.png)

이제 Project에서 확인한다.

![](https://i.imgur.com/Iljgc1w.jpg)

Game Target 본다.

binary library가 2개 있다.

![](https://i.imgur.com/gQZOGz6.jpg)

이것들은 linking binary이므로 암시적으로 Target를 가지고 있다!!

다음으로는 Shaders인데 이것은 Target Dependencies다

![](https://i.imgur.com/kZRUFYW.png)

현재 타겟에서 다른 dependency를 사용해서 하고 있다.

이는 현재 타겟이 빌드하기전에 다른 dependency인 Shaders 타겟을 빌드하고 끝낸 다음에서야 현재 타겟이 빌드가 가능해진다.

다음으로는 Tests dependencies에 대해 알아본다.

![](https://i.imgur.com/cv4nb5y.png)

각 Test

![](https://i.imgur.com/1ZcUb7l.png)

Shaders는 GPU를 많이 사용하고 Utilities는 CPU를 사용한다.

Shader는 그래서 Utilities에 있는 것들 아무것도 필요로 하지 않는다.

그래서 중간매체를 만든다!!

![](https://i.imgur.com/rqDd7ih.png)

그래서 Utilities는 다른 타겟이 빌드하고 있을 때 기다릴 필요 없이 자신의 작업을 병렬로 수행할 수 있다.

Parallelized Target Build Process

![](https://i.imgur.com/xj4g1E1.png)

### Run Script Phases

재빌드에서 작업을 줄여준다.

![](https://i.imgur.com/CvpjtZi.png)

Run Script Phases를 프로젝트내에서 외부 참조를 가져와서 하거나 내부에서 필요한 것을 스크립트로 넣어서 할 수 있다.

이 input Files를 통해서 Xcode가 실제로 run할 수 있는지 없는지 파악한다.

![](https://i.imgur.com/3rJgYoq.png)
![](https://i.imgur.com/K80bTIu.png)

File Lists

![](https://i.imgur.com/ktEZc6F.png)

다음으로는 Output Files
이를 통해 Xcode는 이 script가 실행할 필요가 있는지 여부를 파악한다.

![](https://i.imgur.com/VEuXuxz.png)

여기도 동일하게 File Lists가 존재한다.
![](https://i.imgur.com/a9aYHem.png)

실행 조건

![](https://i.imgur.com/660RPJS.png)

Xcode 10에서부터 되는 것이고 docs가 있으니 읽어봐라!!

Dependency Cycle 탐지 기능도 탑재

![](https://i.imgur.com/qwY6zD2.jpg)

### Measuring Build Time
빌드를 효율적으로 증가시킨다

빌드 로그 필터를 통해 어떤 것이 빌드 되는지 확인할 수 있다.

![](https://i.imgur.com/yScxSth.jpg)

Build with Timing Summary

![](https://i.imgur.com/FlN9gaq.png)

이렇게 보여준다

![](https://i.imgur.com/1A3621z.jpg)

각 빌드가 얼마나 걸리는지 알려주므로 Run script에 무엇이 빠졌는지 어떤 것이 오래 걸리는지 확인할 수 있다.

### Source-level enhancement
재빌드 시간을 줄인다!

![](https://i.imgur.com/B0NTrgS.png)

이것을 꺼라!!

Xcode 10 전에는 모든 내용을 재빌드할 때 전체를 빌드해서 시간이 오래 걸린다.
하지만 10부터는 빌드에 대한 내용을 공유하여 재빌드할 때 필요한 것만 자동적으로 해준다.
그래서 Whole Module 빌드가 필요없어진다. 그래서 기본인 Incremental를 사용해라!

![](https://i.imgur.com/UydEbLz.png)

### Dealing with Complex Expressions
빌드를 효율적으로 증가한다.

![](https://i.imgur.com/bS1Jnvw.png)

타입을 정확하게 적어서 추론하지 않게 해라
크기가 적으면 상관없지만 커지면 점점 이것이 늦출 것이다.

클로저에서도 이렇게 보면 너무 더럽지않는가?

![](https://i.imgur.com/85ZKTt5.png)

심지어 컴파일러에서도 에러를 내보낸다

![](https://i.imgur.com/h2QqwX9.png)

그래서 타입을 적어주려고 하지만 여기에서는 이미 위에서 호출할 때 해두므로 필요없다

![](https://i.imgur.com/dOJU9pc.png)

Obj-c코드에서 어떤 메서드를 실행해야할지 몰라서 문제가 된다

![](https://i.imgur.com/yGFgB1f.jpg)

그래서 프로토콜을 선언해서 추상화로 사용한다.

![](https://i.imgur.com/j30qYuE.jpg)

이렇게 해서 추상적으로 대응해줄 수 있다.

그래서 추상적으로 구현된 메서드가 어떤 것인지 추측할 수 있다.

여기까지는 재컴파일 해야하는 것을 찾악서 그것을 해주는 것에 대해 설명해주었다.

그러면 컴파일러가 재컴파일이 필요없는 것은 어떻게 찾을까?

### Understanding Dependencies in Swift
재빌드 시간을 줄여준다.

우리는 헤더 파일이 없으므로 타겟으로 내부를 볼 수 있다.

그래서 파일 베이스로 우리는 빌드를 확인해서 내부에 잇는 것을 볼 수 있다.

![](https://i.imgur.com/e7ndcgO.jpg)

이렇게 PathSegment가 있는데 이것은 오른쪽 컴파일 할때 영향을 끼치지 않는다.

![](https://i.imgur.com/L6J093s.jpg)

Swift Dependency 규칙

![](https://i.imgur.com/yL1Aypa.png)

### Limiting Yout Objective-C/Swift interface

여기에 여러 header들이 있는데 Obj-c, Swift 여러 헤더가 존재한다.

거기에서 이제 bridging header가 있고 이것이 관리한다.
또한 Swift code에 대해서도 여기에서 관리하므로 중간 인터페이스 역할을 한다.

![](https://i.imgur.com/BWASs2v.jpg)

여기에서도 header가 양쪽에 있는데 오른쪽은 Swift -> objective-C
왼쪽은 이 반대로 코드 변환해준다.

이렇게 2가지 큰 header가 있어서 이 빌드만을 사용하면 되므로 빌드 시간을 줄여준다.

![](https://i.imgur.com/SlgymOm.jpg)

Generated Heade를 최소화하기 위해서 private를 붙여서 사용한다.

그러면 generated header로 부터 아래의 코드가 사라지게된다. 내부에서만 사용하므로?!

하지만 이렇게 하는 것보다 내부에서 해줌!

![](https://i.imgur.com/x5E3ONL.jpg)

요런 친구들 있는데.. 

![](https://i.imgur.com/f1I8FzS.jpg)

그래서 이렇게 크기를 줄인다!!!

![](https://i.imgur.com/Kdvd0Od.jpg)
