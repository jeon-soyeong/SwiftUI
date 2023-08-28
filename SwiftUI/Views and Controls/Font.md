# Font

## System Font 사용 방법
### systemFont
- .font로 Text에 적용이 가능
- systemFont는 맥에서 자주 사용되는 SF pro 폰트(=San Francisco font)를 의미
```Swift
var body: some View {
  VStack {
    Text("Text1")
    
    Text("Text2")
      .font(.system(size: 12))
  }
}
```

### weight 속성
- weight는 굵기를 의미
- SwiftUI에서는 9가지의 weight를 제공
- weight를 따로 선언하지 않으면 regular 속성이 적용

```Swift
// bold
Text("bold")
  .font(.system(size: 15, weight: .bold))
```

### design 속성
- 글자의 모양을 정의 (4가지 존재)
- 영문에만 적용
```Swift
// default
Text(".default")
  .font(.system(size: 15, weight: .bold, design: .default))
```
<br/>

## TextStyle
- 애플 내부적으로 Font안에 TextStyle이라는 타입을 정의하여, 그 안에 static let으로 폰트를 미리 정의해놓은 속성들
```Swift
// TextStyle 예제 코드
Text(".largeTitle")
  .font(.system(.largeTitle))
```
<br/>

## FontStyle
- 배경 색상 backgroundColor(_:)
- 색상 foregroundColor(_:)
- 볼드 bold()
- 기울임 italic()
- 취소선 strikethrough()
- 밑줄 underline()
- font 밑 공간 baselineOffset()

```Swift
// ex) baselineOffset 
Text("baselineOffset(0)")
  .baselineOffset(0)
  .background(.red)
Text("baselineOffset(10)")
  .baselineOffset(10)
  .background(.green)
Text("baselineOffset(-10)")
  .baselineOffset(-10)
  .background(.blue)
```
<br/>

## Kerning, Tracking
- kerning은 폰트 종류에 따라 달라지며, 각 폰트 특성에 따라 글자 간격이 조절
- tracking은 폰트 종류와 관계 없이 글 자 삭이의 간격을 일정하게 설정
<br/>
