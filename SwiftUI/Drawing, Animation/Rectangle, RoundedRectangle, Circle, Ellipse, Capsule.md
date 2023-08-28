# Rectangle, RoundedRectangle, Circle, Ellipse, Capsule

- 도형들은 모두 Shape를 준수
- 대표적인 예로 Rectangle을 보면 Shape를 준수
```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@frozen public struct Rectangle : Shape {
    public func path(in rect: CGRect) -> Path

    @inlinable public init()

    public typealias Body
}
```

- Shape는 내부적으로 Animatable을 준수하고 있는 형태
- Shape는 path라는 메소드가 있는데, 이 메소드에서 특정 도형을 그리는 형태
```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
public protocol Shape : Animatable, View {
    func path(in rect: CGRect) -> Path
    @available(iOS 15.0, macOS 12.0, tvOS 15.0, watchOS 8.0, *)
    static var role: ShapeRole { get }
}
```
<br/>

## Rectangle, RoundedRectangle, Circle, Ellipse, Capsule
- 사용 방법은 모두 View처럼 선언하여 사용
- 아래 예제에서는 overlay를 붙여서, 도형에 텍스트를 추가한 코드
```Swift
struct ContentView: View {
  var body: some View {
    VStack {
      Group {
        Rectangle()
          .overlay { Text("Rectangle").foregroundColor(.white) }
        RoundedRectangle(cornerSize: .init(width: 16, height: 16))
          .overlay { Text("RoundedRectangle").foregroundColor(.white) }
        Circle()
          .overlay { Text("Circle").foregroundColor(.white) }
        Ellipse()
          .overlay { Text("Ellipse").foregroundColor(.white) }
        Capsule()
          .overlay { Text("Capsule").foregroundColor(.white) }
      }
      .foregroundColor(.blue)
      Spacer()
    }
    .frame(maxWidth: 200)
  }
}
```
<br/>
