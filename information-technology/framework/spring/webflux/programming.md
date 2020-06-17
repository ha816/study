
# WebFlux 

## Reactive Streams

[Reactive Streams](https://github.com/reactive-streams/reactive-streams-jvm#reactive-streams)은 비동기/논블러킹(Async/Non-blocking) 스트림 처리를 위한 표준명세입니다. 그리고 Reactor는 Reactive Streams의 실제 구현체 입니다. 그 밖에 다른 구현체로는 Rxjava, Akka Streams등이 있습니다. (사실 같은 스펙을 구현했기 때문에 구현체 끼리 비슷한 모양새와 사용법을 가집니다.)

### MONO VS FLUX

MONO는 데이터 스트림이 하나 있거나 없거나 하는 경우에 사용된다. 반대로 데이터 스트림이 여러개인 경우 FLUX를 사용해야 한다. 
 


#### Annotation-based Programming Model

The same  `@Controller`  programming model and the same annotations used in Spring MVC are also supported in WebFlux. The main difference is that the underlying core, framework contracts — i.e.  `HandlerMapping`,  `HandlerAdapter`, are non-blocking and operate on the reactive  `ServerHttpRequest`  and  `ServerHttpResponse`  rather than on the  `HttpServletRequest`  and  `HttpServletResponse`. Below is an example with a reactive controller:

_@RestController_
public class PersonController {

	private final PersonRepository repository;

	public PersonController(PersonRepository repository) {
		this.repository = repository;
	}

	_@PostMapping("/person")_
	Mono<Void> create(_@RequestBody_ Publisher<Person> personStream) {
		return this.repository.save(personStream).then();
	}

	_@GetMapping("/person")_
	Flux<Person> list() {
		return this.repository.findAll();
	}

	_@GetMapping("/person/{id}")_
	Mono<Person> findById(_@PathVariable_ String id) {
		return this.repository.findOne(id);
	}
}

#### [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#web-reactive-server-functional)Functional Programming Model

##### [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#_functional_programming_model)Functional Programming Model

![[Note]](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/images/note.png)

This section is to be merged into  `web-flux.adoc`.

###### [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#_handlerfunctions)HandlerFunctions

Incoming HTTP requests are handled by a  **`HandlerFunction`**, which is essentially a function that takes a  `ServerRequest`  and returns a  `Mono<ServerResponse>`. The annotation counterpart to a handler function would be a method with  `@RequestMapping`.

`ServerRequest`  and  `ServerResponse`  are immutable interfaces that offer JDK-8 friendly access to the underlying HTTP messages. Both are fully reactive by building on top of Reactor: the request expose the body as  `Flux`  or  `Mono`; the response accepts any  [Reactive Streams](http://www.reactive-streams.org/)  `Publisher`  as body.

`ServerRequest`  gives access to various HTTP request elements: the method, URI, query parameters, and — through the separate  `ServerRequest.Headers`  interface — the headers. Access to the body is provided through the  `body`  methods. For instance, this is how to extract the request body into a  `Mono<String>`:

Mono<String> string = request.bodyToMono(String.class);

And here is how to extract the body into a  `Flux`, where  `Person`  is a class that can be deserialised from the contents of the body (i.e.  `Person`  is supported by Jackson if the body contains JSON, or JAXB if XML).

Flux<Person> people = request.bodyToFlux(Person.class);

The two methods above (`bodyToMono`  and  `bodyToFlux`) are, in fact, convenience methods that use the generic  `ServerRequest.body(BodyExtractor)`  method.  `BodyExtractor`  is a functional strategy interface that allows you to write your own extraction logic, but common  `BodyExtractor`  instances can be found in the  `BodyExtractors`  utility class. So, the above examples can be replaced with:

Mono<String> string = request.body(BodyExtractors.toMono(String.class);
Flux<Person> people = request.body(BodyExtractors.toFlux(Person.class);

Similarly,  `ServerResponse`  provides access to the HTTP response. Since it is immutable, you create a  `ServerResponse`  with a builder. The builder allows you to set the response status, add response headers, and provide a body. For instance, this is how to create a response with a 200 OK status, a JSON content-type, and a body:

Mono<Person> person = ...
ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).body(person);

And here is how to build a response with a 201 Created status, Location header, and empty body:

URI location = ...
ServerResponse.created(location).build();

Putting these together allows us to create a  `HandlerFunction`. For instance, here is an example of a simple "Hello World" handler lambda, that returns a response with a 200 status and a body based on a String:

HandlerFunction<ServerResponse> helloWorld =
  request -> ServerResponse.ok().body(fromObject("Hello World"));

Writing handler functions as lambda’s, as we do above, is convenient, but perhaps lacks in readability and becomes less maintainable when dealing with multiple functions. Therefore, it is recommended to group related handler functions into a handler or controller class. For example, here is a class that exposes a reactive  `Person`  repository:

import static org.springframework.http.MediaType.APPLICATION_JSON;
import static org.springframework.web.reactive.function.BodyInserters.fromObject;

public class PersonHandler {

	private final PersonRepository repository;

	public PersonHandler(PersonRepository repository) {
		this.repository = repository;
	}

	public Mono<ServerResponse> listPeople(ServerRequest request) { [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#CO4-1)![1](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/images/callouts/1.png)
		Flux<Person> people = repository.allPeople();
		return ServerResponse.ok().contentType(APPLICATION_JSON).body(people, Person.class);
	}

	public Mono<ServerResponse> createPerson(ServerRequest request) { [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#CO4-2)![2](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/images/callouts/2.png)
		Mono<Person> person = request.bodyToMono(Person.class);
		return ServerResponse.ok().build(repository.savePerson(person));
	}

	public Mono<ServerResponse> getPerson(ServerRequest request) { [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#CO4-3)![3](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/images/callouts/3.png)
		int personId = Integer.valueOf(request.pathVariable("id"));
		Mono<ServerResponse> notFound = ServerResponse.notFound().build();
		Mono<Person> personMono = this.repository.getPerson(personId);
		return personMono
				.then(person -> ServerResponse.ok().contentType(APPLICATION_JSON).body(fromObject(person)))
				.otherwiseIfEmpty(notFound);
	}
}

[![1](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/images/callouts/1.png)](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#CO4-1)

`listPeople`  is a handler function that returns all  `Person`  objects found in the repository as JSON.

[![2](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/images/callouts/2.png)](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#CO4-2)

`createPerson`  is a handler function that stores a new  `Person`  contained in the request body. Note that  `PersonRepository.savePerson(Person)`  returns  `Mono<Void>`: an empty Mono that emits a completion signal when the person has been read from the request and stored. So we use the  `build(Publisher<Void>)`  method to send a response when that completion signal is received, i.e. when the  `Person`  has been saved.

[![3](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/images/callouts/3.png)](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#CO4-3)

`getPerson`  is a handler function that returns a single person, identified via the path variable  `id`. We retrieve that  `Person`  via the repository, and create a JSON response if it is found. If it is not found, we use  `otherwiseIfEmpty(Mono<T>)`  to return a 404 Not Found response.

###### [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#_routerfunctions)RouterFunctions

Incoming requests are routed to handler functions with a  **`RouterFunction`**, which is a function that takes a  `ServerRequest`, and returns a  `Mono<HandlerFunction>`. If a request matches a particular route, a handler function is returned; otherwise it returns an empty  `Mono`. The  `RouterFunction`  has a similar purpose as the  `@RequestMapping`  annotation in  `@Controller`  classes.

Typically, you do not write router functions yourself, but rather use  `RouterFunctions.route(RequestPredicate, HandlerFunction)`  to create one using a request predicate and handler function. If the predicate applies, the request is routed to the given handler function; otherwise no routing is performed, resulting in a 404 Not Found response. Though you can write your own  `RequestPredicate`, you do not have to: the  `RequestPredicates`  utility class offers commonly used predicates, such matching based on path, HTTP method, content-type, etc. Using  `route`, we can route to our "Hello World" handler function:

RouterFunction<ServerResponse> helloWorldRoute =
	RouterFunctions.route(RequestPredicates.path("/hello-world"),
	request -> Response.ok().body(fromObject("Hello World")));

Two router functions can be composed into a new router function that routes to either handler function: if the predicate of the first route does not match, the second is evaluated. Composed router functions are evaluated in order, so it makes sense to put specific functions before generic ones. You can compose two router functions by calling  `RouterFunction.and(RouterFunction)`, or by calling  `RouterFunction.andRoute(RequestPredicate, HandlerFunction)`, which is a convenient combination of  `RouterFunction.and()`  with  `RouterFunctions.route()`.

Given the  `PersonHandler`  we showed above, we can now define a router function that routes to the respective handler functions. We use  [method-references](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html)  to refer to the handler functions:

import static org.springframework.http.MediaType.APPLICATION_JSON;
import static org.springframework.web.reactive.function.server.RequestPredicates.*;

PersonRepository repository = ...
PersonHandler handler = new PersonHandler(repository);

RouterFunction<ServerResponse> personRoute =
	route(GET("/person/{id}").and(accept(APPLICATION_JSON)), handler::getPerson)
		.andRoute(GET("/person").and(accept(APPLICATION_JSON)), handler::listPeople)
		.andRoute(POST("/person").and(contentType(APPLICATION_JSON)), handler::createPerson);

Besides router functions, you can also compose request predicates, by calling  `RequestPredicate.and(RequestPredicate)`  or  `RequestPredicate.or(RequestPredicate)`. These work as expected: for  `and`  the resulting predicate matches if  **both**  given predicates match;  `or`  matches if  **either**  predicate does. Most of the predicates found in  `RequestPredicates`  are compositions. For instance,  `RequestPredicates.GET(String)`  is a composition of  `RequestPredicates.method(HttpMethod)`  and  `RequestPredicates.path(String)`.

###### [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#_running_a_server)Running a Server

Now there is just one piece of the puzzle missing: running a router function in an HTTP server. You can convert a router function into a  `HttpHandler`  by using  `RouterFunctions.toHttpHandler(RouterFunction)`. The  `HttpHandler`  allows you to run on a wide variety of reactive runtimes: Reactor Netty, RxNetty, Servlet 3.1+, and Undertow. Here is how we run a router function in Reactor Netty, for instance:

RouterFunction<ServerResponse> route = ...
HttpHandler httpHandler = RouterFunctions.toHttpHandler(route);
ReactorHttpHandlerAdapter adapter = new ReactorHttpHandlerAdapter(httpHandler);
HttpServer server = HttpServer.create(HOST, PORT);
server.newHandler(adapter).block();

For Tomcat it looks like this:

RouterFunction<ServerResponse> route = ...
HttpHandler httpHandler = RouterFunctions.toHttpHandler(route);
HttpServlet servlet = new ServletHttpHandlerAdapter(httpHandler);
Tomcat server = new Tomcat();
Context rootContext = server.addContext("", System.getProperty("java.io.tmpdir"));
Tomcat.addServlet(rootContext, "servlet", servlet);
rootContext.addServletMapping("/", "servlet");
tomcatServer.start();

TODO: DispatcherHandler

###### [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#_handlerfilterfunction)HandlerFilterFunction

Routes mapped by a router function can be filtered by calling  `RouterFunction.filter(HandlerFilterFunction)`, where  `HandlerFilterFunction`  is essentially a function that takes a  `ServerRequest`  and  `HandlerFunction`, and returns a  `ServerResponse`. The handler function parameter represents the next element in the chain: this is typically the  `HandlerFunction`  that is routed to, but can also be another  `FilterFunction`  if multiple filters are applied. With annotations, similar functionality can be achieved using  `@ControllerAdvice`  and/or a  `ServletFilter`. Let’s add a simple security filter to our route, assuming that we have a  `SecurityManager`  that can determine whether a particular path is allowed:

import static org.springframework.http.HttpStatus.UNAUTHORIZED;

SecurityManager securityManager = ...
RouterFunction<ServerResponse> route = ...

RouterFunction<ServerResponse> filteredRoute =
	route.filter(request, next) -> {
		if (securityManager.allowAccessTo(request.path())) {
			return next.handle(request);
		}
		else {
			return ServerResponse.status(UNAUTHORIZED).build();
		}
  });

You can see in this example that invoking the  `next.handle(ServerRequest)`  is optional: we only allow the handler function to be executed when access is allowed.

### [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#web-reactive-client)23.2.2 Client Side

WebFlux includes a functional, reactive  `WebClient`  that offers a fully non-blocking and reactive alternative to the  `RestTemplate`. It exposes network input and output as a reactive  `ClientHttpRequest`  and  `ClientHttpResponse`  where the body of the request and response is a  `Flux<DataBuffer>`  rather than an  `InputStream`  and  `OutputStream`. In addition it supports the same reactive JSON, XML, and SSE serialization mechanism as on the server side so you can work with typed objects. Below is an example of using the  `WebClient`  which requires a  `ClientHttpConnector`  implementation to plug in a specific HTTP client such as Reactor Netty:

WebClient client = WebClient.create("http://example.com");

Mono<Account> account = client.get()
		.url("/accounts/{id}", 1L)
		.accept(APPLICATION_JSON)
		.exchange(request)
		.then(response -> response.bodyToMono(Account.class));

![[Note]](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/images/note.png)

The  `AsyncRestTemplate`  also supports non-blocking interactions. The main difference is it can’t support non-blocking streaming, like for example  [Twitter one](https://dev.twitter.com/streaming/overview), because fundamentally it’s still based and relies on  `InputStream`  and  `OutputStream`.

### [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#web-reactive-http-body)23.2.3 Request and Response Body Conversion

The  `spring-core`  module provides reactive  `Encoder`  and  `Decoder`  contracts that enable the serialization of a  `Flux`  of bytes to and from typed objects. The  `spring-web`  module adds JSON (Jackson) and XML (JAXB) implementations for use in web applications as well as others for SSE streaming and zero-copy file transfer.

For example the request body can be one of the following way and it will be decoded automatically in both the annotation and the functional programming models:

-   `Account account` — the account is deserialized without blocking before the controller is invoked.
-   `Mono<Account> account` — the controller can use the  `Mono`  to declare logic to be executed after the account is deserialized.
-   `Single<Account> account` — same as with  `Mono`  but using RxJava
-   `Flux<Account> accounts` — input streaming scenario.
-   `Observable<Account> accounts` — input streaming with RxJava.

The response body can be one of the following:

-   `Mono<Account>` — serialize without blocking the given Account when the  `Mono`  completes.
-   `Single<Account>` — same but using RxJava.
-   `Flux<Account>` — streaming scenario, possibly SSE depending on the requested content type.
-   `Observable<Account>` — same but using RxJava  `Observable`  type.
-   `Flowable<Account>` — same but using RxJava 2  `Flowable`  type.
-   `Flux<ServerSentEvent>` — SSE streaming.
-   `Mono<Void>` — request handling completes when the  `Mono`  completes.
-   `Account` — serialize without blocking the given Account; implies a synchronous, non-blocking controller method.
-   `void` — specific to the annotation-based programming model, request handling completes when the method returns; implies a synchronous, non-blocking controller method.

When using stream types like  `Flux`  or  `Observable`, the media type specified in the request/response or at mapping/routing level is used to determine how the data should be serialized and flushed. For example a REST endpoint that returns a  `Flux<User>`  will be serialized by default as following:

-   `application/json`: a  `Flux<User>`  is handled as an asynchronous collection and serialized as a JSON array with an explicit flush when the  `complete`  event is emitted.
-   `application/stream+json`: a  `Flux<User>`  will be handled as a stream of  `User`  elements serialized as individual JSON object separated by new lines and explicitly flushed after each element. The  `WebClient`  supports JSON stream decoding so this is a good use case for server to server use case.
-   `text/event-stream`: a  `Flux<User>`  or  `Flux<ServerSentEvent<User>>`  will be handled as a stream of  `User`  or  `ServerSentEvent`  elements serialized as individual SSE elements using by default JSON for data encoding and explicit flush after each element. This is well suited for exposing a stream to browser clients.  `WebClient`  supports reading SSE streams as well.

### [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#web-reactive-websocket-support)23.2.4 Reactive WebSocket Support

WebFlux includes reactive WebSocket client and server support. Both client and server are supported on the Java WebSocket API (JSR-356), Jetty, Undertow, Reactor Netty, and RxNetty.

On the server side, declare a  `WebSocketHandlerAdapter`  and then simply add mappings to  `WebSocketHandler`-based endpoints:

_@Bean_
public HandlerMapping webSocketMapping() {
	Map<String, WebSocketHandler> map = new HashMap<>();
	map.put("/foo", new FooWebSocketHandler());
	map.put("/bar", new BarWebSocketHandler());

	SimpleUrlHandlerMapping mapping = new SimpleUrlHandlerMapping();
	mapping.setUrlMap(map);
	return mapping;
}

_@Bean_
public WebSocketHandlerAdapter handlerAdapter() {
	return new WebSocketHandlerAdapter();
}

On the client side create a  `WebSocketClient`  for one of the supported libraries listed above:

WebSocketClient client = new ReactorNettyWebSocketClient();
client.execute("ws://localhost:8080/echo"), session -> {... }).blockMillis(5000);

### [](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html#web-reactive-tests)23.2.5 Testing

The  `spring-test`  module includes a  `WebTestClient`  that can be used to test WebFlux server endpoints with or without a running server.

Tests without a running server are comparable to  `MockMvc`  from Spring MVC where mock request and response are used instead of connecting over the network using a socket. The  `WebTestClient`  however can also perform tests against a running server.

For more see  [sample tests](https://github.com/spring-projects/spring-framework/tree/master/spring-test/src/test/java/org/springframework/test/web/reactive/server/samples)  in the framework.

# reference

https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTY0ODUzMTMyMV19
-->