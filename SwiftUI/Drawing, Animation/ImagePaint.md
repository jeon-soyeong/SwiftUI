# ImagePaint

## ImagePaint
- ImagePaint는 이미지를 격자 무늬로 여러개 그릴때 사용하며, ShapeStyle를 준수하고 있는 형태
- shapeStype을 준수하고 있으므로 fill()이나 stroke()안에 ImagePaint() 인스턴스를 넣어서 사용이 가능
```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@frozen public struct ImagePaint : ShapeStyle {
    public var image: Image
    public var sourceRect: CGRect
    public var scale: CGFloat

    public init(image: Image, sourceRect: CGRect = CGRect(x: 0, y: 0, width: 1, height: 1), scale: CGFloat = 1)
}
```
<br/>

## ImagePaint 사용 방법
- ShapeStyle을 준수하고 있으므로 fill, stoke에 사용
```Swift
// fill()에 사용
RoundedRectangle(cornerRadius: 20) .fill(ImagePaint(image: Image("myImage"), scale: 0.2)) 
// 0.2 - myImage크기가 크므로 작도록 스캐일 조정

// stoke()에 사용
RoundedRectangle(cornerRadius: 20)
  .stroke(ImagePaint(image: Image("myImage"), scale: 0.2), lineWidth: 30)

// stoke()에 sourceRect값을 주어서, stroke가 그럴듯하게 보이도록 사용
RoundedRectangle(cornerRadius: 20)
  .stroke(
    ImagePaint(
      image: Image("myImage"),
      sourceRect: CGRect(x: 0.3, y: 0.3, width: 0.5, height: 0.5),
      scale: 1.0
    ),
    lineWidth: 40
  )
  .frame(width: 300, height: 500)
```
<br/>
