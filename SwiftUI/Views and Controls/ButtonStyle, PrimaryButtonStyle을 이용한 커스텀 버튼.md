# ButtonStyle, PrimaryButtonStyle을 이용한 커스텀 버튼

## Custom Button
- ButtonStyle 준수
- makeBody method를 통해 configuration으로 label, isPressed 등의 접근 가능
```Swift
struct InsetRoundScaleButton: ButtonStyle {
  var labelColor = Color.white
  var backgroundColor = Color.blue
  
  func makeBody(configuration: Configuration) -> some View {
    configuration.label
      .foregroundColor(labelColor)
      .padding(.init(horizontal: 20, vertical: 13))
      .background(Capsule().fill(backgroundColor))
      .scaleEffect(configuration.isPressed ? 0.88 : 1.0) // <- scaleEffect 프로퍼티를 사용하여 애니메이션 효과 부여
  }
}
```

- 사용하는 쪽
```Swift
Button("InsetRoundButton") {
  print("tap button")
}
.buttonStyle(InsetRoundButton(labelColor: .white, backgroundColor: .blue))
```
<br/>

## PrimitiveButtonStyle
- 더욱 상세하게 커스텀할 수 있는 프로토콜 (인터렉션, appearance 등)
- PrimitiveButtonStyle로 action의 트리거 타이밍도 직접 커스텀이 가능
<br/>

- PrimitiveButtonStyle 프로토콜을 준수하여 구현
- makeBody도 구현
```Swift
struct MyPrimitiveButtonStyle: PrimitiveButtonStyle {
  func makeBody(configuration: PrimitiveButtonStyle.Configuration) -> some View
  }
}
```
<br/>

- makeBody에 리턴할 MyBtton을 내부적으로 구현
- configuration.trigger()이것을 실행하는 타이밍에 action이 실행
```Swift
struct MyPrimitiveButtonStyle: PrimitiveButtonStyle {
  func makeBody(configuration: PrimitiveButtonStyle.Configuration) -> some View {
    MyButton(configuration: configuration, labelColor: labelColor, backgroundColor: backgroundColor)
  }
  
  struct MyButton: View {
    @GestureState private var pressed = false
    
    let configuration: PrimitiveButtonStyle.Configuration
    var labelColor: Color
    var backgroundColor: Color
    
    var body: some View {
      configuration.label
        .foregroundColor(labelColor)
        .padding(.init(horizontal: 20, vertical: 13))
        .background(Capsule().fill(backgroundColor))
        .scaleEffect(pressed ? 0.88 : 1.0)
        .gesture(
          // minimumDuration - 몇초 이상 지속되어야 longPress로 볼지 결정
          // maximumDistance - 해당 거리보다 더 움직이면 longPress 실패로 결정
          LongPressGesture(minimumDuration: 2, maximumDistance: 3.0) //  2초 이상 누르고 있어야 long press로 인식, 3 distance만큼 벗어날 경우 long press 실패
            .updating($pressed) { value, state, _ in state = value }
            .onEnded { _ in self.configuration.trigger() }
        )
    }
}
```
<br/>

- 사용하는 쪽
```Swift
  Button("Tap Me!") {
      self.text = "Action Executed!"
  }.buttonStyle(MyPrimitiveButtonStyle())
```
<br/>
