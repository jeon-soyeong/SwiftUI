# Preference, PrefereneceKey (하위뷰에서 상위뷰로 데이터 넘기는 방법)

## Preference
- key와 vallue로 구성된 데이터 전달 메소드
- 데이터 전달: 하위 뷰 -> 상위 뷰
<br/>

- 키를 가지고 접근
  - 1. 키 등록: preferenceKey 프로토콜 준수
  - 2. 하위뷰에서 값 송신: preference(key:value:) 메소드
  - 3. 상위뷰에서 값 수신: onPreferenceChange() 메소드
  - ex) 하위 뷰의 navigationTitle이 변경될 때마다 부모 뷰에서 타이틀이 변경될 때마다 print하기
<br/>

- 1 키 등록: preferenceKey 프로토콜 준수
```Swift
// PreferenceKey 내부 코드
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
public protocol PreferenceKey {
    associatedtype Value
    
    static var defaultValue: Self.Value { get }
    
    static func reduce(value: inout Self.Value, nextValue: () -> Self.Value)
}

// preferenceKey 준수하는 NavigationBarTitleKey 정의
struct NavigationBarTitleKey: PreferenceKey {
  static var defaultValue: String = ""
  static func reduce(value: inout String, nextValue: () -> String) {
    value = nextValue()
  }
}
```
<br/>

- 2 하위뷰에서 값 송신: preference(key:value:) 메소드
```Swift
struct SomeView: View {
  let title: String
  
  var body: some View {
    Text(title)
      .navigationBarTitle(title)
      .preference(key: NavigationBarTitleKey.self, value: title) // <-
  }
}
```

- 3 상위뷰에서 값 수신: onPreferenceChange() 메소드
```Swift
struct ContentView: View {
  var messages: [String] {
    (0...100).map(String.init)
  }
  
  var body: some View {
    NavigationView {
      List(messages, id: \.self) { message in
        NavigationLink {
          SomeView(title: message)
        } label: {
          Text(message)
        }
      }
      .navigationBarTitle("Messages")
    }
    .onPreferenceChange(NavigationBarTitleKey.self) { title in // <-
      print(title)
    }
  }
}

```
<br/>
