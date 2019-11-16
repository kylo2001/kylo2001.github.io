---
layout: post
title:  "MVVM 학습 정리 초기 적용 내용"
categories: Architecture
tags: MVVM
author: GGuJi

---



* content
{:toc}

## MVVM 학습 정리

> 시작하기 전에
>
> 이 글은 MVVM 에 대해 학습에 사용했던 자료들의 중요내용들과 작성한 코드를 일부 정리한 글이다. 어느정도 MVVM에 대해 학습한 사람이라면 링크들만 모아 보는 것을 추천!. 공부를 시작하는 사람들에게 이 글의 내용이 디딤돌이 될수 있기를 바란다.



## 학습방법

MVVM에 대한 글을 가장 기본적인 글을 바탕으로 공부하면서 궁금증이 생기면 관련 내용에 대한 여러 글들을 읽었다. 많은글을 읽어야 하는 이유는 웹상에 많은 글들 중에는 잘못된 내용들도 많기 때문이다.

> 잘못된 글들을 왜 골라낼수 있어야 하는가? 에 대한 개인적인 의견
>
> 몇몇 함정카드 성격의 글들을 골라내지 못하면 혼란에 빠지게 되고, 각 요소의 역활을 분리하기가 점점 어려워진다. 많은 사람들이 그런 부분을 겪고 MVVM에 대한 부정적인 시각을 가지게 되는걸 보았다.
> 이를테면, “비지니스 로직은 어디에 있어야하나 뷰모델?” 랄지, 뷰 모델의 본래 역활이나 가치에 집중하지 않고 뷰에 값을 바인딩 하는데에만 집중한다던지 등의 해석이 그렇다.
> 자칫 잘못하면 이름만 뷰모델이고 뷰컨트롤러의 책임을 일부만 떼어내놓은 모양새가 되기도 한다.
>
> 이제 공부를 시작하는 사람들이라면 충분히 많은 글과 코드를 보고 개념을 이해하면 이런 문제를 방지할수 있을 것 같다. ( TODO 샘플 같은 튜토리얼보다는 좀더 어렵더라도 킥스타터 등의 코드를 보는 것이 도움이 된다고 생각한다)
>
> 나는 잘못해석한 상태에서 많은 코드를 작성했었다. 나와같이 과오를 범하지 않길 바라며 사족을 달아본다.



