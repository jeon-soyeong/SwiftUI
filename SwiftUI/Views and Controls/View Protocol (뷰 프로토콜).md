# View Protocol (뷰 프로토콜)
<br/>

## View Protocol
- View 프로토콜
- 프로토콜이고, body라는 computed property를 가지고 있는 타입
- view를 입력할 수 있게 하는 인터페이스를 제공하는 역할

```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
public protocol View {
  associatedtype Body : View
  
  @ViewBuilder var body: Self.Body { get }
}
```
<br/>

## @ViewBuilder
- @resultBuilder를 사용하여 정의한 struct 형
- @resultBuilder는 swift5.4에서부터 나온 api
- (@resultBuilder를 사용하면 콤마 없이 나열이 가능)

```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@resultBuilder public struct ViewBuilder {
  public static func buildBlock() -> EmptyView
  
  public static func buildBlock<Content>(_ content: Content) -> Content where Content : View
}
```
<br/>

- 즉, body는 ViewBuilder로 정의되어 있고, 이것은 @resultBuilder를 사용했으므로 아래처럼 콤마 없이 뷰를 나열하여 사용할 수 있음
```Swift
struct ContentView: View {
  var body: some View {
    VStack {
      // View1
      Image(systemName: "globe")
        .imageScale(.large)
        .foregroundColor(.accentColor)
      
      // View2
      Text("Hello, world!")
    }
  }
}
```
- View의 특징은 class형태가 아닌, value type을 사용하기 위해서 Struct로 정의
<br/>

## SwiftUI에서 뷰들을 Struct으로 정의하는 이유
- 성능
  - Struct는 Class보다 단순하고 빠른 특징이 존재
  - UIView같은 경우에는, constrant, layer 등 많은 property와 메서드를 가진 클래스에의 subclass인데, UIView의 서브클래스들은 모두 UIView의 속성을 그대로 가져가야만 하는 구조
  - (애플 문서만 봐도, UIView관려 속성이 200개 넘게 존재)
  - SwiftUI에서는 상속을 대신하여 ViewModifier 개념을 사용하여 컴포넌트화 할 수 있도록 제공
- 가변성을 최소화 (함수형 프로그래밍의 핵심)
  - class는 reference type이므로 어디에서도 값을 쉽게 바꿀 수 있지만,
  - Struct는 value type이므로 앱의 전체 상태를 고려할 필요 없이 코드의 일부에 대해서 더 쉽게 추론할 수 있는 장점이 존재
  - 가변성을 최대한 멀리하는게 함수형 프로그래밍의 핵심인데 이때 class보다 Struct가 더 유리 (=프로그램 실행 중에 상태가 변화하는 것을 최소화하는게 좋은 코드)
- 메모리릭 방지
  - Reference type을 사용하다보면 retain cycle때문에 메모리에서 해제가 안되는(memory leak) 현상이 발생하는데, 이 때 계속해서 메모리를 차지하는게 늘어나면서 크래시가 나는 반면에, value type인 struct을 쓰면 Memory leak에 안전한 프로그래밍이 가능
<br/>
