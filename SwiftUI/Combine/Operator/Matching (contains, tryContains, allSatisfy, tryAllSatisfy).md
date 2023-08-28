# Matching (contains, tryContains, allSatisfy, tryAllSatisfy)
<br/>

## contains(where:)
- where 클로저 조건에 따라 Bool 반환
```Swift
[1, 2, 3, 4, 5].publisher
  .contains(where: { $0 == 3 })
  .sink { print($0) }
// true
```
<br/>

## tryContains(where:)
- contains는 true를 반환하면 이후 값에 관해서 체크하지 않음을 주의 (아래에서 true를 먼저 반환하므로 throw 발생 x)
```Swift
[1, 2, 3, 4, 5].publisher
  .tryContains(where: {
    guard $0 != 5 else { throw SomeError() }
    return $0 == 3
  })
  .sink(
    receiveCompletion: { print("completion: \($0)") },
    receiveValue: { print("value: \($0)") }
  )
// value: true
// completion: finished
```
<br/>

## allSatisfy()
- 클로저안의 모든 조건이 true이면 true반환
```Swift
[1, 2, 3, 4, 5].publisher
  .allSatisfy { $0 < 6 }
  .sink { print($0) }
// true
```
<br/>

## tryAllSatisfy()
- tryContains(where:)와는 다르게 모든 값들을 비교해야하므로, throw 발생
```Swift
[1, 2, 3, 4, 5].publisher
 .tryAllSatisfy {
    guard $0 != 5 else { throw SomeError() }
    return $0 < 6
  }
  .sink(
    receiveCompletion: { print("completion: \($0)") },
    receiveValue: { print("value: \($0)") }
  )
// completion: failure(ExMatching.SomeError())
```
<br/>
