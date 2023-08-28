# GeometryEffect

## GeometryEffect
- 위치를 변경해주는 이펙트 형태를 갖고 있는 프로토콜
  - effectValue 메소드에서 CGAffineTransform과 같은 위치 관련 변환하는 코드를 넣고 리턴
  - GeometryEffect라는 프로토콜을 준수하는 struct를 만들고, 사용하는 쪽에서 .modifier()와 같은 곳에 주입하여 사용
  - main thread에서 위치를 보간할 때마다(= 애니메이션이 적용될 때마다) 계속 불리는 메소드가 effectValue
```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
public protocol GeometryEffect : Animatable, ViewModifier where Self.Body == Never {

    /// Returns the current value of the effect.
    func effectValue(size: CGSize) -> ProjectionTransform
}
```
<br/>

## 사용 방법
- 예시 코드) 버튼을 눌렀을 때 따봉 애니메이션 넣기
```Swift
/*
GeometryEffect를 이용한 따봉 애니메이션
사용하는쪽에서 modifier에 LikeEffect라는, GeometryEffect프로토콜을 준수하고 있는 인스턴스를 삽입하여 사용
LikeEffect에는 offset 인수로 likes라는 값을 삽입
offset에 likes가 들어갈때 LikeEffect 내부적으로는 보간
보간 형태
0이 주입되면 0.0, 0.1, 0.2, ... 1.0 
1이 주입하면, 1.0, 1.1, 1.2, ... 2.0
2가 주입되면, 2.0, 2.1, 2.2, ... 3.0
*/
struct ContentView: View {
  @State var likes = 0.0
  
  var body: some View {
    HStack {
      Text("likes: \(Int(likes))")
        .frame(width: 150, alignment: .leading)
        .padding()
      Button(
        action: { withAnimation(.spring()) { self.likes += 1 } },
        label: {
          HStack {
            Text("like more")
            Image(systemName: "hand.thumbsup")
              .modifier(LikeEffect(offset: likes)) // <-
          }
        }
      )
    }
  }
}

// LikeEffect 구현
// GeometryEffect 준수하고 effectValue(size:) 메소드 구현
struct LikeEffect: GeometryEffect {
  var offset: Double
  
  func effectValue(size: CGSize) -> ProjectionTransform {
    /// offset에 1이 들어온 경우: 0.0, 0.1, 0.2, 0.3 ... 1.0
    /// offset에 2가 들어온 경우: 1.0, 1.1, 1.2, 1.3 ... 2.0
    // TODO
  }
}

// GeometryEffect는 내부적으로 Animatable을 준수하고 있으므로, 애니메이션 효과가 동작하려면 animatableData도 구현 필요
// Animatable, animatableData 관련 개념은 이전 포스팅 글 참고
struct LikeEffect: GeometryEffect {
  var offset: Double
  
  // 추가
  var animatableData: Double {
    get { offset }
    set { offset = newValue }
  }  
  
  func effectValue(size: CGSize) -> ProjectionTransform {
    /// offset에 1이 들어온 경우: 0.0, 0.1, 0.2, 0.3 ... 1.0
    /// offset에 2가 들어온 경우: 1.0, 1.1, 1.2, 1.3 ... 2.0
    // TODO
  }
}

// effectValue 메소드 구현
  func effectValue(size: CGSize) -> ProjectionTransform {
    /// offset에 1이 들어온 경우: 0.0, 0.1, 0.2, 0.3 ... 1.0
    /// offset에 2가 들어온 경우: 1.0, 1.1, 1.2, 1.3 ... 2.0
    
    /// 0.0 ~ 1.0까지의 값: 0.0, 0.1, ... 0.9, 0.8, 0.9, 1.0
    let reducedValue = offset - floor(offset)
    print(offset, reducedValue)
    /// 움직인 각도
    let value = 1.0-(cos(2*reducedValue*Double.pi)+1)/2
    let angle = CGFloat(Double.pi*value*0.3)
    let translation = CGFloat(20*value)
    let scaleFactor = CGFloat(1+value)
    
    let affineTransform = CGAffineTransform(translationX: size.width*0.5, y: size.height*0.5)
      .rotated(by: CGFloat(angle))
      .translatedBy(x: -size.width*0.5+translation, y: -size.height*0.5-translation)
      .scaledBy(x: scaleFactor, y: scaleFactor)
    
    return ProjectionTransform(affineTransform)
  }
```
<br/>
