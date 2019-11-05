---
layout: post
title:  "Architecture 비교"
categories: Architecture
tags: MVC MVP MVVM VIPER
author: GGuJi

---



[TOC]

## Architecture 비교

[출처: tucan9389's blog](https://blog.canapio.com/43)



iOS에서 MVC를 사용한다는게 다소 이상하게 느껴질 수 있다. 당신은 MVVM으로 바꾸려고 생각해본 적이 있는가? VIPER을 적용시켜볼 생각을 한 적은 있으나, 그게 의미있는 것인지 확신이 들지 않는가?



이 글을 읽어 내려가면 위 것들에 대한 답을 찾을 수 있을 것이다.



당신은 iOS 환경에서 아키텍처 패턴에 대한 지식을 잘 정리하고 싶을 것이다. 우리는 유명한 것들을 골라 한번 보고, 이론과 비교한 뒤, 몇 작은 예제들과 함께 연습해 볼 것이다. 아래 링크는 당신이 특별히 더 관심있는 것을 연습할 수 있다.

[슬라이드 자료](http://slides.com/borlov/arch/fullscreen#/2)



> 디자인 패턴을 마스터하는 것은 중독될 수 있으므로 조심해야한다: 전보다 더 많은 질문들이 생겨날 것이기 때문에.



- 누가 네트워크 리퀘스트를 소유하여야하나: Model이냐 Controller냐?
- 새로운 View의 ViewModel에 어떻게 Model을 넘겨주나?
- 누가 새로 생긴 VIPER 모듈을 생성해야하나: Router냐 Presenter냐?

![img](https://t1.daumcdn.net/cfile/tistory/2432834556C180121E)



## 왜 아키텍처를 고르는데 신중해야하는가?



당신이 만약 개발을 하다가 디버깅을 해야하는데 엄청난 양의 클래스와 엄청난 양의 다른 것을 비교해야 하며, 이게 아키텍처가 없는 상황이라면, 당신 클래스의 어떠한 버그를 찾지도 고치지도 못하는 상황을 맞이하게 될 것이다. 우리는 클래스의 모든 속성을 머릿속에 담아두고 있을 순 없다. 만약 그짓을 하다보면 중요한 세부적인 요소를 놓힐 수가 있다. 만약 개발하면서 이런 경험을 이미 해보았다면 아래와 같은 것을 겪어봤을 것이다.



- 그 클래스가 UIViewController의 자식클래스이다.
- 당신의 데이터들이 UIViewController에서 바로 저장된다.
- UIView들이 거의 아무 일도 하지않는다.
- Model이 빈 데이터 구조이다.
- 유닛 테스트로 아무것도 하지 않는다.



그래도 애플의 가이드라인이나 [애플의 MVC](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html)를 따랐다해도 이러한 상황은 생길 수 있으니 너무 낙담하지는 마라. 애플의 MVC가 뭔가 잘못되었고, 우리는 그걸 바로잡을 것이다.



## 좋은 아키텍처의 특징

- 엄격한 룰에 따라 개체들간의 책임 분리**(Distribution)**를 균형있게 해야한다.

- 첫번째 말한 특징으로부터 나올 수 있는 테스트들이 가능**(Testability)**해야한다.

   (걱정마라: 적절한 아키텍처를 고른다면 어렵지 않을것이다.)

- **사용하기** 편해야**(Ease of use)**하고 유지보수하기 쉬워야한다.

  

### 왜 책임분리를 해야 하는가?

분배는 우리가 이게 어떻게 동작하는지 알아낼려고 노력하는 동안 우리의 뇌에서 균등하게 생각하도록 해준다. 만약 당신이 천재라 생각되면 그냥 하던대로 해라. 그러나 이 능력은 선형적으로 커지지 않을 뿐더러 굉장히 빨리 한계에 도달해버린다. 그러므로 가장 빨리 복잡한 것을 극복하는 방법은 [단일 책임 원칙](https://en.wikipedia.org/wiki/Single_responsibility_principle)으로 수많은 개체들의 책임을 쪼개는 것이다.



### 왜 테스트 가능해야 하는가?

이미 유닛테스트에 대한 중요성을 알고 있는 사람에게 던지는 질문이 아니라, 새 기능을 추가한 뒤 일때나, 클래스의 몇몇 복잡성을 리팩토링을 하기 위해서 테스트에 실패하는 사람들이 하는 의문이기도 하다. 이것은 테스트가 런타임 내에서의 이슈를 찾는데 도와주며, 반대로 실유저에게 이슈가 발생한다면 그걸 고친 앱을 다시 실유저가 다시 사용하기까지 [일주일씩이나](http://appreviewtimes.com/) 걸린다.



### 왜 사용하기 쉬워야 하는가?

가장 좋은 코드가 뭔지는 한번 언급해 볼 가치가 있다. 하나도 작성하지 않은 코드이다. 따라서 적은 양의 코드는 버그가 적다. 게으른 개발자 말을 빌려 **적은 코드**를 작성 하기를 갈망하며 이것은 코드를 설명해야하면 안된다. 또한 당신이 눈을 감고 허우적대며 **유지보수**하는 솔루션을 원치도 않을 것이다.



## MV - 필수적이지 않다

요즘은 아키텍처 설계를 할때 수많은 선택지가 있다:

- [MVC](https://en.wikipedia.org/wiki/Model–view–controller)
- [MVP](https://en.wikipedia.org/wiki/Model–view–presenter)
- [MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel)
- [VIPER](https://www.objc.io/issues/13-architecture/viper/)

위에서 세개(MVC, MVP, MVVM)는 아래 3개 카테고리중 하나는 들어가있다:

- **Models** — 데이터나 [데이터 접근 레이어](https://en.wikipedia.org/wiki/Data_access_layer)(Person 클래스나 PersonDataProvider 클래스와 같이 데이터를 다루고있는) 소유를 책임지는 부분
- **Views** — 레이어에 표현되있는 것을 책임지는 부분(GUI), iOS 환경에서는 'UI'가 접두로 붙는다(역자주: UILabel, UIView 등등..).
- **Controller/Presenter/ViewModel** — **Model**과 **View**를 붙여준다. 보통 유저가 View에서 어떤 액션을 취할때 Model을 변경하거나 Model이 변경되었을 때, View를 갱신하는 책임을 가지는 부분



개체들을 나눌때 이점:

- 이전보다 더 잘 이해할 수 있다(이미 알고 있다 하더라도).
- 재사용 가능하다(대부분 **View**와 **Model**에 적용 가능하다).
- 독립적으로 테스트 가능하다.

자 어서 *MV(X)* 패턴을 시작하고 나중에는 *VIPER*까지 해보도록 하자*.*



## MVC

**이전에는** **어떻게** **사용해왔을까?**

애플의 MVC를 살펴보기 전에 [전통적인 MVC](https://en.wikipedia.org/wiki/Model–view–controller)는 어떻게 사용되었는지 보자.

![Traditional MVC](https://t1.daumcdn.net/cfile/tistory/2752874556C1801403)*Traditional MVC*

이 경우는 **View**의 범위가 정확하지 않다. **Model**이 바뀌고나서 **Controller**에 의해 한번 랜더링(rendering) 된다. 웹페이지에서 다른 페이지로 갈 수 있는 링크를 누른 후, 다시 로딩되는 것을 생각해봐라. iOS 앱에서 전통적인 MVC를 구현하는것은 가능할지라도 구조적인 문제 때문에 효과적으로 처리할 수 없으며 당신 앱이 그러기도 원치 않는다. 이 모든 개체가 둘씩 꽉 묶여있고, 각 개체는 다른 두개에 대해 알고있다. 이것은 각기 그들이 재사용성을 심각하게 줄여버린다. 이러한 이유로 우리는 흔히 쓰는 MVC로 작성하는 것 또한 스킵 하겠다.

> 전통적인 MVC는 최신 iOS 개발에 적합해 보이지 않는다



## Apple’s MVC



바라는 목표(기대한 애플의 MVC

![Cocoa MVC](https://t1.daumcdn.net/cfile/tistory/242D464556C1801224)*Cocoa MVC*

원래 **Controller**는 **Model**과 **View**를 연결시켜주는 역할을 하므로 서로에 대해 알필요가 없다. 그중에 가장 재사용 불가능한 것이 **Controller**이며, 우리도 그걸 알고있다. 따라서 우리는 모든 특이한 로직을 **Model**이 아닌 **Controller**에 넣어야한다.

이론적으로는 굉장이 전략적으로 보이지만 뭔가 문제가 있다. 당신은 MVC를 **뷰 컨트롤러 덩어리(Massive View Controller)**라 불리는걸 들은적이 있을지도 모른다. 더 나아가 [View Controller offloading](https://www.objc.io/issues/1-view-controllers/lighter-view-controllers/)는 iOS 개발자들에게 중요한 토픽이다. 왜 애플은 전통적인 MVC를 조금 개선하여 사용하여서 이런 일이 일어나버린건가?



실제 애플의 MVC

![Realistic Cocoa MVC](https://t1.daumcdn.net/cfile/tistory/27298D4556C1801326)*Realistic Cocoa MVC*

Cocoa MVC는 **View Controller** 덩어리를 작성하도록 만들어버린다. 그 이유는 **View**들의 라이프 사이클 안에서 뒤엉키는데 그것들을 분리해내기가 어렵기 때문이라고 말한다. 너가 **Model**에 비지니스 로직이나 데이터 변환같은 것을 없애는 능력을 가졌을 지라도 대부분의 **View**에서 반응하면 액션을 **Controller**로 보내게 될 것이다. View Controller는 결국 모든 것의 델리게이트(delegate)나 데이터소스(data source)가 될테고, 종종 네트워크 요청과같은 처리도 하고 있을지 모른다. 

이런 종류의 코드를 얼마나 많이 보았는가:

```swift
var userCell = tableView.dequeueReusableCellWithIdentifier("identifier") as UserCell
userCell.configureWithUser(user)
```

**Model**과 함께 직접적으로 구현된 **View**인 cell은 MVC 가이드라인을 위반한다. 그러나 항상 그렇게 사용하며 사람들은 이게 문제가 아니라고 느낄때가 많다. 좀더 MVC를 따르고자 한다면 cell을 **Controller**에서 구성하고 **View** 안에 **Model**을 거치지 않아야 햔다. 그러나 그렇게 해버리면 **Controller**가 더 커져버리게 될 것이다.

> Cocoa MVC는 View Controller 덩어리의 이유이기도하다.

문제는 [유닛 테스트](http://nshipster.com/unit-testing/)(여러분 프로젝트에 있기를 바란다)에까지 나타날 거라는걸 확신할 수 없다. 당신의 **View** **Controller**는 **View**에 딱 붙어있고, 이렇게하면 그들의 **View** 라이프 사이클이나 테스트를 위한 **View**를 만들기가 어려워지기 때문에 테스트가 힘들어진다. 반면 **View Controller**에 코드를 작성하고 있으면 당신 비지니스 로직은 가능한 **View** 레이아웃 코드로부터 분리될 것이다.



간단한 예제를 보자:

```swift
import UIKit

// Model
struct Person { 
    let firstName: String
    let lastName: String
}

// View + Controller
class GreetingViewController : UIViewController { 
    var person: Person!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        let greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
        self.greetingLabel.text = greeting
        
    }
    // layout code goes here
}
// Assembling of MVC
let model = Person(firstName: "David", lastName: "Blaine")
let view = GreetingViewController()
view.person = model;
```



> MVC로 분리하면 현재 View Controller안에서 동작되게 할 수 있다.



테스트하기 좋아보이지는 않다. 우리는 greeting 생성을 새 GreetingModel 클래스에 옮겨 넣을 수 있다. 그러나 GreetingViewController안에서 UIView와 연관되어있는 메소드(viewDidLoad, didTapButton)를 호출하지 않은체 상연 로직(예제에는 로직이 많이 없지만) 테스트를 할 수가 없다.

사실, 로딩테스트는 디바이스를 바꿔가며(iPhone4S, iPad 등등으로) 확인해보는 것에 대한 이점이 없다. 그래서 Unit Test target configuration에서 “Host Application”을 지우고 시뮬레이터 없이 테스트 해보는것을 추천한다.

> View와 Controller 사이의 상호작용은 [Unit Test로써 테스트하기에 좋지 않다](http://ashfurrow.com/blog/whats-worth-unit-testing-in-objective-c/).



위에서 말한건, Cocoa [MVC](https://en.wikipedia.org/wiki/Model–view–presenter)를 사용하는것은 별로 좋지 않은 선택인것 같아 보인다는 것이다. 그러나 이 글의 서두에 언급했단 **특징**들의 용어를 정의했었다.

- **Distribution**—사실 뷰와 모델은 분리되어 있지만, **View**와 **Controller**는 딱 붙어있다.
- **Testability**—거지같은(?)분리 때문에 아마 **Model**만 테스트 가능할 것이다.
- **Ease of use**—다른 패턴에 비해 코드가 적게 든다. 추가로 많은 사람들이 친숙하게 사용하기도하며 경험해보지 못했던 개발자도 쉽게 접근할 수 있다.



Cocoa MVC는 아키텍처 쪽에 시간을 투자할 시간이 별로 없을때 선택하는 패턴이며, 작은 프로젝트에는 좀 지나친 유지보수 비용이 들어간다는 것을 느낄 수 있을 것이다.

> Cocoa MVC는 개발 속도면에서는 최고의 아키텍처 패턴이다.
>



## MVP



전달될거라 약속한 Cocoa MVC(Cocoa MVC’s promises delivered)

![Passive View variant of MVP](https://t1.daumcdn.net/cfile/tistory/2550864556C1801305)*Passive View variant of MVP*

위 사진이 애플의 MVC와 굉장히 비슷하지 않는가? 이것의 이름은 [MVP](https://en.wikipedia.org/wiki/Model–view–presenter)(Passive View Variant)이다. 흠… 그럼 애플의 MVC가 MVP와 같다는 걸까? 그렇지 않다. MVC에서는 **View**가 **Controller**와 서로 딱 붙어있지만 MVP에서 중간다리 역할을 하는 **Presenter**는 View Controller의 라이프 사이클에 아무런 영향을 끼치지도 않으며, **View**가 쉽게 테스트가능한 복사본(**moked**)을 만들 수 있다. 그러므로 **Presenter**에는 레이아웃 관련 코드가 없고 오직 **View**의 데이터와 상태를 갱신하는 역할만 가진다.

![img](https://t1.daumcdn.net/cfile/tistory/2242624556C1801210)



> 만약 UIViewController가 View라고 말했으면 어떨까..?



사실 **MVP** 입장에서는, UIViewController의 자식클래스에 **Presenter**가 아닌 **View**들이 있다. 이러한 구분은 좋은 테스트 용이함을 제공하지만, 수작업의 데이터나 이벤트 바인딩을 따로 만들어야하기 때문에 개발 속도에 대한 비용도 따라 온다. 아래 예제에서 확인할 수 있다:

```swift
import UIKit

// Model
struct Person { 
    let firstName: String
    let lastName: String
}

protocol GreetingView: class {
    func setGreeting(greeting: String)
}

protocol GreetingViewPresenter {
    init(view: GreetingView, person: Person)
    func showGreeting()
}

class GreetingPresenter : GreetingViewPresenter {
    unowned let view: GreetingView
    let person: Person
    required init(view: GreetingView, person: Person) {
        self.view = view
        self.person = person
    }
    func showGreeting() {
        let greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
        self.view.setGreeting(greeting)
    }
}

class GreetingViewController : UIViewController, GreetingView {
    var presenter: GreetingViewPresenter!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        self.presenter.showGreeting()
    }
    
    func setGreeting(greeting: String) {
        self.greetingLabel.text = greeting
    }
    
    // layout code goes here
}
// Assembling of MVP
let model = Person(firstName: "David", lastName: "Blaine")
let view = GreetingViewController()
let presenter = GreetingPresenter(view: view, person: model)
view.presenter = presenter
```



### Important note regarding assembly

MVP는 실제로 세 개의 개별 레이어가 있기 때문에 발생하는 어셈블리 문제를 나타내는 첫 번째 패턴이다. 그러므로 **View**가 **Model**에 대해 알기를 원치 않기 때문에, 현재 View Controller(**View**일 것이다)를 모아서 동작시키는건 옳지 않으므로 다른곳에서 동작시켜야한다. 예를들어, 우리는 앱에서 범용적인 모아서 수행하거나 **View-to-View**를 보여주기위한 **Router**를 돌릴 수 있다. 이 이슈는 MVP 뿐만아니라 **아래** **모든** **패턴들에게도** **나타나는** **문제**이기도하다.



### MVP의 특징

- **Distribution** —**Presenter**와 **Model**의 책임을 거의 분리했고 **View**는  빈껍데기가 된 셈이다(위 예제에서는 **Model**도 빈껍데기 같았지만..)
- **Testability** —최고로 좋다. **View**의 재사용가능 덕분에 대부분의 비지니스 로직을 테스트 할 수 있다.
- **Easy of use**—위에서 본 비현실적인 예제에서는 MVC에 비해 코드의 양이 2배정도 더 많이 들지만 MVP의 아이디어는 굉장히 명료하다.



> iOS에서 MVP는 테스트하기엔 좋지만 코드가 길어진다.



## MVP - With Bindings and Hooters



MVP의 다른 버전(MVP Supervising Controller)이 있다. 이러한 다양한 MVP들은 Presenter(Supervising Controller)가 View로부터 액션을 처리하고 View를 적합하게 변경하는 동안 View와 Model의 직접 바인딩을 포함한다.

![Supervising Presenter variant of the MVP](https://t1.daumcdn.net/cfile/tistory/2629F34556C1801328)*Supervising Presenter variant of the MVP*

그러나 우리가 이미 이전에 배웠듯, 막연하게 책임을 나누는건 좋지 않은데다, **View**와 **Model을** 합쳐버린다. 이것은 Cocoa 데스크탑 개발에서 어떻게 동작하는지와 비슷하다.

전통적인 MVC와 같이, 결함이 있는 아키텍쳐의 예제를 찾기 힘들었다.



## MVVM

마지막이자 MV(X) 종류의 최고 종류

[MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel)은 최근에 나온 MV(X) 종류이다. 그러므로 이전의 MV(X) 문제들을 해결하여 나오기를 기대해보자.



이론적으로는 Model-View-ViewModel이 굉장히 좋아보인다. **View**와 **Model**은 이미 우리에게 친숙할테고, **View Model** 이라불리는 중계자 또한 마찬가지일 것이다.

![MVVM](https://t1.daumcdn.net/cfile/tistory/25507E4556C1801305)*MVVM*

MVP와 꽤 비슷하다:



### MVVM의 특징

- MVVM은 **View Controller**를 **View**라고 일컫는다.
- **View**와 **Model**이 서로 연결 되어있지 않다.

* MVP의 Supervising버전에서 처럼 **binding**이 있다 

  그러나 여기서는 **View**와 **Model**의 관계가 아닌 **View**와 **View Model** 사이의 관계이다.



그래서 실제 iOS에서 **View Model**이 뭘 의미할까? 그것은 기본적으로 UIKit인데 그로부터 View의 독립된 표현이거나 상태이다. **View Model**은 **Model**에서 변경을 호출하고 **Model** 자체를 갱신한다. 따라서 **View**나 **View Model** 사이에서 바인딩을 하며, 적절히 처음것이 갱신된다.



### Bindings

MVP 파트에서 간당하게 언급한 적이 있다. 그러나 여기서 좀 더 이야기 해보자. 바인딩은 OS X 개발을 위한 박스(역자주: 프레임워크나 툴을 말하는듯 합니다)에서 나왔으나 iOS 툴박스에서는 보지 못한다. 물론 KVO나 notification을 가지고 있긴 하지만 그것이 바인딩만큼 편리하지는 않다.

그러므로 직접 작성하고 싶지 않다면 두 가지 옵션이 있습니다



1. 바인딩 기반 라이브러리인 KVO에는 [RZDataBinding](https://github.com/Raizlabs/RZDataBinding) 혹은 [SwiftBond](https://github.com/SwiftBond/Bond) 이런게 있다.

2. The full scale [functional reactive programming](https://gist.github.com/JaviLorbada/4a7bd6129275ebefd5a6) beasts like [ReactiveCocoa](https://www.google.co.uk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0CB4QFjAAahUKEwj2l6rZv5jJAhUFUhQKHWahCKs&url=https%3A%2F%2Fgithub.com%2FReactiveCocoa%2FReactiveCocoa&usg=AFQjCNHM-pOkluiSuPsaVwVujCDTknVFUA&sig2=54zu-ATo8vDMvtXbxZYTvQ), [RxSwift](https://github.com/ReactiveX/RxSwift/) or [PromiseKit](https://github.com/mxcl/PromiseKit).

   

사실 요즘엔 MVVM을 들으면 바로 ReactiveCocoa를 말하기도 하며, 반대도 그렇다(역자주: 뭐라고??????). 비록 간단한 바인딩으로 MVVM을 만드는게 가능하기는 하나 ReactiveCocoa (혹은 siblings)으로는 최고의 MVVM을 만들수 있게 해준다.



사실, 요즘엔 “MVVM”이 들리면 "ReactiveCocoa"를 생각하고 그 반대도 마찬가지이다. 간단한 바인딩으로 MVVM을 구축 할 수 있지만 ReactiveCocoa (또는 형제)를 통해 대부분의 MVVM을 얻을 수 있다.



**Reactive 프레임워크에는 쓰디쓴 진실이 하나 있다: 큰 책임엔 큰 에너지가 필요하다.** Reactive를 사용하게되면 굉장히 혼잡해지기 쉬워진다. 다른말로 설명하자면, 문제가 하나 생기면 앱을 디버깅하는데 시간이 굉장히 많이 걸리며, 아래와 같은 콜 스택을 보게 될것이다.

![Reactive Debugging](https://t1.daumcdn.net/cfile/tistory/217A483F56C1828236)

Reactive Debugging



우리의 예제에서는 FRF 프레임워크나 KVO까지도 배보다 배꼽이 더 큰 식이다. 그 대신에 *showGreeting* 메소드를 이용하여 갱신하기 위한 View Model을 명백하게 물어 볼 것이고 *greetingDidChange* 콜백 함수를 위해 작은 프로퍼티를 사용할것이다.



```swift
import UIKit

// Model
struct Person { 
    let firstName: String
    let lastName: String
}


protocol GreetingViewModelProtocol: class {
    var greeting: String? { get }
    // function to call when greeting did change
    var greetingDidChange: ((GreetingViewModelProtocol) -> ())? { get set } 
    init(person: Person)
    func showGreeting()
}


class GreetingViewModel : GreetingViewModelProtocol {
    let person: Person
    var greeting: String? {
        didSet {
            self.greetingDidChange?(self)
        }
    }
    var greetingDidChange: ((GreetingViewModelProtocol) -> ())?
    required init(person: Person) {
        self.person = person
    }
    func showGreeting() {
        self.greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
    }
}

class GreetingViewController : UIViewController {
    var viewModel: GreetingViewModelProtocol! {
        didSet {
            self.viewModel.greetingDidChange = { [unowned self] viewModel in
                self.greetingLabel.text = viewModel.greeting
            }
        }
    }
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self.viewModel, action: "showGreeting", forControlEvents: .TouchUpInside)
    }
    // layout code goes here
}
// Assembling of MVVM
let model = Person(firstName: "David", lastName: "Blaine")
let viewModel = GreetingViewModel(person: model)
let view = GreetingViewController()
view.viewModel = viewModel
```

이제 돌아와서 특징들을 나열해보겠다:

- **Distribution**—우리의 작은 예제에서는 명료하게 나타나지 않았지만, 사실 MVVM의 **View**는 MVP의 **View**보다 더 책임이 많다. 왜냐면 두번째 것이 **Presenter**로 포워드(forward)하고 그 자신를 갱신 하지는 않은 그 때, 바인딩을 세팅함으로써 **View Model**에서 처음 것의 상태를 갱신한다.
- **Testability**—**View Model**은 **View**에 대해 전혀 모르며, 이것이 테스트하기 쉽게 해준다. **View** 또한 테스트 가능하지만 UIKit 의존이면 그러고 싶지 않게 원하게 될 것이다.
- **Easy of use**—우리 예제에서는 MVP와 비슷한 양의 코드가 나왔으나 **View**에서 **Presenter**로 모든 이벤트를 포워드하고 **View**를 갱신하는 실제 앱에선 바인딩을 사용했다면 MVVM의 코드 양이 더 적을 것 이다.



> MVVM은 앞에서 말한 장점들을 합쳐놓은것 같아서 굉장히 매력적이다. 그리고 View입장에서 바인딩을 하기 때문에 View를 갱신하는데 추가적인 코드를 필요로 하지도 않는다. 그럼에도 불구하고 테스트에도 굉장히 좋은 수준이다 (역자주: 완전 극찬이군요)



## VIPER

iOS앱 설계에 레고 조립 경험을 적용하다

[VIPER](https://www.objc.io/issues/13-architecture/viper/)는 마지막 지원자다. 이것이 특별히 흥미로운 이유는 MV(X) 카테고리로 부터 나온 녀석이 아니기 때문이다.



이제부터 당신은 책임의 단위가 매우 좋다고 인정하게 될 것이다. VIPER는 분리된 책임이라는 아이디어에서 생겨난 다른 iteration을 만드며, 이번 시간에는 **다섯** 레이어를 볼 것이다.

![VIPER](https://t1.daumcdn.net/cfile/tistory/2642A24556C1801712)*VIPER*

- **Interactor**—엔터티의 새 인스턴스를 만들거나 서버에서 가져 오는 것과 같이 데이터 (엔티티) 또는 네트워킹과 관련된 비즈니스 논리를 포함한다. 이를 위해 VIPER 모듈의 일부로 간주되지 않고 외부 종속성으로 간주되는 일부 Services 및 Managers를 사용한다.
- **Presenter**—Interactor에서 발생되고 비지니스 로직과 관련있는 (그러나 UIKit과는 관련없는) UI를 가진다.
- **Entities**—일반적인 데이터 객체이다. (데이터 접근 레이어(*data access layer*)는 Interactor의 책임이기 때문에 **Entities**는 아니다.)
- **Router**—VIPER 모듈 사이의 연결고리(seques) 책임을 가진다.

기본적으로 VIPER 모듈은 한 스크린(*screen*)이나 당신 어플리케이션의 모든 **사용자 스토리(user story)**가 될 수 있다.

인증(Authentication)을 생각해보면 한 스크린이나 여러개가 하나에 연관되어 있을 수 있다. 얼마나 작은 “LEGO” 블럭이 있어야 할까?—전적으로 당신에게 달려있다.



MV(X) 종류와 비교하면, 우리는 책임의 분리가 좀 다르다는걸 확인할 수 있다:

- **Model**(data interation) 로직은 빈 데이터 구조로써 **Entities**와 함께 **Interactor**에 이동된다.
- 오직 **Controller**/**Presenter**/**ViewModel**이 **Presenter**로 이동하는 UI 표시 책임을 갖지만, 데이터를 변경할 능력은 없다.
- **VIPER**는 명시적으로 **Router**에 의해 결정된 네비게이션 책임을 해결한 첫 패턴이다. 



> iOS 어플리케이션 입장에서는 라우팅 하는게 도전이라고 할 수 있다. MV(X) 패턴들은 이러한 이슈가 발생하지 않는다.

이 토픽이 MV(X) 패턴을 다 반영하지 못했으므로, 예제 또한 라우팅이나 모듈간의 interaction을 반영하지 않았다. 

```swift
import UIKit

// Entity (usually more complex e.g. NSManagedObject)
struct Person { 
    let firstName: String
    let lastName: String
}

// Transport data structure (not Entity)
struct GreetingData { 
    let greeting: String
    let subject: String
}

protocol GreetingProvider {
    func provideGreetingData()
}

protocol GreetingOutput: class {
    func receiveGreetingData(greetingData: GreetingData)
}

class GreetingInteractor : GreetingProvider {
    weak var output: GreetingOutput!
    
    func provideGreetingData() {
        let person = Person(firstName: "David", lastName: "Blaine") // usually comes from data access layer
        let subject = person.firstName + " " + person.lastName
        let greeting = GreetingData(greeting: "Hello", subject: subject)
        self.output.receiveGreetingData(greeting)
    }
}

protocol GreetingViewEventHandler {
    func didTapShowGreetingButton()
}

protocol GreetingView: class {
    func setGreeting(greeting: String)
}

class GreetingPresenter : GreetingOutput, GreetingViewEventHandler {
    weak var view: GreetingView!
    var greetingProvider: GreetingProvider!
    
    func didTapShowGreetingButton() {
        self.greetingProvider.provideGreetingData()
    }
    
    func receiveGreetingData(greetingData: GreetingData) {
        let greeting = greetingData.greeting + " " + greetingData.subject
        self.view.setGreeting(greeting)
    }
}

class GreetingViewController : UIViewController, GreetingView {
    var eventHandler: GreetingViewEventHandler!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()


    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        self.eventHandler.didTapShowGreetingButton()
    }
    
    func setGreeting(greeting: String) {
        self.greetingLabel.text = greeting
    }
    
    // layout code goes here
}
// Assembling of VIPER module, without Router
let view = GreetingViewController()
let presenter = GreetingPresenter()
let interactor = GreetingInteractor()
view.eventHandler = presenter
presenter.view = view
presenter.greetingProvider = interactor
interactor.output = presenter
```

이제 다시 돌아와 특징들을 살펴보자:

- **Distribution** —틀림없이 VIPER는 책임 분배의 최고봉이다.
- **Testability** —분리가 잘되있는만큼 테스트에도 좋다.
- **Easy of use** —마지막으로 여러분이 이미 추측한것처럼 두배 정도의 유지보수 비용이 들것이다. 매우 작은 책임을 위해 수많은 클래스 인터페이스를 작성해야하는 점이다.



### 그래서 래고는 무엇이었나?

VIPER를 사용하는 동안 레고로 엠파이어 스테이트 빌딩(위키:엠파이어 스테이트 빌딩은 1931년부터 1972년까지 세계 최고층 건물이었다.)을 쌓는 기분이 들것이다, 이것이 유일한 [문제](https://inessential.com/2014/03/16/smaller_please)이기도 하다. 아마 당신 앱에 VIPER를 적용시키기에 좀 이를수도 있고 좀더 간편한것으로 고려해도 좋다. 몇몇 사람들은 이걸 아예 무시하는 경우도 있다. 지금은 비록 엄청나게 높은 유지보수 비용이 들지만, 그들이 미래에는 그들의 앱에 VIPER가 필요할지도 모른다는걸 알고있을거라 생각한다. 만일 당신도 생각이 같다면 [Generamba](https://github.com/rambler-ios/Generamba)(VIPER 골격을 제공해주는 툴)를 한번 사용해보길 바란다. 개인적으로는 이건 새총 대신에 자동 대포 조준 시스템을 사용하는 느낌이긴하다.



## 결론

우리는 몇몇 다른 아키텍처 패턴을 살펴보았고, 무엇이 당신을 괴롭히는지 찾아냈기를 바란다. 그러나 **여기에** **완벽한** **해답은** **없고** 아키텍처를 선택하는게 당신의 특별한 상황에서 문제의 비중을 등가교환하게 된다는걸 알게되었음을 의심하지 않는다. 

그러므로 한 앱에 다른 아키텍처를 섞어 사용하는것은 자연스러운 일이다. 예를들어 MVC로 시작했지만 어떤 한 화면에서만 MVC로 관리하기 어려워지는 상황이 생기면 그 부분만 MVVM으로 바꿀 수 있다. 이런 아키텍처들은 서로 잘 공존할 수 있기 때문에, 다른 화면이 MVC 골격으로 잘 동작하면 바꿀 필요가 없다. 



> *Make everything as simple as possible, but not simpler 
> 이론은 가능한 간단해야하지만, 지나치게 간단해서는 안된다
> — Albert Einstein*



[참고: iOS Architecture Patterns](https://medium.com/ios-os-x-development/ios-architecture-patterns-ecba4c38de52#.wtcp3gqzw)