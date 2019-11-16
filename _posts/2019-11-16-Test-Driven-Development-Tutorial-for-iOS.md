---
layout: post
title:  " Test Driven Development Tutorial for iOS "
categories: Test
tags: TDD
author: GGuJi

---



* content
{:toc}
## iOS용 TDD 튜토리얼 : 시작하기(Test Driven Development Tutorial for iOS : Getting Started)



테스트 주도 개발(TDD: Test Driven Development)는 소프트웨어를 작성하는 유명한 방법입니다. 이 방법론은 지원하는 코드를 작성하기 전에 테스트를 작성하도록 지시(dictates)합니다. 뒤떨어져 보일수 있지만, 몇가지 좋은 장점(benefits)이 있습니다.

장점 줌 하나는 개발자가 앱의 동작을 기대하는 방법에 대한 문서를 테스트가 제공해주는 것입니다. 테스트 케이스가 코드와 함게 업데이트되기 때문에, 이 문서는 최신상태를 유지하며, 문서를 작성하거나 유지관리하지 않는 개발자에게 좋습니다.

다른 장점은 TTD를 사용해서 개발된 앱은 코드 적용범위가 개선됩니다. 테스트와 코드는 함께 사용되며, 관계없는, 테스트되지 않은 코드가 있을것 같지 않습니다.

TDD는 페어프로그래밍에 적합하며, 개발자 한명은 테스트를 작성하고, 다른 한명은 테스트를 통과하는 코드를 작성합니다. 이것은 보다 견고한 코드가 되어 개발 주기를 단축 시킬수 있습니다.

마지막으로, TDD를 사용하는 개발자는 나중에 주요 리팩토링을 할때 더 쉬워집니다. 이것은 TDD가 알려진데로 환상적인 테스트 적용범위로서 만들어집니다.

이 테스트 기반 개발 튜토리얼(tutorial)에서, 로마 숫자 변환하는 **Numero**앱을 만드는데 TDD를 사용할것입니다. 이를 따라하면, TDD 흐름에 익숙해지고 TDD를 강력하게 만드는 통찰력을 얻을 수 있습니다.





### 시작하기(Getting Started)

