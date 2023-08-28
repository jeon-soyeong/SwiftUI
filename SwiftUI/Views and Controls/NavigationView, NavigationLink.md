# NavigationView, NavigationLink

## NavigationView
- 뷰들의 관계를 push와 pop을 통해 계층 관계를 이루게끔 하는 뷰 (수평 관계는 TabView로 사용)
- iOS 16.0부터 **Deprecated**되었다는게 가장 중요
- iOS 16.0부터는 **NavigationStack 사용**
<br/>

## NavigationStack
- RootView를 표시하고, Root View에 대한 추가(additional) View를 제공할 수 있는 View​
- Stack은 항상 제거되지 않은 가장 최근에 추가된 View를 표시하며, RootView는 제거할 수 없음
```Swift
NavigationStack {
    List(parks) { park in
        NavigationLink(park.name, value: park)
    }
    .navigationDestination(for: Park.self) { park in // Park type과 link park type이 일치해야 함, List안에 여러 type의 link있으면 destination도 type 여러가지로 생성.
        ParkDetails(park: park)
    }
}
```
<br/>

## NavigationLink
- 네비게이션 형태로 presentation을 제어하는 뷰
```Swift
struct NavigationLink<Label, Destination> where Label : View, Destination : View
```
<br/>

## navigationDestination(for:destination:)  
- Destination View를 NaviationLink에서 제시된 Data 타입과 연결해주는 Method
<br/>

## Manage navigation state
- 기본적으로 NavigationStack은 state를 관리하여 stack에 있는 view들을 추적하는데, 이걸 직접 관리할 수도 있음. 

```Swift
@State private var presentedParks: [Park] = []

NavigationStack(path: $presentedParks) { //
    List(parks) { park in
        NavigationLink(park.name, value: park)
    }
    .navigationDestination(for: Park.self) { park in 
        ParkDetails(park: park)
    }
}

func showParks() {
    presentedParks = [Park("Yosemite"), Park("Sequoia")]
}
```
<br/>
