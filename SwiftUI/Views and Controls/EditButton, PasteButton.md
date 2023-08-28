# EditButton, PasteButton

## EditButton
- 토글 성격의 버튼
- EditButton은 일반적으로 List와 같이 사용
- List는 또 ForEach와 같이 사용하는데, 이 ForEach에 onDelete {}, onMove {}를 추가하여 적용
- .toobar { EditButton }만 추가하면 자동으로 delete버튼과 move버튼이 자동으로 적용
```Swift
struct ContentView: View {
  @State private var fruits = [
    "Apple",
    "Banana",
    "Papaya",
    "Mango"
  ]
  
  var body: some View {
    NavigationView {
      List {
        ForEach(fruits, id: \.self) { fruit in
          Text(fruit)
        }
        .onDelete { fruits.remove(atOffsets: $0) }
        .onMove { fruits.move(fromOffsets: $0, toOffset: $1) }
      }
      .navigationTitle("Fruits")
      .toolbar {
        EditButton()
      }
    }
  }
}
```
<br/>

## PasteButton
- pasteboard에서 item을 읽고 클로저로 전달하는 시스템 버튼

```Swift
@State private var pastedText: String = ""


var body: some View {
    HStack {
        PasteButton(payloadType: String.self) { strings in
            pastedText = strings[0]
        }
        Divider()
        Text(pastedText)
        Spacer()
    }
}
```
<br/>
