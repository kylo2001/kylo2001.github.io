---
layout: post
title:  " Chapter 04 - Observables & Subjects in Practice "
categories: Reactive
tags: RxSwift
author: GGuJi

---


* content
{:toc}
## ViewController에서 Variable 이용하기

다음 코드를 **MainViewController.swift**의 `MainViewController` 내에 입력합니다.

```swift
 private let bag = DisposeBag()
 private let images = BehaviorRelay<[UIImage]>([])
```

- 다른 클래스끼리 통신하지 않는다면 `private`로 정의할 것

- 뷰컨이 모든 observable을 dispose 할 때부터, dispose bag은 뷰컨이 소유한다.

  ![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/04_ObservablesAndSubjectsInPractice/1.memoryControl.png?raw=true)

  - 위 그림은, Rx가 메모리 관리를 얼마나 쉽게 하는지 보여주고 있다.
  - 그냥 `bag`에 구독을 던져놓으면, viewController가 할당 해제 될 때 폐기된다.
  - 단, rootVC 같은 특정 뷰컨에서는 이런 작용이 일어나지 않는다. rootVC은 앱이 종료되기 전까진 해제되지 않기 때문이다.

`actionAdd()`에 다음 코드를 입력하세요

```swift
let newImages = images.value
  + [UIImage(named: "IMG_1907.jpg")!]
images.accept(newImages)
```

- 일반적인 variable과 마찬가지로 `images`의 현재값을 변경한
- `BehaviorRelay` 클래스는 자동적으로 자신의 `accept` 메소드를 통해 부여한 값에 대해 각각의 observable 시퀀스를 생성해낸다.
- `images BehaviorRelay`의 초기값은 빈 array 고, 유저가 `+` 버튼을 누를 때마다 `images`를 통해 만들어진 observable 시퀀스가 새로운 어레이를 `.next` 이벤트로 방출한다.



유저가 현재 선택을 취소할 수 있도록, `actionClear():`에 하기 코드를 추가한다.

```swift
 images.accept([])
```



## 콜라주에 사진 추가하기

- 이제 `image`가 연결되었으므로 변경사항을 관찰할 수 있고, 이에 따라서 콜라주 미리보기를 업데이트 할 수 있다.

- `viewDidLoad()`에서, 다음과 같이 `images`에 대해 구독을 추가한다.

- `images`는 varaible이므로 구독을 위해서 `asObservable()` 해야함을 잊지 말자.

  ```swift
    images
        .subscribe(
          onNext: { [weak imagePreview] photos in
            guard let preview = imagePreview else { return }
            
            preview.image = photos.collage(size: preview.frame.size)
        })
        .disposed(by: bag)
  ```

  - `images`가 방출하는 `.next`이벤트를 구독할 수 있고, 이러한 이벤트를 `UIImage.collage(image:size:)` 함수를 거쳐 콜라주를 만들 수 있다.
  - 이 구독을 뷰컨의 dispose bag에 추가한다.

- 이 chapter에서, `viewDidLoad()`의 observable에 대해서 구독을 할 것이지만, 추후에는 다른 클래스와 MVVM 아키텍처에서도 할 수 있다.

- 이제 UI 콜라주가 생겼으니, 유저는 `images`를 `+`버튼을 탭하여 업데이트 하거나 클리어 할 수 있다.



## 복잡한 View Controller UI 구동하기

UI는 다음과 같은 방법으로 UX를 개선할 수 있다.

- 만약 아직 아무 사진도 추가하지 않았거나, `Clear`버튼을 누른 직후라면, `Clear`이 작동하지 않게 할 수 있다.
- 같은 상황에서 `Save` 버튼 역시 필요없다.
- 빈 공간을 남기고 싶지 않다면, 홀수 개의 사진이 추가되었을 때 `Save` 버튼이 작동하지 않게 할 수 있다.
- 사진을 6개까지만 추가하도록 제한할 수 있다.
- ViewController가 현재 선택 개수를 보여줄 수 있다.



이걸 Reactive 하지 않은 기존의 방식으로 하려면 얼마나 긴 코드를 작성해야 할까요? 하지만 Rx에서는 매우 간단합니다.



