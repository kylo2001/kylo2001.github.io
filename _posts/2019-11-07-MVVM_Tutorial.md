---
layout: post
title:  " Swift Tutorial: An Introduction to the MVVM Design Pattern "
categories: Architecture
tags: MVVM
author: GGuJi
---


* content
{:toc}

원문 : https://www.toptal.com/ios/swift-tutorial-introduction-to-mvvm

출처: https://kka7.tistory.com/83?category=960098



## Swift Tutorial: An Introduction to the MVVM Design Pattern

여러분이 새로운 iOS 프로젝트를 시작하면서, 디자이너에게서 모든 필요한 `.pdf`, `.sketch` 문서를 받았고, 새로운 앱을 어떻게 만들지에 대한 비전을 가지고 있습니다.

디자이너의 스케치로부터 `ViewController`의 `.swift, .xib, .storyboard` 파일들로 UI화면을 바꾸기 시작합니다.

`UITextField`는 여기에, `UITableView`는 거기에, 몇가지 `UILabels`와 `UIButtons`의 핀치(pinch), `IBOutlets`, `IBActions`도 포함되어 있습니다. 좋습니다. 여전히 UI 영역에 있습니다.

하지만, 이러한 모든 UI요소들로 무언가 할 시간입니다; `UIButtons`는 손가락 터치를 받을 것이며, `UILabels`와 `UITableViews`에 표시할 내용과 형식을 알려줄 누군가가 필요할 것입니다.

갑자기 3,000줄 이상의 코드를 가지고 있습니다.

