# Sequence (drop, dropFirst, tryDrop, append, prepend, prefix, tryPrefix)
<br/>

## drop(while:)
- 일반 while (condition) { body } 과 동일
- while 안에 조건이 true일때만 값을 drop
- [1, 3, 5, 7, 8, 9]가 있을때 1, 3, 5, 7 까지 계속 홀수이므로 while문 내부가 true이므로 모두 drop
- 8에서 while문이 false이므로 while문이 break
- while문이 break되었으므로 9는 방출
```Swift
[1, 3, 5, 7, 8, 9].publisher
  .drop(while: { $0 % 2 != 0 }) // 8일 때 while문이 break
  .sink { print($0, terminator: " ") }
// 8 9
```
<br/>

## dropFirst()
- 첫번째 요소만 drop
```Swift
[1, 2, 3, 4, 5].publisher
  .dropFirst()
  .sink { print($0, terminator: " ") }
// 2 3 4 5
```
<br/>

## tryDrop()
- 예외를 던지는 것도 while문이 break 되지 않은 경우만 수행
- 아래 예시처럼 throw를 하기 전에 while문이 break 되므로 throw 던지지 않음
```Swift
[1, 2, -1, 3, 4, 5].publisher
  .tryDrop(while: { value in
    guard value != -1 else { throw MyError() }
    return value % 2 != 0
  })
  .sink(
    receiveCompletion: {
      print()
      print("completion:", terminator: " ")
      print($0, terminator: " ")
    },
    receiveValue: {
      print("value:", terminator: " ")
      print($0, terminator: " ")
    }
  )
  // value: 2 value: -1 value: 3 value: 4 value: 5
  // completion: finished
```

```Swift
[1, -1, 2, 3, 4, 5].publisher
  .tryDrop(while: { value in
    guard value != -1 else { throw MyError() }
    return value % 2 != 0
  })
  .sink(
    receiveCompletion: {
      print()
      print("completion:", terminator: " ")
      print($0, terminator: " ")
    },
    receiveValue: {
      print("value:", terminator: " ")
      print($0, terminator: " ")
    }
  )
// Error
```
<br/>

## append()
```Swift
[1, 2, 3, 4, 5].publisher
  .append(-1, -2, -3) // [-1, -2, -3]도 가능
  .sink { print($0, terminator: " ") }
// 1 2 3 4 5 -1 -2 -3
```
<br/>

## prepend()
- 앞에 추가
```Swift
[1, 2, 3, 4, 5].publisher
  .prepend(-1, -2, -3)
  .sink { print($0, terminator: " ") }
// -1 -2 -3 1 2 3 4 5
```
<br/>

## prefix()
- 앞에서 n개 방출
```Swift
[1, 2, 3, 4, 5].publisher
  .prefix(3)
  .sink { print($0, terminator: " ") }
// 1 2 3
```
<br/>

## prefix(while:)
- 조건에 해당하는 값 방출
```Swift
[1, 2, 3, 4, 5].publisher
  .prefix { $0 < 3 }
  .sink { print($0, terminator: " ") }
// 1 2
```
<br/>

## tryPrefix(while:)
- throw를 던질 수 있는 prefix
- prefix 카운트가 끝나면, throw를 던지기 전에 종료
```Swift
[1, 2, 3, 4, 5].publisher
  .prefix { $0 < 3 }
  .tryPrefix {
    guard $0 != -1 else { throw MyError() }
    return $0 < 3
  }
  .sink(
    receiveCompletion: {
      print()
      print("completion:", terminator: " ")
      print($0, terminator: " ")
    },
    receiveValue: {
      print("value:", terminator: " ")
      print($0, terminator: " ")
    }
  )
  // value: 1 value: 2
  // completion: finished
```
<br/>
