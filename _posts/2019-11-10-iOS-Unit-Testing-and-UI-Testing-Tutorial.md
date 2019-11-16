---
layout: post
title:  " iOS Unit Testing and UI Testing Tutorial "
categories: Test
tags: Unit UI
author: GGuJi
---



* content
{:toc}
## iOS 단위 테스트와 UI 테스트 튜토리얼 (iOS Unit Testing and UI Testing Tutorial)



### 개요

테스트를 작성하는 것은 화려하지 않지만, 테스트를 통해 멋진(spakling) 앱이 버그투성이가 되어 쓰레기로 변하는 것을 막을수 있기 때문에 반드시 필요하다. iOS 단위 테스트 및 UI 테스트 튜토리얼을 읽은 경우라면, 이미 코드와 UI에 대한 테스트를 작성해야 한다는 것을 알고 있지만 XCode에서 어떻게 테스트하는지 모른다.

어쩌면 이미 **"작업중인(working)"** 앱을 가지고 있지만, 아무런 테스트도 설정하지 않은 것일 수 있고, 앱을 확장할때 변경 사항을 테스트 할 수 있기를 원한다. 어쩌면 이미 몇가지 테스트를 작성했지만, 올바른 테스트인지 확실하지 않거나 작업중인 앱에서 원하는대로 테스트 하고 싶을 수도 있다.

여기에 있는 iOS 유닛 테스팅과 UI 테스팅 튜토리얼은 XCode에서 테스트 네비게이터를 사용하여 어떻게 앱의 모델과 비동기 메소드를 테스트 하는지 보여주며, 스텁(stub)과 모형(mocks)을 사용하여 어떻게 라이브러리나 시스템 객체와의 상호작용을 가짜(fake)로 만드는지, UI와 성능 테스트 하는 방법, 그리고 코드 커버리지(coverage) 도구를 사용하는 방법을 배우게 된다. 그 방법을 따르면, 닌자(ninjas) 테스트에 사용되는 어휘중 일부를 골라 낼 것이고, 이 튜토리얼을 마치면 침착하게 SUT(System Under Test)에 종속성을 추가(Dependency injecting)할 수 있다.



### 테스트란 무엇인가? (What to Test?)

테스트를 작성하기 전에, 기초부터 시작하는 것이 중요하다. 무엇을 테스트 해야 하는가? 기존 앱을 확장하는 경우라면, 먼저 변경할 모든 구성요소에 대한 테스트를 작성해야 한다.

보다 일반적으로, 테스트는 다음을 포함해야 한다.



- 핵심기능 : 모델 클래스와 메소드, 컨트롤러와의 상호작용
- 가장 일반적인 UI 흐름도(workflows)
- 경계 조건(boundary conditions)
- 버그 수정



### FIRST : 테스트에 대한 모범사례 (Best Practices for Testing)

두문자어 `FIRST`는 단위 테스트를 위한 간결한 기준(criteria) 세트를 효과적으로 설명한다. 



해당 기준은 다음과 같다.

- `F: Fast`(빠르게) : 테스트는 빨리 실행되야 한다. 그래서 사람들이 신경쓰지 않는다.
- `I: Independent/Isolated`(독립적/분리된) : 테스트는 따로 설정이나 분리를 해서는 안된다.
- `R: Repeatable`(반복가능한) : 테스트 수행할때마다 동일한 결과를 얻어야 한다. 외부의 데이터 공급자나 동시성(concurrency) 문제로 인해 일시적으로 오류가 발생 할 수 있다.
- `S: Self-validating`(자체 검증) : 테스트는 완전히 자동화 되어야 한다. 로그 파일에 대한 프로그래머의 해석보다는 패스(pass)또는 실패(fail)를 출력해야 한다.
- `T: Timely`(적시에) : 이상적인 테스트는 테스트한 생산 코드를 작성하기 전에 작성해야 한다.



### 시작하기 (Getting Started)

