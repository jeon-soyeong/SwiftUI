# Text, Text ViewModifier

## Text
- Text()로 사용
```Swift
struct ContentView: View {
  var body: some View {
    Text("text")
  }
}

struct ContentView: View {
  var body: some View {
    VStack {
      Text("text1")
      Text("underline")
        .underline()
      Text("strikethrough")
        .strikethrough()
      Text("base line ofset(30)")
        .baselineOffset(30)
    }
  }
}
```
<br/>

## kerning과 tracking
- kerning은 문자 간의 offset을 조절
- tracking은 후행 공백을 추가하거나 제거하여 마치, 만주간의 offset으로 조절되는 것처럼 보임
- tracking을 사용하면, 문자열 간 공백이 있는 경우, 그 공백으로 나머지 공간을 채우는 것처럼 표출
```Swift
// 문자간의 offset을 조절
Text("test1 test2 test3 test123")
  .kerning(30)

// 문자간의 offset을 조절하는게 아니라, 후행 공백을 추가하거나 제거하여 조절
Text("test1 test2 test3 test123")
  .tracking(30)
```
<br/>

## Text ViewModifier
- ViewModifier를 사용하여 Text를 커스텀
- MyTextModifier 추가
```Swift
//  MyTextModifier.swift

import SwiftUI

struct MyTextModifier: ViewModifier {
  func body(content: Content) -> some View {
    content
      .font(.system(size: 24, weight: .bold, design: .default))
      .foregroundColor(.blue)
  }
}
```

- 사용할땐 .modifier()로 사용
```Swift
struct ContentView: View {
  var body: some View {
    Text("my_text")
      .modifier(MyTextModifier())
  }
}
```
<br/>
