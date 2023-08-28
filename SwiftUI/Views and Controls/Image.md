# Image

## Image
- 단순히 Image("imageName")으로 사용
```Swift
struct ContentView: View {
  var body: some View {
    Image("book")
      .resizable() // <- 공간에 맞게 이미지 크기를 조정하는 모드 설정, 화면에 맞추어서 채워지는 효과
//    .aspectRatio(contentMode: .fit) // contentMode 설정 (fit, fill) 가능
//    .aspectRatio(contentMode: .fill)
//    .scaledToFit()
//    .scaledToFill()
  }
}
```
<br/>

## ClipShape
- 원 이미지 만들기
- .clipShape(Circle())로 이용

```Swift
Image("book")
  .resizable()
  .scaledToFit()
  .clipShape(Circle())
// .clipShape(Circle().inset(by: 20)) -> inset 적용하면 원 더 작아짐
```
<br/>
