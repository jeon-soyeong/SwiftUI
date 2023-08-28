# ScaledShape, RotatedShape, OffsetShape, TransformedShape

## ScaledShape, RotatedShape, OffsetShape
- 3가지 모두 인자로 shape를 넣고, 두번째 파라미터에 scale, angle, offset을 넣어서 도형들을 편리하게 사용할 수 있는 구조체
```Swift
// ex) ScaledShape(shape:scale:)
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@frozen public struct ScaledShape<Content> : Shape where Content : Shape {
    public var shape: Content
    public var scale: CGSize
    public var anchor: UnitPoint

    @inlinable public init(shape: Content, scale: CGSize, anchor: UnitPoint = .center)

    public func path(in rect: CGRect) -> Path

    @available(iOS 15.0, macOS 12.0, tvOS 15.0, watchOS 8.0, *)
    public static var role: ShapeRole { get }

    public typealias AnimatableData = AnimatablePair<Content.AnimatableData, AnimatablePair<CGSize.AnimatableData, UnitPoint.AnimatableData>>
    public var animatableData: ScaledShape<Content>.AnimatableData

    public typealias Body
}

/*
- 모두 비슷한 방법대로 사용
  - ScaledShape(shape:scale:)
  - RotatedShape(shape:angle:)
  - OffsetShae(shape:offset:)
*/
struct ContentView: View {
  var body: some View {
    VStack {
      Group {
        ScaledShape(shape: Rectangle(), scale: CGSize(width: 0.7, height: 0.7))
        Divider()
        RotatedShape(shape: Rectangle(), angle: Angle(degrees: 45))
        Divider()
        OffsetShape(shape: Rectangle(), offset: CGSize(width: 100, height: 30))
      }
      .foregroundColor(.blue)
      .frame(width: 100, height: 100)
    }
  }
}
```
<br/>

## TransformedShape
- 특정 Shape를 넣고 CGAffineTransform값을 넣어서 affine 변환에 사용
- affine 변환: offset, scaled, rotated 등 모든 변환을 의미
- offset, scaled, rotated 혼합하여 적용시키고 싶을 때 사용

```Swift
// 사용 방법
// CGAffineTransform을 따로 정의
var myTransformation: CGAffineTransform {
    var affineTransform = CGAffineTransform(translationX: 100, y: 30)
    affineTransform = affineTransform.scaledBy(x: 0.7, y: 0.7)
    affineTransform = affineTransform.rotated(by: 45)
    return affineTransform
  }

// TransformedShape(shape:transform:) 사용
var body: some View {
    VStack {
      Group {
        TransformedShape(shape: Rectangle(), transform: myTransformation)
      }
      .foregroundColor(.blue)
      .frame(width: 100, height: 100)
    }
  }
```
<br/>
