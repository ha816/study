# WebFlux 

스프링 5부터 Spring Webflux를 통해 Reactive Programming(RP)이 가능하게 되었습니다. Reactive Programming이란 바로 비동기 데이터 스트림들로 프로그래밍을 하는 것을 말한다.
> Reactive programming is programming with asynchronous data streams.  

Reactive Programing에선 기본적으로 모든 것을 스트림(stream)으로 봅니다. 전통적인 클릭 이벤트도 사실 비동기 이벤트 스트림으로 볼 수 있습니다. 더 나아가 클릭, 호버와 같은 이벤트가 아닌 어떤 것으로든 스트림을 만들 수 있습니다. (변수, 사용자 입력 등) Reactive Programing의 대표로는 JavaScript, RxJS등이 있습니다. 

다시 돌아와, Spring Webflux을 사용한 애플리케이션은 비동기 논블러킹(asynchronous non-blocking)이면서 이벤트 기반(Event-driven)의 특징을 가지게 됩니다. 이 이야기는 후에 보다 자세히 다루겠습니다.

## F

* WebFlux는 MSA와 같이 한정된 작은 자원을 써야할 상황에서 효율적입니다. 
	* 특히 응답성이 많이 향상되므로 상호간 호출이 빈번한 MSA에서 더욱 좋다고 알려져있습니다. 
* ~~신기술을 사용하는 엘레강스한 개발자가 된 느낌을 받을 수 있다.~~ 

WebFlux의 사용은 사실 필수가 아니라고 합니다. (심지어 Spring측 의견) 만약 기존 servelet 기반의 서비스가 이미 있고 이를 WebFlux로 무리해서 옮기는 것은 비관적으로 보입니다. (webflux로 머지하는 과정이 어렵다고 합니다.) 따라서 WebFlux는 새로운 서비스에 접목하는 것이 좋아 보입니다.

다른 단점으로는 비동기의 스트림 기반이다보니 디버깅이 쉽지 않고, 개발자의 사고방식이 비동기 처리에 맞게 바뀌어야 합니다. 마지막으로 WebFlux를 사용할 수 없는 경우가 있는데  non-blocking을 지원하지 않는 RDBMS를 사용하는 경우 입니다. WebFlux에서 지원하는 NoSQL은 MongoDB, Redis, Cassandra, Couchbase가 있습니다.

### Server side

서버 단에서 WebFlux는 두 가지 프로그래밍 모델을 지원한다.

-   Annotation-based with  `@Controller`  and the other annotations supported also with Spring MVC
-   Functional, Java 8 lambda style routing and handling

다행히 위 두가지 모델 모두 Reactive Streams API의 non-blocking HTTPS을 이용하도록 동일하게 처리된다. 

![enter image description here](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/images/webflux-overview.png)

위 그림은 왼쪽 블럭은 전통적인 서블릿 기반의 Spring MVC의 스택을 보여준다. 오른쪽 블럭에는 reactive stack을 보여주고 있다. 

'Spring WebFlux는 어떻게 적은 리소스로 많은 트래픽을 감당할까?'란 궁금증을 시작으로 여기까지 왔다. 이에 대한 답은 I/O를 Non Blockkng을 이용하여 잘 사용하는 것과 Request를 Event-Driven을 통해서 효율적으로 처리하기 때문에 가능하다.

# reference

[https://gist.github.com/staltz/868e7e9bc2a7b8c1f754](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)

[https://share.navercorp.com/techtalk698/lecture/44750](https://share.navercorp.com/techtalk698/lecture/44750)

[https://alwayspr.tistory.com/44](https://alwayspr.tistory.com/44)


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTMzODIwNzgyNCwxODM1MTU2NDAxLC0xNj
c1MDk3NDAyLDIxNDM5MDAxMTAsLTExMTM5NTIxMDIsLTIxMzA1
NTkyNCwtNjY5OTg0MTksMTQzMDAwMzc3NCwtMTEyNDc2MDg0MC
wyMDA4MjEyODYsMjU4ODU3NjMyXX0=
-->