MVVM의 기본적인 설명이 담겨있는 wikipedia: [Model-view-viewmodel - WikipediaMVVM facilitates a separation of development of the graphical user interface - be it via a markup language or GUI code…en.wikipedia.org](https://en.wikipedia.org/wiki/Model–view–viewmodel?source=post_page-----bb7576e23c65----------------------)



블로그 시리즈 글이다. 총 9개의 글로 이루어져 있다. (댓글들도 중요): [Better User and Developer Experiences - From Windows Forms to WPF with MVVMThis series introduces the Model-View-ViewModel Pattern from the point of view of a Windows Forms developer. The goal…reedcopsey.com](http://reedcopsey.com/series/windows-forms-to-mvvm/?source=post_page-----bb7576e23c65----------------------)



이규원님의 블로그 글: [MVVM 아키텍처 패턴MVVM(Model/View/ViewModel) 패턴은 UI를 가지는 응용프로그램을 위한 아키텍처 패턴(architectural pattern)이다. MVVM 패턴은 MVC(Model/View/Controller)…justhackem.wordpress.com](https://justhackem.wordpress.com/2017/03/05/mvvm-architectural-pattern/?source=post_page-----bb7576e23c65----------------------)



> 위 글에서 몇몇 부분을 정리했다.
>
> 1. 뷰를 추상화 하는 것이 핵심이다. (뷰 모델은 뷰를 추상화한 것이다.)
> 2. 뷰 모델은 value converters 를 가진다.
> 3. 모델은 데이터, 비즈니스 논리, 서비스 클라이언트 등으로 구성된다.



이규원 님의 글에 정리된 링크 (3개)

- [Introduction to Model/View/ViewModel pattern for building WPF appsModel/View/ViewModel is a variation of Model/View/Controller (MVC) that is tailored for modern UI development platforms…blogs.msdn.microsoft.com](https://blogs.msdn.microsoft.com/johngossman/2005/10/08/introduction-to-modelviewviewmodel-pattern-for-building-wpf-apps/?source=post_page-----bb7576e23c65----------------------)

- [100 Model/View/ViewModels of Mt. FujiTrapped deep within the pragmatic hacker that is the essential me, there still lurks a computer scientist. The computer…blogs.msdn.microsoft.com](https://blogs.msdn.microsoft.com/johngossman/2005/10/09/100-modelviewviewmodels-of-mt-fuji/?source=post_page-----bb7576e23c65----------------------)

- [Patterns - WPF Apps With The Model-View-ViewModel Design PatternDevelopers often intentionally structure their code according to a design pattern, as opposed to letting the patterns…msdn.microsoft.com](https://msdn.microsoft.com/en-us/magazine/dd419663.aspx?irgwc=1&OCID=AID681541_aff_7593_1243925&tduid=(ir_V7MxEyydAwSjUJgWuUWfixUmUkjTiE3TG2GJRo0)(7593)(1243925)(TnL5HPStwNw-0yD2PscmanInvp_xVbd4Pw)()&irclickid=V7MxEyydAwSjUJgWuUWfixUmUkjTiE3TG2GJRo0&source=post_page-----bb7576e23c65----------------------)



------

iOS 에서 사용되는 여러 아키텍쳐에 대해서 다룬책(유료): [App Architectureobjc.io publishes books on advanced techniques and practices for iOS and OS X developmentwww.objc.io](https://www.objc.io/books/app-architecture/?source=post_page-----bb7576e23c65----------------------)



비지니스 로직은 어디에 있어야 하나? 에 대한 스레드: [MVVM: Businesslogic is part of the ...?Hi concering MVVM if have some question I actually should be able to answer my self, but there still is some unclarity…social.msdn.microsoft.com](https://social.msdn.microsoft.com/Forums/vstudio/en-US/3eb70678-c216-414f-a4a5-e1e3e557bb95/mvvm-businesslogic-is-part-of-the-?forum=wpf&source=post_page-----bb7576e23c65----------------------)



글을 읽은뒤 이해한 내용을 정리하고, 코드를 리뷰하고 잘못 디자인한 코드를 수정하는 과정을 반복해야겠다..



킥스타터 오픈소스: [kickstarter/ios-ossios-oss - Kickstarter for iOS. Bring new ideas to life, anywhere.github.com](https://github.com/kickstarter/ios-oss?source=post_page-----bb7576e23c65----------------------)



> 킥스타터 오픈소스는 테스트 코드까지 모두 다 확인이 가능하기 때문에 매우 도움이 되었다.
>
> 1. 뷰 모델이 input, output protocol 을 나누어 분리하고 테스트 코드는 protocol 을 테스트 하는 형태로 구성되어있다.
> 2. AppDelegateViewModel 에 presentViewController 시그널을 받도록 되어있고, 여기서 viewController present 를 수행한다.



------



## 실제 프로젝트에서 구성한 내용(1)

MVVM 구조에 대한 튜토리얼을 진행한 후 예전 MVC 구조를 가지고 있던 프로젝트에 적용을 해보고자 하였다... 공부가 많이 되지 않은 상태에서 바로 적용하려 하다 보니 뷰 모델 본래의 역할을 충실할 수 있도록 분리 하지도 못했고 이전의 코드를 다시 보니 굉장히 난해하다고 생각이 들었다. 

![](https://user-images.githubusercontent.com/20294786/68688842-ce332f80-05b2-11ea-8e0c-b15ef641ea1f.png)



아래의 내용에 대해 프로젝트 내 코드 일부를 발췌해서 설명하려 한다.

- 모델의 구성
- 뷰모델의 구성
- 뷰모델을 통해 뷰와 모델 연결
- 테스트 코드(미루지 말자...)



### 모델의 구성

기존의 이 프로젝트는 영화 정보를 가져와 리스트 형태로 보여주며 각 영화의 한줄평을 포함한 상세 정보를 제공해주는 앱이다. API 통신을 위해 `Movie`와 `Comment` 라는 모델을 만들었다.



### 뷰모델의 구성

뷰 모델을 구성하기전에 뷰 모델이 갖는 의미에 대해서 생각해볼 필요가 있다.



규원님의 글을 보면 

> 뷰는 자신이 가진 상태를 사용자에게 표현할 뿐 아니라 사용자가 응용프로그램에 명령을 내릴 수단을 제공한다. 뷰모델은 이런 기능을 추상화하기 위해 명령(Commands)을 가진다. 명령을 통해 사용자는 모델의 행위를 실행할 수 있다.

라는 부분이 있는데 

이해한 바로는 "개념적으로 사용자와 상호작용하며 자신의 상태를 사용자에게 표현하고, 응용프로그램에 명령을 내릴 수 있는 수단을 제공하는 것으로써 뷰를 추상화한 것. "이다.



iOS의 MVVM에서 View가 ViewController가 된다고 생각한다. 따라서 ViewController를 추상화하는 것으로 생각할 수 있을 것 같다...

(하지만, 단순히 ViewController의 책임을 ViewModel이 하게 된다면 의미가 없을 것이다.)



따라서 하나의 화면에서 사용자가 원하는 데이터 그리고 취할 수 있는 행동들을 기반으로 ViewModel들을 만들었다.



대표적인 ViewModel을 하나 살표보자.

```swift
protocol MovieListViewModel {
    var movieOrderType: Dynamic<MovieOrderType> { get }
    var movies: [MovieViewModel] { get }
    
    func changeMovieOrderType(to orderType: String)
}
```



`MovieListViewModel`은 영화 리스트를 보여주는 화면이다. 해당 화면에서 사용자가 원하는 데이터는 영화 리스트이며, 취할 수 있는 행동은 영화 리스트의 정렬 방법을 선택하여 리스트의 순서를 변경하는 것이다. 



뷰에서 MovieListViewModel 타입의 프로퍼티를 통해서 영화 리스트를 출력할 수 있고, 또한 사용자가 변경 시킨 정렬 순서에 따라서 해당 화면의 UI를 변경 시킬 수 있다. 



### 뷰모델을 통해 뷰와 모델 연결

```swift
class MovieTableViewController: UITableViewController {

	...
    
    private var movieListViewModel: MovieListViewModel?
    
    ...
```

우선, 뷰에서 사용자의 니즈를 제공할 수 있는 ViewModel(movieListViewModel)을 프로퍼피로 설정한다.

```swift
movieListViewModel.movieOrderType.bind() { [weak self] newOrderType in
            guard let self = self else { return }
            
            DispatchQueue.main.async {
                self.navigationItem.title = newOrderType.navigationItemTitle
                self.fetchMovieList()
            }
        }
```

사용자가 액션을 취했을 때 적절하게 UI를 변경해주기 위해 bindging을 해준다.



```swift
// 사용자가 영화 정렬 순서를 변경하였을 때 호출된다.
@objc private func movieOrderTypeDidChange() {
    
    ... 
    
    movieListViewModel.changeMovieOrderType(to: newOrderType)
    
    ...
    
}
```

사용자의 액션에 의해 ViewModel에 명령이 보내진다. 



정말 많이 미흡하지만 기존의 MVC에서 각 화면이 갖는 역할을 ViewModel로 분리함으로써 View의 역할을 명학이 할 수 있었던 것 같다. MVVM에 대해 공부해 가면서 지속적으로 개선시켜봐야겠다. 



### 테스트 코드..

MVVM의 최대 장점이 테스터블한 코드를 작성할 수 있다는 것이다. 



(테스트를 하지 않으면 의미가 없을 정도..)



추후 테스트도 붙여볼 것이다. 









출처: [Wade's Blog](https://medium.com/@junhyi.park/mvvm-학습-정리-bb7576e23c65)