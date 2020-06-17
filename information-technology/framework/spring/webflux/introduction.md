# webflux 

webflux에 대해 이해하기 전에 Reactive Programming이 무엇인지 먼저 공부해보자. 

reactive programming은 non-blocking applications에 적용되는 프로그래밍을 말하는데, 비동기적이고 이벤트 드라이븐 하고 적은 수의 쓰레드를 필요로 한다. 

reactive applications을 이해하는 키는 backpressure이다. backpressure는 생산자(client)가 소비자(server)를 넘어서지 않도록 보장하는 매커니즘이다. 

Reactive programming 또한 로직의 선언적 비동기 구성을 이끌어내는데 큰 영향을 미친다. 이건 마치 절차적인 blocking code와 CompletableFuture을 사용한 멀티쓰레드를 이용한 것의 차이와 비슷하다. 

## Reactive API and Building Blocks

Spring framework 5는 앞에서 비동기 컴포넌트와 라이브러리에서 사용되는  backpressure 기능을 위해 [Reactive Streams](https://github.com/reactive-streams/reactive-streams-jvm#reactive-streams)을 제공합니다.

Reactive Streams is a specification created through industry collaboration that has also been adopted in Java 9 as  `java.util.concurrent.Flow`.

The Spring Framework uses  [Reactor](https://projectreactor.io/)  internally for its own reactive support. Reactor is a Reactive Streams implementation that further extends the basic Reactive Streams  `Publisher`  contract with the  `Flux`  and  `Mono`  composable API types to provide declarative operations on data sequences of  `0..N`  and  `0..1`.

The Spring Framework exposes  `Flux`  and  `Mono`  in many of its own reactive APIs. At the application level however, as always, Spring provides choice and fully supports the use of RxJava. For more on reactive types check the post  ["Understanding Reactive Types"](https://spring.io/blog/2016/04/19/understanding-reactive-types)  by Sebastien Deleuze.

## [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#web-reactive-feature-overview)23.2 Spring WebFlux Module

Spring Framework 5 includes a new  `spring-webflux`  module. The module contains support for reactive HTTP and WebSocket clients as well as for reactive server web applications including REST, HTML browser, and WebSocket style interactions.

### [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#web-reactive-server)23.2.1 Server Side

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTMyODM1NDkxOV19
-->