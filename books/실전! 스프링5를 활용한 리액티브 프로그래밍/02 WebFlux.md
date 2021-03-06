# WebFlux

Spring WebFlux는 Spring 5에 추가되었으며 reactive-stack web framework으로도 불립니다.

이에 따라 Spring은 **Reactive Stack**과  **Servlet Stack**  두 가지 형태의 웹 프래임워크를 제공하게 되었습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fk.kakaocdn.net%2Fdn%2FsMFrh%2FbtqCMoJkmiO%2FC1DKIoWLdkPTUKoeO4GOuK%2Ftfile.svg)

WebFlux는  [Reactive Streams](https://www.reactive-streams.org/)를 사용하여 Non-Blocking과 Back Pressure를 지원합니다.

WebFlux는 Netty, Undertow 그리고 Servlet 3.1+ containers이상의 서버에서 동작합니다.

Non-Blocking, Back Pressure의 의미는 본 위키에서 설명하고 Reactive Streams은 [LCS - Spring WebFlux - Practice](https://wiki.linecorp.com/display/ContentsAI/LCS+-+Spring+WebFlux+-+Practice)에서 알아보도록 하겠습니다.

## WebFlux Features

WebFlux는 비동기 논블러킹(asynchronous non-blocking)이면서 이벤트 기반(event-driven)이기 때문에 효율적으로 빠르게 동작합니다.

특히 응답성이 크게 향상되므로 상호간 호출이 빈번한 MSA에서 더욱 좋다고 알려져 있습니다.

아래 그림은  [DZone](https://dzone.com/articles/raw-performance-numbers-spring-boot-2-webflux-vs-s)에서 고전적인 Spring MVC를 이용한 Boot1과 Spring WebFlux를 이용한 Boot2의 성능을 비교한 그래프입니다.

주목할 점은 동시 사용자 수가 1000미만일 경우는 성능의 차이가 거의 없지만, 사용자 수가 늘어날수록 성능의 차이가 월등히 커지는 현상입니다.

![](https://3.bp.blogspot.com/-3i759KJap_U/We6baQQFc2I/AAAAAAAAfTs/0G7gLgD2BWsmVbPluFFoeGhViOafX1QqgCLcBGAs/s1600/Boot1VsBoot2.png)

  

이런 성능상 명확한 장점이 있지만, WebFlux를 사용하는게 필수사항은 아니라고 합니다. (심지어 Spring 전문가들의 의견)

만약 기존 servelet 기반의 서비스가 있고 이를 WebFlux로 무리해서 옮긴다고 하면 득보다 실이 많을 수 있다고 합니다. (이관에 많은 공수가 든다고 합니다.)

따라서 WebFlux는 비교적 새로운 서비스에 적용하는 것이 좋아 보입니다.

  

WebFlux는 비동기의 스트림 기반이다보니 디버깅이 쉽지 않고, 개발자의 사고방식이 비동기 처리에 맞게 바뀌어야 합니다.

마지막으로, WebFlux를 사용할 수 없는 경우가 있는데 non-blocking을 지원하지 않는 RDBMS를 사용하는 경우입니다. (R2DBC를 제외한 일반적인 모든 RDBMS가 사용불가)

현재 WebFlux에서 non-blocking을 지원하는 NoSQL은 MongoDB, Redis, Cassandra, Couchbase가 있습니다.

# How WebFlux Works?

이제 어떻게 WebFlux가 효율적으로 동작하는지 아래 두 핵심 개념을 알아보겠습니다.

-   Asynchronous Non-Blocking I/O
-   Back Pressure

## Asynchronous Non-Blocking I/O

Spring Webflux을 사용한 애플리케이션은 비동기 논블러킹(asynchronous non-blocking)합니다.

asynchronous non-blocking을 이해하기에 앞서 Blocking I/O, Synchronous Non-Blocking I/O, Asynchronous Non-Blocking I/O를 차례대로 알아보겠습니다.

### Blocking I/O

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory&fname=http%3A%2F%2Fcfile9.uf.tistory.com%2Fimage%2F991CE83359A87B6D342482)

Spring MVC와 RDBMS를 사용하고 있으면 대부분 이 Blocking I/O 모델을 따릅니다.

Application에서 커널에 I/O 요청을 한 후 완료되기 전까지는 Application이 Block 상태가 되어 다른 작업을 수행할 수 없습니다.

  

하지만 실제 서비스를 하다보면 Blocking 방식임에도 불구하고 마치 Block이 안된듯이 동작하는 것처럼 보입니다. 이것은 기본적으로 서블릿이 Multi Thread 기반으로 동작하기 때문에 그렇게 보이는 것입니다. Block이 되는 순간 다른 Thread로 Context Switching함으로써 CPU 자원을 효율적으로 사용하게 됩니다. 그러나 Context Switching에 드는 비용이 있기 때문에 다수의 I/O를 처리하기 위해 대응하는 다수의 Thread를 사용하는 것은 비효율적인 면이 있습니다.

### Synchronous Non-Blocking I/O

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory&fname=http%3A%2F%2Fcfile10.uf.tistory.com%2Fimage%2F99245C3359A87B9533B7DA)

Application에서 I/O를 요청을 하면 바로 결과가 반환되어 다른 작업을 수행합니다. 다른 작업 중에 주기적으로 I/O 데이터가 준비 되었는지 확인하고 그러다가 준비가 완료되면 후속작업을 합니다.

이렇게 주기적으로 확인하는 방식을  **폴링(Polling)**방식이라고 합니다. 폴링방식은 주기적으로 I/O 작업이 완료되었는지 확인하는 추가 작업이 필요하기 때문에 불필요한 자원을 사용하게 됩니다.

Blocking과 Non-Blocking의 차이는  **요청 측의 작업 요청 이후 바로 제어권을 주는지 여부에 달려 있습니다.**

요청한 측에 바로 제어권을 넘겨주고, 요청측에서 다른 작업을 할 수 있는 기회를 주면 Non-Blocking이라 합니다.

요청 받은측이 작업을 온전히 마칠때 까지 요청측에 제어권을 넘겨주지 않으면 Blocking이라 합니다.

###   
Asynchronous Non-Blocking I/O

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory&fname=http%3A%2F%2Fcfile10.uf.tistory.com%2Fimage%2F99D8873359A87C3D1001BD)

  

앞서 설명한 모델인 Synchronous Non-Blocking과 마찬가지로 I/O 요청이후 즉시 다른 작업을 수행합니다. 그러다가 I/O 데이터가 준비되면 이벤트로 알려주거나, 미리 등록해놓은 callback을 통해 다음 작업이 진행됩니다.

Asynchronous Non-Blocking I/O는 앞서 이야기한 두 모델 보다 효율적입니다. (Context Switching, Polling 관점에서)

Synchronous과 Asynchronous의 차이는  **요청받은 측의 작업 완료를 요청 측에서 확인하는지 여부에 있습니다.**

요청측에서 요청 받은 측 작업의 완료를 기다리거나, 바로 반환 받더라도 작업 완료 여부를 스스로 계속 확인하는 경우는 Synchronous.

요청측에서 더 이상 작업완료를 신경쓰지 않으면 Asynchronous라고 합니다. 이 경우 요청시 callback을 전달하여 작업이 완료되면 해당 callback을 실행하도록 합니다.

## Back Pressure

Back Pressure는 처리가능한 작업량을 넘어서는 작업량을 처리해야할 때, 안정적으로 처리하는 기법을 말합니다.

Back Pressure는 [observer pattern](https://howtodoinjava.com/design-patterns/behavioral/observer-design-pattern/)과 밀접한 연관이 있습니다. [observer pattern](https://howtodoinjava.com/design-patterns/behavioral/observer-design-pattern/)에서는 데이터를 처리하는 방식에 크게 Push 방식과 Pull 방식이 있는데, Back Pressure는 그 중에  Pull 방식의 데이터 처리 기법을 말합니다.

### Push 방식

Push 방식은 Publisher가 Subscriber에게 데이터를 밀어 넣는 방식입니다.

그런데 Publisher는 Subscriber의 상태를 고려하지 않고 데이터를 전달하는 데에만 충실합니다.

만약 Subscriber가 데이터를 빠르게 처리하지 못하면, 처리되지 못한 데이터는 큐(Queue) 메모리 버퍼에 쌓이게 됩니다.

서버가 가용할 수 있는 메모리는 한정되어 있고, 아래 그림은 메모리의 한계를 넘어선 수준의 데이터가 큐에 쌓였을때 발생하는 상황입니다.

![](https://engineering-org.line-apps.com/ko/reactivestreams1-5/)

![](https://engineering-org.line-apps.com/ko/reactivestreams1-6/)

고정 길이 버퍼를 사용한다면, 신규로 수신된 메시지를 거절합니다.

거절된 메시지는 재요청을 하도록 유도할 수 있습니다만, 그러면 네트워크와 CPU 연산 비용이 추가로 발생합니다.

가변 길이 버퍼를 사용한다면, OOM(out of memory)에러가 발생하면서 서버가 죽게 됩니다.

### Pull 방식

앞서 보았던 Push 방식의 문제는  **Publisher가 Subscriber의 상태를 고려하지 않기 때문에 발생합니다.**

Pull 방식은 **Subscriber가 처리할 수 있는 만큼의 데이터를 요청하여 처리하는 방식**입니다. 그리고 이게 백 프레셔(Back Pressure)의 기본 원리입니다.

![](https://engineering-org.line-apps.com/ko/reactivestreams1-7/)

예를 들어, Subscriber가  10개의 데이터를 처리할 수 있다면 Publisher에게 10개만 요청합니다. Publisher는 요청받은 만큼만  Subscriber에게 전달하면 되고, Subscriber  더 이상 OOM(out of memory)  에러를 걱정하지 않아도 됩니다.

# WebFlux FLOW

WebFlux의 처리흐름을 이해하기 전에 앞서 Event-Driven Programming을 이해하면 좋습니다.

Event-Driven Programming은 일종의 프로그래밍 패러다임으로 볼 수 있습니다.

기존의 절차적인 실행 흐름이 아닌 이벤트(마우스 클릭, 키 누르기 또는 다른 프로그램의 메시지와 같은 사용자 작업)에 의해 실행흐름이 정해지는 프로그래밍 방법을 말합니다.

순차적으로 진행되는 과거의 프로그래밍 방식과는 달리 사용자에 의해 종잡을 수 없이 진행되는 GUI(Graphical User Interface)가 발전됨에 따라 Event-Driven 방식이 발전되어 왔습니다.

![](https://deepakpol.files.wordpress.com/2015/09/event-loop.png)

기본적인 흐름은 Event Loop가 돌면서 Event를 감지한 뒤 Event Handler에게 Event 처리를 위임합니다.

단순히 Event Handler로 적합한 행위만 등록해주면 간단히 Event를 제어할 수 있습니다. 이로써 개발자는 Event의 후속작업인 Event Handle에 집중할 수 있게 됩니다.

Event-Driven한 프로그램에선 앞서 이야기한 일련의 과정(Event Loop를 돌면서 Event발생을 감지하고 적합한 Handler에 위임해주는 부분)을 언어 레벨에서 처리 해줍니다.

Event-Driven은 Request를 일종의 Event로 취급할수 있어 일반적인 Server에 적합한 모델입니다. Node.js, Spring WebFlux는 Event-Driven에 맞는 형태로 Architecture가 구현되어 있다고 합니다.

![](https://howtodoinjava.com/wp-content/uploads/2019/02/Non-blocking-request-processing.png)

  

위 그림은 전반적인 WebFlux 처리 흐름을 나타냅니다.

기본적으로 WebFlux에서 모든 요청은 Event Handler와 callback 정보를 가진채로 들어옵니다.

WebFlux에서는 대기 상태인 쓰레드가 없고 일반적으로 요청을 받는 역할을 전담하는 유일무이한 Request Thread가 하나 있습니다.

이 Request Thread는 들어온 요청을 Fixed/Small한 Thread 수를 가지는 Thread Pool에 작업을 위임합니다. 해당 Thread Pool의 Thread들은 Event Loop 역할을 하게되며 아래 두 가지 역할을 합니다.

  

1.  들어온 요청에 적합한 handler function에 요청을 위임하고, 즉시 Request Thread로 부터 들어온 다른 요청을 처리합니다.
2.  handler function의 작업이 완료되면, 그 결과를 받아 callback function으로 던집니다.

이런 식으로 적은 수의 쓰레드를 사용하면 적은 메모리 용량을 쓰고 또한 context switching도 적기 때문에 애플리케이션의 성능이 좋아집니다.

  

**WebFlux로 성능을 최대한 끌어내려면 모든 I/O 작업이 Non-Blocking으로 동작해야 합니다.** Blocking이 되는 곳이 있다면 안 하느니만 못한 상황이 되어버립니다.

예를 들어 Thread가 1개인데 Blocking이 걸리는 API를 10번 동시에 호출한다면, 결국엔 Spring MVC처럼 10번의 I/O 작업이 끝날 때까지 기다려야 됩니다.

# References

[https://gist.github.com/staltz/868e7e9bc2a7b8c1f754](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)

[https://share.navercorp.com/techtalk698/lecture/44750](https://share.navercorp.com/techtalk698/lecture/44750)

[https://alwayspr.tistory.com/44](https://alwayspr.tistory.com/44)


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4MTYwNjcxMTRdfQ==
-->