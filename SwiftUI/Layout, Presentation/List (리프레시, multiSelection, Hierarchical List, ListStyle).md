# List (refresh, multiSelection, Hierarchical List, ListStyle)

## List 
- 형태
  - Hashable을 따르고, View를 준수하는 두 개의 제네릭스로 구성
```Swift
struct List<SelectionValue, Content> where SelectionValue : Hashable, Content : View
```

- 사용
```Swift
// 1. List 후행 클로저 안에 subviews들을 넣어서 구현
struct ContentView: View {
  var body: some View {
    List {
      Text("A List Item")
      Text("A Second List Item")
      Text("A Third List Item")
    }
  }
}

// 2. List(:) { } 형태로도 사용이 가능
struct Ocean: Identifiable {
  let name: String
  let id = UUID()
}

private var oceans = [
  Ocean(name: "Pacific"),
  .init(name: "Atlantic"),
  .init(name: "Indian"),
  .init(name: "Southern"),
  .init(name: "Arctic"),
]

var body: some View {
  List(oceans) {
    Text($0.name)
  }
}

```
<br/>

## Multi Selection (라디오 버튼)
- List에 Radio버튼처럼 구현 방법
- NavigationView와 같이 사용
- List에 selection 파라미터에 여러개의 selection값을 저장할 @State 프로퍼티 주입
- toolbar로 EditButton()을 추가
```Swift
@State private var multiSelection = Set<UUID>()

var body: some View {
  NavigationView {
    List(oceans, selection: $multiSelection) {
      Text($0.name)
    }
    .navigationTitle("Oceans")
    .toolbar { EditButton() }
  }
  Text("\(multiSelection.count) selections")
}
```
<br/>

## refresh
- 가장 일반적인 드래그를 통해 리스트를 아래로 당기는 리프레시 구현
- 리프레시는 refreshable(action:)을 사용
- 클로저 안에 들어가는 값은 async형 (async - await)

```Swift
public func refreshable(
  action: @escaping @Sendable () async -> Void
) -> some View
// .refreshable {}을 추가하기만 해도 아래로 당겼을 때 로딩화면이 표출
```

```Swift
// async 메소드를 넣으면 되므로, 메소드 하나 추가
// async 메소드이고, 중간에 await가 있어서, 3초동안 블락되었다가 다음 isOn.toggle()을 실행하는 코드
func getSomeData() async {
  await Task.sleep(3_000_000_000) // 3seconds
  isOn.toggle()
}

// 사용하는 쪽에서는 await getSomeData()로 호출
@State private var isOn = false

NavigationView {
  VStack {
    List(oceans, selection: $multiSelection) {
      Text($0.name)
    }
    .navigationTitle("Oceans")
    .refreshable {
      await getSomeData() // <-
    }
    Toggle("Toggle", isOn: $isOn)
  }
}
```
<br/>

## Section
- Section을 차지하는 하나의 값과, 그 하나의 값과 연관된 배열들이 들어있는 형태
```Swift
struct Sea: Hashable, Identifiable {
    let name: String
    let id = UUID()
}

struct OceanRegion: Identifiable {
    let name: String
    let seas: [Sea]
    let id = UUID()
}


private let oceanRegions: [OceanRegion] = [
  OceanRegion(name: "Pacific",
              seas: [Sea(name: "Australasian Mediterranean"),
                     Sea(name: "Philippine"),
                     Sea(name: "Coral"),
                     Sea(name: "South China")]),
  OceanRegion(name: "Atlantic",
              seas: [Sea(name: "American Mediterranean"),
                     Sea(name: "Sargasso"),
                     Sea(name: "Caribbean")]),
  OceanRegion(name: "Indian",
              seas: [Sea(name: "Bay of Bengal")]),
  OceanRegion(name: "Southern",
              seas: [Sea(name: "Weddell")]),
  OceanRegion(name: "Arctic",
              seas: [Sea(name: "Greenland")])
]

// 사용하는 쪽에서는 oceanRegions의 ForEach문을 돌면서 content에는 seas배열을 사용하고, header에는 name을 사용
  NavigationView {
    List(selection: $singleSelection) {
      ForEach(oceanRegions) { region in
        Section(
          content: {
            ForEach(region.seas) { sea in
              Text(sea.name)
            }
          },
          header: {
            Text("Header \(region.name)")
          }
        )
      }
    }
  }

```
<br/>

## Hierarchical List
- 마치 컴퓨터에서 폴더를 열듯이 계층구조 UI구현이 가능
- 모델에는 자기 자신의 형 배열 프로퍼티를 갖고 있게 구현
```Swift
struct ContentView: View {
    struct FileItem: Hashable, Identifiable, CustomStringConvertible {
        var id: Self { self }
        var name: String
        var children: [FileItem]? = nil // <- 자기 자신 형태를 배열로 갖고 있는 것
        var description: String {
            switch children {
            case nil:
                return "📄 \(name)"
            case .some(let children):
                return children.isEmpty ? "📂 \(name)" : "📁 \(name)"
            }
        }
    }
    let fileHierarchyData: [FileItem] = [
      FileItem(name: "users", children:
        [FileItem(name: "user1234", children:
          [FileItem(name: "Photos", children:
            [FileItem(name: "photo001.jpg"),
             FileItem(name: "photo002.jpg")]),
           FileItem(name: "Movies", children:
             [FileItem(name: "movie001.mp4")]),
              FileItem(name: "Documents", children: [])
          ]),
         FileItem(name: "newuser", children:
           [FileItem(name: "Documents", children: [])
           ])
        ]),
        FileItem(name: "private", children: nil)
    ]
    var body: some View {
        List(fileHierarchyData, children: \.children) { item in
            Text(item.description)
        }
    }
}
```
<br/>
