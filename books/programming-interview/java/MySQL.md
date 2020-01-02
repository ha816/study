# RDBMS(Relational DataBase Management System)

> SQL은?

SQL은 관계형 데이터베이스에 데이터를 관리하고 질의를 하기 위해 사용하는 선언전 언어다. 관계형 데이터 베이스의 표준 언어이며, 특정 데이터베이스 구현에 얽메이지 않는다. 

# MySQL

> 트랜잭션(transaction)이란 무엇인가?

트랜잭션은 작은 작업들을 하나로 묶은 큰 작업을 말한다. 트래잭션은 ACID 속성을 만족해야 한다.

ACID
: In computer science, **ACID** (Atomicity, Consistency, Isolation, Durability) is a set of properties of database transaction intended to guarantee validity even in the event of errors, power failures, etc.

Atomicity
: 트랜잭션의 내용들은 모두 적용되거나 아니면 하나도 적용되지 않아야 한다. 일부만 적용될수 없다. 

Consistency
: 트랜잭션 적용 후에 데이터베이스에 모든 데이터는 일관 되어야 한다. 

Isolation
: 다수의 트랜잭션이 동시에 동작할때, 트랜잭션이 순차적으로 동작하도록 보장한다. 즉 동시성 컨트롤이 주요 목표인 속성이다.

Durability
: 트랜잭션이 커밋되면 그 내용은 영구히 남아야 한다. 심지어 시스템이 실패해도 남아야 한다. (e.g., power outage or crash. This usually means that completed transactions (or their effects) are recorded in non-volatile memory





# JDBC

> 관계형 데이터베이스에 접근하기 위해 자바를 어떻게 사용하는가?

JDBC(Java Database Connectivity)는 데이터 베이스에 연결하기 위한 표준 자바 라이브러리에 내장된 매커니즘이다.
```
Connection connection = DriverManager.getConnection();
Statement stmt = connection.createStatement();
ResultSet rs = stmt.executeQuery("");
```

>참조 무결성(Referential integrity)이란?

관련있는 테이블 간 데이터의 무결성(청렴)을 말한다.

![enter image description here](https://upload.wikimedia.org/wikipedia/commons/thumb/1/13/Referential_integrity_broken.png/250px-Referential_integrity_broken.png)




## 인메모리 데이터베이스 테스팅

데이터 베이스에 접속할 애플리케이션을 개발할때 데이터 베이스를 테스트 할 수 있는 상태로 유지하는 건 가끔 문제가 될 수 있다. 

인메모리 데이터 베이스의 자료는 JVM 실행을 종료하면 사라지는데, 이는 인메모리 데이터 베이스를 데이터 유지에 신경쓰지 않고 테스트 데이터로 작업하기 쉽게 만든다. 인메모리 데이터베이스는 로컬 장비에서 실행되므로 네트워크 트래픽이 없고 귀찮은 연결관리를 하지 않아도 된다. 


## 프로시저 

저장 프로시저는  SQL이 제공하는 단순한 생성, 읽기, 수정, 삭제 보다 더 많은 기능을 제공한다. 프로시저는 DB서버에서 제공하는 함수와 비슷하다.

저장 프로시저는 정해진 방법으로 데이터를 처리하는 배치성 구문에 유용하다.   

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU4MTgwNTA5OF19
-->