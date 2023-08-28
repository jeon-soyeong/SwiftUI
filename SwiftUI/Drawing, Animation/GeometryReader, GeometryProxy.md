# GeometryReader, GeometryProxy

## GeometryReader, GeometryProxy 란?
- 뷰의 Container라고 생각하면 되고, 인수로 사용할 수 있는 GeometryProxy가 존재
- GeometryProxy란 좌표와 사이즈 값을 가지고 있는 역할
- 즉, 컨테이너뷰에 관련하여 좌표를 구하고 싶을때나 컨테이너뷰의 크기를 알고 싶은 경우, proxy로 사용
```Swift
struct ContentView: View {
  var body: some View {
    VStack {
      Text("Jake iOS 앱 개발 알아가기")
      GeometryReader { proxy in
      
      }
    }
  }
}
```

- GeometryProxy 사용 방법
- size, safeAreaInsets, frame 값을 얻기가 가능
```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
public struct GeometryProxy {
    public var size: CGSize { get }
    public subscript<T>(anchor: Anchor<T>) -> T { get }
    public var safeAreaInsets: EdgeInsets { get }
    public func frame(in coordinateSpace: CoordinateSpace) -> CGRect
}
```

- frame(in:) 메소드는 CoordinateSpace가 존재
  - global: 디바이스의 스크린에 맞춘 좌표
  - local: 가장 가까운 부모 뷰와의 상대적인 좌표
  - named: 어떤 뷰에든지 .coordinateSpace(name: "someName")이름을 등록할수 있는데, 등록한 뷰에 관한 상대적인 좌표를 구하고 싶을때 사용
```Swift
var body: some View {
  VStack {
    Text("Jake iOS 앱 개발 알아가기")
    
    GeometryReader { proxy in
      Rectangle()
        .foregroundColor(.blue)
        .onAppear {
          print("size = \(proxy.size)") // (390.0, 730.357)
          print("safeAreaInsets = \(proxy.safeAreaInsets)") // EdgeInsets(top: 0.0, leading: 0.0, bottom: 34.0, trailing: 0.0)
          
          // global, local, named
          print("global = \(proxy.frame(in: .global))") // (0.0, 79.64241536458333, 390.0, 730.357)
          print("local = \(proxy.frame(in: .local))") // (0.0, 0.0, 390.0, 730.357)
          print("name = \(proxy.frame(in: .named("OuterVStack")))") // (0.0, 32.64241536458333, 390.0, 730.357)
        }
  }
  .coordinateSpace(name: "OuterVStack")
}
```
<br/>

## proxy를 이용하여 초록색 뷰 우측하단에 놓기
```Swift
// 파란 뷰 준비
struct ContentView: View {
  var body: some View {
    VStack {
      Text("Jake iOS 앱 개발 알아가기")
      
      GeometryReader { proxy in
        Rectangle()
          .foregroundColor(.blue)
      }
    }
  }
}
 

// .overay를 사용하여 파란 뷰 위에 초록색 뷰를 얹고, proxy의 size, frame 값을 이용하여 배치
GeometryReader { proxy in
  Rectangle()
    .foregroundColor(.blue)
    .onAppear {
      print("size = \(proxy.size)")
      print("safeAreaInsets = \(proxy.safeAreaInsets)")
      
      // global, local, named
      print("global = \(proxy.frame(in: .global))")
      print("local = \(proxy.frame(in: .local))")
      print("name = \(proxy.frame(in: .named("OuterVStack")))")
    }
    .overlay(
      Rectangle()
        .foregroundColor(.green)
        .frame(width: proxy.size.width/2, height: proxy.size.height/2)
        .offset(
          x: proxy.frame(in: .local).minX+proxy.size.width/2/2,
          y: proxy.frame(in: .local).minY+proxy.size.height/2/2
        )
    )
}
```
<br/>