`viewDidLoad():`내에 아래 코드를 추가한다.

```swift
     images.asObservable()
         .subscribe(onNext: { [weak self] photos in
             self?.updateUI(photos: photos)
         })
         .disposed(by: bag)
```



.updateUI(photos:)` 함수가 없으니 아래 함수를 입력한다.

```swift
    private func updateUI(photos: [UIImage]) {
        buttonSave.isEnabled = photos.count > 0 && photos.count % 2 == 0
        buttonClear.isEnabled = photos.count > 0
        itemAdd.isEnabled = photos.count < 6
        title = photos.count > 0 ? "\(photos.count) photos" : "Collage"
    }
```

- 이 코드는 위에서 나열한 모든 개선을 반영한다. (헐..😳)
- 각각의 로직은 한줄로 표현되어있으며 이해하기 쉽다.

- 지금부터 Rx가 iOS 앱에 적용되었을 때 진짜 어떤점이 좋은지 알 수 있다.



## Subject를 통해 다른 View Controller와 통신하기

여기서 할일은 유저가 카메라롤에 있는 임의의 사진을 선택할 수 있도록 `MainViewController`와 `PhotosViewController`를 연결하는 것이다.

`PhotosViewController`로 push 하기 위해, **MainViewController.swift** 내의 `actionAdd()`에 하단의 코드를 추가한다. 기존에 입력했던 `IMG_1907.jpg` 만을 사용하게 하는 코드는 주석처리 한다.

```swift
   let photosViewController = storyboard?.instantiateViewController(withIdentifier: "PhotosViewController") as! PhotosViewController

     navigationController?.pushViewController(photosViewController, animated: true)
```

이렇게 하고 앱을 실행해보면 (카메라롤 접근허용 창이 뜨고) `photosViewController`로 잘 넘어가는 것을 알 수 있다.

![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/04_ObservablesAndSubjectsInPractice/2.delegate.png?raw=true)

- 기존의 Cocoa 프레임워크를 다음에 해야할 일은 `photosViewController`의 사진들을 `mainViewController`로 서로 통신하기 위해 delegate 프로토콜을 쓰는 것일 것이다. 하지만 이건 매우 Rx 답지아나!
- RxSwift에서는 (이런 ~~그지같은~~ 방법이 아닌) 두개의 **어떠한** 클래스라도 연결할 수 있는 아주 universal 한 방법이 있다. 바로 `Observable`이다! 어떠한 프로토콜도 정의할 필요없다. 왜냐하면 `Observable`은 어떤 종류의 메시지라도 자신을 구독하는 Observer에게 전달할 수 있기 때문이다.



### 1. 선택한 사진에서 Observable 만들기

유저가 카메라롤에 있는 사진을 탭할 때마다 `.next` 이벤트를 방출하는 subject를 `PhotosViewController`에 만들 것이다.

1. **PhotosViewController.swift**내에 `import RxSwift`를 하자.

2. 지금 하고 싶은 것은 선택한 사진을 추출하기 위해 `PublishSubject`를 추가하는 것이다. 

   > 하지만, public하게 접근 허용하긴 싫다. 다른 클래스에서 `onNext(_)`를 호출하여 이 subject가 값을 방출하도록 하면 안되니까. (최소한 이 예제에선)

하단의 코드를 `PhotosViewController`에 추가한다.

```swift
private let selectedPhotosSubject = PublishSubject<UIImage>()
 
var selectedPhotos:Observable<UIImage> {
     return selectedPhotosSubject.asObservable()
 }
