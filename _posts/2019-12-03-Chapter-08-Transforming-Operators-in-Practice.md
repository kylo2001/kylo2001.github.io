---
layout: post
title:  " Chapter 08 - Transforming Operators in Practice "
categories: Reactive
tags: RxSwift
author: GGuJi


---


* content
{:toc}


## 시작하기

RxSwift repository의 최근 활동이 궁금할 때 어떻게 확인할 수 있을까? 여기서는 GitHub repository의 활동을 보여주는 예제를 작성할 것이다.

GitHub의 JSON API와 연결하여 가장 최근 패치된 활동을 보여주는 앱을 만들 것이다. 만약 RxSwift repository가 아닌 다른 repository 확인을 원한다면 그렇게 해도좋다.

이 프로젝트는 다음과 같은 두 가지 스토리 라인이 있을 것이다.

1. 메인으로는 GitHub JSON API에 연결해서 JSON 응답을 받는다. 받은 응답을 객체 collection으로 변환한다.
2. 서버에서 새로운 활동 이벤트가 패치되기 전까지는 테이블뷰가 기존에 패치되어 디스크에 저장된 내용을 표시하도록 한다.



## 웹에서 데이터 패치하기

지난 예제를 통해서 웹 URL과 파라미터를 포함하는 `URLRequest`를 생성한 다음, 이를 인터넷으로 보내고 서버의 응답을 받는 작업을 해봤다.

RxSwift와 기본 RxCocoa의 `URLSession` extension을 사용하여 GitHub API의 JSON을 빠르게 패치할 수 있다.

기존에 URLSession API를 사용해 보았으며 워크 플로에 대한 일반적인 아이디어가 있습니다. 요약하면 웹 URL과 매개 변수가 포함된 URLRequest를 만든 다음 인터넷으로 보냅니다. 약간 후에 서버 응답이 수신 됩니다.
현재 RxSwift에 대한 지식이 있으면 URLSession 클래스에 반응형 확장을 추가하는 것이 어렵지 않습니다. `17 장 "사용자 정의 리 액티브 확장 만들기"`에서 URLSession에 적절한 리액티브 확장을 추가하는 것으로 구체적으로 살펴 보겠지만, 이 장에서는 `RxSwift`의 동반 라이브러리인 `RxCocoa` 함께 박스형 솔루션을 사용합니다.

![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/08_Transforming%20Operators%20in%20Practice/1.%20RxExtension.png?raw=true)



RxCocoa는 RxSwift를 기반으로하는 라이브러리로, Apple 플랫폼에서 RxSwift를 개발하는데 도움이되는 많은 유용한 API를 구현합니다. RxSwift 자체를 RxJS, RxJava 및 RxPython과 같은 모든 구현간에 공유되는 공통 Rx API에 최대한 가깝게 유지하기 위해 모든 "추가 기능"이 RxCocoa로 분리됩니다. 이에 대해서는 12 장과 13 장에서 더 자세히 배울 것입니다.



이 장에서는 기본 RxCocoa URLSession Extension을 사용하여 GitHub의 API에서 JSON을 빠르게 가져올 것 입니다.



### 1. 리퀘스트 작성을 위해 map 사용하기

첫 번째로해야 할 일은 GitHub 서버로 보낼 URLRequest를 작성하는 것입니다. 즉시 이해가 되지 않는 반응형 접근 방식을 따르지만 걱정하지 마십시오. ~~나중에 다시 봐라~~
ActivityController.swift를 열고 내부를 들여다보십시오. viewDidLoad ()에서 ViewController의 UI를 구성하고 완료되면 `refresh()`를 호출합니다. `refresh()`는 `fetchEvents(repo :)`를 호출하고 "ReactiveX / RxSwift"라는 repo 이름을 전달합니다.



`fetchEvents(repo:)` 내부에 다음과 같이 입력한다.

```swift
let response = Observable.from([repo])
```

웹 리퀘스트는 repository의 `full name`으로 시작한다. `URLRequest`를 직접 생성하는 것보다 간략한 String으로 시작하는 방법이 observable의 입력값으로써 유연하게 사용될 수 있다. 즉, repository를 변경하더라도 큰 문제가 되지 않는다는 뜻이다. 이에 대한 자세한 내용은 Challenge 에서 다룰 것이다.



주소 string을 가져와서 activity API EndPoint의 완전한 `URL`을 생성한다.

```swift
 .map { urlString -> URL in
         return URL(string: "https://api.github.com/repos/\(urlString)/events")!
```

