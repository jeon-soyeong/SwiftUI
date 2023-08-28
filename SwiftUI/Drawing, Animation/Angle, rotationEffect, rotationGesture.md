# Angle, rotationEffect, rotationGesture

## Angle
- SwiftUI 내부적으로 정의한 구조체
- Angle은 반지름(radians)와 각도 degrees를 가지고 있는 형태
- Angle은 뒤에서 알아볼 rotationGesture, rotationEffect와 같은 곳에서 사용
```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@frozen public struct Angle {

    public var radians: Double

    @inlinable public var degrees: Double

    @inlinable public init()

    @inlinable public init(radians: Double)

    @inlinable public init(degrees: Double)

    @inlinable public static func radians(_ radians: Double) -> Angle

    @inlinable public static func degrees(_ degrees: Double) -> Angle
```
<br/>

## rotationEffect
- rotationEffect 함수를 호출하여 사용
```Swift
@inlinable public func rotationEffect(_ angle: Angle, anchor: UnitPoint = .center) -> some View
```

```Swift
// ex) 글자를 20도만큼 회전
struct ContentView: View {
  @State private var angle: Angle = .init(degrees: 20)
  
  var body: some View {
    Text("Jake의 iOS 앱 개발 알아가기")
      .foregroundColor(.blue)
      .frame(width: 200, height: 200)
      .rotationEffect(angle)
  }
}
```
<br/>

## rotationGesture
- .gesture()안에 RotationGesture() 인스턴스를 넣어서 사용
```Swift
struct ContentView: View {
  @State private var angle: Angle = .init(degrees: 20)
  
  var body: some View {
    Text("Jake의 iOS 앱 개발 알아가기")
      .foregroundColor(.blue)
      .rotationEffect(angle)
      .gesture(rotationGesture) // <-
  }
  
  private var rotationGesture: some Gesture {
    RotationGesture()
      .onChanged {
        print($0)
        angle = $0
      }
  }
}
```
<br/>
