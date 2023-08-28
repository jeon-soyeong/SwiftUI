# Subscriber
protocol이며 이 프로토콜을 준수하는 subscriber 인스턴스를 만들면, 이 subscriber로 다른 publisher를 구독하여 사용<br/>
구독했을때 처리가 이 Subscriber 구현체에서 처리<br/>
(publisher 내용 참고)
<br/>

## AnySubscriber
타입이 제거된 subscriber (가장 추상적인 Subscriber 형태)
세부 정보를 외부에 숨겨서, 결합도를 낮추는데 사용

```Swift
ex) AnySubscriber의 가장 기본 기능 사용 방법

let subscriber = AnySubscriber<Int, Never>(
  receiveSubscription: { sub in
    print("receiveSubscription: \(sub)")
  },
  receiveValue: { v1 in
    print("receiveValue: \(v1)")
    return .max(1)
  },
  receiveCompletion: { _ in
    print("receiveCompletion")
  }
)

[1, 2]
  .publisher
  .subscribe(subscriber)

// receiveSubscription: [1, 2]
```
