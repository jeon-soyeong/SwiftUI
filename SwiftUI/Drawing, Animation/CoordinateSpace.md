# CoordinateSpace

## coordinateSpace(name:) 란?
- “특정 뷰의 좌표 공간”에 이름을 할당하여, 다른쪽에서 points와 size같은 값을 적용시킬때 이 이름으로 접근 할 수 있게하는 하나의 namespace 역할

- 메소드 시그니쳐
- View의 Extension하여 메소드가 정의되므로, 어떤 뷰에서든 사용이 가능
- 이름을 입력하면 View를 리턴
```Swift
extension View {
  @inlinable public func coordinateSpace<T>(name: T) -> some View where T : Hashable
}
```
<br/>

## coordinateSpace(name:) 사용 방법
- 애플 예제)
- VStack안에 overlay로 circle을 두고, 이 circle을 움직임이 VStack 기준의 좌표값을 알고 싶은 경우?
- Circle에 drag 제스쳐를 등록하고나서 drag제스쳐에 coordinateSpace를 VStack으로 설정하여 구현
- // https://developer.apple.com/documentation/swiftui/view/coordinatespace(name:)
```Swift
struct ContentView: View {
  @State var location = CGPoint.zero
  
  var body: some View {
    VStack {
      Color.red.frame(width: 100, height: 100)
        .overlay(circle)
      Text("Location: \\(Int(location.x)), \\(Int(location.y))")
    }
    .coordinateSpace(name: "stack")
  }
  
  var circle: some View {
    Circle()
      .frame(width: 25, height: 25)
      .gesture(drag)
      .padding(5)
  }
  
  var drag: some Gesture {
    DragGesture(coordinateSpace: .named("stack"))
      .onChanged { info in location = info.location }
  }
}
```
<br/>