클로저 축약을 통해 코드를 간단히 할 수 있지만, 여러가지 연산자들을 연달아 쓸 때, 특히 `map`이나 `flatmap`을 함께 사용하는 코드에서는 축약보다 parameter 또는 반환값 타입을 명시하는게 좋을 수 있다. 불일치하거나 누락된 유형에 대한 오류가 표시되면 클로저에 타입정보를 추가할 수 있으니 크게 유의할 부분은 아니다.

여기까지 작성해서 `URL`을 얻었으니, 이제 이를 완전한 리퀘스트 형태로 변형하자. 다음의 코드를 마지막 연산자에 추가하자.

```swift
 .map { url -> URLRequest in
     return URLRequest(url: url)
 }
```



이로써 `map`을 이용해 제공된 웹주소를 통해 `URL`을 `URLRequest`로 변형했다. 다음과 같은 과정을 진행한 것이다.

![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/08_Transforming%20Operators%20in%20Practice/2.%20url%20to%20urlrequest.png?raw=true)



### 2. 웹에서의 리스폰스에 대기하기 위해 flatMap 사용하기

이전 장에서는 flatMap이 Observable sequences를 flatten 하게된다는 것을 배웠습니다. flatMap의 일반적인 기능중 하나는 transformation chain에 비동기 성을 추가하는 것입니다. 그것이 어떻게 작동하는지 봅시다.

여러 변환을 연결하면 해당 작업이 동기적으로 발생합니다. 다시 말해 모든 변환 연산자는 즉시 서로의 결과를 처리합니다. 

즉, 변형연산자*transformation operators* 는 각각의 output에 대해 다음과 같이 진행하게 된다.

![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/08_Transforming%20Operators%20in%20Practice/3.%20map.png?raw=true)

여기에 `flatMap`을 삽입하면 다른 효과를 낼 수 있다.

- 문자열 또는 숫자 array들의 `observable` 같이 일시적으로 요소를 방출하고 완료되는 observable들을 flatten 할 수 있다. ~~이해 안됨~~
- 비동기적으로 작동하는 observable을 통해 효과적으로 observable들이 "대기"하도록 할 수 있고, 그 동안 다른 연결들은 계속 작동하도록 할 수 있다.



다시 말하면 **GitFeed** 프로젝트에서 필요한 작업은 다음 모습과 같다.

![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/08_Transforming%20Operators%20in%20Practice/4.%20use%20flatmap.png?raw=true)

따라서 다음 코드를 추가해주자.

```swift
 .flatMap { request -> Observable<(response: HTTPURLResponse, data: Data)> in
     return URLSession.shared.rx.response(request: request)
 }
```

RxCocoa의 `URLSession`객체 내의 `response(request:)`메소드를 이용했다. 이 메소드는 앱이 웹 서버를 통해 full response를 받을 때마다 complete되는

`Observable<(response: HTTPURLResponse, data: Data)>`를 반환한다.



> **참고**
>
>  인터넷 연결이 없거나, url이 유효하지 않을 때 `response(request:)`는 에러를 낼 수 있다. 이런 에러들에 대해 관리가 필요한데 자세한 내용은 Ch.14에서 다루고 있다.



상기 코드에서 `flatMap`은 웹 리퀘스트를 보내게 해주고 프로토콜이나 델리게이트 없이도 리스폰스를 받을 수 있게 해준다. 간단하게 `map`과 `flatMap` 연산자의 조합을 통해 비동기적인 일련의 코드 작성이 가능한 것이다.



마지막으로 웹 리퀘스트 결과에 대한 더 많은 구독을 허용하기 위해 `share(replay:, scope:)` 연산자를 추가하자.

```swift
 .share(replay: 1, scope: .whileConnected)
```



### 3. share vs. shareReplay

`URLSession.rx.response(request:)`는 서버로 리퀘스트를 보내고 response를 받으면 받은 데이터를 `.next` 이벤트를 통해 단 한번만  방출한 뒤, complete 된다.

만약 observable complete 후 이를 다시 구독하는 상황이 발생하면, 이는 새로운 구독을 생성하고 서버에 별도의 request를 또 보내게 될 것이다. 이 같은 상황을 방지하기 위해 `share(replay:, scope:)`을 사용할 수 있다. 이 연산자는 마지막 `replay`로 방출된 요소를 버퍼로 가지고 있다가 새로운 구독자가 생길 때 이를 제공해준다. 그러므로 요청이 completed되고 새로운 관찰자가 `share(replay:, scope:)`을 통해 shared sequence를 구독한다면, 서버를 통해 이미 가지고 있던 버퍼를 즉시 response로 받을 수 있다.



