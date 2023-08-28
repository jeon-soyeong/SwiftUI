# Stepper

## Stepper
```Swift
struct Stepper<Label> where Label : View
```
<br/>

- 아래 Stepper 생성자를 이용하여 구현
```Swift
public init(
  @ViewBuilder label: () -> Label,
  onIncrement: (() -> Void)?,
  onDecrement: (() -> Void)?,
  onEditingChanged: @escaping (Bool) -> Void = { _ in }
)
```
<br/>

- onIncrement와 onDecrement에서 쓸 @State 프로퍼티 준비
```Swift
@State private var value = 0
let colors = [
  Color.orange,
  .red,
  .gray,
  .blue,
  .green,
  .purple,
  .pink
]
```
<br/>

- @ViewBuilder에는 Text를 넣어서 구현
```Swift
var body: some View {
  Stepper {
    Text("Value: \(value), color: \(colors[value].description)")
  } onIncrement: {
    value += 1
    guard value >= colors.count else { return }
    value = 0
  } onDecrement: {
    value -= 1
    guard value < 0 else { return }
    value = colors.count - 1
  }
}
```
<br/>

- 직접 value값을 컨트롤하는 onIncrement, onDecrement가 아닌 다른 방법
```Swift
public init<V>(
  _ titleKey: LocalizedStringKey,
  value: Binding<V>,
  step: V.Stride = 1,
  onEditingChanged: @escaping (Bool) -> Void = { _ in }
) where V : Strideable
```
<br/>

- 몇씩 올라가는지 step 값과 range값을 사용
```Swift
Stepper(
  value: $value,
  in: 0...50,
  step: 5
) {
  Text("Current: \(value)")
}
```
<br/>
