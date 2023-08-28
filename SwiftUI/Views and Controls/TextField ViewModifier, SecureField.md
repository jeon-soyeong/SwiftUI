# TextField ViewModifier, SecureField

## TextField ViewModifier
```Swift
// TextField도 마찬가지로 ViewModifier 프로토콜을 구현하는 Struct를 만들어서 구현
struct PrimaryRoundTextField: ViewModifier {
  func body(content: Content) -> some View {
  
  }
}
 
// 텍스트 필드를 사용할 때 일반적으로 불필요한 옵션들 (자동 완성, 첫번째 글자가 대문자로 변환)을 disable 하는 부분도 선언
struct PrimaryRoundTextField: ViewModifier {
  func body(content: Content) -> some View {
    content
      .font(.system(size: 16))
      .textFieldStyle(.roundedBorder)
      .foregroundColor(.black)
      .textInputAutocapitalization(.never)
      .disableAutocorrection(true)
  }
}

// 사용하는쪽
// TextField를 사용할 땐 placeholder부분과 입력되는 상태를 저장할 @State 프로퍼티가 필요
TextField 선언
struct ContentView: View {
  @State private var username = ""
  
  var body: some View {
    TextField(
      "입력하세요",
      text: $username
    )
    .padding()
  }
}

// .modifier()를 통해 위에서 정의한 인스턴스를 주입하여 완성
struct ContentView: View {
  @State private var username = ""
  
  var body: some View {
    TextField(
      "입력하세요",
      text: $username
    )
      .modifier(PrimaryRoundTextField()) // <-
      .padding()
  }
}
```
<br/>

## SecureField
- SecureField는 TextField와 동일한 방법으로 사용(비밀번호 안보이게 처리할 때 사용하는)
```Swift
@State private var password = ""

SecureField(
  "Password",
  text: $password
)
```
<br/>
