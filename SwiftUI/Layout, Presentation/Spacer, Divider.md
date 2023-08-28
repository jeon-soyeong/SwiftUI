# Spacer, Divider

## Spacer
- Stack 안에서 사용되며, Stack의 크기만큼 내부 크기의 공백을 채우고 싶을 때 사용
```Swift
// List와 row가 있을 때 row는 원래 좌측 정렬되어있지만, Spacer()를 추가하면 오른쪽 정렬로 변경
struct ChecklistRow: View {
  let name: String
  
  var body: some View {
    HStack {
      Spacer() // <-
      Image(systemName: "checkmark")
      Text(name)
    }
    .border(Color.blue)
  }
}

// 양쪽 정렬
struct ChecklistRow: View {
  let name: String
  
  var body: some View {
    HStack {
      Image(systemName: "checkmark")
      Spacer() // <-
      Text(name)
    }
    .border(Color.blue)
  }
}

// Spacer()를 뷰 바깥쪽에 각각 추가하면, 뷰가 중간으로 이동, 가운데 정렬
struct ChecklistRow: View {
  let name: String
  
  var body: some View {
    HStack {
      Spacer() // <-
      Image(systemName: "checkmark")
      Text(name)
      Spacer() // <-
    }
    .border(Color.blue)
  }
}
```
<br/>

## Divider
- 단순히 구분선 구현에 사용
```Swift
HStack {
    Text("Some Text1")
    Divider()
      .frame(width: 200) // 색상, 길이 모두 일반 뷰 프로퍼티에 접근하여 지정하듯이 가능
      .background(.blue)
    Text("Some Text2")
}
```
<br/>
