# Combining (combineLatest, merge, zip)
- 스트림에서 방출되는 2개 이상의 요소들을 묶어서 방출하는 방법
</br></br></br></br>
### 준비
- PassthroughSubject는 downstream subscribers에게 값을 broadcast하는 subject
- 값을 저장하고 있지는 않고 단순히 값을 토스 해주는 역할
- RxSwift의 PublishSubject와 동일

```Swift
let passthroughSubject1 = PassthroughSubject<String, Never>()
let passthroughSubject2 = PassthroughSubject<String, Never>()
```
</br>

## merge(with:)
- 두 스트림을 묶어서 각각 방출하는 것들을 묶어서 하나의 sink(receiveValue:)에서 받을 수 있는 연산자
- publisher의 이벤트를 하나의 스트림으로 받기 위해 사용하며, 시간 순으로 사용할 때 유용
```Swift
let cancellable2 = passthroughSubject1
  .merge(with: passthroughSubject2)
  .sink(receiveValue: { print($0) })
// test1 test2

passthroughSubject1.send("test1")
//test1

passthroughSubject2.send("test2")
//test2
```
<br/>

## combineLatest(_:_:)
- 두 스트림 모두 메시지를 받았을때 이벤트 방출
- 해당 연산자에서 이전 값을 기억하고 있으며, sub1에서 이벤트가 발생했을때, sub2의 이전 이벤트가 남아있다면 그 이벤트와 같이 방출
```Swift
let cancellable1 = passthroughSubject1
  .combineLatest(passthroughSubject2) { sub1, sub2 in
    return sub1 + sub2
  }
  .sink(receiveValue: { print($0) })

passthroughSubject1.send("test1")
passthroughSubject2.send("test2")
// test1test2

// 이전 이벤트 기억: sub1만 방출했는데도, sub2의 이전 이벤트와 같이 방출
passthroughSubject1.send("test1") // test1test2
```
<br/>

## zip()
- 두 이벤트가 반드시 하나씩 방출했을때 결과를 튜플로 방출해주는 연산자
- combineLatest와 유사하지만, zip은 이전 이벤트를 기억하지 않고, 반드시 두 이벤트가 방출되어야 동작
- 즉 새로운 이벤트끼리 
```Swift
let cancellable3 = passthroughSubject1
  .zip(passthroughSubject2)
  .sink(receiveValue: { print($0) })

passthroughSubject1.send("test1")
passthroughSubject2.send("test2")
// ("test1", "test2")
        
passthroughSubject1.send("test1")
// 출력x - sub2도 방출해야 출력됨
```
<br/>