`scope`에는 두가지 옵션이 있는데 `.whileConnected` 와 `.forever`가 있다.

- `.whileConnected`: 네트워크 리스폰스 버퍼를 아무도 구독하지 않을 때까지만 가지고 있는 것이다. 구독자가 사라지면 버퍼도 사라진다. 이후 새로운 구독자는 새 네트워크 리스폰스를 가질 것이다.
- `.forever`: 네트워크 리스폰스 버퍼를 계속 가지고 있는 것이다. 새로운 구독자는 버퍼 리스폰스를 가진다.

`share(replay:. scope:)`은 complete 할 것으로 예상되는 sequence에 사용해야한다. 이렇게 해야 observable이 다시 생성되는 것을 방지할 수 있다.



##  Response 변형하기

지금까지는 웹 리퀘스트를 보내기 **이전에** `map`을 사용하여 변형을 했다. 지금부터는 리스폰스를 받은 **이후에** 할 작업에 대해 알아볼 것이다.

`URLSession` 클래스가 `Data` 객체를 줬는데 바로 작업할 수 있는 형태가 아니라면 어떨까? 당연히 이 것을 JSON으로 변환하여 코드로 *안전하게* 사용할 수 있도록 해야할 것이다.

`response` observable에 대한 구독을 만들어서 리스폰스 데이터를 객체로 변환할 수 있도록 하자. 하단의 코드를 추가하면 된다.

```swift
 response
     .filter { response, _ in
         return 200 ..< 300 ~= response.statusCode
 }
```

`filter` 연산자를 이용해서 status 코드가 `200` 에서 `300` 사이(성공)일 때 받은 리스폰스만 받도록 한다.

> **참고**
>
>  [HTTP 리스폰스 코드 리스트 확인하기](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)

또한 관찰 가능 이벤트에 오류 이벤트를 보내는 대신 성공하지 못한 상태 코드를 무시합니다. 이것은 코드를 간단하게 유지하기위한 스타일 선택이지만, 다음 장에서는 Rx를 사용한 오류 전파가 얼마나 쉬운 지 살펴볼 것입니다.
수신되는 데이터는 일반적으로 이벤트 객체 목록이 포함 된 JSON 인코딩 서버 응답입니다. Decodable 호환 구조체를 사용하여 수신 한 데이터 응답을 시도하고 디코딩합니다.

시작 프로젝트에서 Event.swift를 열면 Codable 프로토콜을 이미 준수하는 Event 구조체가 표시됩니다. ActivityController.swift로 돌아가서 마지막 운영자 체인에 다른 맵을 추가하십시오. 

받은 데이터는 이벤트 객체 목록을 포함한 JSON 인코딩된 서버 리스폰스일 것이다. 이렇게 받은 리스폰스 데이터를 `Array>` 타입으로 변환하자.

```swift
.map { _, data -> [Event] in
  let decoder = JSONDecoder()
  let events = try? decoder.decode([Event].self, from: data)
  return events ?? []
}
```

이전에 수행했던 작업과 달리 `response`가 아닌 `data` 만 가져옵니다.

JSONDecoder를 만들고 응답 데이터를 이벤트 배열로 디코딩하려고합니다.

`decode`가 실패하면 빈 array를 반환한다.



RxSwift가 연산자를 사용하여 이러한 개별 작업을 캡슐화하도록 강요하는 것은 정말 멋진 일입니다. 

또한 추가적인 이점으로, 컴파일 타임에 항상 입력 및 출력 유형을 확인해야합니다.



API 응답 처리가 거의 완료되었습니다. UI를 업데이트하기 전에해야 할 일이 몇 가지 있습니다. 먼저 이벤트 객체가 포함되지 않은 응답을 필터링해야 합니다. 다음 코드를 체인에 추가해 보세요.

```swift
.filter { objects in
    return !objects.isEmpty
}
```

이렇게하면 마지막 응답 이후에 오류 응답 또는 새 이벤트가 포함되지 않은 응답이 삭제됩니다. 이 장의 뒷부분에서 새로운 이벤트만 가져 오기를 구현하지만 이 상태 나중의 이해를 도울 수 있습니다.



<img src="https://user-images.githubusercontent.com/20294786/70115981-c42db980-16a4-11ea-95e5-664de3e89c29.png">

