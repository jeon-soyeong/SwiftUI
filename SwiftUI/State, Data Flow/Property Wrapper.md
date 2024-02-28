## @Published
- propertyWrapper 형태이며 내부적으로 didSet에서 이벤트 방출하는 역할
- 사용하는 쪽에서는 $ 기호를 이용하여 projectedValue에 접근
```Swift
final class MyData {
  @Published var number: Int
  
  init(number: Int) {
    self.number = number
  }
}

let data = MyData(number: 20)

data.$number
  .sink { print("change value? = \($0)") }
data.number = 10
data.number = 1

/*
change value? = 20
change value? = 10
change value? = 1
*/
```
</br>

## @ObservableObject
- 이 프로토콜을 준수하면, Observable 인스턴스(이벤트를 방출할 수 있는)라는 의미
- objectWillChange 프로퍼티 사용이 가능
- 이 프로퍼티를 이용하여 내부에서 변경되었을 때 willSet안에서 objectWillChange.send()를 호출하여 알림
- 구독하는 쪽에서는 objectWillChange.sink { } 로 이벤트를 받아서 사용함
```Swift
final class MyData: ObservableObject {
  var number: Int {
    willSet { self.objectWillChange.send() }
  }
  
  init(number: Int) {
    self.number = number
  }
}

var data = MyData(number: 20)
data.objectWillChange
  .send()

data.objectWillChange
  .sink { print("change value? = \($0)") }

data.number = 10
data.number = 1

/*
 change value? = ()
 change value? = ()
*/
```
</br>

## @ObservedObject
- observable 객체가 변경되면 뷰에 업데이트 시켜주는 기능
- ObservableObject를 준수
- ObservableObject는 class 형태만 가능, struct X

```Swift
final class MyViewModel: ObservableObject {
  @Published var isOn = false
  
  func toggle() {
    isOn.toggle()
  }
}

// 위 모델의 인스턴스를 @ObservedObject로 참조
struct ContentView: View {
  @ObservedObject var viewModel1 = MyViewModel()
  
  var body: some View {
    VStack {
      Button(viewModel1.isOn ? "on" : "off") {
        viewModel1.toggle()
      }
    }
  }
}
```
</br>

## @StateObject
- 상태가 변경되면 뷰를 처음부터 다시 만들어서 그리는 동작이 있지만, @StateObject를 사용하면 뷰를 다시 만들지 않고 항상 동일한 뷰가 사용됨
- @ObservedObject와 동일하게 ObservableObject를 만들고, 그 인스턴스를 @StateObject로 만들어서 사용
  ```Swift
   @StateObject var viewModel1 = MyViewModel()
  ```
- 기본적으로 @StateObject를 사용하되, 해당 프로퍼티를 subview에게도 주입시켜야 한다면, @ObservedObject로 선언하여 사용할것
  - subview에 @StateObject 프로퍼티를 주입하면, 해당 @StateObject의 수명 주기가 두 곳에서 관리가 되므로 의존성을 줄이기 위해 @ObservedObejct를 사용
</br>

## @Environment
- 뷰의 환경 변수를 의미하는 property wrapper
- 내부 코드
  - DynamicProperty를 준수하고 있어서, 특정 프로퍼티의 값이 변경되면 뷰에도 업데이트되는 기능이 존재
  - key값은 EnvironmentValues 형태이며, EnvironemntValues 값은 커스텀해서 만들 수 있고 SwiftUI에서 미리 정해진 값들이 존재
- 자식뷰에서 @Environment를 선언하여, 뷰모뷰에서 주입해주는 EnvionmentValues 값을 사용
</br>

### EnvironmentValues
- 환경변수 키 값
- 부모 뷰에서 .environment(_:_:)로 주입하여 사용

```Swift
 // 부모뷰에서 주입
@main
struct ExEnvironmentApp: App {
  var body: some Scene {
    WindowGroup {
      ContentView()
        .environment(\.colorScheme, .dark) // <-
    }
  }
}

// 자식뷰에서 사용
struct ContentView: View {
  @Environment(\.colorScheme) var colorScheme // <-
  
  var body: some View {
    Text("Hello, world!")
      .padding()
      .foregroundColor(colorScheme == .dark ? .black : .white)
  }
}
```
</br>

### EnvironmentValues 종류
- SwiftUI에서 미리 정의해 둔 key, value값들이 다양하게 존재
- 대표적인 key, value
  - openURL - 딥링크 연동
  - refresh - 리프레시 액션
  - colorScheme - 일반모드/다크모드
  - locale - 현재 뷰에서 사용해야할 locale
</br>

### 커스텀 EnvironmentValues
- keyPath와 value를 직접 정의하여 사용
- keyPath - EnvironmentKey를 준수하는 구조체
- values - EnvironmentValues를 extension하여 computed property로 value 구현
```Swift
// key 정의
private struct MyEnvironmentKey: EnvironmentKey {
  static let defaultValue = "Default value"
}

// value 정의
extension EnvironmentValues {
  var myValue: String {
    get { self[MyEnvironmentKey.self] }
    set { self[MyEnvironmentKey.self] = newValue }
  }
}

//사용하는 쪽
@main
struct ExEnvironmentApp: App {
  var body: some Scene {
    WindowGroup {
      ContentView()
        .environment(\.colorScheme, .dark)
        .environment(\.myValue, "some value") // <-
    }
  }
}
```
</br>
