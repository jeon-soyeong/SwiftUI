# Picker, PickerStyle

## Picker
- 상호 배타적인 값들을 선택할때 사용하는 UI
- Picker는 아래처럼 선언되어 있는 구조체
- 특징은 SelectionValue가 Hashable이라는 것
```Swift
struct Picker<Label, SelectionValue, Content> where Label : View, SelectionValue : Hashable, Content : View
```
<br/>

- @State로 selectionOption을 주고 클로저 안에 Text를 삽입
- 앞에 들어가는 문자열 인수는 List와 같이 사용할때 표출 (아래에서 계속)
```Swift
var options = ["옵션1", "옵션2", "옵션3", "옵션4"]
@State private var selectionOption = 0

Picker("Select Choice", selection: $selectionOption) {
  ForEach(0 ..< options.count) {
    Text(options[$0])
  }
}
```
<br/>

- 보통 옵션들은 enum으로 정의하고 사용
```Swift
enum Flavor: String, CaseIterable, Identifiable {
  case chocolate, vanilla, strawberry
  var id: Self { self }
}

@State private var selectedFlavor = Flavor.chocolate

Picker("Flavor", selection: $selectedFlavor) {
  Text("Chocolate").tag(Flavor.chocolate)
  Text("Vanilla").tag(Flavor.vanilla)
  Text("Strawberry").tag(Flavor.strawberry)
}
```
<br/>

- List와 같이 사용하면 Picker의 첫번째 인수 문자열 값이 표출
```Swift
List {
  Picker("Flavor", selection: $selectedFlavor) {
    Text("Chocolate").tag(Flavor.chocolate) // picker에 표출됨.
    Text("Vanilla").tag(Flavor.vanilla)
    Text("Strawberry").tag(Flavor.strawberry)
  }
}
```
<br/>

- enum을 사용했으므로, allCases 사용하여 손쉽게 구현
```Swift
List {
  Picker("Flavor", selection: $selectedFlavor) {
    ForEach(Flavor.allCases) { Text($0.rawValue.capitalized) }
  }
}
```
<br/>

## PickerStyle
- .automatic (디폴트)
- .inline
  - View에다가 바로 사용한 경우 - 스크롤하는 피커 형태 (wheel과 동일)
  - List에다가 사용한 경우 - 옵션들이 구분되어 표출되고 체크마크 UI도 자동으로 표출
- .wheel
- .menu
- .radioGroup
- .segmented

```Swift
Picker("Flavor", selection: $selectedFlavor) {
  ForEach(Flavor.allCases) { Text($0.rawValue.capitalized) }
}
.pickerStyle(.wheel)
```
<br/>
