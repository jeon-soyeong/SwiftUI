# DynamicProperty

## DynamicProperty 란?
- DynamicProperty는 update()라는 메소드가 있고, 내부적으로 이 메소드가 불리며 뷰를 업데이트 시키는데 사용되는 protocol
```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
public protocol DynamicProperty {
    mutating func update()
}
```
<br/>

- 주로 propertyWrapper와 같이 사용되며, 특정 값이 변경될때 뷰를 update() 시키게끔 구현
- ex) Counter라는 property wrapper를 만들고 사용하는 쪽에서 이 값을 변경하면 뷰에도 이 값이 업데이트되는지 확인
```Swift
/*
Counter 모델 정의
DynamicProperty를 준수하는 propertyWrapper
주의할 점은 밖에서 value값을 수정할 수 있어야 하므로 `nonmutating set` 키워드 넣기
*/
@propertyWrapper
struct Counter: DynamicProperty {
  @State private var value = 0
  
  var wrappedValue: Int {
    get { return value }
    nonmutating set { value = newValue }
  }
}

// 사용하는 쪽
struct ContentView: View {
  @Counter private var counter
  
  var body: some View {
    Button("Button, \(counter)") {
      counter += 1
    }
  }
}
```
<br/>

## nonmutating set 개념
- mutating, nonmutating 키워드가 있고 mutating이면 value type 내부를 변경할 수 있고 nonmutating이면 value type 내부를 변경할 수 없는 형태
- struct에서 set 키워드를 쓰면 앞에 mutating이라는게 생략되어있는 형태
```Swift
struct Sample {
  var val = 0

  var computedVal: Int {
    get { return val }
    set { val = newValue } // mutating set { val = newValue }
  }
}
```
<br/>

- 명시적으로 자기 자신에 관한 set을 금지하려면 nonmutating을 작성
- nonmutating set 안에서 self.val 값을 변경하는 코드가 있을때 컴파일 에러 발생
```Swift
struct Sample {
  var val = 0

  var computedVal: Int {
    get { return val }
    nonmutating set { val = newValue } // erorr: Cannot assign to property: 'self' is immutable
  }
}
```
<br/>

- nonmutating set을 사용하는 경우?
- nonmutating set 안에서 self 관련된 프로퍼티가 아닌 다른 프로퍼티를 변경하는 경우
- ex) UserDefaults 접근
```Swift
struct Sample {
  var val = 0

  var computedVal: Int {
    get { return val }
    nonmutating set {
      UserDefaults.standard.set("test", forKey: "key")
    }
  }
}
```
<br/>

- 위에서 알아본 DynamicProperty 내부 nonmutating set에서 self.value값을 변경하고 있어서 컴파일 에러가 날 것 같지만 정상 동작
- **정상 동작하는 이유: @State 프로퍼티를 struct 내부에 선언해놓고 값을 변경해도 정상 동작 하게끔 SwiftUI에서 설계 했으므로, propertyWrapper 안에서도 정상 동작**
```Swift
@propertyWrapper
struct Counter: DynamicProperty {
  @State private var value = 0
  
  var wrappedValue: Int {
    get { return value }
    nonmutating set { value = newValue }
  }
}
```
<br/>
