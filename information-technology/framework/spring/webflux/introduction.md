# webflux 

webfl
What is Reactive Programming?

In plain terms reactive programming is about non-blocking applications that are asynchronous and event-driven and require a small number of threads to scale vertically (i.e. within the JVM) rather than horizontally (i.e. through clustering).

A key aspect of reactive applications is the concept of backpressure which is a mechanism to ensure producers don’t overwhelm consumers. For example in a pipeline of reactive components extending from the database to the HTTP response when the HTTP connection is too slow the data repository can also slow down or stop completely until network capacity frees up.

Reactive programming also leads to a major shift from imperative to declarative async composition of logic. It is comparable to writing blocking code vs using the  `CompletableFuture`  from Java 8 to compose follow-up actions via lambda expressions.

For a longer introduction check the blog series  ["Notes on Reactive Programming"](https://spring.io/blog/2016/06/07/notes-on-reactive-programming-part-i-the-reactive-landscape)  by Dave Syer.


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIxMzI0ODU5NzddfQ==
-->