# WebFlux

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

MIT가 개설한 경영과학 과정에서 최초로 학위를 받은 존 리틀에 의해 만들어진 경제와 관련된 법칙으로 프로세스의 안정상태에서의 재고와 산출율 그리고 흐름 시간의 상관관계를 나타낸 법칙입니다.
  
  $$I=λ×R$$
  
여기서 I는 **재고**이며 λ는 **산출율** 그리고 R은 **흐름시간**을 뜻합니다. 이 공식을 제조업이 아닌 서비스업에 적용한다면 I는 **프로세스 내에 대기하는 손님 숫자**이며 λ는 **손님의 평균 도착율** 그리고 R은 **손님이 프로세스 내에서 평균적으로 대기하는 대기시간**이 됩니다.

리틀의 법칙을 사용하면 안정적인 응답 시간을 유지하면서 지정된 수의 초당 사용자를 처리하는데 필요한 서비스 용량이나 웹 응용 프로그램 인스턴스 수를 계산할 수 있습니다. 

$$ N = X×R$$

$N$은 시스템 또는 대기열에 있는 요청의 수를 말합니다. $X$는 요청 유입양, $R$은 평균 응답 시간 또는 대기 시간 입니다.

예를 들어, 시스템의 평균 응답 시간 R이 0.2초이고 유입양 X가 초당 100건 인 경우 대기열의 요청 수는 20건이 됩니다. 즉 초당 20건의 요청 건을 단일 서버에서 처리하거나 다수의 서버로 처리해야 합니다.

다수의 서버로 처리하는 경우 리틀의 법칙은 자연스럽게 완전 균등한 요청의 분배를 가정하고 있습니다. 하지만 실제로는 서버별로 상황이 다르기 때문에 실제 상황을 규칙이 제대로 반영하지 못합니다. 

