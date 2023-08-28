# HStack, VStack, ZStack, LazySatck

## HStack

```Swift
@frozen struct HStack<Content> where Content : View
```
- @frozen이란?
- frozen: 얼은 상태 (enum 타입에서 case가 추가적으로 생겨나지 않을 것임을 명시)
  - 컴파일러는 더욱 효율적으로 컴파일
  - Library Evelolution mode가 안켜져있으면 키워드를 안써도 암묵적으로 모두 @frozen으로 생성
<br/>

- @frozen struct로 쓰는 경우 
- frozen 어트리뷰트의 정의되로 타입이 더이상 늘어나지 않을것을 제한하는 것이므로, 라이브러리의 다음 버전에서 만약 @frozen이 붙은 struct에서 변경을하거나 제거하거나 프로퍼티의 순서를 변경하려고 하지 못하게 하는것
<br/>

- HSTack, VStack, ZStack은 화면에서 보이지 않아도 모두 렌더링되므로, 가급적이면 subviews들이 적거나 의도적으로 lazy하게 뷰를 렌더링하고 싶지 않은 경우만 사용
- Lazy하게 사용해야하는 경우: ScrollView를 이용해서 현재 디바이스 화면에 보이지 않고, 스크롤해야 다른 뷰가 보일때 굳이 보이지 않는 뷰들은 렌더링을 미리 하지 않아도 되므로 스크롤될때 (뷰가 화면에 보이기 시작할때) 렌더링해주어야 효율적
- subviews들이 많은 경우는 아래에서 알아볼 LazyHStack, LazyVStack, LazyZStack 사용
<br/>

```Swift
// HStack은 아래 생성자를 이용하여 구현
@inlinable public init(
  alignment: VerticalAlignment = .center,
  spacing: CGFloat? = nil,
  @ViewBuilder content: () -> Content
)

// 구현
HStack(
  alignment: .top,
  spacing: 10
) {
  ForEach(1...5, id: \.self) {
    Text("Item \($0)")
  }
}
.padding()
.border(Color.purple)
.font(.body)
```
<br/>

## VStack
```Swift
VStack(
  alignment: .trailing,
  spacing: 10
) {
  ForEach(1...5, id: \.self) {
    Text("Item \($0)")
  }
}
.padding()
.border(Color.purple)
.font(.body)
```
<br/>

## ZStack
- ZStack은 뷰 위쪽에 쌓는 것
- 뷰 위쪽에 손쉽게 레이아웃을 배치할 수 있는 장점이 있어서 사용
- 아래처럼 1 뷰 위에 2뷰가 오른쪽 상단에 위치하고 싶은 경우 ZStack을 사용하면 매우 편리

```Swift
// ZStack의 속성을 trailing, top으로 해놓고, 1번 텍스트를 넣은 후 2번 텍스트를 넣으면 오른쪽 상단에 차곡차곡 쌓이는 것
struct SomeView: View {
  var body: some View {
    ZStack(alignment: Alignment(horizontal: .trailing, vertical: .top)) {
      Text("1")
        .font(.headline)
        .frame(width: 300, height: 300, alignment: .center)
        .border(Color.gray, width: 1)
      
      Text("2")
        .font(.title)
        .frame(width: 100, height: 100, alignment: .center)
        .border(Color.white, width: 1)
    }
  }
}
```
<br/>

## LazyStack
- iOS 14부터 지원
- ScrollView과 Stack을 같이 쓸 때, ScrollView에 담겨있고 스크롤해야 보이는, 뷰를 처음에 모두 렌더링할 필요 없는 경우 효율성을 위해서 lazy하게 사용
```Swift
ScrollView(.horizontal) {
  LazyHStack(alignment: .top, spacing: 10) {
    ForEach(1...100, id: \.self) {
      Text("Column \($0)")
    }
  }
}
```
<br/>
