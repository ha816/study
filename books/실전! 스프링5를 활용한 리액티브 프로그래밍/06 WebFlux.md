# WebFlux

과거 스프링 프레임 워크는

## WebClient 

논블러킹을 지원하는 통신 클라이언트

본질적으로 WebClient는 이전 RestTemplate의 대체품입니다. 그러나 보다 리액티브 방식에 더 잘맞는 함수형 API가 있으며, Flux 또는 Mono와 같은 리액터 프로젝트 타입에 대한 매핑이 내장되어 있습니다. 
```
WebClient.create("uri").get().retrieve().bodyToMono(User.class).map().subscribe()
```

create는 팩터리 메서드이고 기본 URI를 정할 수 있습니다. retreive 메서드는 옵션의 내용을 검색해 조회하거나 다음 처리를 위해 데이터를 준비하는 경우 유용합니다. 
응답 본문을 생성하는 메서드로 bodyToMono를 사용합니다. 이 메서드는 수신 페이로드를 Mono로 변환합니다. 마지막으로 리액터 API를 사용해 응답의 처리 흐름을 구축하고 subscribe 메서드로 원격 호출을 실행합니다.

WebClient는 리액티브 스트림 스펙을 따릅니다. 즉 subscribe 메서드를 호출해서 WebClient가 커넥션을 연결하고 데이터를 원격 서버로 보내기 시작합니다.

대부분의 경우 일반적인 응답 처리는 본문을 처리하는 것이지만, 응답 상태, 헤더 또는 쿠키를 처리하는 경우도 있습니다.

DefaultWebClient는 원격 서버와의 비동기 및 논블로킹 상호 작용을 제공하기 위해 리액터-네티 HttpClient를 사용합니다. 하지만 기본 HTTP 클라이언트를 쉽겨 변경하도록 설계되었습니다. 
WebClient.builer로 사용자가 직접 구현해야 하는 인스턴스를 전달 받을 수 있습니다. 

## 리액티브 웹소켓 API

현대 웹의 중요한 부분 중 하나는 클라이언트와 서버가 서로 메세지를 스트리밍하는 모델입니다. 양방향 클라이언트-서버 통신을 위한 가장 잘 알려진 프로토콜 중 하나인 **웹 소켓**을 알아봅시다. 

웹소켓 프로토콜 통신은 2013년 초 스프링 프레임워크에 도입되었습니다. 이는 비 동기 메세지 전송을 위해 설계되었지만 실제로는 여전히 일부 블로킹 동작이 있었습니다. 예를 들어 I/O 데이터를 쓰거나 읽는 작업은 여전치 차단방식으로 동작하므로 응용 프로그램의 성능에 영향을 미쳤습니다. 그에 따라 웹플럭스 모듈에는 웹소켓을 구조적으로 개선한 새 버전이 추가되었습니다. 

웹 플럭스는 클라이언트와 서버를 모두 지원합니다. 서버측 웹소켓을 먼저 보도록 합시다. 

### 서버 웹소켓 API


웹소켓 연결을 처리하기 위한 핵심 인터페이스로 WebSocketHandler가 있습니다. 인터페이스에는 WebSocketSession을 허용하는 handle이라는 메서드가 있습니다. WebSocketSession 클래스는 클라이언트와 서버간의 성공적인 통신을 나타내며, 핸드세이크, 세션 속성 및 수신 데이터등의 정보를 제공합니다.

```
class EchoWebSocketHandler implements WebSocketHandler {
	@Ovverride
	public Mono<void> handle(WebSocketSession session) {
		return session
				.receive()
				.map(WebSocketMessage::getPayloadAsText)
				.map(tm -> "Echo:" + tm)
				.map(session::textMessage)
				.as(session::send)
	}
}
```

새로운 웹소켓 API는 리액터 프로젝트의 리액티브 타입을 기반으로 만들어졌습니다. 
WebSocketHandler 인터페이스 구현외에 서버 측 웹 소켓 API를 설정하려면 추가로 HandlerMapping 및 WebSocketHandlerAdapter 인스턴스를 구성해야 합니다. 

