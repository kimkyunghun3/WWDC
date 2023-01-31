# [2020] Write tests to fail

![](https://velog.velcdn.com/images/marisol/post/5226cd84-17cd-452f-8bbd-7ed45127c446/image.png)

코드를 작성하기 전에 테그트를 작성했든 후에 작성했든 상관없이, 보통은 테스트를 항상 green으로 만드는것이 목표인데,
✅ 초록색 아이콘이 테스트가 통과되었다는 의미이기 때문이다.

![](https://velog.velcdn.com/images/marisol/post/2a66e42d-5d3e-4986-bad4-72051f96647e/image.png)

그런데 이 세션의 발표자는 올해는 ```실패하는 테스트```를 작성하고자 한다고 한다.

![](https://velog.velcdn.com/images/marisol/post/4a540d68-0215-4966-a709-53f68614aac1/image.png)

왜냐하면, 훌륭한 테스트가 버그를 발견하고, 우리는 실패에 대비해야하기 때문이다.

![](https://velog.velcdn.com/images/marisol/post/4197345f-9f22-4631-8203-07393f2acf2f/image.png)

테스트에서 버그가 발견되었다면, 테스트는 실패한다. 이는 정확히 "설계된 결과"이다.
오류를 분류하기 위한 도구로 ```테스트 결과 번들 (Test Result Bundle)```을 사용한다고 한다.

이 세션에서는 result 번들만으로 테스트를 쉽게 분류하는 방법과, 테스트를 디버깅하는 대신 오류를 분석하는데에 시간을 할애할 수 있도록, 테스트를 보다 견고하게 만드는 방법에 대해 알아본다.

![](https://velog.velcdn.com/images/marisol/post/0742049b-89bc-4f28-82c7-be6e43f9edbe/image.png)

테스트의 템플릿은 Set up(설정), Test(테스트), Tear down(해체)의 패턴을 따른다.

![](https://velog.velcdn.com/images/marisol/post/c8c81ae1-7de6-4455-b75f-7c2d6d73032b/image.png)

그리고 Test 섹션 내에서 다시 action과 assertion으로 나눌 수 있다.

## 1️⃣ Set up

![](https://velog.velcdn.com/images/marisol/post/e30c180d-532c-4c3b-a533-5d69c3d081d5/image.png)

테스트를 실행하기 전, 필요한 가정을 명시적으로 설명하고, 앱와 환경 상태를 설정한다.
Xcode 11.4에서는 ```setUpWithError```라고 하는 새로운 setUp 함수를 도입했는데, 이 함수를 통해 설정 중에 발생하는 오류를 포착하고 전달할 수 있다.
그리고 이전 테스트로 인해 앱의 상태가 변경되거나, 테스트에서 사용하는 데이터가 수정될 수 있기 때문에 ```setUpWithError```메서드를 통해 테스트가 실행되기 전에 필요한 초기 상태를 설정한다.

![](https://velog.velcdn.com/images/marisol/post/6580574d-01aa-4396-87ca-2896bd5cb82c/image.png)

이 예에서는 문제가 발견하면 즉시 테스트가 실패하도록 ```continueAfterFailure```를 false로 설정했다.
이렇게 하면 첫번째 오류를 더 빨리 찾을 수 있다.

![](https://velog.velcdn.com/images/marisol/post/62fdf022-62a5-4cac-903e-d392b1c7e7dc/image.png)

그리고 매 테스트 마다 앱을 새로 실행시킨다.

![](https://velog.velcdn.com/images/marisol/post/601e6bb7-f987-457c-abcc-21dba3d06071/image.png)

또한 ```launchArguments```와 environment 변수를 사용하여 앱 내에서 state를 빠르게 설정할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/5d9d7c5c-a1ff-444a-afcd-f462b528f0cf/image.png)

테스트 중에 이중 인증을 무시하는 등, 필요한 경우가 있을 수 있다.
이 경우에는 ```Menu``` 탭을 건너 뛰고 대신 ```Recipes``` 탭에서 시작하기 위해 사용했다.


이와 같은 작은 변경사항은 불필요한 작업을 방지하여 테스트 실행 속도를 향상시킬 수도 있지만,
더 중요한 것은 레시피 탭을 테스트하기 위한 결과를 보는 동안 메뉴 탭에서 발생할 수 있는 실패를 분류할 필요가 없다는 것이다.

![](https://velog.velcdn.com/images/marisol/post/ec2cdf74-5837-48ad-a466-b1131004ec68/image.png)

요약하자면, 오류 처리를 개선하기 위해 ```setUpWithError```를 사용하고 있고,
앱 실행(app launch)과 같이 class의 모든 테스트에 대해 공통적인 설정 작업을 수행한다.
그리고 state 설정을 위해 앱과 통신하기 위해 ```launchArguments```를 사용하고 있다.

## 2️⃣ Test(Action)

![](https://velog.velcdn.com/images/marisol/post/eac7e38a-d62d-4c15-ac6c-2e690bf93bce/image.png)

첫번째로 고려해야할 것은, 각각의 테스트가 특정한 목표를 염두에 두어야 한다는 것이다.
그리고 그 목표는 test 제목에 반영되어야 한다.

이 경우에는 성분 목록의 정확성에 대해 테스트하고 있다. 이 테스트가 수행해야할 유일한 작업은 "Berry Blue 레시피를 선택하는 것"이다. 작업을 최소화해야 나중에 실패를 분류하기 더 쉬워진다.

![](https://velog.velcdn.com/images/marisol/post/29dddb1a-2e9b-4a3f-80cf-8efd19584d24/image.png)

이 Berry Blue 행을 탭하면 

![](https://velog.velcdn.com/images/marisol/post/90dc1352-fa5d-4225-8cb0-2b4ac9f5463a/image.png)

레시피가 표시되고, action의 결과로 성분 목록을 확인할 수 있다. 

![](https://velog.velcdn.com/images/marisol/post/4b26fcab-1f79-41bc-8ddb-225ae4aa0e16/image.png)

이와 같이 result bundle에서 테스트의 명확한 이름 덕분에 그 테스트가 무엇을 검증하고 있는지 쉽게 알 수 있다.

![](https://velog.velcdn.com/images/marisol/post/b730eed2-fa7a-4c4b-a753-c16892764ace/image.png)

여기서 네이밍에 대해 대해서 잠깐 설명하는데,
발표자는 수년간 UI 요소의 label이 자주 변경된다는 것을 알게 되었고, 예방책으로 모든 문자열 값에 enum을 사용하게 되었다고 한다.

![](https://velog.velcdn.com/images/marisol/post/a463dad8-1a45-4b2f-b665-053632fd90b1/image.png)

이렇게 하면 UI가 변경되면 테스트를 쉽게 업데이트하여 변경 사항에 대응할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/c820f373-6608-46a2-9b52-d197075d037b/image.png)

그리고 UI 변경 사항으로 테스트를 업데이트하는 시간을 절약할 수 있을 뿐만 아니라, 인식하기 어려운 맞춤법 오류로 인한 테스트 실패 횟수를 줄일 수 있다.

![](https://velog.velcdn.com/images/marisol/post/bc040bd0-d144-4de3-8f2c-eabbe0bc77d4/image.png)

모든 문자열을 enum으로 모으는 것과 마찬가지로, 발표자가 실수를 최소화 하는 또 다른 방법을 말해주고 있는데,
공통 코드를 helper 함수로 분해해서 여러 테스트에서 동일한 코드 경로를 사용할 수 있도록 하는 것이다.

이 앱에서는 스무디 목록에 액세스하고, 레시피를 선택해야 하는 여러 테스트가 필요하다.
이 공통 테스트 경로를 사용하면 코드를 복제하는 대신 테스트 오류를 줄이기 위해 시간을 쓸 수 있다.

## 3️⃣ Test(Assertion)

발표자는 Test의 Assertion 부분이 테스트의 핵심이라고 소개하고 있다.
테스트 실패를 쉽게 분류할 수 있도록 하기 위한 test assertion 및 에러 처리에 관한 것들을 다루고 있다.

![](https://velog.velcdn.com/images/marisol/post/84132c24-267b-447b-8f0b-8b03d5358aae/image.png)

먼저 XCTAssert 함수에서 optional message를 이용하는 것이다. result bundle에는 메세지들이 많이 누락될 수 있다.

이 경우에 당연히 3이 2와 같지 않다는 걸 알지만.. 그래서 2가 뭔데? 라고 생각할 수 있다.
아래처럼 optional message를 추가하면 왜 이 expression이 실패했는지에 대한 단서를 찾을 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/00f00752-8ef2-4d96-8d09-5ffd7bcbc4d9/image.png)

Xcode 12에서는 실패를 보고하는 새로운 low-level-way인 XCTIssue가 추가되었다. 
자세한 내용은 "Triage Test Failures with XCTIssue" 참고..

![](https://velog.velcdn.com/images/marisol/post/8ea79b22-218b-48b9-aa63-de6b50b0b8b7/image.png)

그리고 가끔 비동기 이벤트를 분류하는데에 어려움을 겪었는데,
이 경우 레시피 버튼을 누르지만, 코드가 수행하는 작업에 따라 로드하는데 시간이 걸릴 수 있다.
만약 레시피를 즉시 리턴한다면, 아직 존재하지 않을 수도 있다.

![](https://velog.velcdn.com/images/marisol/post/ae80e57d-f25b-44e8-8baf-9190e0cb0dd5/image.png)

이전에는 test에 약간의 시간을 주기 위해 ```sleep```을 사용했지만, 결과를 얻는데 시간이 지연되는 문제가 있다.
XCTest에는 재시도 기능이 내장되어 있지만, 코드에 따라 충분하지 않을 수도 있다.
그래서 ```waitForExistence```를 timeout과 함께 사용하는 것을 선호한다.

결과가 시간 초과 이전에 true일 경우, 대기 시간을 그만큼 절약할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/33acbb51-3d8c-4785-86df-99d690639cb1/image.png)

result bundle에서 내 테스트가 Ingredients View를 찾기 위해 5초 동안 기다린 것을 확인할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/7aa2fd1c-46ec-48dc-b4c9-2133dd1feec4/image.png)

또 다른 방법은 옵셔널을 해제하는 것이다.

이 예에서는 전달된 문자열 배열의 즐겨찾기 수를 반환하려고 하지만, 옵셔널을 해제하지 않고 그냥 진행했다고 해보자.

![](https://velog.velcdn.com/images/marisol/post/5d60cc65-7205-4a29-9570-082f8ef390b1/image.png)

코드를 실행하면 충돌이 발생하여 테스트가 중단된다. 

![](https://velog.velcdn.com/images/marisol/post/0beddedb-2607-435f-9a4f-2c03931ef53d/image.png)

지속적인 통합 환경에서 이 문제가 발생하면, "Test crashed with signal ill"이라는 텍스트가 포함된 result bundle이 표시된다. 옵셔널을 안전하게 해제하면 이 상황을 쉽게 피할 수 있다.

![](https://velog.velcdn.com/images/marisol/post/0e5eb0e3-7b78-4f91-85ed-20b1a80ab480/image.png)

첫 번째로 if 블록 안에서 optional이 해제된 값을 사용할 경우 if let을 사용할 수 있고,
optional이 해제된 값을 블럭 밖에서 사용할 경우 guard let을 사용할 수 있다. 그리고 nil이 발생할 경우, guard 블럭에 오류를 던질 수 있다.
세 번째 옵션은 nil 병합 연산자를 사용하는 것이다. nil일 경우, 제공한 기본 값을 사용한다.
네 번째 옵션은 XCTest 프레임워크에서 제공하는 XCTUnwrap을 사용하는 것이다. 내 테스트에서 nil이 발견되면 에러를 던지는 guard let의 단순화라고 생각하면 된다.

![](https://velog.velcdn.com/images/marisol/post/2c361ddf-a3b8-404c-90e3-65ef1b50e4fc/image.png)

XCTUnwrap을 사용하면 result bundle에 자동 생성된 메세지 외에 호출에 대한 커멘트도 표시된다.

![](https://velog.velcdn.com/images/marisol/post/88c9db6b-b69e-4b92-8dde-5412ce7ace9d/image.png)

또한 이 result bundle에는 사용자가 읽을 수 있는 disclosure group이 포함되어 있다. 이 그룹은 내 코드가 당시 내가 수행하고 있던 작업에 대한 더 많은 컨텍스트를 제공해주기 위해 추가되었다.

여기서 내가 Berry Blue Smoothie에서 grape를 잘못 찾고 있었다는 것을 쉽게 알 수 있다.

![](https://velog.velcdn.com/images/marisol/post/a42ba98b-2e65-4b48-ade9-b889c6cff2ef/image.png)

```XCTContext.runActivity```는 블럭에서 수행되는 작업과 함께 result bundle에 표시된다.
result bundle에 컨텍스트를 추가하고, 테스트에서 수행하는 작업에 따라 쉽게 읽을 수 있도록 하는 좋은 방법이 된다.

![](https://velog.velcdn.com/images/marisol/post/06b3f636-878c-4fbd-bcfb-fa2b93719261/image.png)

![](https://velog.velcdn.com/images/marisol/post/825cb041-c3c8-4ff6-b149-018a355552e4/image.png)

runActivity를 사용하여 수행할 수 있는 또 다른 작업은 ```XCTAttachment```를 사용하여 첨부 파일을 추가하는 것이다.
XCTContext또는 test case에 파일, 이미지, 데이터와 같은 첨부 파일을 추가하면 result bundle에 표시된다.

특히 CI 시스템에서 제공되는 테스트에서 실패한 테스트에 대한 추가 로깅을 수집하는 좋은 방법이다. 나중에 실패를 분류하는 것을 더 쉽게 만들어준다. 나중에 제품의 오류를 분류하는데에 필요한 모든 데이터를 수집하는 것은 테스트의 책임이다.

![](https://velog.velcdn.com/images/marisol/post/52e33e0d-74c4-4932-8090-aa4cd21505e3/image.png)

때때로 테스트가 전혀 실행되지 않아야 하며, optional message를 추가하여 실행되지 않는 테스트를 문서화 하기 위해 ```XCTSkip```, ```XCTSkipUnless```, ```XCTSkipIf```를 사용한다.

주요 용도는, 실행 중인 플랫폼과 관련이 없는 테스트를 건너 뛰는 것이다.
그리고 지금은 고칠 수 없는 test들이 있을 수 있는데, 그것들을 disable하도록 할 수 있다.

XCTSkip을 사용하면 result bundle에서 건너 뛴 테스트를 계속 볼 수 있기 때문에, 문제가 해결될 때 테스트를 다시 작성하거나 수정해야 한다는 사실을 잊지 않게 해준다.

![](https://velog.velcdn.com/images/marisol/post/62f6f91a-3b34-424a-b871-3b4ae33fb838/image.png)

![](https://velog.velcdn.com/images/marisol/post/27164aa5-5d09-4c82-a251-0c4d7fd05422/image.png)

요약하자면, assertion message를 추가하고,
관련된 XCTAssert 함수를 사용하여 result bundle에 컨텍스트를 추가한다.
테스트가 중단되지 않도록 optional을 확실히 unwrap한다.
sleep 대신에 비동기 이벤트와 타이밍 문제에 ```waitForExistence``` 메서드를 사용하고,
XCTContext.runActivity와 첨부 파일을 사용하여 Result bundle에 컨텍스트 및 컨텐츠를 추가한다.
현재 시나리오에서는 실행되지 않을 것으로 예상되는 테스트에 XCTSkip을 사용한다.

## 4️⃣ Tear down

![](https://velog.velcdn.com/images/marisol/post/734f3304-3ac0-4bb1-81b4-a690a2e1622b/image.png)

tearDown 메서드를 활용하여 장애 분석을 포함한 추가 로깅을 수집하고, 환경을 reset하면 된다.

![](https://velog.velcdn.com/images/marisol/post/54830524-58f6-455b-b284-7bb0457bc57e/image.png)

요약하자면
먼저 테스트를 위해 환경을 변경하고, 테스트에 필요한 가정을 확인하는 setUp을 살펴보았다.
그리고 테스트 action을 통해 테스트하고 싶은 필요한 동작을 수행했다.
그리고 helper 메서드, error, test assertion을 통해 작업이 제대로 완료되었는지 확인했다.
그리고 tearDown 메서드로 테스트가 끝난 후 데이터를 수집하고, 환경을 리셋했다.
