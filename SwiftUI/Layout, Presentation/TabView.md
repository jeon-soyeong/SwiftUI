# TabView

## 
```Swift
struct ViewA: View {
  var body: some View {
    Text("ViewA")
  }
}

struct ViewB: View {
  var body: some View {
    Text("ViewB")
  }
}

struct ViewC: View {
  var body: some View {
    Text("ViewC")
  }
}

// TabView하위에 위 뷰들을 추가하고, 탭뷰의 이름은 .tabItem으로 추가
// TabItem 아이콘 우측상단에 쉽게 badge도 추가가 가능
struct ContentView: View {
  var body: some View {
    Text("Hello, world!")
      .padding()
    TabView {
      ViewA()
        .badge(2) // <-
        .tabItem {
          Label("View1", systemImage: "tray.and.arrow.down.fill")
        }
      ViewB()
        .tabItem {
          Label("View2", systemImage: "tray.and.arrow.up.fill")
        }
      ViewC()
        .badge("!") // <_
        .tabItem {
          Label("View3", systemImage: "person.crop.circle.fill")
        }
    }
  }
}

// .tabItem 하위에 Label로 꼭 추가하지 않아도, Image, Text와 같은 뷰로 각각 추가가 가능
ViewD()
  .font(.system(size: 30))
  .tabItem {
    Image(systemName: "house.fill")
    Text("홈")
  }
```
<br/>
