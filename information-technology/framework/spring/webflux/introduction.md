# webflux 

webflux에 대해 이해하기 전에 Reactive Programming이 무엇인지 먼저 공부해보자. 

reactive programming은 non-blocking applications에 적용되는 프로그래밍을 말하는데, 비동기적이고 이벤트 드라이븐 하고 적은 수의 쓰레드를 필요로 한다. 

reactive applications을 이해하는 키는 backpressure이다. backpressure는 생산자(client)가 소비자(server)를 넘어서지 않도록 보장하는 매커니즘이다. 

Reactive programming 또한 로직의 선언적 비동기 구성을 이끌어내는데 큰 영향을 미친다. 이건 마치 절차적인 blocking code와 CompletableFuture을 사용한 멀티쓰레드를 이용한 것의 차이와 비슷하다. 

## Reactive API and Building Blocks

[Reactive Streams](https://github.com/reactive-streams/reactive-streams-jvm#reactive-streams)은 비동기/논블라킹 스트림 처리를 위한 명세

Reactor는 Reactive Streams의 실제 구현체 입니다.

Spring framework는 [Reactor](https://projectreactor.io/)를 내부적으로 사용하며, Reactor는 Reactive Streams 스펙의 구현체 입니다. Reactor는 basic Reactive Streams `Publisher`  뿐만 아니라 복합형의 flux와 mono도 확장한다. 

Spring framework는 flux와 mono를 많은 reactive API에서 노출하고 있다. 그렇지만 애플리케이션 단계에선, 항상, 스프링은 Rxjava 사용을 완벽히 지원한다. 

## Spring WebFlux Module

Spring Framework 5에선 spring-webflux라는 새로운 모듈이 나왔다. 이 모듈은 reactive HTTP, WebSocket client, 더 나아가 server web applications도 지원한다.

### Server side

서버 단에서 WebFlux는 두 가지 프로그래밍 모델을 지원한다.

-   Annotation-based with  `@Controller`  and the other annotations supported also with Spring MVC
-   Functional, Java 8 lambda style routing and handling

다행히 위 두가지 모델 모두 Reactive Streams API의 non-blocking HTTPS을 이용하도록 동일하게 처리된다. 

![enter image description here](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/images/webflux-overview.png)

위 그림은 왼쪽 블럭은 전통적인 서블릿 기반의 Spring MVC의 스택을 보여준다. 오른쪽 블럭에는 reactive stack을 보여주고 있다. 

WebFlux can run on Servlet containers with support for the Servlet 3.1 Non-Blocking IO API as well as on other async runtimes such as Netty and Undertow. Each runtime is adapted to a reactive `ServerHttpRequest` and `ServerHttpResponse` exposing the body of the request and response as `Flux<DataBuffer>`, rather than `InputStream` and `OutputStream`, with reactive backpressure. REST-style JSON and XML serialization and deserialization is supported on top as a `Flux<Object>`, and so is HTML view rendering and Server-Sent Events.


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzNzQyNjA5MTYsMjU4ODU3NjMyXX0=
-->