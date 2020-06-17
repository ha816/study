
# WebFlux Core

'Spring WebFlux는 어떻게 적은 리소스로 많은 트래픽을 감당할까?'란 궁금증을 시작으로 여기까지 왔다. 이에 대한 답은 I/O를 Non Blockkng을 이용하여 잘 사용하는 것과 Request를 Event-Driven을 통해서 효율적으로 처리하기 때문에 가능하다.

## Reactive Streams

[Reactive Streams](https://github.com/reactive-streams/reactive-streams-jvm#reactive-streams)은 비동기/논블러킹(Async/Non-blocking) 스트림 처리를 위한 표준명세입니다. 그리고 Reactor는 Reactive Streams의 실제 구현체 입니다. 그 밖에 다른 구현체로는 Rxjava, Akka Streams등이 있습니다. (사실 같은 스펙을 구현했기 때문에 구현체 끼리 비슷한 모양새와 사용법을 가집니다.)

### MONO VS FLUX

MONO는 데이터 스트림이 하나 있거나 없거나 하는 경우에 사용된다. 반대로 데이터 스트림이 여러개인 경우 FLUX를 사용해야 한다. 
 



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTY0NDk4Mzk0OV19
-->