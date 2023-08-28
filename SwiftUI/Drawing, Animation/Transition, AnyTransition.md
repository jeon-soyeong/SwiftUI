# Transition, AnyTransition

## Transition
- 뷰를 보여질 때와 제거할 때의 애니메이션
  - 주의: Transition 의미는 뷰를 이동할 때의 개념보다는, 뷰를 보여지게 할 때와 사라지게 할 때의 애니메이션 개념으로 이해
- 뷰에 .transition(_:)으로 선언하여 사용이 가능
- .transition(_:)에 들어가는 인수는 AnyTransition

## AnyTransition
- 위에서 알아봤듯이 .transition(_:) 인수에 들어가는 값
```Swift
/// A type-erased transition.
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@frozen public struct AnyTransition {
}
```

- AnyTransition은 extension으로 여러가지 애니메이션 속성이 존재
```Swift
extension AnyTransition {

    public static func offset(_ offset: CGSize) -> AnyTransition

    public static func offset(x: CGFloat = 0, y: CGFloat = 0) -> AnyTransition
}

@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
extension AnyTransition {

    public static var scale: AnyTransition { get }

    public static func scale(scale: CGFloat, anchor: UnitPoint = .center) -> AnyTransition
}

@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
extension AnyTransition {

    /// A transition from transparent to opaque on insertion, and from opaque to
    /// transparent on removal.
    public static let opacity: AnyTransition
}

@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
extension AnyTransition {

    /// A transition that inserts by moving in from the leading edge, and
    /// removes by moving out towards the trailing edge.
    ///
    /// - SeeAlso: `AnyTransition.move(edge:)`
    public static var slide: AnyTransition { get }
}

...
```
<br/>

- transition 사용 방법
```Swift
// transition을 적용할 뷰 준비
// 토글을 하면 Rectangle()이 트랜지션 애니메이션이 동작하는 효과
struct ContentView: View {
  @State private var isOn: Bool = false
  var body: some View {
    VStack {
      Toggle("트랜지션 토글", isOn: $isOn)
      Spacer()
      if isOn {
        Rectangle()
          .foregroundColor(.blue)
          // TODO: transition 적용
      }
    }
  }
}

//transition 적용
// animation과 같이 적용해야 transition 효과 확인이 가능
Rectangle()
  .foregroundColor(.blue)
  .animation(.easeIn) // <-
  .transition(.slide) // <-

```
<br/>

## AnyTransition의 종류
- slide (위에서 알아본 애니메이션)
  - navigation link와 같은 애니메이션
```Swift
.animation(.easeIn)
.transition(.slide)
```

- move(_:)
  - .bottom을 주게 되면 .sheet와 같은 애니메이션 효과
```Swift
.animation(.easeIn)
.transition(.move(edge: .bottom))
```

- opacity
  - opacity는 자체적으로 animation을 설정하여 인수로 넘겨주어야 하므로 위에 있던 .animation(.easeIn) 삭제
```Swift
Rectangle()
  .foregroundColor(.blue)
//  .animation(.easeIn)
  .transition(.opacity.animation(.easeIn))
```

- scale
  - scale도 opacity와 마찬가지로 자체 animation으로 넘겨주어야 애니메이션 동작
```Swift
Rectangle()
  .foregroundColor(.blue)
//  .animation(.easeIn)
  .transition(.scale.animation(.easeIn))
```

- ex) 보여질때는 slide, 사라질때는 opacity
```Swift
Rectangle()
  .foregroundColor(.blue)
  .animation(.easeIn)
  .transition(.asymmetric(insertion: .slide, removal: .opacity.animation(.easeIn)))
```
<br/>
