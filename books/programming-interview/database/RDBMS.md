# RDBMS(Relational DataBase Management System)

> SQL은?
SQL은 관계형 데이터베이스에 데이터를 관리하고 질의를 하기 위해 사용하는 선언전 언어다. 관계형 데이터 베이스의 표준 언어이며, 특정 데이터베이스 구현에 얽메이지 않는다. 

> 트랜잭션(transaction)이란 무엇인가?

트랜잭션은 작은 작업들을 하나로 묶은 큰 작업을 말한다. 트래잭션은 ACID 속성을 만족해야 한다.

Atomicity
: 트랜잭션의 내용들은 모두 적용되거나 아니면 하나도 적용되지 않아야 한다. 일부만 적용될수 없다. 

Consistency
: 트랜잭션 적용 후에 데이터베이스에 모든 데이터는 일관 되어야 한다. 

Isolation
: 다수의 트랜잭션이 동시에 동작할때, 트랜잭션이 순차적으로 동작하도록 보장한다. 즉 동시성 컨트롤이 주요 목표인 속성이다.

Durability
: 트랜잭션이 커밋되면 그 내용은 영구히 남아야 한다. 심지어 시스템이 실패해도 남아야 한다. (e.g., power outage or crash. This usually means that completed transactions (or their effects) are recorded in non-volatile memory





> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA1NDEyMzYzN119
-->