# Publisher
- Publisher는 단순히 이벤트 스트림에서 이벤트를 방출 시킬 수 있는 타입
- extension으로 map, tryMap 등 다양한 연산자를 사용할 수 있어서 이벤트 스트림의 핵심 프로토콜

## AnyPublisher
Publisher 를 준수하고 있는 struct이며, Publisher를 wrapping하고 있는 가장 추상화된 Publisher</br>
</br>
Sequence에 publisher를 정의해주는 extension이 있기 때문에, {sequnce}.publisher로 간편하게 사용이 가능</br>
</br>

```Swift
let publisher = [1,2,3].publisher
// publisher 타입: Publishers.Sequence<[Int], Never>
```

## Publisher 사용 방법

### 1) sink로 구독해서 바로 사용하는 방법
```Swift
let publisher = [1,2,3].publisher
publisher
  .sink { print("출력? \($0)") }
  
/*
출력? 1
출력? 2
출력? 3
*/
```

### 2) SwiftUI에서는 Publisher, Subscriber 코드를 미리 정의하여 처리하는 형태
Subscriber 미리 정의</br>
Subscriber 준수하고, 성공타입과 실패 타입을 정의한 다음 receive 관련 3가지 메소드를 구현</br>

```Swift
class MySubscriber: Subscriber {
  /// 성공 타입
  typealias Input = Int
  /// 실패 타입
  typealias Failure = Never
  
  func receive(_ input: Int) -> Subscribers.Demand {
    print("데이터 수신: \(input)")
    return .none
  }
  
  func receive(subscription: Subscription) {
    print("데이터 구독 시작")
    subscription.request(.unlimited) // 구독할 데이터의 갯수를 제한하지 않는 것
  }
  
  func receive(completion: Subscribers.Completion<Never>) {
    print("모든 데이터 발행 완료")
  }
}
```
</br>

Publisher로 sequence 데이터를 만든다음 구독</br>

```Swift
let publisher = [1,2,3].publisher
let subscriber = MySubscriber()
publisher.subscribe(subscriber)

/*
 데이터 구독 시작
 데이터 수신: 1
 데이터 수신: 2
 데이터 수신: 3
 모든 데이터 발행 완료
*/
```
