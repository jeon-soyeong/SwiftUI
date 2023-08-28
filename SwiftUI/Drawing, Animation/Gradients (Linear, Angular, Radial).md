# Gradients (Linear, Angular, Radial)

- Swift에서 알아본 CAGradientLayer와 개념은 동일하며, SwiftUI에서는 각 속성이 더 직관적인 이름으로 변경
  - axial(linear) -> Linear
  - radial(circle) -> Radial
  - conic(sweep) -> Angular
<br/>

## LinearGradient
- 요소
  - 색상 입력 - colors
  - 시작 지점 - startPoint
  - 종료 지점 - endPoint
```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@frozen public struct LinearGradient : ShapeStyle, View {
    public init(gradient: Gradient, startPoint: UnitPoint, endPoint: UnitPoint)
    public init(colors: [Color], startPoint: UnitPoint, endPoint: UnitPoint)
    public init(stops: [Gradient.Stop], startPoint: UnitPoint, endPoint: UnitPoint)
    public typealias Body
}
```

```Swift
LinearGradient(colors: [.blue, .orange], startPoint: .topLeading, endPoint: .bottomTrailing)
  .overlay(Text("LenarGradient"))
```
<br/>

## RadialGradient
- 요소
  - 색상 입력 - colors
  - 중앙 지점 - center
  - 시작 각도 - startRadius
  - 종료 각도 - endRadius
```Swift
RadialGradient(colors: [.blue, .orange], center: .center, startRadius: 0, endRadius: 270)
  .overlay(Text("RadialGradient"))
```
<br/>

## AngularGradient
- 요소
  - 색상 입력 - colors
  - 중앙 지점 - center
```Swift
AngularGradient(colors: [.blue, .orange], center: .center)
  .overlay(Text("AngularGradient"))
```
<br/>
