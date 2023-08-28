# EmptyView, EquatableView (.equtable(), 최적화)

## EmptyView
- 이름 그대로 아무것도 가지고 있지 않은 뷰
- EmptyView()에 frame, foregroundColor 옵션을 주어도 아무런 표시가 남지 않음
```Swift
struct ContentView: View {
  var body: some View {
    VStack {
      Text("Text1")
      EmptyView()
        .frame(height: 20)
        .foregroundColor(.blue)
      Text("Text2")
      Spacer()
        .frame(height: 20)
      Text("Text3")
    }
  }
}
```

- EmptyView의 사용처
  - 아무런 뷰를 사용하고 싶지 않을때 선언
  - Toggle에 label이라는 파라미터에 EmptyView()를 넣어서 label을 사용하지 않는다는 표현
```Swift
Toggle(isOn: $isOn, label: { EmptyView() } )
```
<br/>

## EquatableView
- 존재하는 뷰 인스턴스의 교체 여부를 감지하는데 사용되는 최적화 기술
  - 원래 @State, @ObservableObject와 같은 뷰의 소스를 변경할 때마다 SwiftUI는 body 속성을 실행하여 새로운 뷰를 생성하지만, EqutableView를 사용하면 매번 새로운 뷰를 생성하지 않는 점이 존재
  - SwiftUI는 변경 사항이 있는 경우 새 View를 렌더링
- EqutableView 사용 방법
  - Equtable을 준수하는 View를 구현
  - 위에서 구현한 뷰를 EqutableView(content:)로 생성하거나, .equtable()을 추가하여 사용 (.equtable() 방법이 선언적이라 더욱 선호)

```Swift
// Equtable이 아니라면, 값이 변경될때마다 body가 호출
struct ContentView: View {
  @State private var n = 3

  var body: some View {
    VStack {
      NumberParity(number: n)
      
      Button("New Random Number") {
        self.n = Int.random(in: 1...1000)
      }.padding(.top, 80)
      
      Text("\(n)")
    }
  }
}

extension Int {
  var isEven: Bool { return self % 2 == 0 }
  var isOdd: Bool { return self % 2 != 0 }
}

struct NumberParity: View {
  let number: Int
  @State private var flag = false
  
  var body: some View {
    let animation = Animation.linear(duration: 3.0).repeatForever(autoreverses: false)
    print("init 호출")
    return VStack {
      if number.isOdd {
        Text("ODD")
      } else {
        Text("EVEN")
      }
    }
    .foregroundColor(.white)
    .padding(20)
    .background(RoundedRectangle(cornerRadius: 10).fill(self.number.isOdd ? Color.red : Color.green))
    .rotationEffect(self.flag ? Angle(degrees: 0) : Angle(degrees: 360))
    .onAppear { withAnimation(animation) { self.flag.toggle() } }
  }
}
// print("init 호출")이 계속 불려서 프린트 되는것을 확인 




// 현재 ODD, EVEN인 경우, 같은 짝수로만 데이터가 변경될 경우 UI 변경은 필요 없으므로 사실상 body 부분이 다시 불리지 않아도 되는 상태
// 같은 짝수가 들어오거나 같은 홀수가 연속으로 들어올때 body가 불리지 않도록 EqutableView를 사용
// Equatable을 준수하고, static func == 로 구현
struct NumberParity: View, Equatable {
  let number: Int
  @State private var flag = false
  
  var body: some View {
    let animation = Animation.linear(duration: 3.0).repeatForever(autoreverses: false)
    print("init 호출")
    return VStack {
      if number.isOdd {
        Text("ODD")
      } else {
        Text("EVEN")
      }
    }
    .foregroundColor(.white)
    .padding(20)
    .background(RoundedRectangle(cornerRadius: 10).fill(self.number.isOdd ? Color.red : Color.green))
    .rotationEffect(self.flag ? Angle(degrees: 0) : Angle(degrees: 360))
    .onAppear { withAnimation(animation) { self.flag.toggle() } }
  }
  
  static func == (lhs: NumberParity, rhs: NumberParity) -> Bool {
      lhs.number.isOdd == rhs.number.isOdd
  }
}

// 사용하는 쪽에서는 2가지 방법으로 사용
// 1. 위에서 구현한 뷰를 EqutableView(content:)로 생성하거나, 2. .equtable()을 추가하여 사용 (.equtable() 방법이 선언적이라 더욱 선호)
      // in body
      // 1번 방법 - EquatableView 사용
//      EquatableView(content: NumberParity(number: n))
      
      // 2번 방법 - .equatable() 호출
      NumberParity(number: n)
          .equatable()
      Button("New Random Number") {
        self.n = Int.random(in: 1...1000)
      }.padding(.top, 80)
      
      Text("\(n)")
// 결과) 홀수가 연속으로 들어올때 `init 호출`이 출력되지 않음 (= body 부분이 불리지 않음)
```
<br/>