그리하여 암달의 법칙(Amdahl's Law)과 이를 확장한 보편적 확장성 법칙(USL)을 적용하는 경우가 많습니다. 

## [Amdalh's Law](https://namu.wiki/w/%EC%95%94%EB%8B%AC%EC%9D%98%20%EB%B2%95%EC%B9%99)


암달의 법칙은 평균 응답시간(대기 시간)에 순차적인 엑세스(Serialized Access)가 미치는 영향에 관한 법칙으로 결국 처리량에 관한 법칙입니다. 

항상 작업을 병렬 처리로 하고 싶더라도 병렬화를 할 수 없는 부분이 발생하고 그 부분에 대해서는 순차적인 처리를 해야 합니다. 예를 들어, 시스템에 분배를 조정하는 역할 워커가 있거나 집계 또는 축소 연산자가 있다면 병렬처리가 불가능합니다. 대규모 마이크로 서비스에서는 로드 밸런서 또는 오케스트레이션 역할을 하는 시스템이 이에 해당합니다.

$$X(N) = \frac{X(1) × N}{1 + σ × (N-1)} $$

$X(1)$은 초기 처리량, $N$은 병렬 처리 개수 또는 워커의 수, $σ$는 경합 계수(직렬화 계수)입니다. 경합 계수란 전체 시간 대비 병렬로 처리할 수 없는 코드를 실행하는데 소비하는 시간의 백분율 입니다. 

![enter image description here](https://upload.wikimedia.org/wikipedia/commons/thumb/e/ea/AmdahlsLaw.svg/1200px-AmdahlsLaw.svg.png)

병렬처리 수를 높이면 성능 향상이 이루어지지만, 어느 순간처리량이 느려지기 시작합니다. 그리고 어느 순간 증가하지않고 특정 값에 수렴하게 됩니다.

즉 암달의 법칙에 따르면 전체적인 작업에 대해 병렬 처리를 도입하더라도 처리량은 선형으로 증가하지 않고 어느 순간 수렴하게 됩니다. **이는 코드의 순차적인 부분 때문에 병목 현상이 발생하기 때문입니다.** 

일반적으로 웹 애플리케이션에서 이 문장을 생각해보면 처리 속도를 증가 시킬 수 없는 단일 조정 지점이 있는 경우 서버를 늘려도 아무런 처리 속도의 증가를 얻을 수 없다는 의미 입니다. 즉 비용을 늘려도 돈을 버리는 행위가 될수 있다는 이야기 입니다. 

자 이제, 앞서 보았던 리틀의 법칙과 암달의 법칙을 합쳐 봅시다. 두 방정식 모두 유입량(X)을 포함합니다. 

$$X(N) = \frac{N}{R}$$ 

위를 암달의 법칙에 대입해 봅시다. 

$$X(N) = \frac{N}{R} = \frac{X(1) × N}{1 + σ × (N-1)} $$

그리고 응답속도 R에 맞게 공식을 변경해봅시다.

$$\frac{1}{R} = \frac{X(1) × N}{(1 + σ × (N-1)) × N}$$$$R = \frac{1 + σ × (N-1)}{X(1)}$$

R 대기시간에 전체적인 증가량이 선형인것을 확인할 수 있습니다. 즉 병렬 작업 수를 늘리면 지연 시간도 늘어납니다. 

암달의 법칙이 설명하는 바와 같이 병렬 실행이 있는 시스템은 병렬화 수준을 높이더라도 추가 오버헤드만 발생시키고 더 이상 처리량이 증가하지 못하는 직렬화 지점을 가지고 있습니다. 그로 인해 더 많은 사용자에게 서비스를 제공하거나 대기시간을 줄이는데 한계가 있기 마련입니다. 

## USL(Universal Scalability Law)

암달의 법칙은 병렬처리 즉 시스템의 확장성의 대해서 설명하지만, 실제 응용 프로그램에서는 상당히 다른 결과를 볼 수 있습니다. 닐 건터는 이 연구에 대해서 순차적 실행 뿐만 아니라 비일관성(incoherence)라는 또 하나의 치명적인 문제점을 파악했습니다. 

**비일관성(incoherence)은 동시성을 지원하는 시스템이 공유 자원을 가진 경우 일반적으로 발생하는 현상입니다.** 

표준 자바 웹 애플리케이션 측면에서 CPU와 같은 자원에 대한 난잡하고 혼란스러운 형태의 스레드 엑세스 형태로 나타납니다. 자바 스레딩 모델은 그다지 이상적이지 않습니다. 실제 프로세서보다 많은 숫자의 Thread 인스턴스가 있는경우, CPU에 엑세스하고 계산을 위한 CPU 시간을 확보하기 위해 서로 다른 Thread 인스턴스 간에 직접적인 충돌이 발생합니다. 이렇게 발생하는 중복된 조정과 일관성 문제를 해결하기 위한 별도의 노력이 필요합니다. 스레드가 공유 메모리에 접근할때 마다 추가적인 동기화가 필요하며 이때문에 응용 프로그램의 처리량이 줄어 들 것입니다. 

보편적 확장성 법칙(USL)은 시스템에서 이러한 동작을 설명하기 위해 병렬 처리에 따라 처리량 변화를 계산하는 아래와 같은 수식을 제공합니다. 

$$X(N) = \frac{X(1) × N}{1 + σ × (N-1) + k × N × (N-1)}$$

위 공식에서는 일관성 계수($k$)가 추가 되었습니다. 중요한 점은 처리량 $X(N)$이 병렬 처리 수 $N$의 제곱에 반비례한다는 점입니다. 

![enter image description here](https://lh3.googleusercontent.com/proxy/5VDNOgQCL6h-XfesNfNig8s49Y2nT0ELWwCBNVPgX3s0vye_fE-_NsLmuG3_qKVGMTq3-UF1Oeiar6W_ilYz75PBmYaqGsvpbMZ6SMtbPA)

위 그림에서 Sync threshing 영역은 보편적 확장성 법칙을 따르는 처리량을 보입니다. 반면 Sync waiting 영역은 암달의 법칙을 따르는 처리량을 보여줍니다. 

Sync threshing 영역은 시스템이 공유 엑세스 지점이 있어 동기화를 위한 추가 연산이 필요한 영역입니다. Sync waiting은 동기화를 위한 공유 엑세스 지점이 없는 경우 입니다. 

종합하자면 확장 가능한 시스템을 모델링하고 시스템 용량을 산정하기 위해서는 리틀의 법칙, 암달의 법칙, 보편적 확장성 법칙을 전박적으로 이해하는 것이 중요합니다. 이들 법칙으로 WebFlux와 WebMVC 모듈을 적절히 분석하고 어떤 방식의 가장 적합한 모델인지 예측해보도록 하겠습니다. 

## WebFlux와 WebMVC 처리 모델이해

시스템 처리량과 대기 시간에 대한 두 모델를 이해하기 위해 간단히 두 모델이 사용자 요청을 처리하는 방법을 간단히 알아봅시다. 






















































> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTczODYyMzcxNiwtNzY4NTM1MjA0LDE4Mj
IxNDgyNzAsLTk2ODQ0ODg5NiwtMTI4MTAyNDYxNCwtMTQ1ODcy
MDc5Niw0NDE3OTcxMTQsNjE1NzAzMzk4LC00MTE0OTY2NDUsLT
gwNzYzMjUzNiwyMTM0ODI4MzA2LDE3OTMyMDI3ODYsLTE3NjA3
NTg0MDksLTEwMzkwMTA3MywxODY4MzEzNTY2LDMwMTUwODYwNS
wxMDE4NzkzMzU5LDExMjQ0NTU1NDEsMTgyOTQ4NDk3NywtMTE4
NTY0NTc1OF19
-->