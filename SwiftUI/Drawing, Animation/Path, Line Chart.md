# Path, Line Chart

## Path
- 2D 선과 면을 그릴때 사용
- move(to:) 메소드를 통해 시작점을 정하고, addLine(to:)를 통해 선을 그림

```Swift
// 삼각형
struct ContentView: View {
  var body: some View {
    Path { path in
      path.move(to: CGPoint(x: 100, y: 100))
      path.addLine(to: CGPoint(x: 100, y: 200))
      path.addLine(to: CGPoint(x: 200, y: 200))
    }
  }
}

// 사각형
struct ContentView: View {
  var body: some View {
    Path { path in
      path.move(to: CGPoint(x: 100, y: 100))
      path.addLine(to: CGPoint(x: 200, y: 100))
      path.addLine(to: CGPoint(x: 200, y: 200))
      path.addLine(to: CGPoint(x: 100, y: 200))
    }
  }
}

// 반원
struct ContentView: View {
  var body: some View {
    Path { path in
      path.addArc(
        center: CGPoint(x: 100, y: 100),
        radius: 50,
        startAngle: Angle(degrees: 90),
        endAngle: Angle(degrees: -90),
        clockwise: true
      )
    }
  }
}

// 원
struct ContentView: View {
  var body: some View {
    Path { path in
      path.addArc(
        center: CGPoint(x: 100, y: 100),
        radius: 50,
        startAngle: Angle(degrees: 180),
        endAngle: Angle(degrees: -180),
        clockwise: true
      )
    }
  }
}
```

- 이 밖에도 다양한 메소드가 존재
  - addArc(center:radius:startAngle:endAngle:clocwise:transform:) - 원호
  - addRect(:transform:) - 직사각형
  - addCurve(to:control1:control2:) - 3차 베지어 곡선
  - addQuadCurve(to:control:) - 2차 베지어 곡선
  - addEllipse(in:transform:) - 타원
<br/>

## Line Chart
```Swift
// - 데이터 준비
let datas = [205.87664248616824, 212.36591529463246, 258.8049099609528, 271.15419904110513, 273.86096203639727, 200.5959198527124, 217.96883356777028, 200.65573463890772, 261.3261775414218, 296.3419276530915, 242.74453036813378, 261.1769798545026, 221.55057031565565, 249.33001867026115, 235.89561987798092, 231.4278357622111, 257.1897839965552, 291.26324127070046, 234.27449372375384, 225.20342071447678, 256.0687554460256, 251.9884291086963, 271.4570906908704, 285.46843524640906, 221.93316123890222, 289.2200239699109, 270.0400095996903, 288.05903959368084, 265.5227107292555, 249.45236572757034, 228.50355096112676, 266.0659074095188, 219.7226062948684, 252.55327498248647, 289.5215302974416, 257.2631777132392, 241.1341168669185, 251.2296256239748, 247.38185750757185, 278.1093403927073, 216.7238654210636]
// iOS의 CGPoint()의 (0,0)은 좌측 상단이기 때문에, y가 증가할수록 아래로 가고 작아질수록 위로 이동
// 데이터를 그릴때도 y의 값이 클 수록 아래쪽으로 길어지게 만든 후, rotation을 통해 돌린 후 대칭이동을 통해 사용


// - ex) 사용하는쪽
// Path 인스턴스를 얻고, rotation과 대칭이동을 사용
  var body: some View {
    getLinePath(values: datas) // 이 부분 아래에서 구현
      .stroke(.green ,style: StrokeStyle(lineWidth: 2, lineJoin: .round))
      .rotationEffect(.degrees(180), anchor: .center) // 시계방향으로 180도 회전
      .rotation3DEffect(.degrees(180), axis: (x: 0, y: 1, z: 0)) // y축 대칭
  }

// - getLinePath(values:) 구현
private func getLinePath(values: [Double]) -> Path {
  guard values.count > 1 else { return Path() }
  let offset = (values.max()! - values.min()!) / 2.0
  
  let step = CGPoint(
    x: totalWidth / CGFloat(values.count - 1),
    y: totalHeight / CGFloat(values.max()! - values.min()!)
  )
  var path = Path()
  let p1 = CGPoint(x: 0, y: step.y * (values[0] - offset))
  path.move(to: p1)
  
  for i in 1..<values.count {
    let p2 = CGPoint(x: step.x * CGFloat(i), y: step.y * (values[i] - offset))
    path.addLine(to: p2)
  }
  
  return path
}

```
<br/>