```
@Configuration
public class WebSocketConfiguration {
	
	@Bean
	public HandlerMapping handlerMapping() {
		SimpleUrlHandlerMApping mapping = new SimpleHandlerMapping();
		mapping.setUrlMap(Collections.singletonMap(
			"/ws/echo", new EchoWebSocketHandler()
		));
		mapping.setOrder(-1);
		return mapping
	}

	@Bean
	public HandlerAdapter handlerAdapter() {
		return new WebSocketHandlerAdapter();
	}
}
```

### 클라이언트 측 웹소켓 API

일반적 웹 소켓모듈과는 달리 웹플럭스는 클라이언트 측면을 지원합니다. 웹 소켓 연결 요청을 보내려면 WebSocketClient 클래스가 필요합니다. WebSocketClient에는 다 코드와 같이 웹소켓 연결을 실행하는 두 메서드가 있습니다.

```java
public interface WebSocketClient {
	Mono<Void> execute(
		URI uri,
		WebSocketHandler handler
	);
	Mono<Void> execute(
		URI uri,
		HttpHeaders headers,
		WebSocketHandler handler
	);
}
```

WebSocketClient는 서버의 메세지를 처리하는 용도와 메세지를 다시 보내기 위한 용도로 모두 WebSocketHandler 인터페이스를 사용합니다. Tomcat, Jetty 와 같이 모두 서버 엔진관 관련된 WebSocketClient 구현체가 있습니다. 아래 예제는 ReactorNettyWebSocketClient의 예제입니다.

```java
public interface WebSocketClient {
	Mono<Void> execute(
		URI uri,
		WebSocketHandler handler
	);
	Mono<Void> execute(
		URI uri,
		HttpHeaders headers,
		WebSocketHandler handler
	);
}
```


# WebFlux vs WebMVC

새로운 WebFlux가 WebMVC와 비교해서 어떤 점이 나은것인지 아직 알 수 없습니다. 웹 플럭스의 장점을 알아봅시다. 

대부분 웹 응용 프로그램의 핵심 성능 지표는 처리량, 대기 시간, CPU 및 메모리 사용량입니다. 

## 리틀의 법칙(Little's Law)

리틀의 법칙 
: 지정한 대기 시간까지 정의된 양을 처리하기 위해 동시에 얼마나 많은 병렬 작업자가 있어야 하는지 계산하는 방법

즉 리틀 법칙을 통해 안정적인 응답 시간을 유지하면서 지정된 수의 초당 사용자를 처리하는데 필요한 서비스 용량이나 웹 응용 프로그램 인스턴스 수를 계산할 수 있습니다. 

$$ N = X * R$$

$N$은 시스템 또는 대기열에 상주하는 평균 요청 수 말합니다.  $X$는 처리량, $R$은 평균 응답 시간 또는 대기 시간 입니다.

예를 들어, 시스템 평균 응답 시간 R이 0.2초이고 처리량 X가 초당 100건 인 경우 초당 20개의 요청을 동시에 처리하거나 병렬로 처리할 수 있어야 합니다. 















> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTAxODc5MzM1OSwxMTI0NDU1NTQxLDE4Mj
k0ODQ5NzcsLTExODU2NDU3NTgsLTYwNzgxNzM0MywtMTQ4MTcx
OTk3NywtMTkxODc1ODA5MywtMTE4NjUzOTkyNCwtMTUwNjQ3Nj
U0OSwtMTE2NDA2OTcyOSwxMDUyNjM5MDc2LDE2NTY3NDE3NTIs
MjY3NzkyNzIxLDMzNDI3MjAwNywtMTM2NDIzOTE5MCw2MTc5OD
M3NDYsLTU5MTgxMTc5OSwtMzEwNTM2MTQ1LC04MjY1NDQzMDAs
LTE1ODQzNjA5OTVdfQ==
-->