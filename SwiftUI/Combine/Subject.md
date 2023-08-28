# Subject
- Publisher의 일종이고 (Publisher 프로토콜을 준수하고 있는 형태), 이 인스턴스는 이벤트를 방출할 수 있는 send(subscription:) 기능이 존재
  - 애플에서는 이벤트 방출을 "inject"라고 명명

```Swift
@available(macOS 10.15, iOS 13.0, tvOS 13.0, watchOS 6.0, *)
public protocol Subject : AnyObject, Publisher {
    func send(subscription: Subscription)
}
```
<br/>

## PassthoughSubject
- downstream subscribers에게 값을 broadcast하는 subject
- 값을 저장하고 있지는 않고 단순히 값을 토스 해주는 역할
- RxSwift의 PublishSubject와 동일

```Swift
let subject = PassthroughSubject<String, Never>()

subject
  .sink(receiveValue: { print($0) })

subject.send("Test1")
subject.send("Test2")

/*
 Test1
 Test2
*/
```
<br/>

## CurrentValueSubject
- single value를 래핑하고 값이 변경될때마다 새로운 값을 publish하는 subject 형태
- 값을 저장하고, subscribers에게 값을 broadcast하는 subject
- RxSwift의 BehaviorSubject와 동일

```Swift
let subject = CurrentValueSubject<String, Never>("currentValueSubject")

subject
  .sink(receiveValue: { print($0) })

subject.value = "Test1" // subject.send("Test1") 와 동일
subject.send("Test2")

/*
 currentValueSubject
 Test1
 Test2
*/
```
