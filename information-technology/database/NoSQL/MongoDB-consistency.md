# Causal Consistency

만약 한 연산이 논리적으로 앞전 연산에 의지하고 있다면, 두 연산자 간의 causal한 관계가 있다고 합니다. 예를 들어, 한 쓰기 연산이 모든 문서를 특정 컨디션에 지우고 있다고 하면, 특정 컨디션을 위한 읽기 연산 때문에 둘의 관계는 causal 관계를 이루고 있씁니다.

causally consistent sessions으로, MongoDB는 그들의 causal 관계십에 기반한 순서대로 causal 연산을 수행합니다. 그리고 클라이언트는 casual 관계들에서 일치성이 맞는지 관찰합니다. 

Causal Consistency을 제공하기 위해선, MongoDB 3.6에서 클라이언트 세션에 가능합니다. A causally consistent session은 `majority` 읽기 연산을 가진것과  `majority` 쓰기 연산의 연속들로 구성됩니다.  애플리케이션은 반드시 오직 하나의 쓰레드에서 

To provide causal consistency, MongoDB 3.6 enables causal consistency in client sessions. A causally consistent session denotes that the associated sequence of read operations with  [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern.%22majority%22 ""majority"")  read concern and write operations with  [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern.%22majority%22 ""majority"")  write concern have a causal relationship that is reflected by their ordering.  **Applications must ensure that only one thread at a time executes these operations in a client session.**

For causally related operations:


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwNTUxNjI2OTYsLTUyMDE1NjI0OSwtMj
kyNDk0NDk3LDIxMzkxNjY2MDQsLTQ1MTc3OTkwNCwtMTk1Njgy
NjU5MSwxNjk3NjMyMzQ1LC0xNzQwNzM4NDQwXX0=
-->