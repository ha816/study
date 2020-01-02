# RDBMS(Relational DataBase Management System)

> SQL은?

SQL은 관계형 데이터베이스에 데이터를 관리하고 질의를 하기 위해 사용하는 선언전 언어다. 관계형 데이터 베이스의 표준 언어이며, 특정 데이터베이스 구현에 얽메이지 않는다. 

--- 

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

--- 
> Statement와 PreparedStatement의 차이

Statement와 PreparedStatement의 차이를 알려면 우선 MySQL 서버가 쿼리를 처리하는 각 단계를 이해해야 한다. 아래 절차는 쿼리를 수행하기 위한 절차를 나타낸다. 

쿼리요청 -> 쿼리 분석 -> 최적화 -> 권한체크 -> 쿼리실행

자바 프로그램에서 Statement로 실행되는 쿼리는 위의 절차를 매번 걸쳐서 쿼리가 실행된다. 
하지만 PreparedStatement를 사용하면 쿼리 분석이나 최적화의 일부작업을 처음 한번만 수행해 별도로 저장해 두고, 다음 부터 요청되는 쿼리는 저장된 분석결과를 재사용한다. 이렇게 하여 매번 쿼리를 실행해야 하는 쿼리분석이나 최적화의 일부 작업을 건너띌 수 있다. 

예를 들어 아래와 같은 쿼리가 100번 실행된다고 해보자.
```
SELECT * FROM employees WHERE emp_no = 10001;
SELECT * FROM employees WHERE emp_no = 10002;
SELECT * FROM employees WHERE emp_no = 10003;
...
```
똑같은 패턴의 쿼리임에도 MySQL 쿼리는 100개의 쿼리 분석 결과를 보관해야 한다. 이럴 
부분을 보완하고자 PreparedStatement에서는 쿼리에 변수를 사용이 가능하다.

```
SELECT * FROM employees WHERE emp_no = ?;
```
PreparedStatement에서 "?" 바인딩 변수 또는 변수 홀더(variable holder)라고 하는데, 실제 쿼리를 실행할때는 변수 대신에 상수 값을 대입해야 한다.  이렇게 바인딩 변수를 사용하면 쿼리를 템플릿화 할 수 있고, Statement 쿼리에 비해서 쿼리 문장의 수를 대폭 줄일 수 있다. 덕분에 MySQL 서버에서 보관해야하는 정보가 줄어 메모리 사용량을 줄일 수 있다.

이렇게 **변수를 사용하는 쿼리를 바인딩 쿼리** 라고 하며, **바인딩 변수 없이 사용하는 쿼리를 동적 쿼리 또는 다이나믹 쿼리**라고 한다. 

```
conn.prepareStatement("SELECT * FROM employees WHERE emp_no=?")
```
PreparedStatement를 사용할 때는 SQL 쿠러ㅣ 문장을 이용해 PreparedStatement 객체를 준비해야 한다. 위 메서드를 실행하면 Connector는 주어진 SQL 문장을 서버로 전송해서 쿼리를 분석하고 그 결과를 저장해 둔다. 그리고 MySQL 서버는 쿼리의 분석 결과의 포인터와 같은 해시 값을 반환하고 이 값으로 PreparedStatement 객체를 생성한다. 이렇게 생성된 PreparedStatement 바인딩 변수 값만 변경하면서 계속 사용하게 된다. 

결론적으로 PreparedStatement의 성능적 장점은 한번 실행된 쿼리는 매번 쿼리 분석 과정을 거치지 않고 재사용한다는 점이다. SQL 문장의 길이가 길다면 SQL 문장 자체가 네트워크로 전송되지 않고 바인딩할 변수 값만 서버로 전달하기 때문에 네트워크 측면에서 다소 효율적이다. 또 다른 장점으로는 바이너리 프로토콜을 사용한다는 점이다.  MySQL 5.0 전에는 내부적으로MySQL서버에 쿼리를 보내기 위해서 문자열 타입으로 데이터를 변환했다. 그러다 보니 데이터의 크기가 커지는 현상이 있었는데 5.0이상에서는 PreparedStatement를 사용할때 타입변환을 하지않는 바이너리 통신 프로토콜을 사용하기 때문에 좋다. 

--- 
> MyBatis에서 #{}과 ${}의 차이는?

https://logical-code.tistory.com/25
[https://www.donnert.net/65](https://www.donnert.net/65)


MyBatis에서는 변수를 바인딩 하는데 #{}, ${}를 활용하는 방법이 있다. 

- #{}
	- PrepareStatment를 활용한다. SQL 인젝션에 안정적이고 일반적으로 MySQL 캐시가 걸려 빠르다. 그래서 거의 항상 사용이 추천된다.
- ${}	
	- Statment를 활용한다. 


 #{}으로 변수를 바인딩 하는데, 기본적으로 #{}을 사용하면 PreparedStatement 특성을 생성하고 사용하고 값을 안정적으로 바인딩한다. 그리고 이 방식이 안전하고 빠르고 거의 항상 추천된다. 그러나 가끔 수정이 없는 SQL Statement를 그대로 넣고 싶을때가 있다. 그럴때는 ${}를 사용한다. 예를 들어, ORDER BY나 LIKE 키워드에 변수를 넣을때는 아래와 같이 써야한다. ${}안에는 변수값 자체가 들어가기 때문에 ""가 들어가지 않는다. 따라서 LIKE, ORDER BY와 같이 더블 스퀏이 들어가지 않는 쿼리에는 ${}를 넣는다. 
```
LIKE %${word}%, ORDER BY ${orderAS}
```
그렇지만 이렇게 Statement 쿼리(동적 쿼리)를 직접 쓰는 것은 SQL Injection이 있을 수 있기 때문에 조심해야 한다. 사용자의 입력이 직접 들어오지 못하게 해야하거나 특수 문자들을 체크 해야 한다. 

---
> JDBC는 무엇인가?

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
eyJoaXN0b3J5IjpbMTIzOTk3MzI3OCwtMTA1MTk1NTMwLDM2MD
Y2NjM0NCwtMzYwNjYzNzI5LC0yMDQyMDkxMjM5LDk5MjIzNDQ5
MSwtNzY4OTk1ODAxLC0xNzM3NzU3NTk3LDEyNDkxNjIxOTQsLT
M4OTUxNTYyOCwxMDg5OTcwNzE0LDcxMDY1NDk2NiwtMTU0MDg4
NDcwMiwtNTgxODA1MDk4XX0=
-->