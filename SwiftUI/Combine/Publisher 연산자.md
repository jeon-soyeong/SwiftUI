# Publisher 연산자

## Just
에러 타입은 항상 Never인 가장 단순한 형태의 Publisher
```Swift
Just(1)
  .sink { print("Just: \($0)") }
// Just: 1

Just((1,2,3))
  .sink { print("Just: \($0)") }
// Just: (1, 2, 3)

Just([1,2,3])
  .sink { print("Just: \($0)") }
// Just: [1, 2, 3]
```
<br/>

## Sequence
주어진 Sequence를 방출하는 Publisher
```Swift
Publishers.Sequence<[Int], Never>(sequence: [1,2,3])
  .sink(
    receiveCompletion: { print("receiveCompletion: \($0)") },
    receiveValue: { print("receiveValue: \($0)") }
  )

/*
 receiveValue: 1
 receiveValue: 2
 receiveValue: 3
 receiveCompletion: finished
*/
```
<br/>

## Future
Result 타입과 같이 성공 or 실패 두 가지 타입을 갖는 Publisher
```Swift
final class Future<Output, Failure> where Failure : Error
```

1) Future의 클로저의 promise 타입은 단순히 Result 타입을 가지고 있는 closure
```Swift
Future<Int, Never> { promise in
  promise(.success(5))
}
.sink { print($0) }

// 5
// promise 타입은 (Result<Int, Never>) -> Void
```

2) Future을 사용하지 않은 코드
```Swift
struct ImageProcessorService {
  func run(
    _ image: UIImage,
    completion: (Result<UIImage, Error>) -> Void
  ) -> Void {
    print("some function")
  }
}
```
3) Future를 사용한 코드
- promise 타입은 단순히 Result타입을 인수로 가지고 있는 클로저라서 위에서 정의한 completion이 있는 코드도 바로 사용이 가능
```Swift
extension ImageProcessorService {
  func run(_ image: UIImage) -> Future<UIImage, Error> {
    Future { promise in
      self.run(image, completion: promise)
    }
  }
}
```
<br/>

## Fail
- fail을 바로 스트림으로 보내는 Publisher
- 첫번째 인수는 에러를 방출할때 같이 방출할 값
```Swift
public struct Fail<Output, Failure> : Publisher where Failure : Error
```

```Swift
["50", nil, "70"]
  .publisher
  .flatMap { value -> AnyPublisher<String, MyError> in
    guard let value = value else {
      return Fail<String, MyError>(error: .isNil).eraseToAnyPublisher()
    }
    return Just(value).setFailureType(to: MyError.self).eraseToAnyPublisher()
  }
  .sink(
    receiveCompletion: { print("receiveCompletion: \($0)") },
    receiveValue: { print("receiveValue: \($0)") }
  )
/*
 receiveValue: 50
 receiveCompletion: failure(__lldb_expr_67.MyError.isNil)
*/
```
<br/>

## Empty
값을 방출하지 않고 즉시 완료되는 Publisher
```Swift
Empty<String, Never>()
  .sink(
    receiveCompletion: { print("receiveCompletion: \($0)") },
    receiveValue: { print("receiveValue: \($0)") }
  )

// receiveCompletion: finished
```
<br/>

## Deferred
구독이 일어나기 전까지 대기하고 있다가, 구독이 일어났을 때 Deferred 클로저 부분이 실행되는 Publisher
```Swift
Deferred { Just(1) }
  .sink(
    receiveCompletion: { print("receiveCompletion: \($0)") },
    receiveValue: { print("receiveValue: \($0)") }
  )
/*
 receiveValue: 1
 receiveCompletion: finished
*/
```