```

선택된 사진을 방출할 private한 `PublishSubject`와 subject의 observable을 방출할 `selcectedPhotos` 프로퍼티를 만들었다.

이 프로퍼티를 구독하는 것이 `MainViewController`에서 다른 간섭/변경 없이 사진 sequence를 관찰하는 방법이다.

`PhotosViewController`는 이미 카메라롤에서 사진을 읽고 그것을 콜렉션뷰로 보여주는 코드를 포함하고 있다.

따라서 유저가 콜렉션뷰의 셀(사진)을 탭할 때마다 그 사진들을 방출하는 코드를 작성하는 것이 여기서 해야할 전부이다.

`collectionView(_:didSelectItemAt:)`를 확인해보자. 이 코드는 선택한 이미지를 가져와서 콜렉션셀을 깜박여서 탭했음을 확인할 수 있는 시각적 피드백을 주는 것이다.

`imageManager.requestImage(...)`은 해당 클로저가 잘 작동할 때 선택한 사진의 `image`와 `info` 파라미터를 줄 수 있도록 하는 것이다. 



여기서 `selectedPhotosSubject`를 통해 `.next`이벤트를 방출하는 것이 해야할 일이다.



해당 클로저 내의 `guard`문 하단에 다음과 같은 코드를 추가한다.

```swift
 if let isThumbnail = info[PHImageResultIsDegradedKey as NSString] as? Bool, !isThumbnail {
     self?.selectedPhotosSubject.onNext(image)
 }
```

`info` dictionary를 통해 이미지가 썸네일인지 원본이미지인지 확인할 수 있다.

`imageManager.requestImage(...)`는 해당 클로저를 각각의 사이즈에 대해 한번씩 호출할 것이다.

원본이미지를 받았을 때는 원본 이미지를 제공할 수 있도록 `onNext(_)`이벤트를 subject를 통해 방출한다.

프로토콜을 제거하면, 두 controller의 관계는 다음과 같이 아주 간단해진다.

![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/04_ObservablesAndSubjectsInPractice/3.simple.png?raw=true)



### 2. 선택한 사진들에 대한 Sequence 관찰하기

- 다음으로 해야할 일은 **MainViewController.swift**로 돌아가서 *선택한 사진들에 대한 Sequence 관찰*을 할 수 있는 코드를 작성하는 것이다.

- `actionAdd()`내 navigation 관련 동작을 구현한 코드 다음에 다음과 같은 코드를 작성한다.

  ```swift
   photosViewController.selectedPhotos
       .subscribe(onNext: { [weak self] newImage in
           guard let images = self?.images else { return }
           images.value.append(newImage)
           }, onDisposed: {
               print("completed photo selection")
       })
       .disposed(by: bag)
  ```



### 3. Disposing subscriptions - review

> 여기까지보고 앱을 구동해보면 아주 잘 작동하는 것처럼 보이지만 한가지 간과한 것이 있다.

상기 코드를 보면 분명히 disposed 되었을 때 `"completed photo selection"` 메시지가 콘솔에 프린트되도록 해놓았다. 하지만 콘솔을 확인해보면 해당 메시지는 보이지 않는다. 이건 아직 해당 subject가 dispose 되지 않았다는 뜻이다.

당연하다. 왜냐하면 dispose bag 을 통해 dispose 되도록 명령해놓았고, `MainViewController`가 완전히 할당 해제 되어야만 dispose bag이 dispose 시킬 것이기 때문이다. 이 것이 싫다면 `.completed` 또는 `.error` 이벤트를 방출하므로써 완전 종료될 수 있을 것이다.



따라서, `PhotosViewController`가 사라질 때, 해당 이벤트를 방출하도록 하면 될 것이다. 아래의 코드를 `PhotosViewController`의 `viewWillDisappear(_:)` 에 추가한다.

```swift
 selectedPhotosSubject.onCompleted()
```

이렇게 하면 `PhotosViewController`가 사라질 때마다 해당 subject가 dispose 되는 것을 확인할 수 있다.



## 커스텀한 Observable 만들기

기존의 Apple API를 이용하면, `PHPhotoLibrary`에 대한 extension을 추가할 수 있을 것이다.

하지만 여기선 `PhotoWriter`라는 명칭의, 완전히 새로운 커스텀 클래스를 만들 것이다.

사진 저장을 쉽게 해줄 수 있는 `Observable`을 만들 것이다.



이미지가 디스크에 성공적으로 읽혀졌다면 해당 이미지의 assetID를 방출하거나 `.completed` 또는 `.error` 이벤트를 방출할 수도 있을 것이다.



### 기존의 API 래핑하기

**PhotoWriter.swift**를 열고 `import RxSwift` 한다.

다음의 코드를 작성한다.

```swift
 // 1
     static func save(_ image: UIImage) -> Observable<String> {
         return Observable.create({ observer in

             // 2
             var savedAssetId: String?
             PHPhotoLibrary.shared().performChanges({

                 // 3
                 let request = PHAssetChangeRequest.creationRequestForAsset(from: image)
                 savedAssetId = request.placeholderForCreatedAsset?.localIdentifier
             }, completionHandler: { success, error in

                 // 4
                 DispatchQueue.main.async {
                     if success, let id = savedAssetId {
                         observer.onNext(id)
                         observer.onCompleted()
                     } else {
                         observer.onError(error ?? Errors.couldNotSavePhoto)
                     }
                 }
             })

             // 5
             return Disposables.create()
         })
     }
