# Data Binding

## @State, @Binding
사용자 인터페이스 레이아웃 내의 뷰 상태를 저장하는 데 사용되며, 현재 콘텐트 뷰에 관한 것이다. 
이 값은 임시적이여서 해당 뷰가 사라지면 값도 없어진다.
* 뷰 내부에서 특정 View 의 상태를 나타내는 변수
* 뷰 내부에서 밖에 사용이 불가능함 때문에 private로 선언
* 하위 뷰나 다른 뷰에서 참조하기 위해선 @Binding 해야함
* state property에 해당하는 변수 값이 변경되면 view를 다시 렌더링한다.
    * 때문에 항상 최신값을 가진다.
* 뷰 전체가 다시 렌더링 되는일을 막기 위해 하위뷰로 데이터 변동이 반영되는 뷰만 따로 빼준다.
    * 따로 뺀 뷰에 state property 를 binding 해준다.
<br/>

## @State
- 내부 코드
- @State 프로퍼티는 항상 private으로 선언하고 가장 상위 뷰에서 @State를 관리할 것
   - 뷰를 초기화할때, @State 프로퍼티 값도 같이 초기화하게 되면 SwiftUI에서 @State 프로퍼티를 관리하는 공간인 Storage에서 conflict가 나기 때문
- 만약 상위 뷰에서의 State값을 하위 뷰에서도 접근할 수 있게하려면 Binding을 하위 뷰에 넘겨주거나 상위 뷰에서 read-only property로 설정하여 값을 공유
```Swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@frozen @propertyWrapper public struct State<Value> : DynamicProperty {
    public init(wrappedValue value: Value)
    public init(initialValue value: Value)
    
    public var wrappedValue: Value { get nonmutating set }
    public var projectedValue: Binding<Value> { get }
}
```

```Swift
struct ContentView: View {
  @State private var isPlaying = false
  
  var body: some View {
    PlayButton(isPlaying: $isPlaying) // <- 자식 뷰에서 @State를 @Binidng으로 변경하고, 부모 뷰에서 자식 뷰로 넘겨줄때 Binding을 넘겨주는 방식
      .onAppear {
        DispatchQueue.main.asyncAfter(deadline: .now() + 3) {
          print("isPaying = \(isPlaying)")
        }
      }
  }
}

struct PlayButton: View {
  @Binding var isPlaying: Bool // <-
  
  var body: some View {
    Button(isPlaying ? "Pause" : "Play") {
      isPlaying.toggle()
    }
  }
}
```
<br/>

##  @ObservedObject, @Published
- 사용자 인터페이스 밖에 있으며 앱 내의 SwiftUI뷰 구조체의 하위 뷰에만 필요한 데이터는 Observable객체 프로퍼티를 사용해야 한다. 
- 이 방법을 사용하면 데이터를 표시하는 클래스는 ObservableObject 프로토콜을 따라야 하며, 뷰와 바인딩 될 프로퍼티는 @Published 프로퍼티 래퍼를 사용하여 선언되어야 한다. 
- 뷰 선언뷰에 Observable 객체 프로퍼티와 바인딩 하려면 프로퍼티는 @ObservedObject 프로퍼티 래퍼를 사용해야 한다.
<br/>

## @EnvironmentObject
- 앱 내의 여러 뷰가 동일한 구독 객체에 접근해야 하는 경우 사용한다.

- Environment 객체는 Observable 객체와 같은 방식으로 선언된다.
- 즉, 반드시 Observable Object프로토콜을 따라야 하며, 적절한 프로퍼티가 게시되어야 한다. 
- 하지만, 중요한 차이점은 이 객체는 SwiftUI 환경에 저장되며, 뷰에서 뷰로 전달할 필요없이 모든 뷰가 접근할 수 있다는 것이다.
- SwiftUI 뷰 파일 내에 선언된다. 
- SceneDelegate.swift 파일의 코드를 통해 뷰 화면이 앱에 추가될 때 Environment 객체 또한 초기화되어야 한다.
<br/>

- parent 뷰에서 정의한 observable 객체이며, subview들이 접근 가능한 프로퍼티로 property wrapper 타입
- 즉, parent 뷰에서 subview에 특정 상태를 주입하여, subview에서 그 상태에 접근할때 사용
- subview에서 superview로부터 주입받은 @EnvironmentObject 프로퍼티를 변경시키면 부모에서도 모두 뷰가 업데이트

```Swift
class MyObservableObject: ObservableObject {
  @Published var isOn = false
}

struct MyView: View {
  @EnvironmentObject var myObject: MyObservableObject // ObservableObject타입을 준수해야됨
  
  var body: some View {
    Button(myObject.isOn ? "child, isOn" : "child, isOff") {
      myObject.isOn.toggle()
      print(myObject.isOn)
    }
  }
}

struct ContentView: View {
  @StateObject var myObject = MyObservableObject()
  
  var body: some View {
    VStack {
      Text(myObject.isOn ? "isOn" : "isOff")
      Divider()
      MyView() // <-
        .environmentObject(myObject)
    }
  }
}
// 결과) 자식 뷰 MyView()에서 주입받은 myObject의 isOn 값을 변경하면 부모 뷰에도 isOn값이 변경
```
<br/>