마지막으로,이 겉보기 끝없는 변환 체인을 마무리하고 UI를 업데이트 할 차례입니다. 코드를 단순화하기 위해‘UI 코드를 별도의 방법으로 작성합니다. 지금은이 코드를 최종 연산자 체인에 추가하기 만하면됩니다.



```swift
.subscribe(onNext: { [weak self] newEvents in
  self?.processEvents(newEvents)
})
.disposed(by: bag)
```



### 1. Processing the response

좋습니다, 이제 부수적인 작업을 수행 할 차례입니다. 간단한 문자열로 시작하여 웹 요청을 작성하여 GitHub로 보낸 후 응답을 받았습니다. 응답을 JSON으로 변환 한 다음 기본 Swift 오브젝트로 변환했습니다. 이제 사용자에게 보여줄 차례입니다.

ActivityController에는 이미 처리 할 수있는 processEvents (_ :)라는 placeholder  메서드가 포함되어 있습니다. 이 메소드에서는 리포지토리의 이벤트 목록에서 마지막 50 개의 이벤트를 가져 와서 목록 컨트롤러의 주제 속성 이벤트에 목록을 저장합니다. sequences를 subjects에 직접 바인딩하는 방법을 아직 배우지 않았으므로 지금은 수동으로 수행해야합니다.

```swift
 func processEvents(_ newEvents: [Event]) {
    var updatedEvents = newEvents + events.value

    if updatedEvents.count > 50 {
        updatedEvents = [Event](updatedEvents.prefix(upTo: 50))
    }

    events.accept(updatedEvents)
 }
```



events.accept (_ :)를 사용하여 새로 가져온 이벤트를 목록에 추가합니다. 또한 목록을 50 개의 개체로 제한합니다. 이렇게하면 테이블뷰에 최신 활동만 표시됩니다.

마지막으로 이벤트 값을 설정하고 UI를 업데이트 할 준비가되었습니다. 데이터 소스 코드가 ActivityController에 이미 포함되어 있으므로 테이블뷰를 다시 로드하여 새 데이터를 표시하기 만하면 됩니다. processEvents (_ :) 끝에 다음 행을 추가하십시오.



```swift
    ...

    tableView.reloadData()
}
```

불행히도 RxSwift를 사용하여 스레드를 관리하는 것에 대해서는 아직 고려하지 않았으므로 권장되는 방법은 아니지만 GCD를 사용하여 기본 스레드로 전환하고 테이블을 업데이트하십시오. reloadData ()에 대한 호출을 다음과 같이 랩하십시오.

```swift
DispatchQueue.main.async {
  self.tableView.reloadData()
}
```



마지막으로 레포 이벤트를 가져온 후 누군가가 레포를 포크하거나 좋아하면 새 셀이 맨 위에 나타납니다.
현재는 테이블뷰를 풀 때 약간의 문제가 있습니다. 앱이 API에서 데이터 가져 오기를 완료 한 경우에도 새로 고침 컨트롤이 사라지지 않습니다.

이벤트 가져 오기가 완료되면 숨기려면 self.tableView.reloadData () 바로 아래에 다음 코드를 추가하십시오. 

```swift
self.refreshControl?.endRefreshing()
```



여기까지 `map`과 `flatMap`의 사용을 잘 이해하고 있어야 합니다.

이번 장의 도전과제에서 현재 느슨한 상태의 코드를 개선시켜야 합니다.



## 디스크에 객체 유지하기

이 섹션에서는 서론에 설명 된대로 서브 플롯에 대해 작업 할 것입니다. 여기서 개체는 디스크에 유지되므로 사용자가 앱을 열면 마지막으로 가져온 이벤트를 즉시 볼 수 있습니다.



<img src = "https://user-images.githubusercontent.com/20294786/70118001-453b7f80-16aa-11ea-9d4a-87144debbd83.png">

이 예에서는 이벤트를 .plist 파일에 유지하려고합니다. 저장하려는 객체의 양이 적으므로 .plist 파일이면 충분합니다. 이 책의 뒷부분에서 데이터를 유지하는 다른 방법에 대해 배우게 됩니다. 예를 들어, 21 장, "RxRealm"의 Realm 모바일 데이터베이스 사용.



`ActivityController` 클래스에 다음과 같이 새로운 프로퍼티를 추가한다.

```swift
private let eventsFileURL = cachedFileURL("events.json")
```

