# webflux 

webflux에 대해 이해하기 전에 Reactive Programming이 무엇인지 먼저 공부해보자. 

reactive programming은 non-blocking applications에 적용되는 프로그래밍을 말하는데, 비동기적이고 이벤트 드라이븐 하고 적은 수의 쓰레드를 필요로 한다. 

reactive applications을 이해하는 키는 backpressure이다. backpressure는 생산자(client)가 소비자(server)를 넘어서지 않도록 보장하는 매커니즘이다. 

Reactive programming 또한 로직의 선언적 비동기 구성을 이끌어내는데 큰 영향을 미친다. 이건 마치 절차적인 blocking code와 CompletableFuture을 사용한 멀티쓰레드를 이용한 것의 차이와 비슷하다. 

## Reactive API and Building Blocks

Spring framework 5는 앞에서 비동기 컴포넌트와 라이브러리에서 사용되는  backpressure 기능을 위해 [Reactive Streams](https://github.com/reactive-streams/reactive-streams-jvm#reactive-streams)을 제공합니다.

Reactive Streams은 스펙으로 자바 9의 java.util.concurrent.Flow. 에서 적용되었습니다.

Spring framework는 [Reactor](https://projectreactor.io/)를 내부적으로 사용하며, Reactor는 Reactive Streams 스펙의 구현체 입니다. Reactor는 basic Reactive Streams `Publisher`  뿐만 아니라 복합형의 flux와 mono도 확장한다. 

Spring framework는 flux와 mono를 많은 reactive API에서 노출하고 있다. 그렇지만 애플리케이션 단계에선, 항상, 스프링은 Rxjava 사용을 완벽히 지원한다. 

## Spring WebFlux Module

Sp
Spring Framework 5 includes a new  `spring-webflux`  module. The module contains support for reactive HTTP and WebSocket clients as well as for reactive server web applications including REST, HTML browser, and WebSocket style interactions.

### Server side


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTcyNjM5OTg0M119
-->