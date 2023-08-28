# Filtering (filter, tryFilter, compactMap, tryCompactMap, removeDuplicates, tryRemoveDuplicates(by:), replaceEmpty(with:), replaceError(with:))
<br/>

## filter
```Swift
[1,2,3].publisher
  .filter { $0 % 2 == 0 }
  .sink(receiveValue: { print($0) })
  .store(in: &cancellable)
// 2
```
<br/>

## tryFilter
- 특정 조건에 해당하면 Error를 throw
- throw를 처리하는 곳은 .sink의 receiveCompletion에서 수행
- 주의할점은 throw가 발생하면 해당 스트림은 해제되기 때문에 3은 방출되지 않음
```Swift
[1, 2, -1, 3].publisher
  .tryFilter {
    if $0 == -1 {
      throw MyError.a
    } else {
      return true
    }
  }
  .sink(
    receiveCompletion: { val in
      switch val {
      case let .failure(error):
        print("error=", error)
      case .finished:
        print("finish")
      }
    },
    receiveValue: { print($0) }
  )
  .store(in: &cancellable)

/*
 1
 2
 error= a
 */
```
<br/>

## compactMap
- Optional 해제
- tryCompactMap 연산자 = compactMap 연산자 + throw를 할 수 있는 연산자
```Swift
[1, 2, Optional(3), 4].publisher
  .compactMap { $0 }
  .sink(receiveValue: { print($0) })
  .store(in: &cancellable)
/*
 1
 2
 3
 4
 */
```
<br/>

## removeDuplicates
- 중복 제거
- tryRemoveDuplicates 연산자 = removeDuplicates 연산자 + try 연산자
```Swift
[1, 2, 2, 3].publisher
  .removeDuplicates()
  .sink(receiveValue: { print($0) })
  .store(in: &cancellable)
/*
 1
 2
 3
 */
```
<br/>

## replaceEmpty(with:)
- empty인 이벤트가 넘어올 때 특정 값으로 변경
```Swift
Empty<Int, Never>()
  .replaceEmpty(with: 10)
  .sink(receiveValue: { print($0) })
  .store(in: &cancellable)
// 10
```
<br/>

## replaceError(with:)
- error 발생 시 다른 값으로 변경
```Swift
[1, 2, 3, -1, 4].publisher
  .tryMap {
    if $0 == -1 {
      throw MyError.a
    } else {
      return $0
    }
  }
  .replaceError(with: 100)
  .sink(receiveValue: { print($0) }) // error가 발생하면 해당 stream은 해제되므로, 뒤에 오는 값 (4)은 방출 x
  .store(in: &cancellable)
/*
 1
 2
 3
 100
 */
```
<br/>
