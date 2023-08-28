# Axis, Form

## Axis
- enum 타입
- 형태
  - 단순히 horizontal, vertical이라는 case가 있는 형태
```Swift
@frozen public enum Axis : Int8, CaseIterable {
  case horizontal
  case vertical
  
  @frozen public struct Set : OptionSet {
    public typealias Element = Axis.Set
    public let rawValue: Int8
    public init(rawValue: Int8)

    public static let horizontal: Axis.Set
    public static let vertical: Axis.Set
    public typealias ArrayLiteralElement = Axis.Set.Element
    public typealias RawValue = Int8
  }
  
  public init?(rawValue: Int8)
  public typealias AllCases = [Axis]
  public typealias RawValue = Int8
  public static var allCases: [Axis] { get }
  public var rawValue: Int8 { get }
}
```
<br/>

## Form
- Form은 매우 직관적으로 설정화면에서 사용되는 기능들을 쉽게 구현할 수 있는 컴포넌트
- Section과 같이 사용되며 쉽게 그룹화가 가능
```Swift
// 데이터 정의
// 그룹화에 사용될 때는 enum으로 정의
enum NotifyMeAboutType {
  case directMessages
  case mentions
  case anything
}

enum ProfileImageSize {
  case large
  case medium
  case small
}

// @State 프로퍼티 선언
struct ContentView: View {
  @State var notifyMeAbout = NotifyMeAboutType.directMessages
  @State var playNotificationSounds = false
  @State var profileImageSize = ProfileImageSize.large
  @State var sendReadReceipts = false
    
  var body: some View {
  }
}

// Form안에 Section을 놓고, Section안에 Picker, Text, Toggle 등을 배치
  var body: some View {
    NavigationView {
      Form {
        Section(header: Text("Notifications")) {
          Picker("Notify Me About", selection: $notifyMeAbout) {
            Text("Direct Messages").tag(NotifyMeAboutType.directMessages)
            Text("Mentions").tag(NotifyMeAboutType.mentions)
            Text("Anything").tag(NotifyMeAboutType.anything)
          }
          Toggle("Play notification sounds", isOn: $playNotificationSounds)
          Toggle("Send read receipts", isOn: $sendReadReceipts)
        }
        Section(header: Text("User Profiles")) {
          Picker("Profile Image Size", selection: $profileImageSize) {
            Text("Large").tag(ProfileImageSize.large)
            Text("Medium").tag(ProfileImageSize.medium)
            Text("Small").tag(ProfileImageSize.small)
          }
          Button("Clear Image Cache") {}
        }
      }
    }
  }

```
<br/>
