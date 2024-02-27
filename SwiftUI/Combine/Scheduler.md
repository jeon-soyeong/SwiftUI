# Scheduler
- 언제, 어떻게 클로저가 실행될지 정하는 프로토콜
  - thread 설정도 가능 (main, global)
  - 시간 설정도 가능
<br/>

## Scheduler 사용 방법
### 스레드 변경
- receive(on:): downstream의 스레드 변경 -> publisher로 부터 element를 수신할 scheduler를 지정하는 역할
- subscribe(on:): upstream의 스레드 변경

### receive(on:), subscribe(on:) 안쓴 경우 스레드 확인
- DispatchQueue.global()로 실행한 경우, main thread가 아닌 global thread에서 sink의 클로저 부분이 동작
- 즉, 따로 스케줄러 설정을 하지 않으면 subject의 이벤트를 발행하는 쪽의 스케줄러와 동일하게 sink 클로저 부분이 동작

```Swift
let subject = PassthroughSubject<Void, Never>()

subject
  .sink(receiveValue: { _ in print(Thread.isMainThread) })

subject.send(())

DispatchQueue.global().async {
  subject.send(())
}

/*
 true
 false
*/
```

### recevie(on:) - downstream에 적용
```Swift
let subject = PassthroughSubject<Void, Never>()

let cancellable = subject
  .handleEvents(receiveOutput: { print("upstream: \(Thread.isMainThread)") })
  .receive(on: DispatchQueue.main)
  .handleEvents(receiveOutput: { print("downstream: \(Thread.isMainThread)") })
  .sink(receiveValue: { _ in print()  })

DispatchQueue.global().async {
  subject.send(())
}

/*
 upstream: false
 downstream: true
*/
```

### subscribe(on:) - upstream에 적용
```Swift
Just(1)
  .map { _ in print(Thread.isMainThread) }
  .subscribe(on: DispatchQueue.global())
  .sink { print(Thread.isMainThread) }

/*
 true
 false
*/
```

### delay를 주어, thread 변경
```Swift
let cancellable = Just(1)
  .receive(on: DispatchQueue.main)
  .map { _ in print(Thread.isMainThread) }
  .delay(for: 2, scheduler: DispatchQueue.global()) // background thread로 변경
  .sink { print(Thread.isMainThread) } // 여기도 background thread 
  
/*
 true
 false
*/
```
<br/>

## subscribe(on:) vs recevie(on:)
### subscribe(on:)
- subject 일경우 
send 하는 곳의 thread를 탐(main이었으면 main으로), 하지만 이 내부의 subscribe, request, cancel은 global로 했으면 global에서 작업함<br/>
(subscribe, cancel, request 동작을 실행시킬 스케쥴러를 지정한다고 나와있다.<br/>
즉, subscribe(on:)으로 지정하게 되는 것은 publisher의 subscribe(_:), subscription의 request(_:)와 cancel()을 실행시킬 스케쥴러뿐이다.)<br/>
<br/>

- 그외, Sequence나 Just는 subscribe(on: ) thread에서 지정한대로 thread 나옴. (global 로 지정시 global 로 나옴)
<br/>

- CurrentValueSubject는 예외로 
초기값은 지정한 thread로, 그 이후에는 send 하는 thread에서 실행됨.<br/>
<br/>

**앞부분(upstream)에 영향**<br/>
<br/>


### receive(on: )
반대로 초기 subscription을 만드는 부분들은 메인 쓰레드에서 동작<br/>
Timer를 통해 값/completion이 방출되는 부분 또한 메인 쓰레드에서 동작<br/>
Subscriber가 받은 값/completion을 처리하는 부분은 global thread 즉, receive(on:)에서 지정한 thread 에서 동작함.<br/>

**뒷부분(downstream) 에 영향**<br/>
<br/>

### 결론
- subscribe(on:)은 publisher, subscription과 같이 upstream 부분에 영향을 미친다.<br/>
- receive(on:)은 subscriber의 값/completion 처리와 같이 downstream 부분에 영향을 미친다.<br/>
https://velog.io/@ictechgy/Combine-subscribeon-VS.-receiveon<br/>
<br/>