시작하기 위해서, 이 튜토리얼의 자료를 다운로드해서 시작합니다. [자료 다운로드(Download materials)](https://koenig-media.raywenderlich.com/uploads/2018/05/Numero.zip)
빌드하고 앱을 실행합니다. 다음과 같은 화면을 보게 될것입니다.

![img](https://t1.daumcdn.net/cfile/tistory/996A72405BACDCE915)

이 앱은 숫자와 로마 숫자를 보여줍니다. 플레이어는 로마 숫자가 정확한 숫자인지를 선택해야 합니다. 선택한 후에, 게임은 다음 숫자를 표시합니다. 게임은 10번 시도하고 끝나며, 그 시점에 플레이어는 게임을 다시 시작할 수 있습니다.

게임을 시작해봅시다. ABCD이 올바른 변환을 표현한다는 것을 빨리 깨닫게 될것입니다. 그것은 실제 변환이 아직 구현되지 않았기 때문입니다. 이 튜토리얼에서 이에대해 다룰것입니다.

Xcode에서 프로젝트를 살펴봅니다. 핵심(main) 파일들이 있습니다.

- **ViewController.swift** : 게임 플레이를 제어하고 게임을 화면에 보여줍니다.
- **GameDoneViewController.swift** : 최종 점수와 게임을 재시작하는 버튼을 보여줍니다.
- **Game.swift** : 게임 엔진을 나타냅니다.
- **Converter.swift** : 로마 숫자 변환기를 나타내는 모델입니다. 현재 비어있습니다.

대부분, `Converter`로 작업할 것이고, 변환기(converter) 테스트 클래스는 다음에 만들것입니다.

> **주의**
> [로마 숫자(Roman numeral)](https://en.wikipedia.org/wiki/Roman_numerals) 기능을 익히기에 좋은 시기 입니다.



### 첫번째 테스트와 기능 만들기(Creating Your First Test and Functionality)

전통적인 TDD 흐름도는 **red-green-refactor** 주기로 설명될수 있습니다.

![img](https://t1.daumcdn.net/cfile/tistory/998DE03C5BACDCFB0F)

구성요소 :

1. **Red** : 실패하는 테스트 작성하기
2. **Green** : 테스트를 통과하기에 충분한 코드 작성하기
3. **Refactor** : 코드를 정리하고 최적화하기
4. 모든 사용자 케이스를 처리할때까지 이전 단계들을 반복하기



#### 단위 테스트 케이스 클래스 만들기(Creating a Unit Test Case Class)

**NumeroTests** 아래에 **단위 테스트 케이스 클래스(Unit Test Case Class)** 템플릿 파일을 만들고 **ConverterTests**이름을 붙입니다.

![img](https://t1.daumcdn.net/cfile/tistory/993560415BACDD0F33)

**ConverterTests.swift**을 열고 `tempExample()`와 `testPerformanceExample()`를 지웁니다.

맨 위에 `import`구문 바로 뒤에 다음을 추가합니다.

```swift
@testable import Numero
```

단위 테스트에서 `Nemero`에 있는 클래스와 메소드에 접근할수 있습니다.
`ConverterTests`클래스의 맨 위에, 다음 프로퍼티를 추가합니다.

```swift
let converter = Converter()
```

테스트에서 사용될 새로운 `Converter` 객체를 초기화 합니다.



#### 첫번째 테스트 작성하기(Writing Your First Test)

클래스의 끝부분에, 새로운 테스트 메소드를 추가합니다.

```swift
func testConversionForOne() {
  let result = converter.convert(1)
}
```

테스트는 `convert(_:)`를 호출하고 결과를 저장합니다. 이 메소드는 아직 정의되지 않았으므로, Xcode에서 컴파일러 오류를 보게 될 것입니다.

![img](https://t1.daumcdn.net/cfile/tistory/9996873A5BACDD3022)

**Converter.swift**에서, 클래스에 다음에오는 메소드를 추가합니다.

```swift
func convert(_ number: Int) -> String {
  return ""
}
```

이것은 컴파일러 오류를 처리합니다.

> **주의**
> 컴파일러 오류가 사라지지 않은 경우에, `Numero` 가져오는(import) 줄을 주석처리하고 다시 주석해제를 합니다. 이 작업이 동작하지 않는 경우에, 메뉴에서 **Product > Builder For > Testing**을 선택합니다.

**ConverterTests.swift**에서, `testConversionForOne()`의 끝에 다음을 추가합니다.

```swift
XCTAssertEqual(result, "I", "Conversion for 1 is incorrect")
```

예상한 변환 결과를 확인하기 위해 `XCAssertEqual`을 사용합니다.

모든 테스트를 실행하기 위해 **Command-U**를 누릅니다(현재 하나밖에 없음). 시뮬레이터가 시작되지만, Xcode 테스트 결과가 더 중요합니다.

![img](https://t1.daumcdn.net/cfile/tistory/99560F415BACDD3F31)

일반적인 TDD 주기의 첫번째 단계입니다. 실패하는 테스트 작성합니다. 그 다음에, 테스트가 통과하도록 작업할 것입니다.



#### 첫번째 실패를 수정하기(Fixing Your First Failure)

**Converter.swift**로 돌아가서, `convert(_:)` 를 다음과 같이 교체합니다.

```swift
func convert(_ number: Int) -> String {
  return "I"
}
```

핵심은 테스트를 통과하기에 충분한 코드를 작성하는 것입니다. 이 경우에, 지금까지 가지고 있는 유일한 테스트에 대한 예상한 결과를 반환하고 있습니다.

**ConverterTests.swift**에 있는 테스트 메소드 이름 옆에 있는 play 버튼을 눌러서 테스트 할 수 있습니다. (테스트는 하나만 있기 때문)

![img](https://t1.daumcdn.net/cfile/tistory/992E413E5BACDD5419)

이제 테스트를 통과합니다.

![img](https://t1.daumcdn.net/cfile/tistory/99F844395BACDD620B)

실패하는 테스트로 시작하여 코드가 통과하도록 수정하는 이유는 잘못된 테스트(허위양성: false-positive)를 피하기 위함입니다. 테스트가 실패하는 것을 보지못하는 경우에, 올바른 시나리오로 테스트하고 있다는 것을 확신할 수 없습니다.

첫번째 TDD를 실행하기 위해 스스로를 격려하세요(pat yourself on the back).

![img](https://t1.daumcdn.net/cfile/tistory/992ABA3D5BACDD720A)

하지만 너무 오래 축하(celebrate) 하지는 마세요. 하나의 숫자만 처리하는 로마 숫자 변환기(Roman Numeral converter)는 소용없기 때문에, 해야할 일이 많습니다.



### 기능 확장하기(Extending the Functionality)

#### 테스트 2 작업하기(Working on Test #2)

`2`에 대한 변환을 시도해보는 것은 어떤가요? 다음 단계는 훌륭한것 같습니다.

**converterTests.swift**에서, 클래스의 끝에 다음에 오는 새로운 테스트를 추가합니다.

```swift
func testConversionForTwo() {
  let result = converter.convert(2)
  XCTAssertEqual(result, "II", "Conversion for 2 is incorrect")
}
```

이 테스트는 `2`에 대한 예상된 결과는 `II`입니다.

새로운 테스트를 실행합니다. 이 사니리오를 처리하는 코드가 추가되지 않았기 때문에 실패하는 것을 보게 될 것입니다.

![img](https://t1.daumcdn.net/cfile/tistory/990F9B355BACDD8303)

**Converter.swift**에서, `convert(_:)`를 다음에 오는 것으로 교체합니다.

```swift
func convert(_ number: Int) -> String {
  return String(repeating: "I", count: number)
}
```

이 코드는 입력에 따라 여러번 `I`를 반환합니다. 이것은 지금까지 테스트한 케이스들을 모두 포함합니다.

모든 테스트를 실행하여 변경한 것이 이상하지 않은지 확인합니다. 
그 테스트들은 모두 통과될것입니다.

![img](https://t1.daumcdn.net/cfile/tistory/9929DB415BACDD901D)



#### 테스트 3 작업하기(Working on Test #3)

이미 작성한 코드를 기반으로 테스트 `3`을 건너 뛸것입니다. `4`또한 마찬가지입니다. 나중에 처리할 특별한 케이스 이기 때문에, 최소한 지금은 건너 뛸것입니다. `5`는 어떨까요?

**ConverterTests.swift**에서, 클래스의 끝에 다음에 오는 새로운 테스트를 추가합니다.

```swift
func testConversionForFive() {
  let result = converter.convert(5)
  XCTAssertEqual(result, "V", "Conversion for 5 is incorrect")
}
```

`5`에 대한 이 테스트의 예상한 결과는 `V`입니다.

새로운 테스트를 실행합니다. 5에 대한 올바른 결과가 아니므로 실패를 보게 될 것입니다.

![img](https://t1.daumcdn.net/cfile/tistory/998B8F405BACDD9B16)

**Converter.swift**에서, `convert(_:)`를 다음에 오는 것으로 교체 합니다.

```swift
func convert(_ number: Int) -> String {
  if number == 5 {
    return "V"
  } else {
    return String(repeating: "I", count: number)
  }
}
```

테스트를 통과하기 위해서 최소한의 작업을 해야 합니다. 그 코드는 5를 확인하는 것을 분리하며, 그렇지 않으면 이전 구현으로 돌아갑니다.

모든 테스트를 실행합니다. 그것들은 모두 통과해야 합니다.

![img](https://t1.daumcdn.net/cfile/tistory/9989CF3D5BACDDA805)



#### 테스트 4 작업하기(Working on Test #4)

`6`을 테스트하는 것은 또다른 도전과제 이며, 잠시 후에 보게 될 것입니다.

**ConverterTests.swift**에서, 클래스의 끝부분에 새로운 테스트를 추가합니다.

```swift
func testConversionForSix() {
  let result = converter.convert(6)
  XCTAssertEqual(result, "VI", "Conversion for 6 is incorrect")
}
```

`6`에 대한 테스트의 예상한 결과는 `VI` 입니다.

새로운 테스트를 실행합니다. 처리되지 않은 시나리오이므로 실패를 보게 될 것입니다.

![img](https://t1.daumcdn.net/cfile/tistory/999248485BACDDB413)

**Converter.swift**에서, `convert(_:)`를 다음에 오는 것으로 교체합니다.

```swift
func convert(_ number: Int) -> String {
  var result = "" // 1
  var localNumber = number // 2
  if localNumber >= 5 { // 3
    result += "V" // 4
    localNumber = localNumber - 5 // 5
  }
  result += String(repeating: "I", count: localNumber) // 6
  return result
}
```

코드는 다음을 수행합니다.

1. 빈 출력 문자열로 초기화합니다.
2. 작업하기 위해 입력된 것을 로컬 변수로 만듭니다.
3. 입력된 것이 `5` 이상인지 확인합니다.
4. 출력에 `5`에 대한 로마 숫자를 추가합니다.
5. 입력된 로컬 변수를 `5`만큼 뺍니다.
6. 갯수(count)만큼 1에 대한 로마 숫자 변환을 반복해서 출력에 추가합니다. count는 이전에 감소된 로컬 입력변수 입니다.

이것은 지금까지 봐왔던 것을 기반으로 사용하는 합리적인 알고리즘으로 보입니다. 너무 멀리까지 생각하고 테스트 되지 않은 다른 케이스까지 처리하려는 것을 피하는게 가장 좋습니다.

모든 테스트를 실행합니다. 그것들은 모두 통과해야 합니다.

![img](https://t1.daumcdn.net/cfile/tistory/99DA304C5BACDDBF31)



#### 테스트 5 작업하기(Working on Test #5)

무엇을 언제 테스트 해야하는지를 현명하게 고르는 것이 필요합니다. `7`과 `8`을 테스트 하는 것은 새롭지 않고, `9`는 또다른 특별한 케이스 이므로, 지금은 건너 뛸수 있습니다.

![img](https://t1.daumcdn.net/cfile/tistory/9929504F5BACDDCD2B)

`10`을 가져오고, 밝혀내야 합니다.

**ConverterTests.swift**에서, 클래스의 끝 부분에 다음에 오는 새로운 테스트를 추가합니다.

```swift
func testConversionForTen() {
  let result = converter.convert(10)
  XCTAssertEqual(result, "X", "Conversion for 10 is incorrect")
}
```

`10`에 대한 테스트의 예상한 결과는 새로운 기호인 `X` 입니다.

새로운 테스트를 실행합니다. 처리되지 않은 시나리오이기에 실패를 보게 될것입니다.

![img](https://t1.daumcdn.net/cfile/tistory/99B0DB4C5BACDDDC08)

**Converter.swift**로 돌아가고 `converter(_:)`에 `localNumber`이 선언된 곳 바로 뒤에 다음에 오는 코드를 추가합니다.

```swift
if localNumber >= 10 { // 1
  result += "X" // 2
  localNumber = localNumber - 10 // 3
}
```

이것은 이전에 `5`를 처리한 것과 유사한 방법입니다. 그 코드는 다음을 수행합니다.

1. 입력이 `10`이상인지 확인합니다.
2. 출력 결과에 `10`의 로마 숫자 표현을 추가합니다.
3. `5`와 `1`을 처리하는 다음 단계로 전달하기 전에, 로컬 변수 복사본에 `10`을 감소합니다.

모든 테스트를 실행합니다. 그것들은 모두 통과해야 합니다.

![img](https://t1.daumcdn.net/cfile/tistory/991C51455BACDDEA02)



#### 패턴을 알아내기(Uncovering a Pattern)

패턴을 만드는 것처럼, `20`을 처리하는 것을 다음에 시도해보는 것이 좋습니다.

**ConverterTests.swift**에서, 클래스의 끝부분에 다음에 오는 새로운 테스트를 추가합니다.

```swift
func testConversionForTwenty() {
  let result = converter.convert(20)
  XCTAssertEqual(result, "XX", "Conversion for 20 is incorrect")
}
```

테스트에서 `20`에 대한 예상된 결과는 `10`을 두번 반복해서 로마 숫자를 표현하는 `XX`입니다.

새로운 테스트를 실행합니다. 실패하는 것을 보게 될 것입니다.

![img](https://t1.daumcdn.net/cfile/tistory/99E4BF505BACDDF623)

예상했던 것과 다르게, 실제 결과는 `XVIIIII`입니다.

조건문을 교체합니다.

```swift
if localNumber >= 10 {
```

다음과 같이 :

```swift
while localNumber >= 10 {
```

이 작은 변경은 한 번만 통과하는 대신에 `10`을 처리할때 입력을 사용하여 반복합니다. `10` 단위의 숫자에 따라 `X`를 반복해서 출력에 추가합니다.

모든 테스트를 실행합니다. 그것들은 모두 통과해야 합니다.

![img](https://t1.daumcdn.net/cfile/tistory/9914D3455BACDE0131)

작은 패턴이 드러나는 것이 보이나요? 이것은 건너뛴 특별한 케이스를 처리하기에 좋은 시간입니다.



### 특별한 케이스 처리하기(Handling the Special Cases)

**ConverterTests.swift**에서, 클래스의 끝부분에 다음에 오는 새로운 테스트를 추가합니다.

```swift
func testConversionForFour() {
  let result = converter.convert(4)
  XCTAssertEqual(result, "IV", "Conversion for 4 is incorrect")
}
```

테스트에서 `4`에 대한 예상된 결과는 `IV`입니다. 로마 숫자 체계에서 `4`는 `5`에서 `1`뺀것을 표현합니다.

![img](https://t1.daumcdn.net/cfile/tistory/99FA55465BACDE0C07)

새로운 테스트를 실행합니다. 실패를 보는 것에 너무 놀라지 마세요. 이 시나리오는 처리되지 않았습니다

![img](https://t1.daumcdn.net/cfile/tistory/9926EF4B5BACDE1739)

**Converter.swift**에서, `convert(_:)`에 반복하는 `I`를 추가하는 구문 바로 앞에 다음을 추가합니다.

```swift
if localNumber >= 4 {
  result += "IV"
  localNumber = localNumber - 4
}
```

이 코드는 `10`과 `5`가 처리된 후에 로컬 입력이 `4`보다 크거나 같은 경우를 검사합니다. 그리고나서 로컬 입력을 `4`로 감소시키기 전에 `4`에 대한 로마 숫자 표현을 추가합니다.

모든 테스트를 실행합니다. 다시한번, 모두 통과될 것입니다.

![img](https://t1.daumcdn.net/cfile/tistory/99C9B0455BACDE2320)

건너 뛴 `9`또한 마찬가지 입니다. 이제 그것을 시도해 볼 시간입니다.

**ConverterTests.swift**에서, 클래스의 끝부분에 다음에 오는 새로운 테스트를 추가합니다.

```swift
func testConversionForNine() {
  let result = converter.convert(9)
  XCTAssertEqual(result, "IX", "Conversion for 9 is incorrect")
}
```

테스트에서 `9`에 대한 예상한 결과는 `IX`입니다.

새로운 테스트를 실행합니다. `VIV` 결과는 정확하지 않습니다.

![img](https://t1.daumcdn.net/cfile/tistory/997211475BACDE2E2F)

지금까지 본 것을 바탕으로, 이것을 고칠수 있는 방법에 대한 아이디어가 있나요?

**Converter.swift**에 전환해서, `convert(_:)`에 `10`과 `5`를 처리하는 코드 아래에 다음을 추가합니다.

```swift
if localNumber >= 9 {
  result += "IX"
  localNumber = localNumber - 9
}
```

이것은 `4`를 처리하는 방법과 비슷합니다.
모든 테스트를 실행하고, 또다시 모두 통과될 것입니다.

![img](https://t1.daumcdn.net/cfile/tistory/9964534D5BACDE390E)

놓친 경우에서, 다음은 많은 사용자 케이스를 처리할때 나타나는 패턴입니다.



1. 입력이 숫자보다 크거나 같은지 확인합니다.
2. 숫자에 대한 로마 숫자 표현을 추가해서 결과물을 만들어 냅니다.
3. 입력을 숫자만큼 감소시킵니다.
4. 특정 숫자에 대한 입력을 반복을 통해 확인합니다.



TDD의 다음 단계로 넘어갈때 이 사실을 마음속에 간직하세요.



### 리팩토링(Refactoring)

중복된 코드를 인식하고 이를 정리하는 것을 리팩토링이라고 하며, TDD 주기에서 필수적인 단계입니다.

이전 섹션의 끝부분에서, 패턴은 변환 로직에서 나타났습니다. 이 패턴은 완전히 식별할 것 입니다.



#### 중복 코드 보기(Exposing the Duplicate Code)

여전히 **Converter.swift**에서, 변환 메소드를 보세요.

```swift
func convert(_ number: Int) -> String {
  var result = ""
  var localNumber = number
  while localNumber >= 10 {
    result += "X"
    localNumber = localNumber - 10
  }
  if localNumber >= 9 {
    result += "IX"
    localNumber = localNumber - 9
  }
  if localNumber >= 5 {
    result += "V"
    localNumber = localNumber - 5
  }
  if localNumber >= 4 {
    result += "IV"
    localNumber = localNumber - 4
  }
  result += String(repeating: "I", count: localNumber)
  return result
}
```

중복 코드를 강조하기 위해, `convert(_:)`를 수정하고 `if`로 된 모든 표현을 `while`로 변경합니다.

재귀(regression) 도입하지 않은 것을 확인하기 위애, 모든 테스트를 실행합니다. 여전히 통과해야 합니다.

![img](https://t1.daumcdn.net/cfile/tistory/99640D4F5BACDE4626)

TDD 방법론으로 코드 정리와 리팩토링하는 장점입니다. 기존 기능을 망가지지 않도록 안심할 수 있습니다.

![img](https://t1.daumcdn.net/cfile/tistory/99755E445BACDE520C)

중복을 완전히 드러내는(expose) 것에 더 큰 변화가 있습니다. `convert(_:)`를 수정하고 교체합니다.

```swift
result += String(repeating: "I", count: localNumber)
```

다음과 같이:

```swift
while localNumber >= 1 {
  result += "I"
  localNumber = localNumber - 1
}
```

두 가지 코드는 동일하고 `I` 문자열 표현을 반환합니다.
모든 테스트를 실행합니다. 그것들은 모두 통과해야 합니다.

![img](https://t1.daumcdn.net/cfile/tistory/99C917465BACDE5D0D)



### 코드 최적화하기(Optimizing Your Code)

`while` 구문을 교체해서 `convert(_:)` 에 있는 코드 리팩토링을 계속하기 위해 다음과 같이 `10`을 처리합니다.

```swift
let numberSymbols: [(number: Int, symbol: String)] // 1
  = [(10, "X")] // 2
    
for item in numberSymbols { // 3
  while localNumber >= item.number { // 4
    result += item.symbol
    localNumber = localNumber - item.number
  }
}
```

코드를 단계별로 살펴봅니다

1. 숫자와 해당 로마숫자 기호를 표현하는 튜플(tuples)의 배열을 만듭니다.
2. `10`에 대한 값으로 배열을 초기화 합니다.
3. 배열을 반복합니다
4. 배열에 있는 각 항목을 숫자 변환을 처리하는 패턴으로 실행합니다.

모든 테스트를 실행합니다. 계속 통과합니다.

![img](https://t1.daumcdn.net/cfile/tistory/99EE354F5BACDE6933)

이제 논리적인 결론에 대한 리팩토링이 가능합니다. `convert(_:)`를 다음과 같이 교체합니다.

```swift
func convert(_ number: Int) -> String {
  var localNumber = number
  var result = ""

  let numberSymbols: [(number: Int, symbol: String)] =
    [(10, "X"),
     (9, "IX"),
     (5, "V"),
     (4, "IV"),
     (1, "I")]
    
  for item in numberSymbols {
    while localNumber >= item.number {
      result += item.symbol
      localNumber = localNumber - item.number
    }
  }

  return result
}
```

추가적인 숫자와 심볼로 `numberSymbols`을 초기화 합니다. 그리고나서 각 숫자에 대한 이전 코드를 `10`을 처리하는데 추가된 일반적인 코드로 교체 합니다.

모든 테스트를 실행합니다. 모두 통과합니다.

![img](https://t1.daumcdn.net/cfile/tistory/99739D4F5BACDE740F)



### 다른 극한 상황의 케이스 처리하기(Handling Other Edge Cases)

변환기(converter)는 먼길을 달려왔지만, 처리할수 있는 케이스가 더 있습니다. 이제 이러한 작업을 만드는데 필요한 모든 도구가 갖추어져 있습니다(equipped).

`0`에 대한 변환으로 시작합니다. 하지만, `0(zero)`은 로마 숫자로 표현되지 않는 것을 명심하세요(keep in mind). 이것이 전달될때 예외를 던지거나 빈 문자열을 반환하도록 선택할 수 있습니다.

**ConverterTests.swift**에서, 클래스의 끝부분에 다음에 오는 새로운 테스트를 추가합니다.

```swift
func testConverstionForZero() {
  let result = converter.convert(0)
  XCTAssertEqual(result, "", "Conversion for 0 is incorrect")
}
```

이 테스트는 `0`에 대한 예상하는 결과는 빈 문자열을 예상합니다. 
새로운 테스트를 실행합니다. 이것은 작업한 방법에 따라 동작합니다.

![img](https://t1.daumcdn.net/cfile/tistory/998E024A5BACDE8001)

**Numero**에서 지원된 마지막 숫자에 대한 테스트 해봅니다 : `3999`

**converterTests.swift**에서, 클래스의 끝부분에 다음에 오는 새로운 테스트를 추가합니다.

```swift
func testConverstionFor3999() {
  let result = converter.convert(3999)
  XCTAssertEqual(result, "MMMCMXCIX", "Conversion for 3999 is incorrect")
}
```

이 테스트는 `3999`에 대한 결과가 예상됩니다.

새로운 테스르를 실행합니다. 이러한 극한 상황(edge)에 대한 케이스를 처리하는 코드가 추가되지 않았기 때문에 실패하는 것을 보게 될 것입니다.

![img](https://t1.daumcdn.net/cfile/tistory/99364C445BACDE8B28)

**converter.swift**에서, `convert(_:)` 를 수정하고 `numberSymbols` 초기화를 다음과 같이 변경합니다.

```swift
let numberSymbols: [(number: Int, symbol: String)] =
  [(1000, "M"),
   (900, "CM"),
   (500, "D"),
   (400, "CD"),
   (100, "C"),
   (90, "XC"),
   (50, "L"),
   (40, "XL"),
   (10, "X"),
   (9, "IX"),
   (5, "V"),
   (4, "IV"),
   (1, "I")]
```

이 코드는 `40`에서 `1,000` 사이의 관련된 숫자에 대한 맵핑을 추가합니다. `3,999`에 대한 테스트 또한 포함됩니다.

모든 테스트를 실행합니다. 모두 통과합니다.

![img](https://t1.daumcdn.net/cfile/tistory/99C96B4A5BACDE9611)

TDD로 완전히 만드는 경우, 테스트 대상이 아닌 `40`과 `400`에 대한 숫자 심볼 맵핑을 추가하는 것에 대해 항의했을 것입니다. 맞습니다! TDD에서, 테스트를 처음에 작성하지 않으면 코드를 추가하지 않습니다. 이것이 코드 커버리지를 유지하는 방법입니다. 자유시간에 잘못을 바로 잡는 연습은 여러분에게 맡길것입니다.

> **주의**
> 특별히 언급한 앱에 있는 알고리즘에 대한 것은 [Jim Weirich - Roman Numberals kata](https://www.youtube.com/watch?v=983zk0eqYLY) 입니다.



### 여러분의 변환기 사용(Use Your Converter)

축하합니다! 이제 완전한 기능의 로마 숫자 변환기를 가졌습니다. 게임에서 시도해 보기 위해, 몇가지를 추가로 변경해야 합니다.

**Game.swift**에서, `generateAnswers(_:number:)`를 수정하고 `correctAnswer`를 다음에 오는 할당문으로 교체합니다.

```swift
let correctAnswer = converter.convert(number)
```

이렇게 하면 하드코딩된 값 대신에 변환기(converter)를 사용하도록 전환됩니다.

빌드하고 앱을 실행합니다.

![img](https://t1.daumcdn.net/cfile/tistory/993FBB475BACDEA41C)

모든 케이스를 처리하도록 몇차례 플레이합니다.



### 다른 테스트 방법론(Other Test Methodologies)

TDD에 대해 더 자세히 보면, 다른 테스트 방법론을 듣게 될지도 모릅니다. 예를 들어,

- **승인 테스트 주도 개발(ATDD: Acceptance Test-Driven Development)** : TDD와 비슷하지만, 고객과 개발자는 공동 작업으로 승인(acceptance) 테스트를 작성합니다. 프로젝트 매니져는 고객의 예이고, 승인 테스트는 기능 테스트(functional tests)라고 합니다. 일반적으로 사용자 관점의 인터페이스 수준으로 테스트 합니다.
- **동작 주도 개발(BDD: Behavior-Driven Development)** : TDD 테스트를 포함해서 테스트를 작성하는 방법을 설명합니다. BDD는 세부 구현보다는 원하는 동작을 테스트하도록 권장합니다. 이것은 단위 테스트를 구성하는 방법에서 나타납니다. iOS 에서, **given-when-then** 포멧을 사용할 수 있습니다.이 포멧에서, 먼저 필요한 값을 설정하고, 마지막으로 결과를 확인하기 전에 테스트 코드를 실행합니다.



### 여기에서 어디로 가야하나요?(Where to Go Form Here?)

**Numero**를 완성한(rounding out) 것을 축하합니다. 튜토리얼의 마지막 프로젝트를 [다운로드](https://koenig-media.raywenderlich.com/uploads/2018/05/Numero.zip)할 수 있습니다.

이제 TDD 동작 방법에 대해 아주 잘 알고 있습니다. 더 많이 사용할수록, 더 나아질것이며, 이를 기억합니다. 여러분의 코드로 작업하는 다른 개발자는 TDD를 감사할 것입니다.

모델 클래스를 개발하는데에 TDD를 사용했지만, UI 개발에서도 사용될 수 있습니다. UI 테스트를 작성하고 TDD 방법론을 적용하는 방법에 대한 가이드에 대해 [iOS 단위 테스트와 UI 테스트 튜토리얼(iOS Unit Testing and UI Testing Tutorial)](https://www.raywenderlich.com/150073/ios-unit-testing-and-ui-testing-tutorial)을 참조하세요.

TDD의 장점은 소프트웨어 개발 방법론 이라는 것입니다. 이것은 iOS 앱을 개발하는 것 이상으로 유용합니다. 안드로이드 앱, 자바스크립트 앱, 등의 개발에서 TDD를 사용할 수 있습니다. 단위 테스트를 작성하는 것에 대한 프레임워크가 있는한 TDD를 사용할 수 있습니다!



출처: https://kka7.tistory.com/134?category=975272 

원문 : [Test Driven Development Tutorial for iOS : Getting Started](https://www.raywenderlich.com/5522-test-driven-development-tutorial-for-ios-getting-started)