![img](https://t1.daumcdn.net/cfile/tistory/260AAF505951ECA826)

스파게티 코드가 많았습니다.



이를 해결하기 위한 첫번째 단계는 **Model-View-Controller(MVC)** 디자인 패턴을 적용하는 것입니다. 하지만, 이 패턴은 자체적으로 문제가 있습니다. 그 문제를 피할수 있는(saves the day) **Model-View-ViewModel(MVVM)**디자인 패턴이 있습니다.



### 스파게티 코드 다루기(Dealing With Spaghetti Code)

빠른 시일내에, 여러분의 시작 `ViewController`은 너무 똑똑해지고 너무 거대해지고(massive) 있습니다.

네트워킹 코드, 데이터 파싱 코드, UI 프리젠테이션에 대한 데이터 조정 코드, 앱 상태 알림, UI 상태 변경. 단일 파일의 `if` 문장 내부에 있는 모든 코드는 재사용 할 수 없고 이 프로젝트에서만 적합합니다.

여러분의 `ViewController`코드는 악명높은 스파게티 코드가 됩니다.



어떻게 된건가요?

이것이 가능한 이유는 다음과 같습니다.



`UITableView` 내부에서 백엔드(back-end) 데이터가 어떻게 동작하는지 보고 싶었으며, `ViewController`의 `temp` 메소드 안에 몇줄의 네트워킹 코드를 넣어 네트워크로 `.json`을 가져옵니다. 다음으로, `.json`안에 있는 데이터를 처리할 필요가 있었기 때문에, 그것을 달성하기 위해 또다른 `temp`메소드를 작성했습니다. 또는, 더 나쁜 것은, 동일한 방법으로 해왔습니다.

`ViewController` 는 사용자 인증코드가 올때 계속 커졌습니다. 그리고 나서 데이터 포멧이 변경되기 시작했으며, UI는 개선되고 근본적인 변화가 필요했고, 이미 거대해진 `if` 문장에 더 많은 `if`를 추가하고 있습니다.

하지만, 어째서 `UIViewController`가 감당할수 없게 되었나요?

`UIViewController`은 UI 코드 작업을 시작하는 논리적인 곳입니다. iOS 기기의 모든 앱을 사용하는 동안 보고 있는 실제 화면을 나타냅니다. 심지어는 애플이 메인 시스템 앱에서 다른 앱과 에니메이션 UI 간에 전환할때 `UIViewControllers`를 사용합니다.

애플은 `UIViewController`내부에서 UI 추상화를 기반으로 하며, 그것은 iOS UI 코드의 핵심이고 **MVC** 디자인 패턴의 일부 입니다.

> 참고 : [iOS 개발자도 잘 모르는 가장 일반적인 실수 10가지(The 10 Most Common Mistackes iOS Developers Don’t know They’re Making)](https://www.toptal.com/ios/top-ios-development-mistakes)

### MVC 디자인 패턴으로 업그레이드하기(Upgrading to the MVC Design Pattern)

![img](https://t1.daumcdn.net/cfile/tistory/2102BE3C5951ECC932)

MVC 디자인 패턴에서, **View**는 수동적(inactive)이고 요청에 따라 준비된 데이터를 표시만 한다고 가정합니다.

**Controller**는 **Model**데이터를 작업하여 데이터를 표시하는 **Views**를 준비해야 합니다.

**View**는 사용자 터치 같은, 모든 동작에 대해서 통보해야 하는 책임이 있습니다.

말했던것 처럼, `UIViewController`은 보통 UI 화면 생성하는 시작점입니다. view와 controller 모두 포함하는 이름에 주목(notice)합니다. 이것은 뷰를 제어한다라는 의미입니다. controller와 view 코드 모두 들어가야 하는 것을 의미하지는 않습니다.

뷰와 컨트롤러 코드의 조합은 `UIViewController` 내부의 작은 서브 뷰들의 `IBOutlets`을 이동할때 자주 발생하고, `UIViewController`의 서브 뷰들를 직접 조작합니다. 그 대신 사용자 정의 `UIView` 서브 클래스의 코드를 래핑해야 합니다.

뷰와 컨트롤러 코드 경로가 교차될 수 있는 것을 쉽게 알수 있습니다.



### 구출하기 위한 MVVM(MVVM To the Rescue)

여기에서 **MVVM** 패턴이 유용합니다.

`UIViewController`은 MVC 패턴에서 **Controller**가 되어야 하기 때문에, **Views**는 이미 많은 일을 하고 있으며, 우리는 그것들을 새로운 패턴 **MVVM**의 **View**로 합칠수 있습니다.

![img](https://t1.daumcdn.net/cfile/tistory/27760F345951ECDF13)

MVVM 디자인 패턴에서, **Model**은 MVC 패턴에서 같습니다. 그것은 단순히 데이터를 표현합니다.

**View**는 `UIView` 또는 `UIViewController` 객체들에 의해 표현되며, `.xib`와 `.storyboard` 파일들과 함께 추가되며, 준비된 데이터만 표시해야 합니다. (예를 들어 View안에서 `NSDateFormatter`코드를 원하지 않습니다)

**ViewModel**로부터 가져온 단순하게 포멧된 문자열.

**ViewModel**은 모든 비동기 네트워킹 코드, 시각적 표현을 위한 데이터 준비코드, **Model** 변경을 수신하는 코드를 숨깁니다. 이러한 모든 것은 특정 **View**에 맞도록 잘 정의된 API 뒤로 숨겨져 있습니다.

MVVM을 사용하는 장점중 하나가 테스트하기 입니다. **ViewModel**은 순수한 `NSObject`(또는 `struct`)이고, `UIKit`코드와 결합되어 있지 않으며, UI 코드에 영향을 주지 않으면서 단위테스트에서 보다 쉽게 테스트 할 수 있습니다.

이제, **View**(UIViewController/UIView)는 훨씬 간단해 졌으며 **ViewModel**은 **Model**과 **View**간의 연결 역할을 합니다.



### Swift에서 MVVM 적용하기(Applying MVVM In Swift)

![img](https://t1.daumcdn.net/cfile/tistory/2241D9395951ECF32A)

MVVM이 동작하는 것을 보여주기 위해, [여기에서](https://bitbucket.org/dino4674/mvvmswiftexample-starter/get/25663cc4ee77.zip) 튜토리얼에서 만든 Xcode 프로젝트 예제를 다운로드하고 검토할수 있습니다. 이 프로젝트는 Xcode 8.1과 Swift 3을 사용합니다.

2가지 버젼의 프로젝트가 있습니다: [시작(Starter)](https://bitbucket.org/dino4674/mvvmswiftexample-starter/get/25663cc4ee77.zip)과 [완료(Finished)](https://bitbucket.org/dino4674/mvvmswiftexample-finished/get/892ee747f5f3.zip)

*완료(Finished)* 버젼은 완성된 작은 앱이며, *시작(Starter)*은 동일한 프로젝트이지만, 구현된 메소드와 객체가 없습니다.

먼저, *시작(Starter)* 프로젝트를 다운받고, 튜토리얼을 따라하는 것을 제안합니다. 나중에 프로젝트에 대한 빠른 참조가 필요한 경우, *완료(Finished)* 프로젝트를 다운로드 합니다.



### 튜토리얼 프로젝트 소개(Tutorial Project Introduction)

튜토리얼 프로젝트는 게임중에 플레이어의 행동을 추적하는 농구 앱입니다.

![img](https://t1.daumcdn.net/cfile/tistory/235CF3375951ED070A)

픽업 게임(pickup game)에서 사용자 이동과 전체 점수를 빠르게 추적하는데 사용됩니다.

두 팀은 15점이 될때까지 플레이합니다(최소 2점 차이). 각 플레이어는 1점에서 2점까지 점수 매길수 있고, 각 선수들은 어시스트, 리바운드, 반칙할 수 있습니다.



프로젝트 계층 구조는 다음과 같습니다.

![img](https://t1.daumcdn.net/cfile/tistory/26632D355951ED1B1D)



**Model**

- `Game.swift`
  
  : 게임로직을 포함하고, 전체 스코어를 추적하고, 각 플레이어의 움직임을 추적합니다.
- `Team.swift`
  
  : 팀 이름과 플레이어 목록을 포함합니다.(각 팀은 3명의 플레이어가 있습니다)
- `Player.swift`
  
  : 이름있는 한명의 플레이어

#### View

- `HomeViewController.swift`
  
  `GameScoreboardEditorViewcontroller`을 제공하는 루트 뷰 컨트롤러
- `GameScoreboardEditorViewController.swift`
  
  `Main.storyboard`에서 인터페이스빌더 뷰로 추가됨
  
  튜토리얼에 대해 흥미로운 화면
- `PlayerScoreboardMoveEditorView.swift`
  
  `PlayerScoreboardMoveEditorView.xib`의 인터페이스 빌더 뷰로 추가됨
  
  위의 뷰의 서브 뷰이며, MVVM 디자인 패턴을 사용함.

#### ViewModel

- `ViewModel`그룹은 비어있으며, 이 튜토리얼에서 여러분이 만들것입니다.



이미 다운로드된 Xcode 프로젝트는 이미 **View**객체들(`UIview`와 `UIViewController`)에 대한 대체표시(placeholders)를 포함하고 있습니다. 그 프로젝트는 **ViewModel**객체들에(`Services` 그룹) 데이터를 제공하는 방법중 하나를 시연하기 위해 만든 사용자 정의 객체도 포함되어 있습니다.

`Extensions` 그룹은 UI 코드에 유용한 확장 기능을 포함하였고 이 튜토리얼의 범위에는 포함되지 않고 자명하다.

앱을 실행하는 시점에, 완성된 UI가 표시될 것이지만, 사용자가 버튼을 누를때 아무일도 일어나지 않습니다.

이것은 앱 로직에 연결과 UI요소를 모델의 데이터를 채우지 않고 뷰와 `IBActions`만을 생성했기 때문입니다. (`Game`객체에 대해 나중에 배울것입니다)



### ViewModel로 View와 Model 연결하기(Connecting View and Model with ViewModel)

MVVM 디자인 패턴에서, View는 Model에 대해서 아무것도 모릅니다. View가 알고 있는 유일한 것은 ViewModel로 작업하는 방법입니다.

View를 검토해서 시작하세요.

`GameScoreboardEditorViewController.swift` 파일에서, 이 시점에 `fillUI`메소드는 비어있습니다. 이것은 UI를 데이터로 채우는 곳입니다. 이를 달성하기 위해, `ViewController`에 대한 데이터를 제공해야 합니다. ViewModel 객체로 이것을 수행합니다.

비어있는 ViewModel Xcode 프로젝트 그룹로 가서, `GameScoreboardEditorViewModel.swift`파일을 생성하고 프로토콜을 만듭니다.

```swift
import Foundation

protocol GameScoreboardEditorViewModel {
    var homeTeam: String { get }
    var awayTeam: String { get }
    var time: String { get }
    var score: String { get }
    var isFinished: Bool { get }
    
    var isPaused: Bool { get }
    func togglePause()
}
```

이처럼 프로토콜을 사용하면 멋지고 명확하게 해준다; 사용할 데이터를 정의해주기만 하면 됩니다.

다음으로, 프로토콜에 대한 구현을 생성합니다.

`GameScoreboardEditorViewModelFromGame.swift`라는 새파일을 만들고, `NSObject`의 서브클래스 객체를 만듭니다.

또한 `GameScroeboardEditorViewModel`프로토콜을 준수하도록 만듭니다.

```swift
import Foundation

class GameScoreboardEditorViewModelFromGame: NSObject, GameScoreboardEditorViewModel {
    
    let game: Game
    
    struct Formatter {
        static let durationFormatter: DateComponentsFormatter = {
            let dateFormatter = DateComponentsFormatter()
            dateFormatter.unitsStyle = .positional
            return dateFormatter
        }()
    }
    
    // MARK: GameScoreboardEditorViewModel protocol
    
    var homeTeam: String
    var awayTeam: String
    
    var time: String
    var score: String
    var isFinished: Bool
    
    var isPaused: Bool
    func togglePause() {
        if isPaused {
            startTimer()
        } else {
            pauseTimer()
        }
        
        self.isPaused = !isPaused
    }
    
    // MARK: Init
    
    init(withGame game: Game) {
        self.game = game
        
        self.homeTeam = game.homeTeam.name
        self.awayTeam = game.awayTeam.name
        
        self.time = GameScoreboardEditorViewModelFromGame.timeRemainingPretty(for: game)
        self.score = GameScoreboardEditorViewModelFromGame.scorePretty(for: game)
        self.isFinished = game.isFinished
        self.isPaused = true
    }
    
    // MARK: Private
    
    fileprivate var gameTimer: Timer?
    fileprivate func startTimer() {
        let interval: TimeInterval = 0.001
        gameTimer = Timer.schedule(repeatInterval: interval) { timer in
            self.game.time += interval
            self.time = GameScoreboardEditorViewModelFromGame.timeRemainingPretty(for: self.game)
        }
    }
    
    fileprivate func pauseTimer() {
        gameTimer?.invalidate()
        gameTimer = nil
    }
    
    // MARK: String Utils
    
    fileprivate static func timeFormatted(totalMillis: Int) -> String {
        let millis: Int = totalMillis % 1000 / 100 // "/ 100" <- because we want only 1 digit
        let totalSeconds: Int = totalMillis / 1000
        
        let seconds: Int = totalSeconds % 60
        let minutes: Int = (totalSeconds / 60)
        
        return String(format: "%02d:%02d.%d", minutes, seconds, millis)
    }
    
    fileprivate static func timeRemainingPretty(for game: Game) -> String {
        return timeFormatted(totalMillis: Int(game.time * 1000))
    }
    
    fileprivate static func scorePretty(for game: Game) -> String {
        return String(format: "\(game.homeTeamScore) - \(game.awayTeamScore)")
    }
    
}
```

초기화 작업을 하기 위해 ViewModel이 필요한 모든 것을 제공했다는 것을 주목(Notice)하세요.

여러분은 ViewModel아래에 있는 Model인 `Game`객체를 제공했습니다.

지금 앱을 실행하면, ViewModel 데이터를 View 자체에 연결하지 않았으므로 여전히 동작하지 않습니다.

따라서, `GameScoreboardEditorViewController.swift`파일로 돌아가고 `viewModel` 공개(public) 프로퍼티를 생성합니다.

`GameScoreboardEditorViewModel`타입을 확인합니다.

`GameScoreboardEditorViewController.swift`내부에 `viewDidLoad` 메소드 바로 앞에 배치합니다.

```swift
var viewModel: GameScoreboardEditorViewModel? {
    didSet {
        fillUI()
    }
}
```

다음으로, `fillUI`메소드 구현이 필요합니다.

이 메소드가 `viewModel`프로퍼티 옵져버(`didSet`)와 `viewDidLoad` 메소드 두 군데에서 호출되는 것을 주목합니다. `ViewController`를 생성하고 ViewModel을 뷰에 첨부하기 전에 할당하세요.(`viewDidLoad`호출되기 전에)

한편으로는, ViewController의 뷰에 다른 뷰를 첨부하고 `viewDidLoad`을 호출할 수 있지만, 만약 이 시점에 `viewModel`이 설정되지 않으면, 아무것도 일어나지 않습니다.

첫번째로, UI를 채우기 위해 데이터가 모두 설정 되었는지 확인해야 합니다. 예상치 못한 경우에 코드를 보호(guard)하는 것이 중요합니다.

따라서, `fillUI`메소드로 이동해서 다음코드로 교체합니다.

```swift
fileprivate func fillUI() {
    if !isViewLoaded {
        return
    }
    
    guard let viewModel = viewModel else {
        return
    }
    
    // we are sure here that we have all the setup done
    
    self.homeTeamNameLabel.text = viewModel.homeTeam
    self.awayTeamNameLabel.text = viewModel.awayTeam
    
    self.scoreLabel.text = viewModel.score
    self.timeLabel.text = viewModel.time
    
    let title: String = viewModel.isPaused ? "Start" : "Pause"
    self.pauseButton.setTitle(title, for: .normal)
}
```

이제 `pauseButtonPress`메소드를 구현합니다.

```swift
@IBAction func pauseButtonPress(_ sender: AnyObject) {
    viewModel?.togglePause()
}
```

이제 여러분이 해야할 것은, `ViewController`에 있는 실제 `viewModel`프로퍼티를 설정하는 것입니다. 이것을 외부에서 부터 합니다.

`HomeViewController.swift`파일을 열고 ViewModel 주석을 제거합니다:
`showGameScoreboardEditorViewController` 메소드 안에 생성하고 설정합니다.

```swift
// uncomment this when view model is implemented
let viewModel = GameScoreboardEditorViewModelFromGame(withGame: game)
controller.viewModel = viewModel
```

이제, 앱을 실행합니다. 다음과 같이 보일것입니다.

![img](https://t1.daumcdn.net/cfile/tistory/26578B395951ED3829)

점수, 시간, 팀 이름을 담당하는 **middle view**는 더이상 인터페이스 빌더에 설정된 값을 표시하지 않습니다.

이제, ViewModel 객체 자체의 값을 보여주며, 실제 Model 객체에서 데이터를 가져옵니다(`Game`객체)



훌륭합니다! 하지만 플레이어의 뷰들은 어떤가요? 그 버튼들은 여전히 아무것도 하지 않습니다.



플레이어 이동 추적에 대한 6개의 뷰가 있는 것을 알고 있습니다.

현재 실제 데이터와 아무런 관련이 없이 인터페이스 빌더를 통해 `PlayerScoreboardMoveEditorView.xib` 파일 안에 있는 정적인 값을 보여주는 별도의 서브 뷰 `PlayerScoreboardMoveEditorView`를 만듭니다.

약간의 데이터를 줘야 합니다.

`GameScoreboardEditorViewController`와`GameScoreboardEditorViewModel`과 똑같은 방법으로 작업할 것입니다.



Xcode프로젝트에서 ViewModel 그룹을 열고 새로운 프로토콜을 정의합니다.

`PlayerScoreboardMoveEditorViewModel.swift` 파일을 생성하고 다음 코드를 입력합니다.

```swift
import Foundation

protocol PlayerScoreboardMoveEditorViewModel {
    var playerName: String { get }
    
    var onePointMoveCount: String { get }
    var twoPointMoveCount: String { get }
    var assistMoveCount: String { get }
    var reboundMoveCount: String { get }
    var foulMoveCount: String { get }
    
    func onePointMove()
    func twoPointsMove()
    func assistMove()
    func reboundMove()
    func foulMove()
}
```

이 ViewModel 프로토콜은 `GameScoreboardEditorViewController` 부모 뷰에서 했던 것 처럼 `PlayerScoreboardMoveEditorView`에 맞게 설계되었습니다.

사용자가 만들수 있는 5개의 다른 동작에 대한 값이 필요하고, 사용자가 액션 버튼들 중 하나를 터치할때 반응해야 합니다. 또한 플레이어의 이름에 대한 `String`이 필요합니다.

이것을 끝낸후에, 부모 뷰(`GameScoreboardEditorViewController`)와 마찬가지로 프로토콜을 구현하는 구체적인 클래스를 만듭니다.

다음으로, 이 프로토콜의 구현을 작성합니다: `PlayerScoreboardMoveEditorViewModelFromPlayer.swift` 이름의 새로운 파일을 생성하고, `NSObject`의 서브 클래스 객체로 만듭니다. 또한, `PlayerScoreboardMoveEditorViewModel`프로토콜을 준수하도록 만듭니다.

```swift
import Foundation

class PlayerScoreboardMoveEditorViewModelFromPlayer: NSObject, PlayerScoreboardMoveEditorViewModel {
    
    fileprivate let player: Player
    fileprivate let game: Game
    
    // MARK: PlayerScoreboardMoveEditorViewModel protocol
    
    let playerName: String
    
    var onePointMoveCount: String
    var twoPointMoveCount: String
    var assistMoveCount: String
    var reboundMoveCount: String
    var foulMoveCount: String
    
    func onePointMove() {
        makeMove(.onePoint)
    }
    
    func twoPointsMove() {
        makeMove(.twoPoints)
    }
    
    func assistMove() {
        makeMove(.assist)
    }
    
    func reboundMove() {
        makeMove(.rebound)
    }
    
    func foulMove() {
        makeMove(.foul)
    }
    
    // MARK: Init
    
    init(withGame game: Game, player: Player) {
        self.game = game
        self.player = player
        
        self.playerName = player.name
        self.onePointMoveCount = "\(game.playerMoveCount(for: player, move: .onePoint))"
        self.twoPointMoveCount = "\(game.playerMoveCount(for: player, move: .twoPoints))"
        self.assistMoveCount = "\(game.playerMoveCount(for: player, move: .assist))"
        self.reboundMoveCount = "\(game.playerMoveCount(for: player, move: .rebound))"
        self.foulMoveCount = "\(game.playerMoveCount(for: player, move: .foul))"
    }
    
    // MARK: Private
    
    fileprivate func makeMove(_ move: PlayerInGameMove) {
        game.addPlayerMove(move, for: player)
        
        onePointMoveCount = "\(game.playerMoveCount(for: player, move: .onePoint))"
        twoPointMoveCount = "\(game.playerMoveCount(for: player, move: .twoPoints))"
        assistMoveCount = "\(game.playerMoveCount(for: player, move: .assist))"
        reboundMoveCount = "\(game.playerMoveCount(for: player, move: .rebound))"
        foulMoveCount = "\(game.playerMoveCount(for: player, move: .foul))"
    }
    
}
```

이제, 외부로부터 이 인스턴스를 생성할 객체가 필요하고 `PlayerScoreboardMoveEditorView`내부 프로퍼티를 설정해야 합니다.

`HomeViewController`가 `GameScoreboardEditorViewController`에서 `viewModel`프로퍼티를 어떻게 설정했는지 기억합니까?

같은 방법으로, `GameScoreboardEditorViewController`은 `PlayerScoreboardMoveEditorView`의 부모 뷰이고 `GameScoreboardEditorViewController`은 `PlayerScoreboardMoveEditorViewModel`객체를 생성할 책임을 가지고 있습니다.

먼저 `GameScoreboardEditorViewModel` 확장해야 합니다.

`GameScoreboardEditorViewModel`을 열고 다음 두개의 프로퍼티를 추가합니다.

```swift
var homePlayers: [PlayerScoreboardMoveEditorViewModel] { get }
var awayPlayers: [PlayerScoreboardMoveEditorViewModel] { get }
```

`GameScoreboardEditorViewModelFromGame`에서 `initWithGame`메소드 바로 위에 다음과 같은 프로퍼티 2개를 업데이트 합니다:

```swift
let homePlayers: [PlayerScoreboardMoveEditorViewModel]
let awayPlayers: [PlayerScoreboardMoveEditorViewModel]
```

`initWithGame`안쪽에 다음 두줄을 추가합니다:

```swift
self.homePlayers = GameScoreboardEditorViewModelFromGame.playerViewModels(from: game.homeTeam.players, game: game)
self.awayPlayers = GameScoreboardEditorViewModelFromGame.playerViewModels(from: game.awayTeam.players, game: game)
```

물론, 누락된 `playerViewModelWithPlayers`메소드를 추가합니다.

```swift
// MARK: Private Init

fileprivate static func playerViewModels(from players: [Player], game: Game) -> [PlayerScoreboardMoveEditorViewModel] {
    var playerViewModels: [PlayerScoreboardMoveEditorViewModel] = [PlayerScoreboardMoveEditorViewModel]()
    for player in players {
        playerViewModels.append(PlayerScoreboardMoveEditorViewModelFromPlayer(withGame: game, player: player))
    }
    
    return playerViewModels
}
```

훌륭합니다!

홈팀(home)과 원정팀(away) 플레이어 배열로 ViewModel(`GameScoreboardEditorViewModel`)을 업데이트 하였습니다. 여전히 두 배열을 채워야 합니다.

UI를 채우기 위해 `viewModel`을 사용했던 동일한 위치에서 이 작업을 할것입니다.

`GameScoreboardEditorViewController`을 열고 `fillUI`메소드로 갑니다. 메소드의 끝에 다음 줄을 추가합니다.

```swift
homePlayer1View.viewModel = viewModel.homePlayers[0]
homePlayer2View.viewModel = viewModel.homePlayers[1]
homePlayer3View.viewModel = viewModel.homePlayers[2]
        
awayPlayer1View.viewModel = viewModel.awayPlayers[0]
awayPlayer2View.viewModel = viewModel.awayPlayers[1]
awayPlayer3View.viewModel = viewModel.awayPlayers[2]
```

순간, `PlayerScoreboardMoveEditorView`에 실제 `viewModel`프로퍼티를 추가하지 않았기 때문에 빌드 오류가 납니다.

`PlayerScoreboardMoveEditorView`안의 `init` 메소드 위에 다음 코드를 추가하세요.

```swift
var viewModel: PlayerScoreboardMoveEditorViewModel? {
    didSet {
        fillUI()
    }
}
```

그리고 `fillUI` 메소드를 구현합니다.

```swift
fileprivate func fillUI() {
    guard let viewModel = viewModel else {
        return
    }
    
    self.name.text = viewModel.playerName
    
    self.onePointCountLabel.text = viewModel.onePointMoveCount
    self.twoPointCountLabel.text = viewModel.twoPointMoveCount
    self.assistCountLabel.text = viewModel.assistMoveCount
    self.reboundCountLabel.text = viewModel.reboundMoveCount
    self.foulCountLabel.text = viewModel.foulMoveCount
}
```

마지막으로, 앱을 실행하고, UI요소의 데이터가 `Game`객체의 실제 데이터인지 확인합니다.

![img](https://t1.daumcdn.net/cfile/tistory/2525D5385951ED5529)

이 시점에, 여러분은 MVVM 디자인패턴을 사용하는 함수형 앱을 가지고 있습니다.

View로 부터 Model를 멋지게 숨기고, View는 MVC로 사용했을때 보다 매우 단순합니다.

지금까지, View와 ViewModel이 포함된 앱을 만들었습니다.

View는 ViewModel과 동일한 서브 뷰(player view)의 인스턴스 6개를 가지고 있습니다.

하지만, 알다시피, UI에 데이터를 한번만 표시할 수 있고(`fillUI` 메소드 에서), 그 데이터는 정적(static)입니다.

View의 생명주기 동안 뷰의 데이터가 변하지 않는 경우, 이런 방식으로 MVVM을 사용하면 훌륭하고 명확한 솔루션을 얻을수 있습니다.



### ViewModel을 동적으로 만들기(Making the ViewModel Dynamic)

데이터가 변경되기 때문에, ViewModel을 동적으로 만들어야 합니다.

이것이 의미하는 것은 Model이 변경될때, ViewModel은 public 프로퍼티 값을 변경해야 합니다. UI를 업데이트 할 View에 변경사항을 다시 전달합니다.



이를 수행하는 많은 방법이 있습니다.



Model이 변경될때 ViewModel은 제일먼저 통지 받습니다.

변경 사항을 View에 전달할 수 있는 메카니즘(mechanisim)이 필요합니다.



#### View의 변경사항을 Observing 할 수 있는 방법

- RxSwift
- NSNotification
- KVO
- Generics & Closure



방법중에 꽤 큰 라이브러리인 [RxSwift](https://github.com/ReactiveX/RxSwift)를 포함하고 익숙해지는데 약간의 시간이 필요합니다.

ViewModel은 각 프로퍼티 값이 변경할때 `NSNotification`을 보낼수 있지만, 이렇게 하면 추가적인 처리가 필요한 많이 코드가 추가되며, 뷰에 할당이 해제될때 알림 구독과 구독취소 할수 있습니다.

[Key-Value-Observing(KVO)](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)는 다른 방법이지만, 사용자가 API가 만족스럽지 않은지 확인해야 합니다.

이 튜토리얼에서는, [바인딩, 제네릭, Swift와 MVVM 기사(Bindings, Generics, Swift and MVVM article)](http://rasic.info/bindings-generics-swift-and-mvvm/)에서 잘 설명된 Swift 제네릭과 클로져를 사용할 것입니다.

이제, 예제로 돌아가 봅시다.

ViewModel 프로젝트 그룹으로 가고, 새로운 Swift 파일 `Dynamic.swift`을 생성합니다.

```swift
class Dynamic<T> {
    typealias Listener = (T) -> ()
    var listener: Listener?
    
    func bind(_ listener: Listener?) {
        self.listener = listener
    }
    
    func bindAndFire(_ listener: Listener?) {
        self.listener = listener
        listener?(value)
    }
    
    var value: T {
        didSet {
            listener?(value)
        }
    }
    
    init(_ v: T) {
        value = v
    }
}
```

View 생명주기 동안 변경하려는 ViewModel의 프로퍼티에 이 클래스를 사용할 것입니다.

우선, `PlayerScoreboardMoveEditorView`, 이 ViewModel인 `PlayerScoreboardMoveEditorViewModle`으로 시작합니다.

`PlayerScoreboardMoveEditorViewModel`을 열고 프로퍼티를 살펴봅니다.

`playerName`은 변경되지 않기 때문에, 그대로 놔둘수 있습니다.

다른5개의 프로퍼티들(5개의 이동 타입)은 변경할 것이므로, 그것들에 대해 무언가 해야 합니다. 해결책은? 위에서 언급한 `Dynamic`클래스를 프로젝트에 추가합니다.

`PlayerScoreboardMoveEditorViewModel`에서 이동 횟수를 나타내는 5개의 문자열 정의를 제거하고 다음으로 교체 합니다.

```swift
var onePointMoveCount: Dynamic<String> { get }
var twoPointMoveCount: Dynamic<String> { get }
var assistMoveCount: Dynamic<String> { get }
var reboundMoveCount: Dynamic<String> { get }
var foulMoveCount: Dynamic<String> { get }
```

ViewModel 프로토콜은 다음과 같아야 합니다.

```swift
import Foundation

protocol PlayerScoreboardMoveEditorViewModel {
    var playerName: String { get }
    
    var onePointMoveCount: Dynamic<String> { get }
    var twoPointMoveCount: Dynamic<String> { get }
    var assistMoveCount: Dynamic<String> { get }
    var reboundMoveCount: Dynamic<String> { get }
    var foulMoveCount: Dynamic<String> { get }
    
    func onePointMove()
    func twoPointsMove()
    func assistMove()
    func reboundMove()
    func foulMove()
}
```

`Dynamic`타입은 특정 프로퍼티의 값이 변경할 수 있고, 동시에, 변경 리스너(change-listener) 객체에 알리며, 이 경우에는 View가 될것입니다.

이제 실제 ViewModel `PlayerScoreboardMoveEditorViewModelFromPlayer`의 구현을 업데이트 합니다.

이것들을

```swift
var onePointMoveCount: String
var twoPointMoveCount: String
var assistMoveCount: String
var reboundMoveCount: String
var foulMoveCount: String
```

다음으로 교체합니다:

```swift
let onePointMoveCount: Dynamic<String>
let twoPointMoveCount: Dynamic<String>
let assistMoveCount: Dynamic<String>
let reboundMoveCount: Dynamic<String>
let foulMoveCount: Dynamic<String>
```

> **주의**
> 실제 프로퍼티를 변경하지 않으므로 `let` 프로퍼티를 사용하여 이러한 프로퍼티를 상수로 선언하는 것이 좋습니다. `Dynamic` 객체의 `value`프로퍼티를 변경할 것입니다.

이제, `Dynamic` 객체를 초기화하지 않았기 때문에 빌드 오류가 발생합니다.

`PlayerScoreboardMoveEditorViewModelFromPlayer`의 `init`메소드에서 이동 프로퍼티들의 초기화를 다음으로 교체합니다.

```swift
self.onePointMoveCount = Dynamic("\(game.playerMoveCount(for: player, move: .onePoint))")
self.twoPointMoveCount = Dynamic("\(game.playerMoveCount(for: player, move: .twoPoints))")
self.assistMoveCount = Dynamic("\(game.playerMoveCount(for: player, move: .assist))")
self.reboundMoveCount = Dynamic("\(game.playerMoveCount(for: player, move: .rebound))")
self.foulMoveCount = Dynamic("\(game.playerMoveCount(for: player, move: .foul))")
```

`PlayerScoreboardMoveEditorViewModelFromPlayer`에 `makeMove` 메소드로 이동하고 다음 코드로 교체합니다

```swift
fileprivate func makeMove(_ move: PlayerInGameMove) {
    game.addPlayerMove(move, for: player)
    
    onePointMoveCount.value = "\(game.playerMoveCount(for: player, move: .onePoint))"
    twoPointMoveCount.value = "\(game.playerMoveCount(for: player, move: .twoPoints))"
    assistMoveCount.value = "\(game.playerMoveCount(for: player, move: .assist))"
    reboundMoveCount.value = "\(game.playerMoveCount(for: player, move: .rebound))"
    foulMoveCount.value = "\(game.playerMoveCount(for: player, move: .foul))"
}
```

보다시피, `Dynamic`클래스의 인스턴스를 만들었고, `String`값을 할당하였습니다. 데이터가 업데이트가 필요할때, `Dynamic`프로퍼티 자체는 변하지 않습니다: 반대로 `value`프로퍼티를 업데이트 합니다.

훌륭합니다! `PlayerScoreboardMoveEditorViewModel`은 이제 동적입니다.

그것을 이용하고, 이러한 변경사항을 실제로 듣을수 있는 view로 이동하세요.

`PlayerScoreboardMoveEditorView`와 `fillUI`메소드를 열어주세요. (`Dynamic` 객체 타입에 `String`값을 할당하려는 시점에 이 메소드에서 빌드 오류가 납니다)

오류난 줄을

```swift
self.onePointCountLabel.text = viewModel.onePointMoveCount
self.twoPointCountLabel.text = viewModel.twoPointMoveCount
self.assistCountLabel.text = viewModel.assistMoveCount
self.reboundCountLabel.text = viewModel.reboundMoveCount
self.foulCountLabel.text = viewModel.foulMoveCount
```

다음으로 교체합니다.

```swift
viewModel.onePointMoveCount.bindAndFire { [unowned self] in self.onePointCountLabel.text = $0 }
viewModel.twoPointMoveCount.bindAndFire { [unowned self] in self.twoPointCountLabel.text = $0 }
viewModel.assistMoveCount.bindAndFire { [unowned self] in self.assistCountLabel.text = $0 }
viewModel.reboundMoveCount.bindAndFire { [unowned self] in self.reboundCountLabel.text = $0 }
viewModel.foulMoveCount.bindAndFire { [unowned self] in self.foulCountLabel.text = $0 }
```

다음으로, 이동 동작을 나타내는 5가지 메소드를 구현합니다.(*Button Action* 부분)

```swift
@IBAction func onePointAction(_ sender: Any) {
    viewModel?.onePointMove()
}

@IBAction func twoPointsAction(_ sender: Any) {
    viewModel?.twoPointsMove()
}

@IBAction func assistAction(_ sender: Any) {
    viewModel?.assistMove()
}

@IBAction func reboundAction(_ sender: Any) {
    viewModel?.reboundMove()
}

@IBAction func foulAction(_ sender: Any) {
    viewModel?.foulMove()
}
```

앱을 실행하고 이동 버튼들을 클릭합니다. 액션 버튼을 클릭할때 플레이어 뷰의 값의 숫자들이 변하는 것을 볼수 있을 것 입니다.

![img](https://t1.daumcdn.net/cfile/tistory/2246EF335951ED721D)

`PlayerScoreboardMoveEditorView`와 `PlayerScoreboardMoveEditorViewModel`를 완료합니다.

이것은 간단합니다.

이제, 메인 뷰에서 동일한 작업을 해야합니다(`GameScoreboardEditorViewController`).

우선, `GameScoreboardEditorViewModel`을 열고 View의 생명주기에서 변화가 예상되는 값들을 봅니다.

`time, score, isFinished, isPaused`정의를 `Dynamic` 버젼으로 교체합니다.

```swift
import Foundation

protocol GameScoreboardEditorViewModel {
    var homeTeam: String { get }
    var awayTeam: String { get }
    var time: Dynamic<String> { get }
    var score: Dynamic<String> { get }
    var isFinished: Dynamic<Bool> { get }
    
    var isPaused: Dynamic<Bool> { get }
    func togglePause()
    
    var homePlayers: [PlayerScoreboardMoveEditorViewModel] { get }
    var awayPlayers: [PlayerScoreboardMoveEditorViewModel] { get }
}
```

ViewModlel 구현(`GameScoreboardEditorViewModelFromGame`)으로 이동하고 프로토콜내의 프로퍼티 선언을 동일한 작업을 수행합니다.

이것을

```swift
var time: String
var score: String
var isFinished: Bool
 
var isPaused: Bool
```

다음으로 교체합니다

```swift
let time: Dynamic<String>
let score: Dynamic<String>
let isFinished: Dynamic<Bool>
    
let isPaused: Dynamic<Bool>
```

ViewModel의 타입을 `String`, `Bool`에서 `Dynamic`, `Dynamic`으로 변경되었기 때문에, 지금은 몇가지 오류가 날것입니다.

그것들을 수정하세요.

`togglePause`메소드를 다음으로 교체하여 수정합니다.

```swift
func togglePause() {
    if isPaused.value {
        startTimer()
    } else {
        pauseTimer()
    }
        
    self.isPaused.value = !isPaused.value
}
```

프로퍼티에서 직접 프로퍼티 값을 설정하지 않는 점을 주목합니다. 대신에, 객체의 `value`프로퍼티에 설정합니다.

이제, `initWithGame`메소드를 다음으로 교체하여 수정합니다.

```swift
self.time = GameScoreboardEditorViewModelFromGame.timeRemainingPretty(game)
self.score = GameScoreboardEditorViewModelFromGame.scorePretty(game)
self.isFinished = game.isFinished
self.isPaused = true
```

다음으로 교체합니다:

```swift
self.time = Dynamic(GameScoreboardEditorViewModelFromGame.timeRemainingPretty(for: game))
self.score = Dynamic(GameScoreboardEditorViewModelFromGame.scorePretty(for: game))
self.isFinished = Dynamic(game.isFinished)
self.isPaused = Dynamic(true)
```

이제 포인트(point)를 얻어야 합니다.

`String, Int, Bool` 기본 값을 가벼운 바인딩 메카니즘을 제공하는 `Dynamic`버젼의 객체로 래핑합니다.

수정할 오류가 하나 더 있습니다.

`startTimer` 메소드에서, 오류나는 줄을 다음으로 교체합니다.

```swift
self.time.value = GameScoreboardEditorViewModelFromGame.timeRemainingPretty(for: self.game)
```

`ViewModel`을 dynamic으로 업그레이드 하며, 플레이어의 ViewModel에서 했던것처럼 합니다. 하지만 여전히 뷰를 업데이트 해야 합니다(`GameScoreboardEditorViewController`).

`fillUI`메소드 전체를 다음으로 교체합니다.

```swift
fileprivate func fillUI() {
    if !isViewLoaded {
        return
    }
    
    guard let viewModel = viewModel else {
        return
    }
    
    self.homeTeamNameLabel.text = viewModel.homeTeam
    self.awayTeamNameLabel.text = viewModel.awayTeam
    
    viewModel.score.bindAndFire { [unowned self] in self.scoreLabel.text = $0 }
    viewModel.time.bindAndFire { [unowned self] in self.timeLabel.text = $0 }
    
    viewModel.isFinished.bindAndFire { [unowned self] in
        if $0 {
            self.homePlayer1View.isHidden = true
            self.homePlayer2View.isHidden = true
            self.homePlayer3View.isHidden = true
            
            self.awayPlayer1View.isHidden = true
            self.awayPlayer2View.isHidden = true
            self.awayPlayer3View.isHidden = true
        }
    }
    
    viewModel.isPaused.bindAndFire { [unowned self] in
        let title = $0 ? "Start" : "Pause"
        self.pauseButton.setTitle(title, for: .normal)
    }
    
    homePlayer1View.viewModel = viewModel.homePlayers[0]
    homePlayer2View.viewModel = viewModel.homePlayers[1]
    homePlayer3View.viewModel = viewModel.homePlayers[2]
    
    awayPlayer1View.viewModel = viewModel.awayPlayers[0]
    awayPlayer2View.viewModel = viewModel.awayPlayers[1]
    awayPlayer3View.viewModel = viewModel.awayPlayers[2]
}
```

유일한 차이점은 4개의 동적인 프로퍼티를 변경하고 각각에 변경 리스너(listeners)를 추가하는 것입니다.

이 시점에, 앱을 실행하는 경우, *시작(Start)/일시정지(Pause)* 버튼을 토글(toggling)하면 게임 타이머가 시작하고 일시정지 할 것입니다. 이것은 게임중 타임아웃에 사용합니다.

포인트 버튼(`1, 2` 포인트 버튼)을 누를때, 점수가 UI에서 변경되지 않는 것을 제외하고 거의 완료되었습니다.

ViewModel까지 기본 `Game` 모델 객체의 점수 변경이 실제로 전달되지 않았기 때문입니다.

따라서, 약간 조사하기 위해 `Game`모델 객체를 엽니다. `updateScore` 메소드를 확인합니다.

```swift
fileprivate func updateScore(_ score: UInt, withScoringPlayer player: Player) {
    if isFinished || score == 0 {
        return
    }
    
    if homeTeam.containsPlayer(player) {
        homeTeamScore += score
    } else {
        assert(awayTeam.containsPlayer(player))
        awayTeamScore += score
    }
    
    if checkIfFinished() {
        isFinished = true
    }
    
    NotificationCenter.default.post(name: Notification.Name(rawValue: GameNotifications.GameScoreDidChangeNotification), object: self)
}
```

이 메소드는 두가지 중요한 일을 합니다.

첫번째, 두 팀의 점수에 따라 게임이 끝난 경우에 `isFinished`프로퍼티를 `true`로 설정합니다.

그런 다음에, 점수가 변경되었다는 알림을 보냅니다. `GameScoreboardEditorViewModelFromGame`에서 알림을 듣고 알림 처리 메소드에서 동적으로 점수 값을 업데이트 합니다.

다음을 `initWithGame`메소드의 아래줄에 추가합니다(오류를 피하기 위해 `super.init()`를 호출하는 것을 잊지마세요).

```swift
super.init()
subscribeToNotifications()
```

정리를 제대로 하고 `NotificationCenter`으로 발생하는 충돌을 피하길 원하기 때문에, 아래 `initWithGame` 메소드는 `deinit`메소드를 추가합니다.

```swift
deinit {
    unsubscribeFromNotifications()
}
```

마지막으로, 이러한 메소드 구현을 추가하세요. `deinit` 메소드 바로 아래에 다음 섹션(section)을 추가하세요.

```swift
// MARK: Notifications (Private)

fileprivate func subscribeToNotifications() {
    NotificationCenter.default.addObserver(self,
                                           selector: #selector(gameScoreDidChangeNotification(_:)),
                                           name: NSNotification.Name(rawValue: GameNotifications.GameScoreDidChangeNotification),
                                           object: game)
}

fileprivate func unsubscribeFromNotifications() {
    NotificationCenter.default.removeObserver(self)
}

@objc fileprivate func gameScoreDidChangeNotification(_ notification: NSNotification){
    self.score.value = GameScoreboardEditorViewModelFromGame.scorePretty(for: game)
    
    if game.isFinished {
        self.isFinished.value = true
    }
}
```

이제 앱을 실행하고, 플레이어 뷰에서 점수를 변경하기 위해 클릭합니다. 이미 ViewModel에서 View와 동적으로 `score`와 `isFinished`를 연결되었으므로, ViewModel 내부의 점수 값을 변경될때 모든 것이 동작합니다.



### 앱을 더 개선하는 방법(How to Further Improve the App)

항상 개선될 여지가 있지만, 이 튜토리얼에서는 다루지 않습니다.

예를 들어, 게임이 종료될때(팀중에 한팀이 15점에 도달하면) 시간을 자동으로 멈추지 않으며, 단지 플레이어 뷰를 숨깁니다.

게임 제작자뷰를 가지도록 업그레이드하고 앱 게임을 할 수 있으며, 
게임을 만들고, 팀 이름을 할당하고, 플레이어 이름을 할당하고 `GameScoreboardEditorViewController`을 표현하는데 사용할 수 있는 `Game`객체를 만듭니다.

`UITableView`를 사용하여 테이블 셀의 상세 정보로 진행중인 여러 게임들을 보여주는 다른 게임 목록뷰를 생성할 수 있습니다. 셀을 선택하면, 선택된 `Game`으로 `GameScoreboardEditorViewController`을 보여줄 수 있습니다.

`GameLibrary`는 이미 구현되어 있습니다. 단지 초기화에서 ViewModel 객체에 전달해야 하는 것은 기억하세요. 예를 들어 게임 제작자들 ViewModel은 초기화를 통해 `GameLibrary`의 인스턴스를 전달해야 하며, 생성된 `Game` 객체를 라이브러리에 삽입할 수 있습니다. 게임 목록 ViewModel은 라이브러리에서 모든 게임을 가져오려면 참조가 필요하며, UITableView에서 필요할 것입니다.

그 아이디어는 ViewModel 내부의 지저분한(UI가 아닌) 모든 작업을 숨기고 UI(View)는 준비된 표현 데이터로만 동작합니다.



### 이제 무엇을 할까요?(What now?)

MVVM에 익숙해지면, [밥 아저씨의 클린 아키텍쳐(Uncle Bob’s Clean Architecture rules)](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html) 를 사용하여 더 향상시킬수 있습니다.

추가적으로 좋은 읽을 거리는 Android 아키텍쳐에 대한 튜토리얼 3개입니다.

- [Android Architecture: Part 1 - Every new begining is hard](http://five.agency/android-architecture-part-1-every-new-beginning-is-hard/)
- [Android Architecture: Part 2 - The clean architecture](http://five.agency/android-architecture-part-2-clean-architecture/)
- [Android Architecture: Part 3 - Applying clean architecture](http://five.agency/android-architecture-part-3-applying-clean-architecture-android/)

예제는 자바(Android)로 작성되어 있고, 자바에 익숙하다면(Java는 Objective-C 보다는 Swift에 훨씬 가깝다), ViewModel 객체 내부에서 iOS 모듈(`UIKit` 또는 `CoreLocation`)을 가져오지 않고 코드를 더 리팩토링 하는 방법에 대한 아이디어를 얻을수 있습니다.

이러한 iOS 모듈은 순수한 `NSObject` 뒤에 숨어있을수 있으며, 코드 재사용에 좋습니다.

MVVM은 대부분 iOS앱에 좋은 선택이고, 희망컨데, 다음 프로젝트에서 시도해 보세요. 또는 현재 프로젝트에서 `UIViewController`을 생성할때 사용해 보세요.
