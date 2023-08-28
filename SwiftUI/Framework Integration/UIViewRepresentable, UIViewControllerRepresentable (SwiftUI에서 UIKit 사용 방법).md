# UIViewRepresentable, UIViewControllerRepresentable (SwiftUI에서 UIKit 사용 방법)

## SwiftUI에서 UIKit 사용 방법
- UIView 관련 사용 - UIViewRepresentable
- UIViewController 관련 사용 - UIViewControllerRepresentable
<br/>

## UIViewRepresentable 사용 방법
- SwiftUI에서 UIKit에 있는 뷰를 사용할 때 이용
  - makeUIView에 UIKit 인스턴스를 리턴
  - updateUIView은 SwiftUI에서 뷰가 업데이트 될 때 불리는 메소드로, 이곳에서 delegate와 같은 처리
```Swift
public protocol UIViewRepresentable : View where Self.Body == Never {
	func makeUIView(context: Self.Context) -> Self.UIViewType
	func updateUIView(_ uiView: Self.UIViewType, context: Self.Context)
}
```

- SwiftUI에서 UITableView 사용 방법
  - makeUIView에서 UITableView() 인스턴스를 리턴
```Swift
struct MyTableView: UIViewRepresentable {
  // MARK: Properties
  @Binding var isPresented: Bool
  
  // MARK: Methods
  func makeUIView(context: Context) -> UITableView {
    UITableView()
  }
  func updateUIView(_ uiView: UITableView, context: Context) {
  }
}
```

- 이제 MyTableView를 일반 SwiftUI의 뷰처럼 사용이 가능
- 사용하는 쪽 코드
// ContentView.swift

```Swift
struct ContentView: View {
  @State private var isTableViewOpend = false
  
  var body: some View {
      Button {
        isTableViewOpend = true
      } label: {
        Text("Open MyTableView")
      }
      MyTableView(isPresented: $isTableViewOpend)
    }
    .background(.white)
  }
}
```
- UITableView 는 dataSource 설정을 해주어야 데이터를 사용할 수 있는데, 이때 사용되는 개념이 Coordinator
- Coordinator 란 SwiftUI에서 UITableView와 같은 것을 사용할 때 dataSource를 전달해주어야 하는데 이때 사용
- isPresented 라는 @Binding 프로퍼티가 업데이트 되면 MyTableView의 updateUIView(_:context:)가 호출되고 여기서 view에 context.coordinator로 주입해주면 적용 완료
- Coordinator라는 것은 UITableViewDataSource, UITableViewDelegate를 준수하도록 MyTableView 내부에 구현
```Swift
struct MyTableView: UIViewRepresentable {
  // MARK: Types
  class Coordinator: NSObject, UITableViewDelegate, UITableViewDataSource {
    private let sampleData = (0...20).map(String.init)
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
      let cell: UITableViewCell = tableView.dequeueReusableCell(withIdentifier: "cell", for: indexPath)
      cell.textLabel?.text = sampleData[indexPath.row]
      cell.textLabel?.textColor = .black
      cell.backgroundColor = .white
      return cell
    }
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
      sampleData.count
    }
  }
  
  // MARK: Properties
  @Binding var isPresented: Bool
  
  // MARK: Methods
  func makeUIView(context: Context) -> UITableView {
    UITableView()
  }
  func updateUIView(_ uiView: UITableView, context: Context) {
    guard isPresented else { return }
    uiView.register(UITableViewCell.self, forCellReuseIdentifier: "cell")
    uiView.delegate = context.coordinator
    uiView.dataSource = context.coordinator
  }
  func makeCoordinator() -> Coordinator {
    Coordinator()
  }
}
```
<br/>

## UIViewControllerRepresentable 사용 방법
- UIKit의 ViewController를 나타내는 프로토콜
- 위에서 알아본 UIViewRepresentable 원리와 동일하게 사용
- 형태
  - makeUIViewController(context:) 를 구현하면 SwiftUI에서 바로 사용이 가능

```Swift
public protocol UIViewControllerRepresentable : View where Self.Body == Never {
	func makeUIViewController(context: Self.Context) -> Self.UIViewControllerType

}
```

- UIViewControllerRepresentable를 준수하는 MyActivityView 구현
```Swift
struct MyActivityView: UIViewControllerRepresentable {
  var activityItems: [Any]
  var applicationActivities: [UIActivity]? = nil
  
  func makeUIViewController(context: UIViewControllerRepresentableContext<MyActivityView>) -> UIActivityViewController {
    UIActivityViewController(activityItems: activityItems, applicationActivities: applicationActivities)
  }
  
  func updateUIViewController(
    _ uiViewController: UIActivityViewController,
    context: UIViewControllerRepresentableContext<MyActivityView>
  ) {}
}
```

- 사용하는 쪽
```Swift
struct ContentView: View {
  @State private var isActivityViewOpend = false
  
  let uiImage = UIImage(systemName: "circle")
  
  var body: some View {
    VStack {
      Button {
        isActivityViewOpend = true
      } label: {
        Text("Open Activity")
      }
      .sheet(isPresented: $isActivityViewOpend, onDismiss: {
        print("Dismiss")
      }, content: {
        MyActivityView(activityItems: [uiImage])
      })
    }
  }
}
```
<br/>
