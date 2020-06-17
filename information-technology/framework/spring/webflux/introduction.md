# webflux 

webflux에 대해 이해하기 전에 Reactive Programming이 무엇인지 먼저 공부해보자. 

reactive programming은 non-blocking applications에 적용되는 프로그래밍을 말하는데, 비동기적이고 이벤트 드라이븐 하고 적은 수의 쓰레드를 필요로 한다. 

reactive applications을 이해하는 키는 backpressure이다. backpressure는 생산자(client)가 소비자(server)를 넘어서지 않도록 보장하는 매커니즘이다. 

Reactive programming 또한 로직의 선언적 비동기 구성을 이끌어내는데 큰 영향을 미친다. 이건 마치 blocking code와 CompletableFuture을 사용한 

In plain terms reactive programming is about non-blocking applications that are asynchronous and event-driven and require a small number of threads to scale vertically (i.e. within the JVM) rather than horizontally (i.e. through clustering).

A key aspect of reactive applications is the concept of backpressure which is a mechanism to ensure producers don’t overwhelm consumers. For example in a pipeline of reactive components extending from the database to the HTTP response when the HTTP connection is too slow the data repository can also slow down or stop completely until network capacity frees up.



Reactive programming also leads to a major shift from imperative to declarative async composition of logic. It is comparable to writing blocking code vs using the  `CompletableFuture`  from Java 8 to compose follow-up actions via lambda expressions.

For a longer introduction check the blog series  ["Notes on Reactive Programming"](https://spring.io/blog/2016/06/07/notes-on-reactive-programming-part-i-the-reactive-landscape)  by Dave Syer.


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjAxMDQ4MDc3NF19
-->