eventsFileURL은 이벤트 파일을 기기의 디스크에 저장하는 파일 URL입니다. cachedFileURL 함수를 구현하여 파일을 읽고 쓸 수있는 URL을 가져와야 합니다. 이것을 뷰 컨트롤러 클래스의 정의 외부에 추가하십시오.

```swift
func cachedFileURL(_ fileName: String) -> URL {
  return FileManager.default
    .urls(for: .cachesDirectory, in: .allDomainsMask)
    .first!
    .appendingPathComponent(fileName)
}
```

`processEvents(_:)`로 이동하여 하단에 다음 코드를 추가한다.

```swift
let encoder = JSONEncoder()
if let eventsData = try? encoder.encode(updatedEvents) {
  try? eventsData.write(to: eventsFileURL, options: .atomicWrite)
}
```

이 코드에서는 updatedEvents를 Data 객체로 인코딩하려고합니다. 다음으로, 결과 데이터와 함께 write (to : options :)를 호출하고 파일을 작성하거나 기존 파일을 겹쳐 쓰려는 파일의 URL을 제공하십시오.



좋아요! processEvents (_ :)는 부수 작용을 수행 할 수 있는 곳이므로 디스크에 이벤트를 작성하는 것이 좋습니다. 그러나 디스크에서 저장된 이벤트를 읽는 코드를 어디에 추가 할 수 있습니까?

파일에서 객체를 한 번만 다시 읽어야하므로 viewDidLoad ()에서 수행 할 수 있습니다. 이벤트가 저장된 파일이 있는지 확인하고, 있는 경우 해당 컨텐츠를 이벤트에 로드합니다.
viewDidLoad ()로 스크롤하여 refresh () 호출 바로 위에 추가하십시오.

```swift
let decoder = JSONDecoder()
if let eventsData = try? Data(contentsOf: eventsFileURL),
  let persistedEvents = try? decoder.decode([Event].self, from: eventsData) {
  events.accept(persistedEvents)
}
```

이 코드는 객체를 디스크에 저장하는 데 사용한 코드와 유사하게 작동하지만 그 반대입니다.
먼저 디스크에서 저장소 데이터를 읽습니다. 그런 다음 `JSONDecoder`를 작성하고 데이터를 이벤트 배열로 다시 디코딩하려고 시도합니다. `accept` 메소드를 사용하여 이벤트 릴레이에 이벤트 배열을 추가하거나 오류가 발생하면 빈 배열을 추가합니다. 이벤트를 디스크에 유지 했으므로 모두 유효해야 하지만 안전이 최우선입니다!



그렇게해야합니다. 시뮬레이터 또는 작업중인 장치에서 앱을 삭제하십시오. 그런 다음 앱을 실행하고 이벤트 목록이 표시 될 때까지 기다린 다음 Xcode에서 중지하십시오. 프로젝트를 두 번째로 실행하고 앱이 웹에서 최신 이벤트를 가져 오는 동안 테이블뷰에 이전 데이터가 즉시 표시되는 방식을 관찰하십시오.



## Request에 마지막으로 수정된 헤더 추가

`flatMap`과 `map`을 한 번 더 연습해봅시다. 그냥 얘네가 참 중요해여..



여기서는 이 전에 반입하지 않은 이벤트만 요청하도록 **GitFeed**를 최적화 할 것이다. 이렇게 하면 트래킹하는 repository가 아무도 fork, like 하지 않은 놈이라면 서버에서 빈 응답만 받을 것이다. 이렇게 하면 네트워크 트래픽과 처리 능력을 절약할 수 있다.



먼저 `ActivityController`에 파일을 저장하기 위해 새로운 프로퍼티를 추가한다.

```swift
private let modifiedFileURL = cachedFileURL("modified.txt")
```

이번에는 `.plist` 파일이 필요하지 않습니다. 기본적으로 `Mon, 2017 년 5 월 30 일 04:30:00 GMT`와 같은 단일 문자열을 저장해야하기 때문입니다. 서버가 JSON 응답과 함께 보내는 `Last-Modified`라는 헤더 값입니다. 다음 요청과 함께 동일한 헤더를 서버로 다시 보내야합니다. 이렇게하면 마지막으로 가져온 이벤트와 그 이후로 새로운 이벤트가 있는지 파악하기 위해 서버에 남겨 둡니다.



![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/08_Transforming%20Operators%20in%20Practice/8.%20server.png?raw=true)



이전에 이벤트 목록에서 했던 것처럼 `subject를` 사용하여 `Last-Modified` 헤더를 추적합니다. ActivityController에 다음 새 특성을 추가하십시오.