[시작 프로젝트](https://koenig-media.raywenderlich.com/uploads/2016/12/Starters.zip) 를 다운로드 받고 압축을 푼다.

BullsEye는 [iOS Apprentice(초심자)](https://www.raywenderlich.com/store/ios-apprentice) 안의 샘플 앱을 기반한다. BullsEyeGame 클래스에 게임 로직을 추출하고 다른 게임 스타일을 추가했다.

오른쪽 아래에는 사용자가 게임 스타일을 선택 할 수 있도록 분리된 컨트롤이 있다. `Slide(슬라이더)`를 움직여 가능한 한 목표 값에 가까워지거나 `Type(타입)`을 사용하여 슬라이더 위치가 어디인지 추측할수 있다. 컨트롤의 동작은 사용자가 선택한 게임 스타일을 기본 값으로 한다.

`HalfTunes`는 [NSURLSession 튜토리얼](https://www.raywenderlich.com/110458/nsurlsession-tutorial-getting-started)의 샘플 앱이며, Swift3로 업데이트 되었다. 사용자는 노래에 대해 iTunes API로 조회한 다음에 노래 일부분을 다운로드하고 재생 할 수 있다.



### XCode에서 유닛 테스트 하기 (Unit Testing in XCode)

#### Unit Test Target 생성하기 (Creating a Unit Test Target)

`XCode Test Navigator`는 테스트 하기 가장 쉬운 방법을 제공한다. 앱에서 테스트 타겟을 생성하고 테스트를 실행한다.

`BullsEye`프로젝트를 열고 `Command-6`를 눌러서 테스트 네비게이터를 열어준다.

왼쪽 아래의 `+`버튼을 누르고, 메뉴에서 `New Unit Test Target`를 선택한다.



![img](https://t1.daumcdn.net/cfile/tistory/23250B3658D07F8F13)





기본으로 `BullsEyeTests`이 설정된다. 테스트 네비게이터에서 테스트 번들이 보여질때, 클릭하여 편집기를 열어준다. `BullsEyeTest`가 자동으로 보여지지 않으면, 다른 네비게이터 중 하나를 클릭하였다가 테스트 네비게이터로 돌아가는 것으로 문제를 해결할 수 있다.

![img](https://t1.daumcdn.net/cfile/tistory/2256B53558D07F9C13)



`XCTest`를 가져오고(import) `BullsEyeTests`를 `XCTestCase`의 하위클래스로 정의하며, `setup(), tearDeown()` 그리고 테스트 메소드를 정의한다.



테스트 클래스를 실행하는데 3가지 방법이 있다.

1. `Product\Test`또는 `Command-U`. 실제로 모든 테스트 클래스를 실행한다.
2. 테스트 네비게이터에서 화살표 버튼을 클릭한다.
3. 거터(gutter: 배수로, 홈통)에서 다이아몬드 버튼을 클릭한다.

![img](https://t1.daumcdn.net/cfile/tistory/2249703A58D07FA811)



테스트 네비게이터 또는 거터(gutter)에서 다이아몬드 버튼을 클릭하면 각각 테스트 메소드를 실행할 수 있다. 여러가지 방법으로 테스트를 수행하여 소요시간과 모양을 확인해본다. 샘플은 아직 아무것도 하지 않았기 때문에, 빨리 실행될 것이다.

모든 테스트가 성공하면, 다이아몬드는 녹색으로 바뀌고 체크 표시가 나타난다. `testPerformanceExample()`의 끝에 있는 회색 다이아몬드를 클릭하여, 성능 결과를 연다.



![img](https://t1.daumcdn.net/cfile/tistory/2417EB4158D07FB607)



`testPerformanceExample()`은 필요하지 않으므로, 삭제한다.



#### 모델 테스트에 XCTAssert을 사용 (Using XCTAssert to Test Models)

먼저, `BullsEye's` 모델의 핵심 기능을 XCTAssert을 사용하여 테스트한다. 

>  `BullsEyeGame` 객체가 라운드 점수를 올바르게 계산하는가?



`BullsEyeTests.swift`에서 `import`구문 아래에 다음 줄을 추가한다.



```swift
@testable import BullsEye
```

이것은 유니테스트가 `BullsEye`의 클래스와 메소드에 접근 할 수 있게 해준다.

`BullsEyeTest`클래스 맨위에 프로퍼티를 추가한다.



```swift
var gameUnderTest: BullsEyeGame!
```



`setUp()` 에서 `super` 를 호출 후에, 새로운 `BullsEyeGame` 객체를 만들고 시작한다.

```swift
gameUnderTest = BullsEyeGame()
gameUnderTest.startNewGame()
```



이렇게 하면 클래스 수준에서 SUT(System Under Test) 객체를 만든다. 이 테스트 클래스의 모든 테스트들은 SUT 객체의 프로퍼티와 메소드에 접근 할수 있다.

여기에서는 게임의 `startNewGame()` 메소드를 호출하며, `targetValue`를 생성한다. 많은 테스트가 `targetValue`를 사용할 것이며, 게임이 점수를 정확하게 계산하는지 테스트 한다.

잊어버리기 전에, `super`를 호출하기 전에 `tearDown()`에서 SUT 객체를 해제(release) 해준다.

```swift
gameUnderTest = nil
```

> 모든 테스트가 깨끗한 슬레이트(slate)로 시작하는지 확인하기 위해 `setup()`에서 SUT를 생성하고, `tearDown()`에서 해제하는게 가장 좋은 방법이다. 자세한 내용은 [`Jon Reid's의 게시물`](http://qualitycoding.org/teardown/)을 확인한다.

이제 첫번째 테스트를 작성할 준비가 되었다.



`testExample()`를 다음 코드로 변경한다.

```swift
// XCTAssert to test model
func testScoreIsComputed() {
  // 1. given
  let guess = gameUnderTest.targetValue + 5
 
  // 2. when
  _ = gameUnderTest.check(guess: guess)
 
  // 3. then
  XCTAssertEqual(gameUnderTest.scoreRound, 95, "Score computed from guess is wrong")
}
```

테스트 메소드의 이름은 항상 `test`로 시작하며, 그 뒤에 무엇을 테스트하는지 설명을 해준다.

`given, when, then`섹션으로 테스트 형식을 지정하는것이 좋다.



1. `given` 섹션에서, 필요한 모든 값을 설정한다. : 이 예제에서, `guess` 값을 작성하여 `targetValue`와 얼마나 다른지 지정 할 수 있다.
2. `when` 섹션에서, 테스트 중인 코드를 실행한다. : `gameUnderTest.check(_:)` 호출한다.
3. `then` 섹션에서, 예상한 결과를 확인하며(이 경우에, `gameUnderTest.scoreRound`가 100 - 5 이다.), 테스트가 실패 할때의 메시지를 출력한다.



거터(gutter)에서 다이아몬드 아이콘을 클릭하거나 테스트 네비게이터에서 테스트를 실행한다. 앱은 빌드되고 실행될 것이며, 다이아몬드 아이콘은 녹색 체크 표시로 변경될것이다.



> `XCTestAssertions`의 전체 목록을 보기위해, 코드에서 `XCTAssertEquale`을 `Commnad-click`하면 XCTestAssertions.h 또는 [Apple’s Assertions Listed by Category](https://developer.apple.com/library/prerelease/content/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/04-writing_tests.html#//apple_ref/doc/uid/TP40014132-CH4-SW35)로 이동한다.

![img](https://t1.daumcdn.net/cfile/tistory/220A554458D07FC70D)



> `Given-When-Then`테스트의 구조는 클라이언트 친화적인 행동 주도적인 개발(BDD: Behavior Driven Development)에서 시작되었으며, 은어(low-jargon)의 명칭이다. 대체할 시스템 이름은 `Arrange-Act-Assert`와 `Assemble-Activate-Assert`이다.



#### 디버깅 테스트

`BullsEyeGame`에는 의도적으로 내장된 버그가 있으며, 지금은 그것을 찾는 연습을 할 것이다. 버그가 동작하는 것을 보려면, `testScoreIsComputed`을 `testScoreIsComputedWhenGuessGTTarget`로 변경한다. 그리고 나면, 복사-붙여넣기-편집으로 `testScoreIsComputedWhenGuessLTTarget`을 만든다.

테스트에서, `given`섹션의 `targetValue`에서 5를 빼준다. 다른 것은 모두 그대로 둔다.

```swift
func testScoreIsComputedWhenGuessLTTarget() {
  // 1. given
  let guess = gameUnderTest.targetValue - 5
 
  // 2. when
  _ = gameUnderTest.check(guess: guess)
 
  // 3. then
  XCTAssertEqual(gameUnderTest.scoreRound, 95, "Score computed from guess is wrong")
}
```

`guess`와 `targetValue`는 여전히 5 차이이며, 점수는 여전히 95여야 한다.

네비게이터 중단점에서, `Test Failure Breakpoint`를 추가한다. : 이는 테스트 메소드가 실패 할때, 테스트 실행이 중지된다.

![img](https://t1.daumcdn.net/cfile/tistory/2458E84258D07FD519)



테스트를 실행하면, 테스트 실패로 `XCTAssertEqual` 줄에서 멈춰야 한다.

디버그 콘솔에서 `gameUnderTest`와 `guess`를 검사한다.

![img](https://t1.daumcdn.net/cfile/tistory/242DDB3E58D07FE01C)



`guess`는 `targetValue - 5`이지만, `scoreRound`는 95가 아니라 105이다.

더 조사하려면, 일반 디버깅 프로세스를 사용한다.`when` 구문의 `BullsEyeGame.swift`에서 `check(_:)`내의 `difference`를 생성하는 곳에 중단점을 설정한다. 그리고 나서 테스트를 다시 실행하고, 앱에서 `difference`의 값을 조사하기 위해 `let difference`문으로 단계를 이동한다.

![img](https://t1.daumcdn.net/cfile/tistory/2154D23D58D07FEF13)



`difference`가 음수인 것이 문제이다. 그래서 점수가 100 - (-5) : 차이 값을 절대값을 사용하여 수정한다. `check(_:)`에서, 교정된 줄의 주석을 제거하고 잘못된 것을 삭제한다.

두개의 중단점들을 제거하고 테스트를 다시 실행하여 이제는 성공하는 것을 확인한다.



#### XCTestExpectation을 사용하여 비동기 작업을 테스트 하기

이제 어떻게 모델을 테스트 하는지와 테스트 실패를 디버깅하는 것을 배웠다. `XCTestExpectation`을 사용하여 네트워크 작업을 테스트해보자.

`HalfTunes` 프로젝트를 연다 : URLSession을 사용하여 iTunes API를 조회하고 샘플 노래를 다운로드 한다. 네트워크 작업에 대해 [AlamoFire](https://www.raywenderlich.com/121540/alamofire-tutorial-getting-started)를 사용하고, 수정하는 것을 가정한다. 무엇이 깨지는지 보기위해, 네트워크 작업 테스트를 작성해야 하고 코드를 변경하기 전과 후에 테스트를 실행한다.

`URLSession`메소드들은 비동기이다 : `URLSession`메소드들은 바로 리턴하지만, 약간의 시간이 지날때까지는 실제 실행이 끝난게 아니다. 비동기 메소드들을 테스트 하려면, `XCTestExpectation`을 사용하여, 비동기 작업이 완료될때까지 기다려야 한다.

비동기 테스트는 일반적으로 느리므로 더 빠른 단위 테스트들과 분리시켜야 한다.

`+`메뉴에서 `New Unit Test Target...`을 선택하고, `HalfTunesSlowTests` 이름을 넣는다. `import`문 바로 아래에 HalfTunes 앱을 가져오기(import)한다.

```swift
@testable import HalfTunes
```

이 클래스 내에서 테스트들은 애플 서버들에 요청을 보내기 위해 기본 세션(default session)을 사용하며, `sessionUnderTest`객체를 선언하고, `setup()`에서 생성하고 `tearDown()`에서 해제(release)한다.

```swift
var sessionUnderTest: URLSession!
 
override func setUp() {
  super.setUp()
  sessionUnderTest = URLSession(configuration: URLSessionConfiguration.default)
}
 
override func tearDown() {
  sessionUnderTest = nil
  super.tearDown()
}
```

`testExample()`을 비동기 테스트로 변경한다.

```swift
// Asynchronous test: success fast, failure slow
func testValidCallToiTunesGetsHTTPStatusCode200() {
  // given
  let url = URL(string: "https://itunes.apple.com/search?media=music&entity=song&term=abba")
  // 1
  let promise = expectation(description: "Status code: 200")
 
  // when
  let dataTask = sessionUnderTest.dataTask(with: url!) { data, response, error in
    // then
    if let error = error {
      XCTFail("Error: \(error.localizedDescription)")
      return
    } else if let statusCode = (response as? HTTPURLResponse)?.statusCode {
      if statusCode == 200 {
        // 2
        promise.fulfill()
      } else {
        XCTFail("Status code: \(statusCode)")
      }
    }
  }
  dataTask.resume()
  // 3
  waitForExpectations(timeout: 5, handler: nil)
}
```

이 테스트는 iTunes에 유효한 쿼리를 보내면 상태 코드 200을 반환하는지 확인한다. 대부분의 코드는 앱 내에서 쓰는 것과 같으며, 다음과 같이 몇줄을 추가한다.

1. `expectation(_:)`은 `promise`에서 저장하는 `XCTestExpectation`객체를 반환한다. 이 객체에 대해 일반적으로 사용되는 다른 이름들은 `expectation`과 `future`이다. `description` 매개변수는 무슨 일이 일어날지 설명한다.
2. `description`이 일치하도록, 비동기 메소드의 완료 핸들러의 성공 조건 클로저에서 `promise.fulfill()`을 호출한다.
3. `waitForExpectations(_:handler:)`은 `timeout`시간이 끝날때까지 기다리거나, 기대되는 모든 것들이 충족될때까지 테스트 실행을 유지해준다.

테스트 실행한다. 인터넷에 연결되어 있다면, 테스트는 시뮬레이터에서 앱이 로드 되기 시작한 후에 테스트가 성공하는데 1초가 걸린다.



#### 빠른 실패 (Fail Faster)

실패는 아프지만, 영원히 그럴 필요는 없다. 여기에서 테스트 실패 여부를 빠르게 파악하는 방법을 설명한다. :]

비동기 작업이 실패하도록 테스트를 수정하려면, 간단하게 URL에서 itunes에서 s를 삭제해준다.

```swift
let url = URL(string: "https://itune.apple.com/search?media=music&entity=song&term=abba")
```

테스트를 실행 : 실패하지만, 타임아웃하는 시간이 필요하다. 이것은`promise.fulfill()`을 호출한 부분에서 그 요청이 성공할 것이라 기대했기 때문이다. 요청이 실패하기 때문에, 타임아웃이 만료된 경우에만 테스트가 완료된다.

그 예상치를 변경하여 테스트를 빨리 실패하게 만들수 있다 : 요청이 성공하기를 기다리는 대신에, 비동기 메서드의 완료 핸들러가 호출될 때까지 기다린다.
이는 앱이 서버로 부터 응답(성공이나 오류)을 받자 마자 발생시켜 기대를 충족시킨다. 테스트에서 요청이 성공했는지 여부를 확인 할 수 있다.

어떻게 작업하는지 보려면, 새로운 테스트를 만들어야 하며, `url` 변경한 것을 취소하여 테스트를 고친다, 그리고나서 클래스에 다음 테스트 코드를 추가한다.



```swift
// Asynchronous test: faster fail
func testCallToiTunesCompletes() {
  // given
  let url = URL(string: "https://itune.apple.com/search?media=music&entity=song&term=abba")
  // 1
  let promise = expectation(description: "Completion handler invoked")
  var statusCode: Int?
  var responseError: Error?
 
  // when
  let dataTask = sessionUnderTest.dataTask(with: url!) { data, response, error in
    statusCode = (response as? HTTPURLResponse)?.statusCode
    responseError = error
    // 2
    promise.fulfill()
  }
  dataTask.resume()
  // 3
  waitForExpectations(timeout: 5, handler: nil)
 
  // then
  XCTAssertNil(responseError)
  XCTAssertEqual(statusCode, 200)
}
```

여기에서 중요한 것은 완료 핸들러가 기대를 충족시키도록 간단하게 입력하는 것이다. 그리고 약 1초가 걸린다. 요청이 실패하면 `then`에서 실패를 확인한다.

테스트를 실행한다 : 이제 실패하는데 1초가 걸리고, 테스트 실행이 타임아웃을 초과한 것이 아니고, 요청이 실패했기 때문에 실패하였다.

`url`을 수정하여, 테스트를 다시 수행하여 이제 성공하는지 확인한다.



#### 가짜 객체와 상호작용(Faking Objects and Interactions)

비동기 테스트는 코드가 비동기 API에 올바른 입력을 생성한다는 확신을 준다. 
`URLSession`에서 입력을 받거나, UserDefault나 CloudKit 데이터베이스에 올바르게 업데이트 할 수 있는지 코드가 올바르게 동작하는지 테스트 할 수 있다.

대부분의 앱들은 시스템 또는 라이브러리 객체와 상호작용을 하고, 이러한 객체와 상호 작용하는 테스트는 느리고 반복할수 없으며, FIRST 원칙 중 두가지를 위배한다. 대신에, `stub`에서 입력을 가져오거나 `mock`객체들을 업데이트하여, 상호작용을 가짜로 할 수 있다.

코드가 시스템이나 라이브러리 객체에 의존(`dependency`)될때 가짜를 사용한다. - 그 부분을 실행(play)하는 가짜 객체를 만들고, 가짜 코드를 코드에 삽입(`inject`)한다. 이를 수행하는 몇가지 방법을 [Jon Reid의 의존성 삽입(Dependency Injection)](https://www.objc.io/issues/15-testing/dependency-injection/)에서 설명한다.



![img](https://t1.daumcdn.net/cfile/tistory/210A6C4658D0800412)



#### Stub에서의 가짜 입력 (Fake Input From Stub)

테스트에서, 앱의 `updateSearchResults(_:)`메소드가 세션에 의해 다운로드된 데이터를 `searchResults.count`가 올바른지 확인하여, 올바르게 파싱하는지 확인 할수 있다. SUT는 뷰 컨트롤러이고, 미리 다운로드 된 데이터로 stub에서 사용하는 세션을 가짜로 만들 것이다.

`+`메뉴에서 `New Unit Test Target...` 선택하고 `HalfTunesFakeTests`로 이름 짓는다. `import`구문 바로 아래에 HalfTunes를 추가한다.

```swift
@testable import HalfTunes
```

SUT에 선언하며, `setup()`에 생성하고, `tearDown()`에서 해제한다.

```swift
var controllerUnderTest: SearchViewController!
 
override func setUp() {
  super.setUp()
  controllerUnderTest = UIStoryboard(name: "Main", 
      bundle: nil).instantiateInitialViewController() as! SearchViewController!
}
 
override func tearDown() {
  controllerUnderTest = nil
  super.tearDown()
}
```

> SUT는 뷰 컨트롤러이기 때문에, HalfTunes는 무거운(massive) 뷰 컨트롤러 문제가 있다. - 모든 작업은 SearchViewController.swift에서 완료된다. [네트워크 코드를 별도의 모듈로 옮기면(Moving the networking code into separate modules)](http://williamboles.me/networking-with-nsoperation-as-your-wingman/) 문제가 줄어들고 쉽게 테스트 한다.

다음으로, 테스트에서 가짜 세션에 제공할 JSON 데이터가 필요하다. 몇가지 항목만 있으면 되며, iTunes에서 다운로드 결과를 제한하려면 URL 문자열에 `&limit=3`을 추가한다.

```swift
let url = URL(string: "https://itunes.apple.com/search?media=music&entity=song&term=abba&limit=3")
```

URL을 복사하고 브라우져에 붙여넣는다. 다운로드한 파일 이름을 `1.txt`또는 비슷하게 한다. JSON 파일을 미리 확인한다. 그리고 나서 `abbaData.json`으로 이름을 변경하고, `HalfTunesFakeTests`그룹에 파일을 추가한다.

HalfTunes 프로젝트에는 `DHURLSessionMock.swift` 지원 파일이 들어있다. `DHURLSession`이라는 간단한 프로토콜을 정의하며, 메소드(stubs)를 사용하여 `URL` 또는 `URLRequest`로 데이터 타스크(task)를 생성한다. 또한, 프로토콜을 준수하는 `URLSessionMock`을 정의하며, 생성자에서 원하는 데이터, 응답 및 오류를 사용하여 `URLSession` 모형(mocks) 객체를 생성한다.

`setup()`에서 SUT를 생성하는 문장 뒤에 가짜 데이터와 응답을 설정하고, 가짜 세션 객체를 만든다.

```swift
let testBundle = Bundle(for: type(of: self))
let path = testBundle.path(forResource: "abbaData", ofType: "json")
let data = try? Data(contentsOf: URL(fileURLWithPath: path!), options: .alwaysMapped)
 
let url = URL(string: "https://itunes.apple.com/search?media=music&entity=song&term=abba")
let urlResponse = HTTPURLResponse(url: url!, statusCode: 200, httpVersion: nil, headerFields: nil)
 
let sessionMock = URLSessionMock(data: data, response: urlResponse, error: nil)
```

`setup()`의 끝부분에서, 가짜 세션을 SUT의 프로퍼티로 앱에 삽입합니다.

```swift
controllerUnderTest.defaultSession = sessionMock
```

> 테스트에서 가짜 세션을 직접 사용 할 것이지만, 나중에 테스트할때 뷰 컨트롤로의 `defaultSession` 프로퍼티를 사용하는 SUT 메소드들을 호출 할 수 있도록, 어떻게 추가하는지 보여준다.

이제 `updateSearchResults(_:)` 호출이 가짜 데이터를 파싱하는지 확인하는 테스트할 준비가 되었다. `testExample()`를 다음과 같이 변경해준다.

```swift
// Fake URLSession with DHURLSession protocol and stubs
func test_UpdateSearchResults_ParsesData() {
  // given
  let promise = expectation(description: "Status code: 200")
 
  // when
  XCTAssertEqual(controllerUnderTest?.searchResults.count, 0, "searchResults should be empty before the data task runs")
  let url = URL(string: "https://itunes.apple.com/search?media=music&entity=song&term=abba")
  let dataTask = controllerUnderTest?.defaultSession.dataTask(with: url!) {
    data, response, error in
    // if HTTP request is successful, call updateSearchResults(_:) which parses the response data into Tracks
    if let error = error {
      print(error.localizedDescription)
    } else if let httpResponse = response as? HTTPURLResponse {
      if httpResponse.statusCode == 200 {
        promise.fulfill()
        self.controllerUnderTest?.updateSearchResults(data)
      }
    }
  }
  dataTask?.resume()
  waitForExpectations(timeout: 5, handler: nil)
 
  // then
  XCTAssertEqual(controllerUnderTest?.searchResults.count, 3, "Didn't parse 3 items from fake response")
}
```

stub가 비동기적인 메소드인것처럼 해야 하기 때문에, 비동기 테스트로 작성해야 한다.

`when` 확인(assertion)부분은 `searchResults`가 데이터 작업을 실행하기 전에 비어 있다는 것을 확인한다. - `setup()`에서 완전히 새로운 SUT를 생성하였기 때문에 `true`여야 한다.

가짜 데이터는 3개의 `Track`객체들에 대한 JSON을 가지고 있으며, `then`확인(assertion)부분은 뷰 컨트롤러의 `searchResults`배열에 3개의 항목을 가지고 있다.

테스트를 실행한다. 실제 네트워크 접속하는것이 아니기 때문에, 빨리 성공해야한다.



#### 모형 객체에 가짜 업데이트 (Fake Update to Mock Object)

이전 테스트에서 가짜 객체로부터 입력을 제공하기 위해 `stub`를 사용하였다. 다음은, 코드에서 `UserDefaults`를 제대로 업데이트 하는지 테스트 하기 위해 `mock object`를 사용할 것이다.

`BullsEye`프로젝트를 다시 연다. 앱은 두개의 게임 스타일을 가지고 있다. : 사용자는 슬라이더를 이용하여 목표값과 일치시키거나 슬라이더 위치에서 목표값을 추측한다. 오른쪽 아래 구석에 있는 세그먼트(segmented) 컨트롤은 게임 스타일을 전환하고, `gameStyle` user default를 업데이트 한다.

다음 테스트는 앱이 제대로 `gameStyle` user default를 업데이트 했는지를 확인할 것이다.

테스트 네비게이터에서, `New Unit Test Target...`을 클릭하고 이름을 `BullsEyeMockTests`로 짓는다. `import`구문 아래에 다음을 추가한다.

```swift
@testable import BullsEye
 
class MockUserDefaults: UserDefaults {
  var gameStyleChanged = 0
  override func set(_ value: Int, forKey defaultName: String) {
    if defaultName == "gameStyle" {
      gameStyleChanged += 1
    }
  }
}
```

`MockUserDefaults`는 `gameStyleChanged` 플래그를 증가하기 위해 `set(_:forKey:)`메소드를 오버라이드(overrides) 한다. 종종 `Bool`변수를 설정하는 유사한 테스트를 볼수 있지만, `Int`를 증가시키는 것이 유연성을 향상 시킨다. - 예를들어, 테스트에서 메소드가 정확히 한번 호출되는지 확인 할수 있다.

SUT와 `BullsEyeMockTests`에 있는 모형(mocks) 객체를 선언한다.

```swift
var controllerUnderTest: ViewController!
var mockUserDefaults: MockUserDefaults!
```

`setup()`에서, SUT와 모형(mock) 객체를 생성하며, SUT의 프로퍼티에 모형(mock) 객체를 추가한다.

```swift
controllerUnderTest = UIStoryboard(name: "Main", bundle: nil).instantiateInitialViewController() as! ViewController!
mockUserDefaults = MockUserDefaults(suiteName: "testing")!
controllerUnderTest.defaults = mockUserDefaults
```

`tearDown()`에서 SUT와 모형(mock) 객체를 해제한다.

```swift
controllerUnderTest = nil
mockUserDefaults = nil
```

`testExample()`를 다음 처럼 변경한다.

```swift
// Mock to test interaction with UserDefaults
func testGameStyleCanBeChanged() {
  // given
  let segmentedControl = UISegmentedControl()
 
  // when
  XCTAssertEqual(mockUserDefaults.gameStyleChanged, 0, "gameStyleChanged should be 0 before sendActions")
  segmentedControl.addTarget(controllerUnderTest, 
      action: #selector(ViewController.chooseGameStyle(_:)), for: .valueChanged)
  segmentedControl.sendActions(for: .valueChanged)
 
  // then
  XCTAssertEqual(mockUserDefaults.gameStyleChanged, 1, "gameStyle user default wasn't changed")
}
```

`when`확인(assertion) 부분에서 세그먼트 컨트롤의 메소드 `taps`을 테스트 하기 전에 `gameStyleChanged`플래그가 0 이다. `then` 확인(assertion) 부분이 true이면, `set(_:forKey:)`가 정확히 한번 호출되었다는 것을 의미한다.

테스트를 실행하면, 성공할 것이다.



### XCode에서 UI 테스트 (UI Testing in XCode)

XCode7에서 UI 테스트가 소개되었으며, UI와 상호 작용을 기록하여 UI 테스트를 만들수 있다. UI 테스트는 쿼리를 사용하여 조회하여 앱의 UI 객체 찾아 작업하며, 이벤트를 동기화 한다. 그런 다음에 그 객체에 전달한다. API를 사용하면 UI 객체들의 프로퍼티와 상태를 검토하여 예상되는 상태와 비교 할 수 있다.

`BullsEye`프로젝트의 테스트 네비게이터에서, `UI Test Target`을 추가한다. `Target to be Tested`이 `BullsEye`인지 확인한다. 그리고 나서, 기본 이름 `BullsEyeUITests`를 적용한다.

```swift
var app: XCUIApplication!
```

`setup()`에서, `XCUIApplication().launch()` 구분을 다음과 같이 변경한다.

```swift
app = XCUIApplication()
app.launch()
```

`testExample()` 이름을 `testGameStyleSwitch()`로 변경한다.

`testGameStyleSwitch()`에 새 줄을 열고, 에디터 윈도우 아래에 있는 빨간색 `Record` 버튼을 클릭한다.

![img](https://t1.daumcdn.net/cfile/tistory/270EDA4258D080190C)



시뮬레이터에서 앱이 나타날때, 게임 스타일 스위치의 `Slide` 세그먼트와 상단 라벨(label)을 탭한다. 그런 다음에 XCode 레코드 버튼을 클릭하여 녹화를 중지한다.

`testGameStyleSwitch()`에 다음과 같은 3개의 라인을 가진다.

```swift
let app = XCUIApplication()
app.buttons["Slide"].tap()
app.staticTexts["Get as close as you can to: "].tap()
```

만약 다른 구문들이 있으면, 삭제한다.

첫번째 라인은 `stup()`에서 만든 프로퍼티를 복제하고 아직 아무것도 탭할 필요가 없다, 첫번째 라인과 2번째와 3번째 끝에 있는 .tap()도 삭제한다. `["Slide"]`옆에 있는 작은 메뉴를 열고 `segmentedControls.buttons["Slide"]`를 선택한다.

그래서 다음을 가진다.

```swift
app.segmentedControls.buttons["Slide"]
app.staticTexts["Get as close as you can to: "]
```

`given`섹션을 만드려면 이렇게 고쳐야 한다.

```swift
// given
let slideButton = app.segmentedControls.buttons["Slide"]
let typeButton = app.segmentedControls.buttons["Type"]
let slideLabel = app.staticTexts["Get as close as you can to: "]
let typeLabel = app.staticTexts["Guess where the slider is: "]
```

이제 두개의 버튼과 두개의 상단 라벨의 이름을 가지고 있으며, 다음을 추가한다.

```swift
// then
if slideButton.isSelected {
  XCTAssertTrue(slideLabel.exists)
  XCTAssertFalse(typeLabel.exists)
 
  typeButton.tap()
  XCTAssertTrue(typeLabel.exists)
  XCTAssertFalse(slideLabel.exists)
} else if typeButton.isSelected {
  XCTAssertTrue(typeLabel.exists)
  XCTAssertFalse(slideLabel.exists)
 
  slideButton.tap()
  XCTAssertTrue(slideLabel.exists)
  XCTAssertFalse(typeLabel.exists)
}
```

각 버튼을 선택하거나 탭 할때 올바른 라벨이 있는지 확인한다. 테스트 실행하면 모두 성공해야 한다.



### 성능 테스트 (Performance Testing)

[애플 문서](https://developer.apple.com/library/prerelease/content/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/04-writing_tests.html#//apple_ref/doc/uid/TP40014132-CH4-SW8)에서 : 성능 테스트는 평가하고자 하는 코드 블록을 사용하고 10번 실행하여, 평균 수행시간과 실행하는 표준편차를 수집한다. 각각의 측정 값의 평균은 테스트 실행 값을 형성하며, 이를 비교하여 성공 또는 실패를 평가 하는 기준이 된다.

성능 테스트를 작성하는 것은 매우 쉽다. 측정하고자 하는 코드를 `measure()` 메소드의 클로져안에 넣는다.

이 동작을 보려면, `HalfTunes`프로젝트를 다시 열고, `HalfTunesFakeTests`에서 `testPerformanceExample()`을 다음 테스트 코드로 변경한다.

```swift
// Performance 
func test_StartDownload_Performance() {
  let track = Track(name: "Waterloo", artist: "ABBA", 
      previewUrl: "http://a821.phobos.apple.com/us/r30/Music/d7/ba/ce/mzm.vsyjlsff.aac.p.m4a")
  measure {
    self.controllerUnderTest?.startDownload(track)
  }
}
```

테스트를 실행하면, `measure()` 클로져의 끝에 있는 아이콘을 클릭하여 통계를 본다.

![img](https://t1.daumcdn.net/cfile/tistory/22373E3E58D0802F15)



`Set Baseline`을 클릭하면, 성능 테스트를 다시 실행하고 결과를 본다 - 기준선 보다 좋거나 나쁠수 있다. `Edit` 버튼은 새로운 결과로 기준선을 재설정 할 수 있다.

기준선은 장치 구성별로 저장되며, 여러 다른 장치에서 동일한 테스트를 실행 할 수 있고, 각각의 특정 환경의 프로세스 속도, 메모리 등등에 따라 다른 기준선을 유지해야 한다.

테스트중인 메소드의 성능에 영향을 줄 수있는 앱을 언제든지 변경할 수 있으며, 성능 테스트를 다시 실행하여, 기준선과 차이가 나는지 본다.



### 코드 커버리지 (Code Coverage)

코드 커버리지 툴은 테스트에 의해 실제 실행된 앱 코드가 무엇인지 알려주며, 앱 코드의 어떤 부분이 아직 테스트 되지 않았는지 알려준다.

> 코드 커버리지가 활성화 되어 있는 동안 성능 테스트를 해야하나? [애플 문서](https://developer.apple.com/library/prerelease/content/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/07-code_coverage.html#//apple_ref/doc/uid/TP40014132-CH15-SW1)에서 : 코드 커버리지 데이터 수집은 성능을 저하시킨다. 이를 활성화 했을때 선형적으로 코드 실행에 영향을 미치므로 성능 결과는 테스트 실행에서 유사한 결과를 가지지만, 테스트에서 정밀한 성능평가를 할때, 코드 커버리지를 사용할지 말지 결정해야 한다.

코드 커버리지를 활성화 하려면, 스키마 편집의`Test` 액션과 `Code Coverage` 박스를 선택한다.

![img](https://t1.daumcdn.net/cfile/tistory/215CE14358D0803F18)



모든 테스트를 실행(Command-U)하고나면, 보고서 네비게이터(Command-8)를 연다. `By Time`을 선택하고 목록의 상단 항목을 선택하고 나서 `Coverage` 탭을 선택한다.

![img](https://t1.daumcdn.net/cfile/tistory/26159B4158D0804D09)



`SearchViewController.swift`에서 함수 목록을 보기 위해 펼침 삼각형을 클릭한다.

![img](https://t1.daumcdn.net/cfile/tistory/236B7C4358D0806804)



`updateSearchResults(_:)`의 파란색 커버리지 바에 마우스를 올리면, 커버리지가 71.88%인 것이 보여진다.

함수에 대한 화살표 버튼을 클릭하여 소스 파일을 연다. 그리고 나서 함수를 찾는다. 오른쪽 사이드바에 있는 커버리지 주석에 마우스를 올리면, 코드 영역이 녹색이나 빨간색으로 강조된다.

![img](https://t1.daumcdn.net/cfile/tistory/2116FB3F58D0807D18)



커버리지 주석은 각 코드 영역이 얼마나 많이 테스트되었는지 보여준다. 호출되지 않은 영역(sections)은 빨간색으로 강조 된다. 예상대로, for-loop는 3번 실행되었지만, 오류 경로는 실행되지 않았다. 이 함수의 커버리지를 늘리려면, `abbaData.json`을 복제하고, 오류가 발생하도록 편집한다. - 예를 들어, `print("Results key not found in dictionary").`를 테스트 하기 위해 `"results"`를 `"result"`로 변경한다.



#### 100% 커버리지? (100% Coverage?)

100% 코드 커버리지를 위해 얼마나 노력해야 하나? 구글은 단위 테스트를 100% 커버리지한다 그리고 100% 커버리지의 정의에 대한 논쟁과 함께 여러가지 논쟁거리를 찾을 것이다. 논쟁에서 반대자들은 마지막 10~15%가 가치가 없다고 말한다. 논쟁은 테스트가 어렵기 때문에, 마지막 10-15%가 가장 중요하다고 말한다. 구글은 [테스트가 불가능한 코드는 심각한 설계의 문제를 나타낸다(untestable code is a sign of deeper design problems)](https://www.toptal.com/qa/how-to-write-testable-code-and-why-it-matters)라는 설득력 있는 주장을 찾기 위해 단위 테스트가 어려운 나쁜 설계라고 한다. 좀더 숙고하면 [테스트 주도 개발(Test Driven Development)](http://qualitycoding.org/tdd-sample-archives/)이라는 결론을 이끌어 낼 수 있다.



### 여기서 어디로 가야 하나? (Where to Go From Here?)

이제 프로젝트에 대해 테스트 작성에 사용하는 훌륭한 툴을 가지고 있다. iOS 단위 테스트와 UI 테스트 지침서가 모든 것을 시험해 볼수 있는 확신을 주기를 희망한다.



완성된 프로젝트 [zp 파일](https://koenig-media.raywenderlich.com/uploads/2016/12/Finished-3.zip) 를 다운로드 할 수 있다.

추가적으로 공부하기 위한 몇가지 자료는 다음과 같다.

- 프로젝트에 대한 테스트를 작성했기에 다음 단계는 자동화이다 : 지속적인 통합과 지속적인 전달. 애플의 XCode 서버와 `xcodebuild`를 사용하여 [테스트 프로세스 자동화(Automating the Test Process)](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/08-automation.html#//apple_ref/doc/uid/TP40014132-CH7-SW1) 및 [ThoughtWorks](https://www.thoughtworks.com/continuous-delivery)의 전문지식을 활용한 [위키피디아의 지속적인 전달 기사(Wikipedia’s continuous delivery article)](https://en.wikipedia.org/wiki/Continuous_delivery) 부터 시작한다.
- [Swift 플레이그라운드에서의 TDD(TDD in Swift Playgrounds)](http://initwithstyle.net/2015/11/tdd-in-swift-playgrounds/)는 XCTestObservationCenter를 사용하여 플레이그라운드에서 XCTestCase 단위 테스트를 실행한다.
- [CMD+U Conference](http://www.cmduconf.com/)에서 [워치 앱: 어떻게 테스트 하는가(Watch Apps: How Do We Test Them?)](https://realm.io/news/cmduconf-boris-bugling-how-test-watch-apps/)?는 [PivotalCoreKit을](https://github.com/pivotal/PivotalCoreKit) 사용하여 watchOS앱을 테스트 하는 방법을 보여준다.
- 이미 앱은 있지만 아직 테스트를 작성하지 않은 경우에는, 테스트가 없는 코드는 레거시(legacy) 코드이기 때문에, [Working Effectively with Legacy Code by Michael Feathers](https://www.amazon.com/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052/ref=sr_1_1?s=books&ie=UTF8&qid=1481511568&sr=1-1) 참조 할수 있다.
- Jon Reid의 품질 코딩 샘플 앱 아카이브는 [테스트 주도 개발(Test Driven Development](http://qualitycoding.org/tdd-sample-archives/)에 대해 알기 좋은 곳이다.



출처: https://kka7.tistory.com/68?category=975272

원문 : https://www.raywenderlich.com/150073/ios-unit-testing-and-ui-testing-tutorial