```

주석을 따라 하나씩 살펴보자

1. `save(_:)` 정적함수를 만든다.

   사진을 저장하려는 코드에 다시 관찰 할 수 있는 Observable 객체가 만들어집니다.  해당 함수는 `Observable`을 리턴할 것이다. 왜냐하면 사진을 저장한 다음에는 생성된 에셋의 local identifier를 방출할 것이기 때문이다.

2. `Observable.create(_)`는 새로운 `Observable`을 생성할 것이기 때문에 어떤 Observable을 생성할 것인지를 이 클로저 내부에서 구현해야 한다.

3. `performChanges(_:completionHandler:)`의 첫 번째 클로저 파라미터에서 제공된 이미지를 통해 에셋을 생성할 것이다. 그리고 두 번째 클로저 파라미터에서 assetID 또는 `.error` 이벤트를 방출하게 될 것이다.

4. `PHAssetChangeRequest.creationRequestForAsset(from:)`을 통해 새로운 에셋를 만들 수 있고 이건 `savedAssetId`에 있는 해당 id로 저장할 것이다.

5. 만약 성공 리스폰스를 받고 `savedAssetId`가 유효한 assetID 라면 `.next`와 `.completed` 이벤트를 방출할 것이다. 그렇지 않다면 `.error` 이벤트를 통해 에러를 방출할 것이다.

6. `Disposible`이 리턴되도록 한다. (`.create`)의 리턴 값



왜 `.next`이벤트를 한번만 방출하는 `Observable`이 필요할까? 의문이 들 수 있다. 



이전 장에서 배운 내용을 잠시 생각해보십시오. 예를 들어 다음 중 하나를 사용하여 Observable을 만들 수 있습니다. 

- `Observable.never()`: 어떤 요소도 방출하지 않는 Observable sequence

- `Observable.just(_:)`: `.completed` 이벤트와 하나의 요소만 방출.
- `Observable.empty()`: `.completed` 이벤트만 방출.
- `Observable.error(_)`: 하나의 `.error` 이벤트만 방출



보시다시피, `Observable`은 0 개 이상의 `.next` 이벤트를 조합하여 `.completed` 또는 `.error`로 종료 될 수 있습니다.
PhotoWriter의 경우 저장 작업이 한 번만 완료되므로 하나의 이벤트에만 관심이 있습니다. 

성공적인 write에는 `.next + .completed`를 사용하고 특정 write에 실패하면 `.error`를 사용합니다.
"Single은 어떻습니까?"라는 비명을 지르면 큰 보너스 포인트가 생깁니다.



## RxSwift trait 연습하기

2 장 "Observable"에서는 RxSwift traits에 대해 배울 수 있었습니다. 특정 경우에 매우 유용한 Observable 구현의 특수 변형입니다.

이 장에서는 빠른 검토를 수행하고 Combinestagram 프로젝트의 일부에서 몇몇의 traits을 사용합니다! Single부터 시작하겠습니다.


### Single

[![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/04_ObservablesAndSubjectsInPractice/4.%20single.png?raw=true)](https://github.com/fimuxd/RxSwift/blob/master/Lectures/04_ObservablesAndSubjectsInPractice/4. single.png?raw=true)

- Single은 `.success(Value)` 이벤트 또는 `.error` 이벤트를 한번만 방출한다.
- `success` = `.next` + `.completed`
- 파일 저장, 파일 다운로드, 디스크에서 데이터 로딩 같이, 기본적으로 값을 산출하는 비동기적 모든 연산에도 유용하다.



#### 사용예시

```swift
PhotoWriter.save(_)
```

1. 이 장 앞부분의 PhotoWriter.save()와 마찬가지로 성공시 정확히 하나의 요소를 방추랗는 랩핑 작업의 경우에 Observable 대신 Singel을 직접 만들수 있습니다. 실제로 PhotoWriter에서 save() 메소드를 업데이트하여 이 장의 과제 중 하나에서 Single을 만듭니다.

2. signle sequence가 둘 이상의 요소를 방출하는지 구독을 통해 확인하면 error가 방출될 수 있다. 즉, 시퀀스에서 단일 요소를 사용하려는 의도를 보다 잘 표현할 수 있다.

   > 모든 Observable을 subscibe하고 .asSingle ()을 사용하여 Single로 변환 할 수 있습니다. 이 섹션을 다 읽은 후에 바로 시도해보십시오. 



### Maybe

`Maybe`는 `Single`과 비슷하지만 유일하게 다른 점은 성공적으로 complete 되더라도 아무런 값을 방출하지 않을 수도 있다는 것이다.

[![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/04_ObservablesAndSubjectsInPractice/5.%20maybe.png?raw=true)](https://github.com/fimuxd/RxSwift/blob/master/Lectures/04_ObservablesAndSubjectsInPractice/5. maybe.png?raw=true)



사진 관련 예제를 이어서 상상해보십시오. 

앱이 사용자 지정 사진 앨범에 사진을 저장하고 있습니다. 이를 위해서 UserDefaults에서 앨범 식별자를 가지 있으며 해당 앨범을 “open”하여 사진을 저장 할 때마다 해당 ID를 사용합니다. 



이 때!! 

```swift
func open(albumId: String) -> Maybe<String> {}
```

 

이 같은 상황을 처리하기 위해 open (albumId :)-> Maybe <String> 메소드를 디자인한다면

다음과 같은 상황을 관리할 수 있다.

- 주어진 ID가 여전히 존재하는 경우, `.completed` 이벤트를 방출한다.
- 유저가 앨범을 삭제하거나, 새로운 앨범을 생성하는 경우 `.next` 이벤트롤 새로운 ID 값과 함께 방출시킨다. 이렇게 함으로써 UserDefaults가 해당 값을 보존할 수 있도록.
- 뭔가 잘못 되었거나 사진 라이브러리에 엑세스할 수 없는 경우, `.error` 이벤트를 방출한다.



`Single` 처럼 직접적으로 Maybe.create({})를 통해서 생성할 수도 있으며, `asSingle`처럼, 어떤 Observable을 `Maybe`로 바꾸고 싶다면, `asMaybe()`를 쓸 수 있다.



### Completable

마지막 traits는 `Completable`입니다. 이러한 Observable 변형은 구독이 dispose되기 전에 하나의 `.completed` 또는 `.error` 이벤트만 발생시킬 수 있습니다. 



[![img](https://github.com/fimuxd/RxSwift/raw/master/Lectures/04_ObservablesAndSubjectsInPractice/6.%20completable.png?raw=true)](https://github.com/fimuxd/RxSwift/blob/master/Lectures/04_ObservablesAndSubjectsInPractice/6. completable.png?raw=true)

> **주의**
>
> 하나 기억해야 할 것은, observable을 completable로 바꿀 수 없다는 것이다.



observable이 값요소를 방출한 이상, 이 것을 completable로 바꿀 수는 없다. 하지만, `ignoreElements()` 연산자를 사용하여 Observable 시퀀스를 Completable로 변환 할 수 있습니다. 이 경우 Completable에 필요한대로 완료되거나 오류 이벤트 만 발생하면 다음 이벤트는 모두 무시됩니다.

> 궁금증
>
> 잘 이해가 안간다. 이해한 바로는 
>
> "통상적으로 Observable은 Completable로 변경할 수 없지만, completed되었거나 error를 방출한 경우라면 ignoreElements() 연산자를 사용하여 Completable로 변경할 수 있다. 단, 모든 다음 이벤트들은 무시된다."



completable sequence를 생성하고 싶으면 `Completable.create({...})`을 통해 생성하는 수 밖에 없다. 이 코드는 다른 observable을 `create`를 이용하여 생성한 방식이랑 매우 유사하다.

`Completeble`은 어떠한 값도 방출하지 않는다는 것을 기억해야 한다. 



솔직히 이런게 왜 필요한가 싶을 것이다.



하지만!!!



동기식 연산의 성공여부를 확인할 때 `completeble`은 아주 많이 쓰인다.

작업했던 `Combinestagram` 예제를 통해 생각해보자.

유저가 작업할 동안 자동저장되는 기능을 만들고 싶다.

background queue에서 비동기적으로 작업한 다음에, 완료가 되면 작은 노티를 띄우거나 저장 중 오류가 생기면 alert을 띄우고 싶다.

저장 로직을 `saveDocument() -> Completable` 에 래핑했다고 가정해보자. 



다음과 같이 표현할 수 있다.

```swift
 saveDocument()
 	.andThen(Observable.from(createMessage))
 	.subscribe(onNext: { message in
 		message.display()
 	}, onError: { e in
 		alert(e.localizedDescription)
 	})
