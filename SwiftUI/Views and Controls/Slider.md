# Slider

## Slider
```Swift
struct Slider<Label, ValueLabel> where Label : View, ValueLabel : View
```
<br/>

- Slide(value:in:onEditingChanged:)를 사용하여 구현
  - value - 초기값
  - in - 범위
  - step - 증분단위
  - onEditingChanged - 변경되었을때 호출되는 클로저
```Swift
struct ContentView: View {
  @State private var value = 30.0
  @State private var isEditing = false
  
  var body: some View {
    VStack {
      Slider(
        value: $value,
        in: 0...100,
        step: 5 // <- 증분단위
      ) { editing in
          isEditing = editing
        }
      Text("\(value)")
        .foregroundColor(isEditing ? .red : .blue)
    }
  }
}
```
<br/>

- 범위에 label 붙이기
```Swift
Slider(
  value: $value,
  in: 0...100,
  step: 5
) {
  Text("Title")
} minimumValueLabel: {
  Text("0")
} maximumValueLabel: {
  Text("100")
} onEditingChanged: { editing in
  isEditing = editing
}
// 양쪽 끝 0  100으로 생긴 slider 생성
```
<br/>
