# Alert, ActionSheet (.alert, .confirmationDialog)

## Alert
- iOS 13~iOS 15까지는 위 Alert를 사용했지만, deprecated
- iOS 15부터는 .alert 사용을 권장
- .alert는 더욱 선언적으로 alert를 사용할 수 있는 방법
- 위 Alert를 하나의 View로 존재했지만, iOS15부터는 메소드로 사용이 가능 (더욱 선언적 프로그래밍)
<br/>

- 사용 방법은 Button에 .alert하여 추가
  - alert가 보이는 상태 변수도 하나 추가
  - .alert 메소드에는 titleKey, isPresented, View 순서대로 주입하여 사용
```Swift
@State var isShowing = false

var body: some View {
  Button("Alert") {
    isShowing = true
  }
  .alert("title", isPresented: $isShowing) {
    Button("OK", role: .destructive) { print("tap ok") }
    Button("cancel", role: .cancel) { print("tap cancel") }
  }
}
```
<br/>

## ActionSheet
- ActionSheet도 Alert와 동일하게 iOS 16까지만 지원
- Alert와 동일하게 별도의 뷰 인스턴스로 ActionSheet를 사용하는 것보단 메소드로 접근하여 사용하는게 더욱 선언적 프로그래밍이라서 deprecated
<br/>

- 사용 방법은 .alert 사용방법과 완전 동일하고 메소드 이름만 .alert에서 .confirmationDialog로 다르게 사용
```Swift
@State var isActionSheetShowing = false

Button("ActionSheet") {
  isActionSheetShowing = true
}
.confirmationDialog("title", isPresented: $isActionSheetShowing) {
  Button("OK", role: .destructive) { print("tap ok") }
  Button("cancel", role: .cancel) { print("tap cancel") }
}
```
<br/>
