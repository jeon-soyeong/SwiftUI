# Mathematical (count, max, tryMax, min, tryMin)
<br/>

## .count()
- 의미 그대로 배열의 갯수를 반환
<br/>

## max(by:) 
- by에 해당하는 조건을 갖는 최댓값 반환
```Swift
[(1, 2), (1, 3), (1, 4)].publisher
  .max(by: { $0.1 < $1.1 })
  .sink { print($0) }
// (1, 4)
```
<br/>

## tryMax()
- throw를 던질 수 있는 max 연산자
```Swift
struct MinusError: Error {}

[1, 2, -1, 3, 4].publisher
  .tryMax { first, second in
    // first, second = (1, 2), (2, -1), (-1,3), ...
    guard first != -1, second != -1 else { throw MinusError.init() }
    return first < second
  }
  .sink(
    receiveCompletion: { print("receiveCompletion: \($0)")},
    receiveValue: { print("recieveValue: \($0)") }
  )
// receiveCompletion: failure(ExMath.MinusError())
```
<br/>

## min(by:)
- max(by:)에서 by의 조건이 반대이므로 주의
```Swift
[1, 2, 3, 4].publisher
  .min(by: { $0 < $1 }) // max(by: { $0 > $1 })와 동일
  .sink { print($0) }
// 1
```
<br/>

## tryMin()
- 예외를 던질 수 있는 min
```Swift
struct MinusError: Error {}

[1, 2, -1, 3, 4].publisher
  .tryMin { first, second in
    // first, second = (1, 2), (2, -1), (-1,3), ...
    guard first != -1, second != -1 else { throw MinusError.init() }
    return first < second
  }
  .sink(
    receiveCompletion: { print("receiveCompletion: \($0)")},
    receiveValue: { print("recieveValue: \($0)") }
  )
// receiveCompletion: failure(ExMath.MinusError())
```
<br/>
