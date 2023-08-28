# DatePicker, ColorPicker

## DatePicker 사용 방법
- DatePicker 생성자에 이름, 선택 할 @State date 프로퍼티, 날짜를 표시할것인지 시간을 표시할것인지 선택
```Swift
struct ContentView: View {
  @State var date = Date()
  
  var body: some View {
    DatePicker(
      "DatePicker",
      selection: $date,
      displayedComponents: [.date]
    )
  }
}
```

- 시간 정보를 표출하고 싶은 경우, displayedComponents 인수인 배열에 .hourAndMinute 추가
```Swift
DatePicker(
  "DatePicker",
  selection: $date,
  displayedComponents: [.date, .hourAndMinute] // <-
)
```
<br/>

- Date 제한 주기
- DatePicker의 생성자는 아래처럼 in 파라미터가 존재
```Swift
public init<S>(
  _ title: S, 
  selection: Binding<Date>, 
  in range: ClosedRange<Date>, 
  displayedComponents: DatePicker<Label>.Components = [.hourAndMinute, .date]
) where S : StringProtocol
```
<br/>

- ClosedRange<Date> 값을 DatePicker의 in 인수에 주입
- 먼저 ClosedRange<Date> 프로퍼티 정의
```Swift
var dateRange: ClosedRange<Date> {
  let min = Calendar.current.date(
    byAdding: .year,
    value: -10, 
    to: date
  )!
  let max = Calendar.current.date(
    byAdding: .year,
    value: 10,
    to: date
  )!
  return min...max
}
```
<br/>

- 위 값을 in 파라미터에 주입
```Swift
var body: some View {
  DatePicker(
    "DatePicker",
    selection: $date,
    in: dateRange, // <- 10년 전 후 선택 불가하도록 제한 가능
    displayedComponents: [.date, .hourAndMinute]
  )
}
```
<br/>

## DatePicker의 .datePickerStyle
- .compact - 디폴트 (위에서 알아본 UI)
- .automatic - 맥, 아이폰 플랫폼에 따라 자동으로 달라지는 옵션이고, 아이폰에서는 위 compact와 동일
- .graphical - 달력 형식
- .wheel - 일반적인 피커 방식의 휠 형식
```Swift
DatePicker(
  "DatePicker",
  selection: $date,
  in: dateRange,
  displayedComponents: [.date, .hourAndMinute]
)
  .datePickerStyle(.compact) // <-
```
<br/>

## ColorPicker
- @State color 프로퍼티 준비
- ColorPicker의 selection에 $color를 넣어서 초기화

```Swift
@State var color = Color(.sRGB, red: 0.98, green: 0.9, blue: 0.2)

ColorPicker(
  "ColorPicker",
  selection: $color
)
```
<br/>

