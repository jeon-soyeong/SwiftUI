# Cancellable
- Combine 작업들을 취소할 수 있다는 의미를 가지고 있는 프로토콜
- Combine에서는 이벤트 스트림을 action이라는 이름을 사용, action을 취소할 수 있는 프로토콜이 Cancellable이라는 의미

## AnyCancellable 
```Swift
final public class AnyCancellable : Cancellable, Hashable
```
</br>

.sink로 이벤트를 구독하면 리턴값으로 AnyCancellable이 얻어지는데 이 값을 가지고 cancel()하여 언제든지 구독을 취소시킬 수 있는 기능
```Swift
final class MyModel {
  @Published var number: Int
  
  init(number: Int) {
    self.number = number
  }
}

let model = MyModel(number: 20)
let anyCancellable = model.$number
  .sink { print($0) }
model.number = 10
model.number = 1

// 구독이 취소되어 더 이상 print($0)가 실행 x
anyCancellable.cancel()
model.number = 2
model.number = 3
/*
 20
 10
 1
*/ 
```
</br>

## RxSwift의 DisposeBag처럼 사용하기
특정 화면의 인스턴스가 해제되면 그 안에 있던 구독들도 같이 날아가게끔 하고 싶은 경우 store(in:)메소드를 사용</br>
```Swift
var cancellables = Set<AnyCancellable>() 
let model = MyModel(number: 20)
let anyCancellable = model.$number
  .sink { print($0) }
  .store(in: &cancellables)

model.number = 10
model.number = 1

// Set<AnyCancellable>() 타입의 cancellables을 하나 선언해놓고, sink {} 밑에 .store(in:)을 선언하여 사용
```
