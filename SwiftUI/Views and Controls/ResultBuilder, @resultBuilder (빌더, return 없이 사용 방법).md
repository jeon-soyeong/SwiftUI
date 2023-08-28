# ResultBuilder, @resultBuilder (빌더, return 없이 사용 방법)

## ResultBuilder 사용 예시
```Swift
// ex) Person이라는 구조체에서 name값 뒤에 "🍎"을 붙인 name으로 리네임하는 프로그램
struct Person {
  var name: String
  let age: Int
}

/*
ResultBuilder를 사용한 경우 콤마를 사용하지 않아도 가능
@resultBuilder 키워드를 이용하여 Builder 컴포넌트 정의
키워드를 이용하면, static func buildBlock(_ components:) 메소드를 강제 재정의가 필요
*/
@resultBuilder
struct PersonBuilder {
  static func buildBlock(_ components: Person...) -> [Person] {
    components.map { Person(name: $0.name + "🍎", age: $0.age) }
  }
}​

// @resultBuilder 키워드가 등록된 PersonBuilder를 사용 -> 여러 표현식을 단일 값으로 결합하여 결과를 빌드
// (콤마를 사용하지 않아도 결합)
@PersonBuilder
func getPerson() -> [Person] {
  Person(name: "jake", age: 20)
  Person(name: "kim", age: 22)
  Person(name: "paul", age: 32)
}​
```
<br/>

## 응용 - builder 만들어서 사용하기
- return 키워드를 사용하지 않아도 builder를 통해 리턴임을 암시하여 컴파일 에러가 뜨지 않으므로 간결한 코드 형성
- if문, else문 안에서도 return 키워드를 사용하지 않아도 리턴임을 암시
```Swift
private func getPerson() -> Person {
  builder { // <- return 키워드 없어도 가능
    if num % 2 == 0 {
      Person(name: "jake", age: 2) // <- return 키워드 없어도 가능
    } else {
      Person(name: "jake", age: 1) // <- return 키워드 없어도 가능
    }
  }
}
```

- builder의 3가지 메소드를 정의
- buildBlock(_:): result를 컴바인하기 위한 필수로 정의가 필요한 메소드
- buildEither(first:): if-else와 switch에서 result가 가능하도록하는 메소드 (buildEither(second:)도 하나의 세트처럼 정의)
- buildEither(second:)

```Swift
#if swift(>=5.4)
@resultBuilder
public struct ContainerOfBlocksBuilder {
    /// Required by every result builder to build combined results from
    /// statement blocks.
    public static func buildBlock(_ components: [Block & Node]...) -> [Block & Node] {
        return components.flatMap { $0 }
    }

    /// If declared, provides contextual type information for statement
    /// expressions to translate them into partial results.
    public static func buildExpression(_ expression: Block & Node) -> [Block & Node] {
        return [expression]
    }

    /// If declared, provides contextual type information for statement
    /// expressions to translate them into partial results.
    public static func buildExpression(_ expression: [Block & Node]) -> [Block & Node] {
        return expression
    }

    /// If declared, provides contextual type information for statement
    /// expressions to translate them into partial results.
    public static func buildExpression(_ expression: Section) -> [Block & Node] {
        return expression.children
    }

    /// If declared, provides contextual type information for statement
    /// expressions to translate them into partial results.
    public static func buildExpression(_ expression: String?) -> [Block & Node] {
        guard let expression = expression, !expression.isEmpty else { return [] }
        
        let document = try? Document(expression)

        // Unlink the children from the document node to prevent dangling pointers to the parent.
        let children = document?.removeChildren() ?? []

        return children
    }

    /// Enables support for `if` statements that do not have an `else`.
    public static func buildOptional(_ component: [Block & Node]?) -> [Block & Node] {
        return component ?? []
    }

    /// With buildEither(second:), enables support for 'if-else' and 'switch'
    /// statements by folding conditional results into a single result.
    public static func buildEither(first component: [Block & Node]) -> [Block & Node] {
        return component
    }

    /// With buildEither(first:), enables support for 'if-else' and 'switch'
    /// statements by folding conditional results into a single result.
    public static func buildEither(second component: [Block & Node]) -> [Block & Node] {
        return component
    }

    /// Enables support for 'for..in' loops by combining the
    /// results of all iterations into a single result.
    public static func buildArray(_ components: [[Block & Node]]) -> [Block & Node] {
        return components.flatMap { $0 }
    }

    /// If declared, this will be called on the partial result of an 'if
    /// #available' block to allow the result builder to erase type
    /// information.
    public static func buildLimitedAvailability(_ component: [Block & Node]) -> [Block & Node] {
        return component
    }
}
#endif
```

- resultBuilder를 사용하지 않고 builder 함수를 만들어서 사용하면 compile error 발생
```Swift
// resultBuilder를 사용하지 않은 빌더 함수
func build<T>(_ closure: () -> T) -> T { closure() }​
```

- resultBuilder를 사용
- @resultBuilder로 builder 함수 정의: 제네릭을 이용하여 런타임 시점에, 타입을 선언하고 선언한 타입을 클로저의 리턴값으로 반환
```Swift
@resultBuilder
enum Builder<T> {
  static func buildBlock(_ component: T) -> T { component }
  static func buildEither(first component: T) -> T { component }
  static func buildEither(second component: T) -> T { component }
}

func builder<T>(@Builder<T> _ closure: () -> T) -> T { closure() }​
```

- 사용: return 키워드 없이 builder {  } 블록을 통해 바로 사용 가능
```Swift
private func getPerson() -> Person {
  builder {
    if num % 2 == 0 {
      Person(name: "jake", age: 2)
    } else {
      Person(name: "jake", age: 1)
    }
  }
}​
```
<br/>