```

> `andThen` 연산자는 성공 이벤트에 대해 더 많은 completables이나 observables를 연결하고 최종 결과를 구독할 수 있게 합니다. 그중 하나라도 오류가 발생하면 코드가 최종 onError 클로저로 넘어갑니다.



## 커스텀한 Observable 구독하기

- `PhotoWriter.save(_)` observable은 새로운 asset ID 또는 Error를 한번만 방출하거나 에러를 방출한다. 따라서 이건 아주 좋은 `Single` 케이스가 될 수 있다.

- **MainViewController.swift**를 열고 `actionSave()`에 아래의 코드를 추가한다. 이 것은 Save 버튼을 눌렀을 때 실행될 액션에 대한 것이다.

  ```swift
   guard let image = imagePreview.image else { return }
  
   PhotoWriter.save(image)
       .asSingle()
       .subscribe(onSuccess: { [weak self] id in
           self?.showMessage("Saved with id: \(id)")
           self?.actionClear()
           } , onError: { [weak self] error in
               self?.showMessage("Error", description: error.localizedDescription)
       })
       .disposed(by: bag)
  ```

  1. 상기 코드는 현재 콜라주를 저장하기 위해 `PhotoWriter.save(image)`를 호출한 것이다.

  2. 그런 다음에 구독이 하나의 요소를 받을 때, 리턴된 `Observable`을 `Single`로 전환한다.

  3. 이 후 해당 메시지가 성공인지 에러인지를 표시한다.

  4. 추가적으로, 만약 이미지가 성공적으로 저장되면 콜라주 화면을 클리어한다.



> **한번 더 중요!!**
>
> `asSingle ()`은 시퀀스가 둘 이상 방출되면 오류를 발생시켜 최대 하나의 요소를 얻는다는 것을 명시적으로 약속하고 있다.



아직 RxSwift의 어두운 면을 생각하지 말자.. 

이 책과 함께하면 네트워킹, 스레드 전환 및 오류 처리에 곧 대처할 수 있을 것이니까.. 화이팅(오글)



## Challenge 1: It's only logical to use a Single

카메라 롤에 사진을 저장할 때 `.asSingle ()`을 사용하면 별다른 이득을 얻지 못했을 것입니다. `Observable`은 이미 최대 하나의 요소를 방출하기 때문입니다.

1. PhotoWriter.swift를 열고 save (_)의 리턴 타입을 Single <String>으로 변경하십시오. 

2. 그런 다음 Observable.create를 Single.create로 바꿉니다.



대부분의 오류를 해결해야합니다. 마지막으로주의해야 할 것이 있습니다. 

```swift
Observable.create( { (observer) in
    // 여러가지 세팅
})
```

Observable.create는 옵저버를 매개 변수로 받아 여러 값을 생성하거나 이벤트를 종료 할 수 있습니다. 

```swift
Single.create { (<#@escaping (SingleEvent<_>) -> Void#>) -> Disposable in
      <#code#>
    }
```

Single.create는 클로저를 매개 변수로 받으며 .success (T) 또는 .error (E) 값을 생성하기 위해 한 번만 사용할 수 있습니다.



변환을 직접 완료하고 매개 변수는 옵저버 개체가 아닌 클로저이므로 single (.success (id))와 같이 호출합니다.



```swift
static func save(_ image: UIImage) -> Single<String> {
    return Single.create { singleObserver in
      var savedAssetId: String?
      
      PHPhotoLibrary.shared().performChanges({
        let request = PHAssetChangeRequest.creationRequestForAsset(from: image)
        savedAssetId = request.placeholderForCreatedAsset?.localIdentifier
      }) { (success, error) in
        DispatchQueue.main.async {
          if success, let id = savedAssetId {
            singleObserver(.success(id))
          } else {
            singleObserver(.error(error ?? Errors.couldNotSavePhoto))
          }
        }
      }
      
      return Disposables.create()
    }
   }
```



```swift
PhotoWriter.save(image)
      .subscribe(
        onSuccess: { [weak self] id in
          self?.showMessage("Saved with id: \(id)")
          self?.actionClear()
        },
        onError: { [weak self] error in
          self?.showMessage("Error", description: error.localizedDescription)
        }
      )
      .disposed(by: bag)
```



## Challenge 2: 현재 alert에 커스텀한 observable 추가하기

**MainViewController.swift** 에서 `showMessage(_:description:)` 메소드를 확인해보자.

이 메소드는 유저가 alert 화면을 끄기 위해 **Close** 버튼을 누르면 실행될 것이다. 이미 앞선 예제를 통해 `PHPhotoLibrary.performChanges(_)`를 구현했던 것과 비슷해보인다.



다음과 같이 진행해보자.

1. `UIViewController`에 extension을 추가하여 화면에 제목과 메시지를 포함한 alert을 띄우고 Completable을 리턴하는 메소드를 작성해보자.

2. 구독이 종료되었을 때 alert도 종료시켜야 한다

3. 마지막에는 새로운 completable을 이용하여 `showMessage(_:description:)` 내에서 alert을 띄울 수 있도록 한다.



다음과 같이 UIViewController extension을 작성

```swift
extension UIViewController {
  func showMessage(_ title: String, description: String? = nil) -> Completable {
    return Completable.create { [weak self] observer in
      let alert = UIAlertController(title: title, message: description, preferredStyle: .alert)
      let closeAction = UIAlertAction(title: "Close", style: .default) { _ in
        observer(.completed)
      }
      
      alert.addAction(closeAction)
      self?.present(alert, animated: true, completion: nil)
      
      return Disposables.create {
        self?.dismiss(animated: true, completion: nil)
      }
    }
  }
}
```

1. MainViewController.swift의 showMessage(_:description:)에 구현할 것

```swift
PhotoWriter.save(image)
  .subscribe(
    onSuccess: { [weak self] id in
      guard let self = self else { return }
      self.showMessage("Saved with id: \(id)")
        .subscribe(
          onCompleted: {
            self.actionClear()
        })
        .disposed(by: self.bag)
    },
    onError: { [weak self] error in
      guard let self = self else { return }
      self.showMessage("Error", description: error.localizedDescription)
        .subscribe().disposed(by: self.bag)
    }
  )
  .disposed(by: bag)
```



> **탐구**
>
> close가 눌리고 바로 Dispose가 되는 것은 close가 눌리면 completable의 .onCompleted가 호출되고 이로써 completable이 갖는 한번의 이벤트가 발생 된 것이다. 따라서  disposeBag에 의해 dispose()가 된다.



> **궁금증**
>
> RxSwift와는 별개의 궁금증이 생겼다.
>
> Challenge상에서 Dispose되면 해당 Alert을 dismiss시키라는 조건이 있었는데
>
> 기본적으로 Alert의 ActionButton 중 하나를 누르면 자동적으로 해제된다.
>
> (기본적으로 UIAlertController는 경고 메세지나 간단한 액션시트를 위한 뷰이기 때문에)
>
> 그래서 명시적으로 Dispose될 때 Alert을 dismiss 시키는 코드를 작성하지 않아도 되는데 
>
> 왜 이러한 조건이 있는걸까?? 내가 놓치고 있는 부분이 있는 걸까?



출처: [RxSwift Study Github](https://github.com/fimuxd/RxSwift)