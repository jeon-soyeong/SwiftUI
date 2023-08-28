# FetchRequest, FetchedResults (Core Data)

## FetchRequest
- SwiftUI에서는 Core Data 가져오기 요청 작업을 위한 전용 property wrapper를 지원하고 이것이 FetchRequest
- FetchRequest는 SwiftUI 뷰에 별도 로직 없이 직접 추가가 가능
- @FetchRequest 사용 시 데이터 id와 정렬에 사용되는 값을 선언하여 사용
```Swift
@FetchRequest(
  entity: Quake.entity(), 
  sortDescriptors: [SortDescriptor(\.time, order: .reverse)],
  predicate: NSPredicate(format: "name == %jake", ascending: true) // 이 밖에도 predicate에는 NSPredicate(format:_:)를 추가하여 쿼리 조건을 추가 가능
)
private var quakes: FetchedResults<Quake>
```

- NSManagedObjectContext값을 해당 뷰에 주입해야 coreData 스토리지에 접근할 수 있기 때문에 부모로 부터 주입받을 수 있는 @Environment 사용하여 주입
- context 주입 코드 - core data 사용하는 뷰
```Swift
struct ContentView: View {
  @Environment(\.managedObjectContext) private var viewContext
  ...
}
```

- 부모 뷰에서 .environment를 통해 CoreDataStorage의 context 주입
```Swift
@main
struct ExCoredataApp: App {
  let viewContext = CoreDataStorage.shared.viewContext
  var body: some Scene {
    WindowGroup {
      ContentView()
        .environment(\.managedObjectContext, viewContext)
    }
  }
}
```
</br>

## FetchedResults
- @FetchRequest를 선언하고, 타입은 FetchResults로 정의하여 CoreData에 접근하는 프로퍼티 items 추가
```Swift
struct ContentView: View {
  @Environment(\.managedObjectContext) private var viewContext
  @FetchRequest(sortDescriptors: [NSSortDescriptor(keyPath: \MyItem.updatedDate, ascending: false)])
  private var items: FetchedResults<MyItem> // <-

  ...
}
```
- items 사용은 ForEach로 접근하여 사용
```Swift
struct ContentView: View {
  @Environment(\.managedObjectContext) private var viewContext
  @FetchRequest(sortDescriptors: [NSSortDescriptor(keyPath: \MyItem.updatedDate, ascending: false)])
  private var items: FetchedResults<MyItem>
  let repo = MyItemRepository()
  
  var body: some View {
    VStack {
      ForEach(items) { item in
        Text(item.name!)
      }
      Text("Hello, world!")
        .padding()
        .onAppear {
          print(items.count)
          print(repo.getItems().count)
          // 샘플 데이터 준비
          // repo.add(name: "jake")
          // repo.add(name: "kim")
        }
    }
  }
}
```

- 따로 CoreDataSotorage를 정의하여 사용하지 않아도, SwiftUI 스럽게 해당 뷰에서 addItem 메소드를 추가하여 코어데이터에 접근하는 방식으로 구현도 가능
```Swift
 // ContentView.swift
  
  private func addItem(_ name: String) {
    withAnimation {
      let newItem = MyItem(context: viewContext)
      newItem.updatedDate = Date()
      newItem.name = name
      do {
        try viewContext.save()
      } catch {
        print(error)
      }
    }
  }
```

- 버튼을 누를경우 addItem 호출
```Swift
var body: some View {
  VStack {
    ForEach(items) { item in
      Text(item.name!)
    }
    Text("Hello, world!")
      .padding()
      .onAppear {
        print(items.count)
        print(repo.getItems().count)
        // 샘플 데이터 준비
        // repo.add(name: "jake")
        // repo.add(name: "kim")
      }
    Button("Add 'jake123'") {
      addItem("jake123")
    }
  }
}
```
<br/>