```swift
private let lastModified = BehaviorRelay<String?>(value: nil)
```



다음 코드를 `viewDidLoad()`의 `refresh()` 이전에 추가하자.

```swift
if let lastModifiedString = try? String(contentsOf: modifiedFileURL, encoding: .utf8) {
  lastModified.accept(lastModifiedString)
}
```

만약 `Last-Modified` 헤더 값이 파일에 이미 저장되어있다면 `String(contentsOf :)`를 사용하여 다시 가져옵니다. 가져온 데이터를 사용하여 선택적으로 문자열을 생성한 다음 lastModified에 전달하여 교체합니다.

오류 응답 필터링부터 시작하십시오. `fetchEvents()`로 이동하고 메소드 하단에 다음 코드를 추가하여 `response observable`에 대한 두 번째 구독을 작성하십시오.

```swift
 .filter { response, _ in
     return 200 ..< 400 ~= response.statusCode
 }
```

이제 `filter`, `map`, (그리고 한번 더) `filter`를 이용해서 다음과 같은 작업을 해야한다.

- `Last-Modified` 헤더를 포함하지 않는 모든 응답을 필터링하십시오.
- 헤더 값을 가져옵니다.
- 헤더 값을 고려하여 sequence를 한 번 더 필터링하십시오.



복잡한 작업처럼 들리며 `filter`, `map`, 또 다른 `filter` 등을 사용할 계획을 생각해낼 수 있다. 하지만, 이 섹션에서는 하나의 `flatMap`을 사용하여 sequence를 쉽게 필터링할 것이다.

`flatMap`을 사용하여 `Last-Modified` 헤더가 없는 응답을 필터링 할 수 있습니다.



이것을 위에서 operator chain에 추가하십시오.

```swift
.flatMap { response, _ -> Observable<String> in
  guard let value = response.allHeaderFields["Last-Modified"] as? String else {
    return Observable.empty()
  }
  return Observable.just(value)
}
```

guard를 사용하여 응답에 `Last-Modified`라는 이름의 HTTP 헤더가 포함되어 있는지 확인합니다. 이 헤더의 값은 문자열로 캐스트 될 수 있습니다.
캐스트를 할 수 있으면 단일 요소와 함께 Observable <String>을 반환합니다. 그렇지 않으면 어떤 요소도 방출하지 않는 Observable을 반환합니다.



![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/08_Transforming%20Operators%20in%20Practice/9.%20guard.png?raw=true)



이제 필요한 헤더 값을 얻었으므로 `lastModified` 프로퍼티를 업데이트하고 디스크에 값을 저장할 차례다. 다음을 추가하자.

```swift
.subscribe(onNext: { [weak self] modifiedHeader in
  guard let self = self else { return }

  self.lastModified.accept(modifiedHeader)
  try? modifiedHeader.write(to: self.modifiedFileURL, atomically: true, encoding: .utf8)
})
.disposed(by: bag)
```

구독의 onNext 마감시 accept (_) 메소드를 사용하여 lastModified 릴레이에 최신 날짜를 추가 한 다음 modifiedHeader.write (to : atomically : encoding :)을 호출하여 디스크에 저장합니다. 결국 뷰 컨트롤러의 처리 백에 구독을 추가합니다.
이 부분의 앱 작업을 마치려면 GitHub API 요청에 저장된 헤더 값을 사용해야합니다. `fetchEvents (repo :)` 상단으로 스크롤하여 아래에서 URLRequest를 작성하는 특정 `map`을 찾으십시오.

```swift
.map { url -> URLRequest in
  return URLRequest(url: url)
}
```



이 부분을 다음의 코드로 대체하자.

```swift
.map { [weak self] url -> URLRequest in
  var request = URLRequest(url: url)
  if let modifiedHeader = self?.lastModified.value {
    request.addValue(modifiedHeader, 
      forHTTPHeaderField: "Last-Modified")
  }
  return request
}
```



이 새로운 코드에서는 이전과 마찬가지로 URLRequest를 작성하지만 `lastModified` 값이 파일을 통해서 로드 되었는지 아니면 JSON을 가져온 후 저장했는지에 구분할 수 있도록 추가 조건을 추가해야 합니다. 

![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/08_Transforming%20Operators%20in%20Practice/10.%20lastModified.png?raw=true)

이렇게 헤더를 추가함으로써 GitHub에게 이 헤더보다 오래된 이벤트에 대해서는 관심이 없다는 것을 알려줄 수 있다. 이 작업은 트래픽을 저장하지 않게 해줄 뿐만 아니라, 데이터를 반환하지 않기 때문에 GitHub API의 사용 제한 수를 증가하지 않는 효과도 있다.



