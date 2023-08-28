# TextField

## TextField
- 값을 입력받을 수 있는 컴포넌트
- SwiftUI 에서는 @State 상태 프로퍼티 하나를 두고 값이 입력되면 여기에도 입력되도록 구현
```Swift
enum Field {
  case username
  case email
}

@State private var username = ""
@State private var email = ""
  
var body: some View {
  VStack {
    TextField(
      "User name",
      text: $username
    )
    
    TextField(
      "Email",
      text: $email
    )
    
    ...
```
<br/>

## @FocusState
- 텍스트 필드의 포커스 (firstResponder) 관리는 @FocusState를 사용하면 매우 손쉽게 코드에서도 제어가 가능
- @FocusState 프로퍼티를 선언하고, 이 프로퍼티에 바인딩
- 여기서 바인딩의 의미: 양방향 업데이트
- TextField가 포커스를 얻으면 focusField 프로퍼티에 값을 변경하고 focusField의 값이 변경되면 TextField에 focus를 획득

```Swift
@FocusState private var focusField: Field?

var body: some View {
  VStack {
    TextField(
      "User name",
      text: $username
    )
    .focused($focusField, equals: .username)
    
    TextField(
      "Email",
      text: $email
    )
    .focused($focusField, equals: .email)
    ...
```

- 버튼을 추가하고 여기서 focusField를 변경
- username가 입력 안되었으면 username으로 포커싱
- email이 입력 안되었으면 email로 포커싱
```Swift
      Button("Sign in") {
        if username.isEmpty {
          focusField = .username
        } else if email.isEmpty {
          focusField = .email
        } else {
          print("Complete Input and sign in...")
        }
      }
```
<br/>

## TextField 스타일
```Swift
// 키보드 타입 변경 - .keyboardType(_:)
// number 패드로 변경
TextField(
  "User name",
  text: $username
)
.keyboardType(.numberPad)

// return key를 눌렀을 때 처리 - .onSubmit { }

//첫 글자를 자동으로 대문자로 설정하는 기능 비활성화
.textInputAutocapitalization(.never)

//자동 맞춤법 수정 비활성화
.disableAutocorrection(true)

//텍스트 필드 rounded 윤곽선 표출
.textFieldStyle(.roundedBorder)
```
<br/>

## 키보드 숨기기, 내리기
```Swift
// 키보드를 내리는 메소드를 View에서 쉽게 사용하기 위해 extension으로 정의
extension View {
  func hideKeyboard() {
    UIApplication.shared.sendAction(#selector(UIResponder.resignFirstResponder), to: nil, from: nil, for: nil)
  }
}

// 버튼을 탭하고 모든 TextField에 입력된 경우, 키보드 숨기기 적용
Button("Sign in") {
  if username.isEmpty {
    focusField = .username
  } else if email.isEmpty {
    focusField = .email
  } else {
    hideKeyboard() // <-
    print("Complete Input and sign in...")
  }
}

// 빈 공간을 탭한 경우에도 키보드 숨기기 방법
// VStack에 onTapGesture { }로 탭 이벤트를 처리
// 현재 VStack로 감싸져 있는데, 이 VStack은 내부 contents의 크기만큼만 늘어나 있기 때문에 터치 영역이 앱 화면 전체가 되려면 VStack의 크기를 디바이스의 크기와 동일하게 설정
VStack {
  ...
}
.frame(maxWidth: .infinity, maxHeight: .infinity) // <-
.onTapGesture { // <-
  hideKeyboard()
}
```
<br/>
