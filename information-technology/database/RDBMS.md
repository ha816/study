# RDBMS(Relational DataBase Management System)

> SQL은?
SQL은 관계형 데이터베이스에 데이터를 관리하고 질의를 하기 위해 사용하는 선언전 언어다. 관계형 데이터 베이스의 표준 언어이며, 특정 데이터베이스 구현에 얽메이지 않는다. 

>참조 무결성(Referential integrity)이란?

관련있는 테이블 간 데이터의 무결성(청렴)을 말한다.

![enter image description here](https://upload.wikimedia.org/wikipedia/commons/thumb/1/13/Referential_integrity_broken.png/250px-Referential_integrity_broken.png)


## 인메모리 데이터베이스 테스팅

데이터 베이스에 접속할 애플리케이션을 개발할때 데이터 베이스를 테스트 할 수 있는 상태로 유지하는 건 가끔 문제가 될 수 있다. 

인메모리 데이터 베이스의 자료는 JVM 실행을 종료하면 사라지는데, 이는 인메모리 데이터 베이스를 데이터 유지에 신경쓰지 않고 테스트 데이터로 작업하기 쉽게 만든다. 인메모리 데이터베이스는 로컬 장비에서 실행되므로 네트워크 트래픽이 없고 귀찮은 연결관리를 하지 않아도 된다. 


## 프로시저 

저장 프로시저는  SQL이 제공하는 단순한 생성, 읽기, 수정, 삭제 보다 더 많은 기능을 제공한다. 프로시저는 DB서버에서 제공하는 함수와 비슷하다.

저장 프로시저는 정해진 방법으로 데이터를 처리하는 배치성 구문에 유용하다.   

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


>트랜잭션 격리 수준(Transaction Isolation Level)

참조하는 데이터스프링 프레임 워크에서는 데이터베이스의 기본 설정(DEFAULT)과 4개의 트랜잭션 격리 수준을 이용할수 있다. 스프링 프레임워크에서 지원하는 격리 수준은 아래와 같다. 다만 지원하는 모든 격리 수준이 실제로 사용할 수 있는지는 사용하는 데이터베이스를 어떻게 구현했느냐에 따라 달라질 수 있다. MySQL은 REPEATABLE_READ, Oracle DB는 READ_COMMITED 

| 격리수준| 설명 | Dirty Read, Unrepeatable Read, Phantom Read |
|--|--|--|
| DEFAULT  | 사용하는 데이터베이스의 기본 격리수준을 사용  |
| READ_UNCOMMITTED| 더티리드(Dirty Read), 반복되지 않은 읽기(Unrepeatable Read), ad)가 발생한다. 이 격리 수준은 커밋되지 않은 변경 데이터를 다른 트랜잭션에서 참조하는 것을 허용한다. 만약 변경 데이터가 롤백된 경우 다음 트랜잭션에서는 무효한 데이터를 조회하게 된다. | O, O, O|
| READ_COMMITTED| 더티리드(Dirty Read)는 방지하지만, 반복되지 않은 읽기(Unrepeatable Read), 팬텀 읽기(Phantom Read)는 발생한다. 이 격리 수준은 커밋되지 않은 변경 데이터를 다른 트랜잭션에서 참조하는 것을 금지한다. |X, O, O|
| REPEATABLE_READ| 더티리드, 반복되지 않은 읽기를 방지하지만 팬텀읽기는 발생한다. |X, X, O|
| SERIALIZABLE| 더티리드, 반복되지 않은 읽기, 팬텀읽기를 방지한다.|X, X, X| 

더티리드(Dirty Read)
: A dirty read occurs when a transaction is allowed to read data from a row that has been modified by another running transaction and not yet committed. Dirty Read also knwo as **write–read conflict**, **reading uncommitted data**.
더티리드는 한 트랜잭션이 아직 커밋하지 않은 상태로 수정 중일때 데이터 row 읽기가 가능하다면 발생한다. 

$$
\begin{bmatrix}T1&T2\\R(A)&\\W(A)&\\&R(A)\\&W(A)\\&R(B)\\&W(B)\\&Com.\\R(B)&\\W(B)&\\Com.&\end{bmatrix}
$$

T2 could read a database object A, modified by T1 which hasn't committed. This is a **dirty read**. T1 may write some value into A which makes the database inconsistent. It is possible that interleaved execution can expose this inconsistency and lead to inconsistent final database state, violating ACID rules.
더티리드는 한 트랜잭션이 아직 커밋하지 않은 상태로 수정 중일때 데이터 row 읽기가 가능하다면 발생한다. 예를 들면, TX1에서 가져온 row 데이터가 있고, TX2에서 커밋하지 않은 상태로 데이터를 수정했다. 커밋되지 않은 상태로 TX1이 동일한 row 데이터를 가져오면 수정된 데이터를 가져온다. 이때 TX2에서 롤백이 발생한다면, TX1에서 가져온 수정된 데이터는 잘못된 데이터가 된다. 

반복되지 않은 읽기(Unrepeatable Read)
: A non-repeatable read occurs, when during the course of a transaction, a row is retrieved twice and the values within the row differ between reads. 
반복되지 않는 읽기는 한 트랜잭션의 과정 중에서, 한 데이터가 두번 읽기가 되고 읽기 작업간에 데이터 값이 달라질때 발생한다. 이는 read-write conflict 라고도 한다.

$$\begin{bmatrix}
T1 & T2 \\
R(A) & \\
&R(A) \\
&W(A) \\
&Commit \\
R(A) &\\
W(A)&\\
Commit&\\
\end{bmatrix}$$

T1 has read the original value of A, and is waiting for T2 to finish. T2 also reads the original value of A, overwrites A, and commits.

However, when T1 r row 복구가 두번이 되고 데이터 row 안에 값들이 읽는 과정에서 달라지는 상황에 발생한다. 
반복되지 않는 읽기 현상은 아마 락 기반 동시성 제어 상황에서 발생할지도 모른다. 읽기 락이 얻어지지 않은 상태에서 select를 실행하거나 또는 복수의 row에 영향을 주는 얻어진 락이 SELECT 동작이 수행되자마자 풀려버린 경우에
반복되지 않는 읽기는 한 트랜잭션이 커밋 컨플릭에 노출되면 반드시 롤백하라는 요구사항이 있다면 이를 편하게 해줄수도 있다.

예를 들어 TX1에서 과거 한 row의 데이터 

Transaction 2 commits successfully, which meadns from A, it discoverthat its changes two different versions of A, anthe row with id T1 wshould be forced to  abort, because T1 would not know what to do.


팬텀 읽기(Phantom Read)
: A  phantom read  occurs when, in the course of a transaction, new rows are added or removed by another transaction to the records being read. The phantom reads anomaly is a special case of **Non-repeatable reads** when Transaction 1 repeats a ranged  _SELECT ... WHERE_  query and, between both operations, Transaction 2 creates Insert new rows (in the target table) which fulfil that  WHERE  clause.
 
$$\begin{bmatrix}
T1 & T2 \\
Select(T) & \\
&Insert(T) \text{ or } Delete(T) \\
Select(T) &\\
\end{bmatrix}$$

T1이 Select 쿼리로 테이블 T에서 가져온 결과가 있는데, T2가 같은 테이블에 Insert 또는 Delete 쿼리를 한다면, 두 번재 Select 쿼리는 결과가 달라질 수 있다. 




> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTg0MTk5NzkwOCwtNzA1MTQwNzA2XX0=
-->