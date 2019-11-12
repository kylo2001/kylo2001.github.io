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



출처: https://kka7.tistory.com/68?category=975272

원문 : https://www.raywenderlich.com/150073/ios-unit-testing-and-ui-testing-tutorial



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

`BullsEye`프로젝트를 열고 `Command-5`를 눌러서 테스트 네비게이터를 열어준다.

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

> `XCTestAssertions`의 전체 목록을 보기위해, 코드에서 `XCTAssertEquale`을 `Commnad-clicke`하면 XCTestAssertions.h 또는 [Apple’s Assertions Listed by Category](https://developer.apple.com/library/prerelease/content/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/04-writing_tests.html#//apple_ref/doc/uid/TP40014132-CH4-SW35)로 이동한다.

![img](https://t1.daumcdn.net/cfile/tistory/220A554458D07FC70D)



> `Given-When-Then`테스트의 구조는 클라이언트 친화적인 행동 주도적인 개발(BDD: Behavior Driven Development)에서 시작되었으며, 은어(low-jargon)의 명칭이다. 대체할 시스템 이름은 `Arrange-Act-Assert`와 `Assemble-Activate-Assert`이다.



#### 디버깅 테스트



