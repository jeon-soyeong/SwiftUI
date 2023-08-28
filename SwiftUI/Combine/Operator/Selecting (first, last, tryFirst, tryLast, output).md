# Selecting (first, last, tryFirst, tryLast, output)
<br/>

## first()
```Swift
[1, 2, 3, 4, 5].publisher
  .first()
  .sink { print($0) }
// 1
```
<br/>

## last()
```Swift
[1, 2, 3, 4, 5].publisher
  .last()
  .sink { print($0) }
// 5
```
<br/>

## tryFirst
- 조건에 해당하는 first를 만나기 전까지 throw가 발생 안하면 에러 x
```Swift
struct SomeError: Error {}
[1, 2, 3, 4, 5].publisher
  .tryFirst(where: {
    guard $0 != 3 else { throw SomeError() }
    return $0 % 2 == 0
  })
  .sink(receiveCompletion: { print($0 )}, receiveValue: { print($0) })
// 2
// finished
```
<br/>

## tryLast
- Sequence 마지막까지 순회하면서 throw가 없으면 조건에 해당하는 값 방출
```Swift
[1, 2, 3, 4, 5].publisher
  .tryLast(where: {
    guard $0 != 3 else { throw SomeError() }
    return $0 % 2 == 0
  })
  .sink(receiveCompletion: { print($0 )}, receiveValue: { print($0) })
// failure(.SomeError())
```
<br/>

## output(in:)
- 인덱스에 해당하는 값을 방출하며, 만약 인덱스가 범위를 벗어나면 에러는 나지 않고 finished 이벤트가 방출
```Swift
[1, 2, 3, 4, 5].publisher
  .output(at: 3)
  .sink(receiveCompletion: { print($0 )}, receiveValue: { print($0) })
// 4

[1,2,3,4,5].publisher
  .output(at: 6)
  .sink(receiveCompletion: { print($0 )}, receiveValue: { print($0) })
// finished

// 범위도 가능
let numbers = [1, 1, 2, 2, 2, 3, 4, 5, 6]
numbers.publisher
    .output(in: (3...))
    .sink { print("\($0)", terminator: " ") }
    
//결과값
// 2
// 2
// 3
// 4
// 5
// 6
```
<br/>
