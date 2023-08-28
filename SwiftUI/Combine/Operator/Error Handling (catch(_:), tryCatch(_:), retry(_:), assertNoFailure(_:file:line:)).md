# Error Handling (catch(_:), tryCatch(_:), retry(_:), assertNoFailure(_:file:line:))
<br/>

## Catch(_:)
- 에러가 발생할때 해당 에러에 대한 catch에 걸리면 catch에서 리턴한 데이터만 방출하고 위에있던 남은 데이터는 무시
```Swift
struct MyError: Error {}

let _ = [1, 2, -3, 4, 5].publisher
  .tryMap {
    guard $0 > 0 else { throw MyError() }
    return $0
  }
  .catch { error in
    [9,8,7].publisher
  }
  .sink(
    receiveCompletion: { print("Completion: \($0)") },
    receiveValue: { print("Value:", $0) }
  )
  
// Value: 1
// Value: 2
// Value: 9
// Value: 8
// Value: 7
// Completion: finished
```
<br/>

## tryCatch(_:)
- try + catch 기능 (에러가 발생했을때 해당 에러 처리를 할 수 있고, throw도 할 수 있는 기능)
```Swift
let _ = [1, 2, -3, 4, 5].publisher
  .tryMap {
    guard $0 > 0 else { throw MyError() }
    return $0
  }
  .tryCatch { error -> Publishers.Sequence<[Int], Never> in
    guard error is MyError else { throw MyAnotherError() }
    return [9,8,7].publisher
  }
  .sink(
    receiveCompletion: { print("Completion: \($0)") },
    receiveValue: { print("Value:", $0) }
  )
          
// Value: 1
// Value: 2
// Value: 9
// Value: 8
// Value: 7
// Completion: finished
```
<br/>

## retry(_:)
- 에러 발생 시 재시도
```Swift
let _ = [1, 2, -3, 4, 5].publisher
  .tryMap {
    guard $0 > 0 else { throw MyError() }
    return $0
  }
  .retry(1)
  .sink(
    receiveCompletion: { print("Completion: \($0)") },
    receiveValue: { print("Value:", $0) }
  )

// Value: 1
// Value: 2
// Value: 1
// Value: 2
// Completion: failure(ExErrorHandlingWithCombine.MyError())
```
<br/>

## assertNoFailure(_:file:line:)
- 에러 발생 시 fatalError
```Swift
let _ = [1, 2, -3, 4, 5].publisher
  .tryMap {
    guard $0 > 0 else { throw MyError() }
    return $0
  }
  .assertNoFailure()
  .sink(
    receiveCompletion: { print("Completion: \($0)") },
    receiveValue: { print("Value:", $0) }
  )

// Value: 1
// Value: 2
// Thread 1: EXC_BREAKPOINT (code=1, subcode=0x19b3c2588)
```
<br/>
