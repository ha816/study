# webflux 

webflux에 대해 이해하기 전에 Reactive Programming이 무엇인지 먼저 공부해보자. 

reactive programming은 non-blocking applications에 적용되는 프로그래밍을 말하는데, 비동기적이고 이벤트 드라이븐 하고 적은 수의 쓰레드를 필요로 한다. 

reactive applications을 이해하는 키는 backpressure이다. backpressure는 생산자(client)가 소비자(server)를 넘어서지 않도록 보장하는 매커니즘이다. 

Reactive programming 또한 로직의 선언적 비동기 구성을 이끌어내는데 큰 영향을 미친다. 이건 마치 절차적인 blocking code와 CompletableFuture을 사용한 멀티쓰레드를 이용한 것의 차이와 비슷하다. 

# webflux의 장단점

MSA와 같이 한정된 작은 자원을 써야할 상황에서 효율적이다. 특히 응답성이 많이 향상되므로 상호간 호출이 빈번한 MSA에서 더욱 좋다. ~~마지막으로, 새로운 기술을 활용하는 엘레강스한 개발자가 된 느낌을 받을 수 있다.~~

여러 장점이 있지만 webflux의 사용은 사실 필수가 아니다. 심지어 Spring 내부에서도 반드시 써야하는건 아니라고 했다. 또 기존 servelet기반의 코드를 webflux로 머지하는 과정이 쉽지 않다. 따라서 무리하여 	webflux로 이관하는 것은 좋지 않으며, 새로운 서비스에 접목하는 것이 좋아 보인다.

다른 단점으로는 스트림 기반이다보니 디버깅이 쉽지 않고, 개발자의 사고방식이 비동기 처리에 맞게 바뀌어야 한다. 

webflux를 사용할 수 없는 경우가 있는데 바로 RDBMS를 사용하고 있을때이다. Spring에서 지원하는 NoSQL은 MongoDB, Redis, Cassandra, Couchbase가 있다. 




## Reactive Streams

[Reactive Streams](https://github.com/reactive-streams/reactive-streams-jvm#reactive-streams)은 비동기/논블라킹 스트림 처리를 위한 명세입니다. 그리고 Reactor는 Reactive Streams의 실제 구현체 입니다. 그 밖에 다른 구현체로는 Rxjava, Akka Streams등이 있습니다. (사실 같은 스펙을 구현했기 때문에 구현체 끼리 비슷한 모양새와 사용법을 가집니다.)

### MONO VS FLUX

MONO는 데이터 스트림이 하나 있거나 없거나 하는 경우에 사용된다. 반대로 데이터 스트림이 여러개인 경우 FLUX를 사용해야 한다. 
 

## Spring WebFlux Module

Spring Framework 5에선 spring-webflux라는 새로운 모듈이 나왔다. 이 모듈은 reactive HTTP, WebSocket client, 더 나아가 server web applications도 지원한다.

### Server side

서버 단에서 WebFlux는 두 가지 프로그래밍 모델을 지원한다.

-   Annotation-based with  `@Controller`  and the other annotations supported also with Spring MVC
-   Functional, Java 8 lambda style routing and handling

다행히 위 두가지 모델 모두 Reactive Streams API의 non-blocking HTTPS을 이용하도록 동일하게 처리된다. 

![enter image description here](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/images/webflux-overview.png)

위 그림은 왼쪽 블럭은 전통적인 서블릿 기반의 Spring MVC의 스택을 보여준다. 오른쪽 블럭에는 reactive stack을 보여주고 있다. 



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTExMjQ3NjA4NDAsMjAwODIxMjg2LDI1OD
g1NzYzMl19
-->