이 장에서는 `map`과 `flatMap`에 대한 다양한 실제 사용 사례에 대해 배웠고 여전히 main thread (스마트 프로그래머와 같은)에서 결과를 처리해야하더라도 멋진 프로젝트를 구축했습니다.

그러나 여전히 더 잘할 수 있습니다! 과제 섹션에서는 백그라운드 스레드에서 변환을 수행하고 기본 스레드로 전환하여 UI 업데이트를 수행 할 수 있도록 스레딩 전략을 프로젝트에 추가하는 작업을 수행합니다. 이렇게하면 앱이 빠르게 반응합니다.
추가 과제에서 더 많은 `map`과 `flatMap`을 믹스에 던져서 프로젝트를 쉽게 확장 할 수 있는 방법을 알게 될 것입니다.

과제를 해결 한 후에는 다음 장으로 넘어 가서 더 복잡한 구독을 크게 단순화하기 위해 연산자 결합에 대해 배우게 됩니다.



## Challenges

### 최상단의 repository를 패치하고 피드하기

이 연습문제를 통해 `map/flatMap` 사용을 한 번 더 해볼 것이다.



주어진 repository에 대해 최근 활동을 매번 패칭하는 대신, 가장 인기있는 `Swift 리포지토리`를 찾아 앱에 반영된 활동을 표시합니다.

![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/08_Transforming%20Operators%20in%20Practice/11.%20top%20trending.png?raw=true)



딱 봤을 때는 이 작업이 매우 복잡해 보이겠지만, 이 작업은 12줄의 코드면 해결 가능하다.

시작하기 전에, `fetchEvents(repo:)`의 `let response = Observable.from([repo])`를 다음과 같이 변경하자.

```swift
let response = Observable.from(["https://api.github.com/search/repositories?q=language:swift&per_page=5"])
```

이 API EndPoint에서는 가장 인기있는 Swift repository 중 상위 5개의 리스트를 반환할 것이다. 별도의 순서 파라미터를 입력하지 않고 API를 호출하였기 때문에 GitHub는 각 항목의 검색어와 관련이 있는 "`score`"로 반환된 결과를 정렬 합니다.

