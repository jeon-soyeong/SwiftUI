# Button, ButtonStyle

## Button
- Button안에 텍스트를 적고 closure에 액션을 기입
```Swift
Button("MyButton") { print("did tap") }
```
<br/>

- action을 앞에서 받고 뒤에 Text와 같은 컴포넌트를 넣어서 표출도 가능
- 색상은 systemBlue가 디폴트로 설정
```Swift
Button(action: { print("tap button!") }) {
  Text("MyButton")
}
```
<br/>

- action에 클로저로 넣어도 되지만, 일반 func 함수를 넣어도 가능
```Swift
struct ContentView: View {
  var body: some View {
    Button(action: self.didTapButton) { // <-
      Text("MyButton")
    }
  }
  
  func didTapButton() {
    print("tap button!")
  }
}
```
<br/>

## ButtonStyle
buttonStyle(_:)로 지정

-  5가지가 존재
- .default - 파란색 버튼
- .plain - 일반 Text 형태 그대로 따라가는 버튼
- .bordered - 버튼의 tint색상을 기반으로 자동으로 테두리에 어울리는 색상이 생기는 버튼
- .borderedProminent - 버튼의 tint색상을 기반으로 텍스트가 `눈에 띄도록` 해주는 스타일
- .borderless - 테두리가 없는 버튼 (= .default와 동일)

```Swift
Button("default") { }
.buttonStyle(.automatic) // default
```
<br/>

ex) .bordered와 .borderedProminent 에서 tint색상을 다르게 하면 테두리 색상이 자동으로 그에 어울리는 색상으로 변화
- VStack의 마지막에 .tint(.purple)을 사용
```Swift
VStack {
  Button("default") { }
  .buttonStyle(.automatic) // default

  Button("plain") { }
  .buttonStyle(.plain) // default

  Button("bordered") { }
  .buttonStyle(.bordered)

  Button("borderedProminent") { }
  .buttonStyle(.borderedProminent)

  Button("borderless") { }
  .buttonStyle(.borderless)
}
.tint(.purple) // <-
```
<br/>
