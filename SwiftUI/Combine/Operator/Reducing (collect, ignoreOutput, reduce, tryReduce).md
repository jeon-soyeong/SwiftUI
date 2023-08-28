# Reducing (collect, ignoreOutput, reduce, tryReduce)

## collect
- collect(n)이면 n 만큼 묶어서 방출
```Swift
(0...10).publisher
  .collect(5)
  .sink { print($0) }
/*
 [0, 1, 2, 3, 4]
 [5, 6, 7, 8, 9]
 [10]
 */
```
<br/>

## ignoreOutput
- 값이 방출되는 값은 모두 무시하고 completion의 failure만 방출
```Swift
struct NoZeroValuesAllowedError: Error {}
let numbers = [1, 2, 3, 4, 5, 0, 6, 7, 8, 9]
numbers.publisher
  .tryFilter({ anInt in
    guard anInt != 0 else { throw NoZeroValuesAllowedError() }
    return anInt < 20
  })
  .ignoreOutput()
  .sink(
    receiveCompletion: { print("completion: \($0)") },
    receiveValue: { print("value \($0)") }
  )
// completion: failure(.NoZeroValuesAllowedError())

```
<br/>

## reduce
- 첫번째 인수에는 초기값을 넣고, 두번째에는 연산 ($0, $1) 결과를 리턴
- $0는 누적값, $1는 배열을 순회하는 값
```Swift
print("-------")
[1, 2, 3, 4].publisher
  .reduce(0, { $0 + $1 })
  .sink { print($0) }
// 10
```
<br/>

## tryReduce
- 위 reduce의 기능을하면서 동시에 예외를 던지고 싶은 경우 throw로 예외 던지기가 가능
- 처리는 catch 연산자 사용
```Swift
struct DivisionByZero: Error {}
[1, 2, 3, 4, 0].publisher
  .tryReduce(0, {
    guard $1 != 0 else { throw DivisionByZero() }
    return $0 / $1
  })
  .catch { _ in Just(-1) }
  .sink { print($0) }
// -1
// tryScan과는 달리 tryReduce에서는 마지막 최종값만 publish하므로 1, 2, 3, 4값 출력 X
```
<br/>
