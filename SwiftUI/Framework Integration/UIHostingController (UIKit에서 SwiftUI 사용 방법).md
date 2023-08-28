# UIHostingController (UIKit에서 SwiftUI 사용 방법)

## UIHostingController
- UIKit에서 SwiftUI 뷰를 사용하고 싶을때 사용
- UIHostingController 코드를 보면 UIViewController를 상속받고 있는 형태
- 동시에 SwiftUI의 View 형태를 준수
- UIKit에서 SwiftUI 사용 원리
  - SwiftUI 뷰를 생성
  - HostingController를 만들고, 이곳의 제네릭스 타입에 위에서 생성한 뷰로 지정 (SwiftUI를 UIKit에서 접근 할 수 있도록 wrapping하는 작업)
  - UIKit에서 HostingController를 불러와서 사용
```Swift
open class UIHostingController<Content> : UIViewController where Content : View {
```
<br/>

## UIKit에서 SwiftUI 사용하기
- 예제 내용) UIKit ViewController에서 present하여 SwiftUI의 뷰(HostingController) 띄우기
  - 프로젝트 생성 > Interface를 SwiftUI가 아닌 Storyboard로 생성
  - UIKit에서 띄울 SwiftUI뷰 추가
```Swift
import SwiftUI

struct MySwiftUIView: View {
  var body: some View {
    VStack {
      Text("SwiftUI 뷰")
      Text("Hello, World!")
    }
  }
}

struct MySwiftUIView_Previews: PreviewProvider {
  static var previews: some View {
    MySwiftUIView()
  }
}
```

- SwiftUI 뷰를 UIKit에서 접근할 수 있도록 wrapping해주는 HostingController 추가 & 제네렉스 타입에 위에서만든 MySwiftUIView 지정
```Swift
import UIKit
import SwiftUI

// 제네릭스 타입에 SwiftUI 뷰를 넣어주기만하면 완성
final class MyHostingController: UIHostingController<MySwiftUIView> {
}
```

- UIKit의 ViewController에 특정 버튼을 눌렀을때 SwiftUI를 띄워야하므로 버튼 준비
```Swift
// ViewController.swift

import UIKit

class ViewController: UIViewController {
  private let button: UIButton = {
    let button = UIButton()
    button.translatesAutoresizingMaskIntoConstraints = false
    button.setTitle("버튼", for: .normal)
    button.setTitleColor(.systemBlue, for: .normal)
    return button
  }()
  
  override func viewDidLoad() {
    super.viewDidLoad()
    
		view.backgroundColor = .white

    button.addTarget(self, action: #selector(tapButton), for: .touchUpInside)
    
    view.addSubview(button)
    NSLayoutConstraint.activate([
      button.centerXAnchor.constraint(equalTo: view.centerXAnchor),
      button.centerYAnchor.constraint(equalTo: view.centerYAnchor),
    ])
  }

  // 단순히 위에서 SwiftUI 뷰를 wrapping한 MyHostingController를 일반 UIViewController 처럼 사용
  //HostingController를 초기화할땐 rootView에 SwiftUI뷰를 주입
  @objc private func tapButton() {
    present(MyHostingController(rootView: MySwiftUIView()), animated: true)
  }
}
```
<br/>
