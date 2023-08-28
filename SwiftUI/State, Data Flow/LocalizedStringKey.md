# LocalizedStringKey, Localization 지역화 방법

## Localizable 적용
```Swift
.environment(\.locale, .init(identifier:)) 사용하여 적용
struct ContentView: View {
  var body: some View {
    Text("confirm")
      .environment(\.locale, .init(identifier: "en"))
  }
}
```
<br/>

https://ios-development.tistory.com/1167
