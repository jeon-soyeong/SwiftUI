# Shape, InsettableShape

## Shape
- 2D 모양의 뷰를 의미하는 프로토콜 (Circle, Capsule, Ellipse, ... 등)
<br/>

- Shape 프로토콜 형태
- path(in:)->Path: shape의 형태를 Path로 리턴
- role: 모양을 채우는 스타일을 정의하며, 결합 모양(composite shape)과 같은 것을 만들 때 이 프로퍼티를 재정의하여 사용
- default implementation이 존재 - ShapeRole.fill이 있고 stroke와 separator가 있는 타입
```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
public protocol Shape : Animatable, View {
    func path(in rect: CGRect) -> Path

    static var role: ShapeRole { get }
}
```

- ex) Shape 프로토콜을 준수하는 대표적인 Shape - Capsule()
```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@frozen public struct Capsule : Shape {

    public var style: RoundedCornerStyle

    @inlinable public init(style: RoundedCornerStyle = .circular)

    public func path(in r: CGRect) -> Path

    public typealias AnimatableData = EmptyAnimatableData

    public typealias Body
}
```

- 커스텀 Shape 만들기 (Circle)
- Shape를 준수하고, path(in:) -> Path 메소드 구현
```Swift
struct ContentView: View {
  var body: some View {
    MyCircle()
      .frame(width: 120, height: 120)
      .foregroundColor(.blue)
  }
}

struct MyCircle: Shape {
  func path(in rect: CGRect) -> Path {
    var path = Path()
    path.addArc(
      center: CGPoint(x: rect.midX, y: rect.midY), // 중심
      radius: rect.width / 2, // 반지름
      startAngle: Angle.degrees(180), // 시작 위치
      endAngle: Angle.degrees(-180), // 끝 위치
      clockwise: true // true - 시계 방향, false - 반시계 방향
    )
    return path
  }
}
```
<br/>

## InsettableShape
- 사용하는 쪽에서 inset(by:)를 사용하여 frame에 대해서 inset 값을 줄 수 있는 형태
```Swift
// MyCicle에 InsettableShape를 준수하고, inset(by:) -> InsettableShape 메소드를 정의
struct MyCircle: Shape, InsettableShape {
  func path(in rect: CGRect) -> Path {
    ...
  }
  
  func inset(by amount: CGFloat) -> some InsettableShape {
    // TODO
  }
}

/*
inset(by:) 구현부에서는 현재 self 인스턴스를 복사한 후 처리하는 방식
insetValue라는 프로퍼티 선언
path 내부에서 초기화할 때 insetValue를 이용
inset(by:) 메소드 내부에서 insetValue를 적용
*/
struct MyCircle: Shape, InsettableShape {
  private var insetValue = 0.0 // <-
  
  func path(in rect: CGRect) -> Path {
    var path = Path()
    path.addArc(
      ...
      radius: rect.width / 2 - insetValue, // <-
      ...
    )
    return path
  }
  
  func inset(by amount: CGFloat) -> some InsettableShape {
    var myCircle = self
    myCircle.insetValue = amount // <-
    return myCircle
  }
}

// 사용하는 쪽
struct ContentView: View {
  var body: some View {
    MyCircle()
      .inset(by: 20) // <-
      .frame(width: 120, height: 120)
      .foregroundColor(.blue)
  }
}
```
<br/>
