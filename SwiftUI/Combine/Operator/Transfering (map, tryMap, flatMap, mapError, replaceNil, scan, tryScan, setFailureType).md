# Transfering (map, tryMap, flatMap, mapError, replaceNil, scan, tryScan, setFailureType)
<br/>

## Map
- 값 요소들을 하나씩 맵핑 (값에 변화를 주는 연산자)
```Swift
let publisher1 = [1, 2, 3].publisher
let cancellable1 = publisher1
  .map { $0 + 2 }
  .sink(receiveValue: { print($0) })
/*
 3
 4
 5
*/
```
<br/>

## tryMap
- map 클로저 내부에서 예외가 던져질 수 있는 연산이 있을때 사용
- 예외 처리하는 곳은 sink의 receiveCompletion에서 수행
```Swift
enum SomeError: Error {
  case zero
}
let publisher2 = [2, 1, 0].publisher
let cancellable2 = publisher2
  .tryMap {
    guard $0 != 0 else { throw SomeError.zero }
    return 10 / $0
  }
  .sink(
    receiveCompletion: { result in
      switch result {
      case let .failure(error):
        print("failure: \(error)")
      case .finished:
        print("finished")
      }
    },
    receiveValue: { value in
      print(value)
    }
  )
/*
 5
 10
 failure: zero
*/
```
<br/>

## flatMap
- 이벤트로 넘어오는 값에 접근하는 것은 map과 동일하지만, 변형을 줄 때 값으로 변형을 주는게 아닌, Publisher 형태로 변형
```Swift
let publisher3 = [2, 1, 0].publisher
let cancellable3 = publisher3
  .flatMap { value -> AnyPublisher<Int, Never> in
    Future { promise in
      promise(.success(value + 10))
    }
    .eraseToAnyPublisher()
  }
  .sink(receiveValue: { print($0) } )
  /*
 12
 11
 10
*/
```
<br/>

## mapError
- 이벤트로 에러가 넘어올 때 다른 Error로 변형시켜서 이벤트를 변형시키는 연산자
```Swift
enum MyError: Error {
  case zero
  case other
}
enum GeneralError: Error {
  case first
}
let publisher4 = [2, 1, 0].publisher
let cancellable4 = publisher4
  .tryMap { try divideValue($0) }
  .mapError { _ in return GeneralError.first }
  .sink(
    receiveCompletion: { result in
      switch result {
      case let .failure(error):
        print("failure: \(error)")
      case .finished:
        print("finished")
      }
    },
    receiveValue: { value in
      print(value)
    }
  )

func divideValue(_ value: Int) throws -> Int {
  guard value != 0 else { throw MyError.zero }
  return 10 / value
}
/*
 5
 10
 failure: first
*/
```
<br/>

## replaceNil
- nil 값이 넘어오는 경우, 다른 값으로 변경할때 사용
```Swift
let publisher5 = [2, nil, 0].publisher
let cancellable5 = publisher5
  .replaceNil(with: -1)
  .sink { print($0) }
/*
 Optional(2)
 Optional(-1)
 Optional(0)
*/
```
<br/>

## scan
- 초기값을 정한 후, 이벤트가 발생하면 계속 누적되며 이벤트 처리
```Swift
let publisher6 = [1, 2, 3].publisher
let cancellable6 = publisher6
  .scan(10, { $0 + $1 })
  .sink { print($0) }
/*
 11
 13
 16
*/
```
<br/>

## scan
- 초기값을 정한 후, 이벤트가 발생하면 계속 누적되며 이벤트 처리
```Swift
let publisher6 = [1, 2, 3].publisher
let cancellable6 = publisher6
  .scan(10, { $0 + $1 })
  .sink { print($0) }
/*
 11
 13
 16
*/
```
<br/>

## tryScan
- scan 기능 + 예외 던지기 기능
```Swift
enum NilError: Error {
  case isNil
}
let publisher7 = [0, nil, 2].publisher
let cancellable7 = publisher7
  .tryScan(0) { try handleSomeValue(lhs: $0, rhs: $1) }
  .sink(
    receiveCompletion: { print("receiveCompletion: \($0)") },
    receiveValue: { print("receiveValue: \($0)") }
  )
func handleSomeValue(lhs: Int?, rhs: Int?) throws -> Int {
  guard
    let lhs = lhs,
    let rhs = rhs
  else { throw NilError.isNil }
  return lhs + rhs
}
/*
 receiveValue: 0
 receiveCompletion: failure(__lldb_expr_24.NilError.isNil)
*/
```
<br/>

## setFailureType
- Publisher<Int, Never>과 같이 에러 타입이 Never인 경우, 다른 에러 타입으로 스트림을 변경할 수 있는 연산자
```Swift
enum SomeErrorType: Error {
  case some
}
let publisher8 = [0, nil, 2].publisher // 타입: Publishers.Sequence<[Int?], Never>
let cancellable8 = publisher8
  .setFailureType(to: SomeErrroType.self)
  .sink(
    receiveCompletion: { print($0) },
    receiveValue: { print($0) }
  )
```
<br/>