> **참고**
>
> score 프로퍼티 -  Github 만의 비밀스러운 놀라운 연산 프로퍼티이다.
>
> 
>
> GitHub JSON API는 함께 사용할 수있는 훌륭한 도구입니다. 트렌딩 리포지토리, 공개 활동 등과 같은 매우 흥미로운 데이터를 얻을 수 있습니다. 자세한 내용을 보려면 API 홈페이지 (https://developer.github.com/v3/)를 방문하십시오.



1. 이제 챕터에서 했던 것과 똑같은 방식으로 해당 문자열을 URL로 변환하고 URLRequest로 변환하십시오. ~~Last-Modified 헤더를 포함 할 필요가 없습니다.~~

2. Response 헤더가 필요하지 않기 때문에 `URLSession.shared.rx.json (request :)`을 사용할 수 있습니다. 이는 원시 데이터 대신 변환 된 JSON을 직접 반환하는 방법입니다.

3. 마지막 단계로  [String: Any] Dictionary 형태로 JSON response을 받아와 해당 item들의 key를 가져와야 합니다. item들에는 각각의 인기 저장소들을 나타내는 [String: Any] Distionary가 포함되어야 합니다. 이들 각각의 `full_name`이 필요합니다.

   `icanzilb/EasyAnimation`, `realm/realm-cocoa`, `ReactiveX/RxSwift` 등과 같은 `user name/repository name`을 포함하는 저장소 이름입니다.

   

4. flatMap을 사용하고 이러한 과정 중 하나라도 실패하면 이전과 마찬가지로 Observable.empty ()를 반환하십시오. 계획에 따라 모든 것이 진행된다면, 트렌드 저장소의 `full_name` 목록에서 생성 된 Observable <String>을 반환하십시오.



이제 기존 코드를 해당 flatMap에 연결할 수 있습니다.

```swift
let response = Observable.from(["https://api.github.com/search/repositories?q=language:swift&per_page=5"])

[map to convert to to URLRequest]

[flatMap to fetch JSON back]

[flatMap to convert JSON to list of repo names, 
  and create Observable from that list]

[existing code follows below]

.map { urlString -> URL in
  return URL(string: "https://api.github.com/repos/\(urlString)/events?per_page=5")!
}
.map { [weak self] url -> URLRequest in
  var request = URLRequest(url: url)
  ...
} 
```

이제 앱을 구동하고 테이블뷰를 pull to refresh 할 때마다 앱은 최근 가장 인기있는 5개의 Swift repository를 보여줄 것이며, 5개의 서로 다른 repository에서 발생하는 각각의 이벤트들을 패치하기 위해 GitHub에 서로 다른 리퀘스트를 보낼 것이다. 동일한 repository의 이벤트가 너무 많아지면 URL에 `per_page=5` 쿼리 매개변수를 추가하여 서버 응답을 제한 할 수 있다.



이제 앱을 시작하거나 테이블을 가져와 새로 고칠 때마다 앱은 상위 5 개의 Swift 리포지토리 목록을 가져온 다음 GitHub에 5개의 서로 다른 요청을 실행하여 각 리포지토리에 대한 이벤트를 가져옵니다.
동일한 저장소에서 너무 많은 이벤트가 표시되면 per_page = 5 쿼리 매개 변수를 URL에 추가하여 서버 응답을 제한 할 수 있습니다. 그런 다음 이벤트를 로컬로 저장하고 최신 데이터로 테이블을 업데이트합니다.



<img src = "https://user-images.githubusercontent.com/20294786/70122682-6dc87700-16b4-11ea-9f72-bd8cf08d40fb.png" width = "70%">



더 많은 것을 즐기고 싶다면 날짜 및 기타 흥미로운 방법으로 결합된 이벤트 목록을 정렬 할 수 있습니다. 다른 유형의 정렬 또는 필터링을 할 수 있습니까?

이 도전을 성공적으로 마무리했다면 자신을 혁신 전문가로 생각할 수 있습니다! 

오 ...  `map`만 사용하여 납을 금으로 바꿀 수 있다면, 그것은 정말로 무언가 일 것입니다! 그러나 RxSwift를 사용한 데이터 변환은 매우 빠릅니다.



```swift
func fetchRepositories(from repo: String) {
    let response = Observable.from(["https://api.github.com/search/repositories?q=language:swift&per_page=5"])
      .map { urlString -> URL in
        return URL(string: urlString)!
      }
      .map { url -> URLRequest in
        return URLRequest(url: url)
      }
      .flatMap { request -> Observable<Any> in
        return URLSession.shared.rx.json(request: request)
      }
      // response가 [String: Any]타입의 Dictionary로 온다.
      // 그 중에 key가 "items"인 것이 바로 trending Repo들이다.
      // items의 value는 [String: Any]타입의 Array 인데,
      // 각 item 마다 74개의 요소를 가지고 있으며,
      // repo의 이름을 얻기 위해 "full_name" key를 사용한다.
      .flatMap { response -> Observable<String> in
        guard let response = response as? [String: Any],
          let trendingRepos = response["items"] as? [[String: Any]] else {
            return Observable.empty()
        }
        return Observable.from(trendingRepos.map { $0["full_name"] as! String })
      }
      .map { urlString -> URLRequest in
        let url = URL(string: "https://api.github.com/repos/\(urlString)/events?per_page=5")!
        let request = URLRequest(url: url)
        return request
      }
      .flatMap { request -> Observable<(response: HTTPURLResponse, data: Data)> in
        return URLSession.shared.rx.response(request: request)
      }
      .share(replay: 1, scope: .whileConnected)
    
    response
      .filter { response, _ in
        return 200 ..< 300 ~= response.statusCode
      }
      .map { _, data -> [Event] in
        let decoder = JSONDecoder()
        let events = try? decoder.decode([Event].self, from: data)
        return events ?? []
      }
      .filter { object in
        return !object.isEmpty
      }
      .subscribe(onNext: { [weak self] newEvents in
        self?.processEvents(newEvents)
      })
      .disposed(by: bag)

    response
      .filter { response, _ in
          return 200 ..< 400 ~= response.statusCode
      }
      .flatMap { response, _ -> Observable<String> in
        guard let value = response.allHeaderFields["Last-Modified"] as? String else {
          return Observable.empty()
        }
        return Observable.just(value)
      }
      .subscribe(onNext: { [weak self] modifiedHeader in
        guard let self = self else { return }

        self.lastModified.accept(modifiedHeader)
        try? modifiedHeader.write(to: self.modifiedFileURL, atomically: true, encoding: .utf8)
      })
      .disposed(by: bag)
    }
```

