# Overview

이 장에서는 MySQL의 동시성에 영향을 미치는 잠금(Lock)과 트랜잭션, 그리고 트랜잭션의 격리 수준(Isolation Level)을 공부한다.

# MySQL Transaction

작은 작업 단위를 묶어 이 작업들의 원자성을 보장하는 것이 트랜잭션이다. 이 말은 작은 작업 일부만 적용되는 현상(partial update)가 없다는 말이다. 

MySQL에서 트랜잭션을 지원하는 스토리지 엔진은 대표적으로 InnoDB다. MyISAM은 지원하지 않는다.

트랜잭션은 꼭 필요한 영역에만 최소로 적용하는게 좋다. 프로그램 코드상에서 트랜잭션의 범위를 최소화하라는 의미다. 

# MySQL Isolation Level

트랜잭션의 격리 수준이란 여러 트랜잭션이 동시에 처리될 때, **특정 트랜잭션이 다른 트랜잭션에서 변경하거나 조회하는 데이터를 볼 수 있도록 허용할지 말지를 결정**하는 것이다. 

격리 수준에는 `READ UNCOMMITED, READ COMMITED, REPEATABLE READ, SERIALIZABLE`가 있다. 언급한 순서대로 트랜잭션간의 고립(격리)정도가 높아지고, 반면에  동시성은 떨어지는 것이 일반적이다. 격리 수준이 높아질 수록 MySQL 서버의 처리 성능이 큰폭으로 떨어질 것으로 걱정하는 사용자가 많은데, 사실 SERIALIZABLE 격리 수준이 아니라면 큰 차이는 나지 않는다.

READ_UNCOMMITED는 일반적인 DB에서 거의 사용하지 않고, 마찬가지로 SERIALIZABLE도 동시성이 중요한 DB외에서는 거의 사용되지 않는다. 

### READ UNCOMMITED

이 격리 수준에서는 **한 트랜잭션의 변경 내용을 COMMIT이나 ROLLBACK 여부와 상관없이 다른 모든 트랜잭션에서 공유한다.** 어떤 트랜잭션에서 처리한 작업이 확정(Commit, Rollback)되지 않았는데도 다른 트랜잭션에서 볼 수 있는 격리 수준이 바로 READ_UNCOMMITED이다. 간단하게는 그저 현재 데이터를 그대로 사용한다는 의미이다.
READ_UNCOMMITED는 RDBMS 표준에서는 트랜잭션 격리 수준으로 인정하지 않을 정도로 데이터 정합성에 문제가 많다. (Dirty Read, UNREPEATBLE READ, PHANTOM READ 모두가 발생) 따라서 MySQL을 사용한다면 READ_COMMITED 이상의 격리수준을 사용하자. 

#### Dirty Read

$$\begin{bmatrix}
T1(id = 6) & T2(id = 9)\\
select(A) & \\
&update(A \rightarrow A') \\
select(A') & \\
&commit \\
\end{bmatrix}$$

T2 트랜잭션이 존재하고 T1은 데이터 A를 읽는 작업을 수행하고 T2는 A를 A'으로 수정하는 작업을 한다. T2의 update 작업으로 데이터가 변경되었고 commit이 되기 전에 T1이 select 작업을 하면 변경된 데이터 A'을 가져오게 된다. 즉 commit이 되지 않은 단순히 현재 변경된 데이터를 공유하다보니 데이터의 정합성이 깨지게 된다. 이런 현상을 Dirty Read 현상이라 한다. 

### READ COMMITED

READ_COMMITED는 한 트랜잭션이 데이터를 조회할때, 다른 트랜잭션이 그 데이터를 변경하고 있다면 **변경 작업 시작 전 백업(원본) 데이터를 조회한다.** 그 어떤 트랜잭션이 데이터를 변경하였더라도 Commit 되기 전에는 변경 전의 데이터를 조회하고 Commit 후에는 변경된 데이터를 조회한다.

매커니즘을 설명하자면, 하나의 트랜잭션이 데이터를 변경하면 UNDO 영역에 변경전 데이터를 백업한다. 이때 다른 트랜잭션에서 해당 데이터를 조회하면 UNDO 영역에 백업된 전 데이터를 조회하게 된다. 그리고 Commit 후에는 변경된 데이터를 조회하게 된다. 

READ_COMMITED 격리수준에서는 REPEATBLE_READ 정합성이 깨지는 문제가 있다. REPEATBLE READ  **하나의 트랜잭션 내에서 동일한 SELECT 쿼리를 수행했을때 그 결과가 항상 같아야 하는 것**을 말한다. REPEATBLE READ을 지키지 못하는 경우, (UNREPEATABLE_READ라 한다.

#### UN) REPEATBLE READREPEATBLE READ은정합성이란 **하나의 트랜잭션 내에서는 동일한 SELECT 쿼리를 수행했을때 그 결과가 항상 같지 않는 경우**를 말한다. 즉 UNREPEATBLE READ에서는 상황에 따라 동일한 SELECT 쿼리를 수행했을때 결과가 같지 않을 수 있으므로 주의해야 한다. 

$$\begin{bmatrix}
T1(id = 6) & T2(id = 9)\\
select(A) & \\
&update(A \rightarrow A') \\
&commit \\
select(A') & \\
&update(A' \rightarrow A'') \\
&commit \\
select(A'') & \\
&...\\
...\\
\end{bmatrix}$$

T2의 update 작업으로 데이터가 변경되었고 commit마저 되었다고 하자. 현재 격리 수준이 READ_COMMITED 격리 수준 이하라면 T1에서 select 작업을 할때마다 commit이 된 변경 데이터를 가져오게 된다. 

UNREPEATBLE READ 부정합 현상은 일반 웹 서비스에서는 크게 문제되지 않지만, 하나의 트랜잭션에서 동일한 데이터를 여러 번 읽고 변경하는 작업이 금전적인 처리와 연결되면 문제가 될 수 있다. 예를 들어, 한 트랜잭션(T2)에서 입금과 출금 처리가 계속 진행되고 있을때, 다른 트랜잭션(T1)에서 오늘 입금된 금액 총합을 조회한다고 해보자. SELECT 쿼리는 실행될때마다 결과가 변경될 것이다. 

### REPEATABLE READ

이 격리 수준은 REPEATBLE_READ의 정합성(하나의 트랜잭션 내에서는 같은 SELECT 쿼리를 수행 시 항상 같은 결과가 나와야함; 반복 읽기 가능)이 보장된다. REPEATBLE_READ는 MySQL InnoDB 스토리지에서 기본적으로 사용하는 격리 수준이다. 

매커니즘을 설명하자면, UNDO 영역에 백업된 이전 데이터를 이용해 동일 트랜잭션 내에서 동일한 결과를 보여주도록 보장하는데, 사실 READ_COMMITED 보장 매커니즘과 유사하게도 UNDO 영역의 과거 commit 커밋전 데이터를 보여준다. 차이점은 **UNDO영역에 백업된 레코드의 여러 버전 중 몇 번째 이전까지 찾아 들어가는지가 다르다.**

모든 InnoDB의 트랜잭션은 고유한 트랜잭션 번호를 가지며, UNDO 영역에 **백업된 모든 레코드에는 변경을 수행한 트랜잭션 번호가 포함되어 있다.** 그리고 UNDO 영역에 백업 데이터는 스토리지 엔진이 어느 시점에 불필요하다고 판단되면 주기적으로 삭제한다. 

$$\begin{bmatrix}
T1(id = 6) & T2(id = 9) &  T3(id = 12) \\
insert(A) & \\
&select(A) & \\
&&update(A \rightarrow A') \\
&&commit \\
&select(A) &\\
\end{bmatrix}$$

세 번째 트랜잭션(id=12)가 데이터 변경을 하면 UNDO 영역에 아래 레코드가 남는다. 즉 데이터 변경 전에는 첫 번째 트랜잭션(6)에서 A라는 값을 삽입했다는 기록이 남는다. 두 번째 트랜잭션(9)는 세번째 트랜잭션이 변경을 실행하기 전과 UNDO 데이터 보기 때문에. 

|TRX-ID | prime | cols ...|
|--|--|--|
| 6 | A | ... |

이제 UNDO 영역에 백업위 데이터는 언제 제거되는지 생각해보도록 하자. **실행 중인 트랜잭션 가운데 가장 오래된 트랜잭션 번호보다 작은(더 오래된) 트랜잭션 번호를 가지는 UNDO 영역의 데이터는 삭제할 수 없다.** 그렇다고 가장 오래된 트랜잭션 번호 이전의 트랜잭션에 의해 변경된 모든 UNDO 데이터가 필요한 것은 아니다. 더 정확하게는 **특정 트랜잭션 번호 구간 내에서 ( 후에 한번씩 조회를 했지만 UNDO 영역의 데이터는를 보전되어야 한다는 것이다.** 

사실 두 번째 트랜잭션(id=9)이 SELECT 쿼리를기 때문에 항상 A 로우를 조회 실행 할때 부터 **실행되는 모든 SELECT 쿼리는 자신의 트랜잭션 번호(9) 보다 작은 트랜잭션 번호에서 변경한 것만을 보게 된다.** 

가끔 트랜잭션 내에서 실행되는 SELECT 문장과 트랜잭션 없이 실행되는 SELECT 문장의 차이를 혼동하는 경우가 있다. READ_COMMITED 격리 수준에서는 트랜잭션 내에서 실행되는 SELECT 문장과 트랜잭션 없이 외부에서 실행되는 SELECT 문장의 차이가 거의 없다. 
REPEATABLE READ에선 트랜잭션 내에서 SELECT 문장만 동일한 SELECT 쿼리를 수행했을때 언제나 동일한 결과가 나온다. 트랜잭션 없이 실행되는 SELECT 문장은 언제나 동일한 결과를 보지 못한다.

#### PHANTOM_READ 

아쉽게도 REPLEATBLE_READ에서도 부정합이 발생할 수 있는데 바로 PHANTOM_READ 부정합이다. 아래 흐름을 보도록 하자.
$$\begin{bmatrix}
T1(id = 6) & T2(id = 9)\\
select(A)&\\
&insert(A)\\
&commit(A)\\
select(A)&\\
\end{bmatrix}$$

T2 트랜잭션이 UPDATE가 아닌 INSERT를 했다는 점에 주목하자. UPDATE와는 다르게 INSERT는 UNDO 영역에 저장할 과거 데이터가 없다. 그러니 T2 트랜잭션 INSERT 후 커밋하기 전과 후의 T1의 SELECT 조회 결과가 달라진다.  이렇게 **다른 트랜잭션에서 수행한 삽입 작업에 의해 레코드가 안보였다가 보이는 현상을 PHANTOM_READ라고 한다**. 

### SERIALIZABLE

SERIALIZABLE은 가장 단순한 격리수준이지만 가장 엄격한 격리 수준이다. 그만큼 성능은 느리다. 트랜잭션이 격리 수준이라면 읽기 작업도 읽기 잠금을 획득해야만 하며, 동시에 다른 트랜잭션은 그러한 레코드를 변경할 수 없게 된다. 즉, **한 트랜잭션에서 읽고 쓰는 레코드를 다른 트랜잭션에서 절대 접근할 수 없는 것이다.** 

MySQL InnoDB 스토리지 엔진에서는 REPETABLE_READ 격리 수준에서도 PHANTOM_READ 현상이 발생하지 않기 때문에 굳이 SERIALIZABLE 수준을 사용할 필요는 없다. 

### In SpringFramework

스프링 프레임 워크에서는 데이터베이스의 기본 설정(DEFAULT)과 4개의 트랜잭션 격리 수준을 이용할수 있다. 스프링 프레임워크에서 지원하는 격리 수준은 아래와 같다. 다만 지원하는 모든 격리 수준이 실제로 사용할 수 있는지는 사용하는 데이터베이스를 어떻게 구현했느냐에 따라 달라질 수 있다. MySQL은 REPEATABLE_READ, Oracle DB는 READ_COMMITED 

| 격리수준| 설명 | Dirty Read, Unrepeatable Read, Phantom Read |
|--|--|--|
| DEFAULT  | 사용하는 데이터베이스의 기본 격리수준을 사용  |
| READ_UNCOMMITTED| 더티리드(Dirty Read), 반복되지 않은 읽기(Unrepeatable Read), ad)가 발생한다. 이 격리 수준은 커밋되지 않은 변경 데이터를 다른 트랜잭션에서 참조하는 것을 허용한다. 만약 변경 데이터가 롤백된 경우 다음 트랜잭션에서는 무효한 데이터를 조회하게 된다. | O, O, O|
| READ_COMMITTED| 더티리드(Dirty Read)는 방지하지만, 반복되지 않은 읽기(Unrepeatable Read), 팬텀 읽기(Phantom Read)는 발생한다. 이 격리 수준은 커밋되지 않은 변경 데이터를 다른 트랜잭션에서 참조하는 것을 금지한다. |X, O, O|
| REPEATABLE_READ| 더티리드, 반복되지 않은 읽기를 방지하지만 팬텀읽기는 발생한다. |X, X, O|
| SERIALIZABLE| 더티리드, 반복되지 않은 읽기, 팬텀읽기를 방지한다.|X, X, X| 


# MySQL Lock

**잠금은 동시성을 제어하기 위한 기능**이다. 잠금은 여러 커넥션에서 동시에 동일한 자원(레코드나 테이블)을 요청할 경우 순서대로 한 시점에선 하나의 커넥션에서만 변경할 수 있도록 한다. 

MySQL에서 사용하는 잠금은 크게 **MySQL엔진 레벨**과 **스토리지 엔진 레벨**로 나눌 수 있다. MySQL 엔진 레벨의 잠금은 모든 스토리지 엔진에 영향을 미치지만 스토리지 엔진 레벨의 잠금은 스토리지 간 상호에는 영향을 미치지 않는다. 

## MySQL Engine Lock(엔진 레벨)

### Global Lock(글로벌 락)

글로벌 락은 "FLUSH TABLES WITH READ LOCK"이라는 명령으로만 획들 할 수 있으며, MySQL에서 제공하는 잠금 가운데 가장 범위가 크다. 한 세션에서 글로벌 락을 얻으면 다른 세션에서는 SELECT를 제외한 대부분의 DDL 문장이나 DML문장을 실행할 경우, 락이 풀릴때 까지 대기로 남는다.

글로벌 락의 영향은 MySQL 전체 서버이며, 테이블이나 데이터베이스가 다르더라도 동일하게 영향을 미친다. 여러 데이터 베이스에 존재하는 테이블에 대해서 mysqldump로 일괄된 백업을 받아야 할때 글로벌 락을 사용한다.

### Table Lock(테이블 락)

개별 테이블 단위로 설정되는 잠금이다. 명시적이나 또는 묵시적으로 특정 테이블의 락을 얻을 수 있다. 
명시적 방법은 아래와 같다. 명시적 테이블 락도 특별한 상황이 아니면 거의 사용할 필요가 없다. 
```
LOCK TABLES table_name [ READ | WRITE ]
UNLOCK TABLES
```
묵시적인 테이블 락은 테이블에 **데이터를 변경하는 쿼리를 실행**하면 발생한다. MySQL 서버가 데이터가 변경되는 테이블에 잠금을 설정하고 데이터 변경 후, 해제를 한다. 즉 묵시적 테이블 락은 자동적으로 얻고 해제 된다. InnoDB에서는 레코드 기반의 잠금을 제공하기 때문에 데이터 변경 쿼리로 테이블 락이 발생하지는 않는다. 더 정확히는 InnoDB 테이블에도 테이블 락이 설정되지만, 데부분의 데이터 변경 쿼리(DML은 무시되고 스키마를 변경하는 쿼리(DDL)에만 영향을 미친다.

### User Rock(유저 락)

유저락은 잠금 대상이 테이블이나 레코드와 같은 데이터 베이스 컴포넌트가 아니다. 단순히 사용자가 지정한 문자열(String)로 락을 획득하고 반납한다. 즉 String으로 주어진 특정 이름으로 락을 얻고 해제한다. 

한 세션이 락을 가지고 있으면, 다른 세션은 동일한 이름의 락을 걸지 못한다.
GET_LOCK 함수를 이용해 임의로 잠금을 설정할 수 있다. 유저락은 동기화 문제를 해결하는데 사용될 수 있다. 

### Name Lock

데이터 베이스 객체(테이블이나 뷰)의 이름을 변경하는 경우 획득하는 잠금이다. 그래서 네밍 락이라 부른다.
네임 락은 명시적으로 획득하거나 해제할 수 없다. 아래와  같이 테이블의 이름을 변경하는 경우 자동으로 획득하는 잠금이다. 
```
RENAME TABLE tab_a TO tab_b
```

## InnoDB 잠금(스토리지 엔진 레벨)

비관적 잠금(Pessimistic locking)
: 현재 트랜잭션에서 변경하고자 하는 레코드에 대해 잠금을 획득하고 작업을 처리하는 방식. 현재 변경하고자 하는 레코드를 다른 트랜잭션에서도 변경할 수 있을 거라는 비관적인 가정을 한다는 의미다. 일반적으로 높은 동시성 처리에서는 비관적 잠금이 유리하다고 하며 InnoDB는 비관적 잠금 방식을 쓴다.

낙관적 잠금(Optimistic locking)
: 기본적으로 각 트랜잭션이 같은 레코드를 변경할 가능성이 희박할것이라고 낙관적으로 생각한다. 그래서 변경 작업을 수행하고 마지막에 잠금 충돌이 있었는지 확인해보고 있으면 Rollback 처리를 한다. 

###  Record nly lock(레코드 락)
레코드 자체만 잠그는 락이다. InnoDB에서는 레코드 자체가 아니라 인덱스를 잠근다. 만약 인덱스가 하나도 없는 테이블이라도 자동 생성된 클러스터 인덱스를 이용해 잠금을 수행한다.

### Gap Lock(갭 락)

갭락은 레코드와 레코드 사이 간격(Gap)을 잠그는 락이다. 레코드 자체에 대해서는 잠그지 않는다. 갭락은 순수하게 모든 트랜잭션을 대상으로 해당 Gap 간격 사이에 어떠한 변경 작업을 하지 못하도록 하는 예방용으로만 사용한다.

갭락은 Master 와 Slave의 데이터 동기화 (더 정확히는 Binary 로그의 정확한 기록을 위해서)  및 Phantom 레코드 방지를 위해 사용한다.

### 넥스트 키 락(Next Key ock)
*코드 락과 갭락을 합쳐 놓은 형태의 잠금이다. 넥스트 키락은 Binary바이너리 로그에 기록되는 로그의 데이터의 정합성을 위해 사용한다. 이 정합성은 Master에서 수정된 결과에 대한 로그 데이터와 Slave에서 수정된 결과가 일치해야 한다는 것을 말한 것이 목적이이다.

![enter image description here](https://letmecompile.s3.amazonaws.com/wp/wp-content/uploads/2018/06/next_key_lock.png)

###  자동증가 락(Auto Increment Lock)

AUTO_INCREMENT 기능하는 숫자 값을 추출하기컬럼이 사용된 테이블에선 동시에 여러 INSERT가 되는 경우, 저장되는 각 레코드는 중복되지 않고 순서대로 증가한 일련번호를 가져야한다. 이를 위해 InnoDB 스토리지 엔진에서는 내부적으로 AUTO_INCREMENT 락이라는 테이블 수준의 잠금을 제공한다.

 락은 INSERT와 REPLACE 쿼리 문장과 같이 새로운 레코드를 입력하는 쿼리에서만 사용한다. **UPDATE나 DELETE 쿼리에서는 걸리지 않는다.** 

다른 잠금과는 달리, 트랜잭션 관계없이 INSERT나 REPLACE 문장에서 AUTO_INCREMENT 값을 가져오는 순간만 AUTO_INCREMENT 락이 걸렸다가 즉시 해제된다. 

AUTO_INCREMENT 락은 명시적으로 획득하고 해제하는 방법이 없다. 그리고 태이블 단위로 하나만 존재하기 때문에, 하나의 쿼리가 락을 걸게 되면 나머지 쿼리는 락 해제를 기다려야 한다. 일반적으로 아주 짧은 시간만 걸렸다가 해제가 되기 때대문에 해당 락은 성능상 이슈가 지 않다.

### InnoDB 인덱스와 잠금

InnoDB의 잠금과 인덱스는 상당히 밀접한 관계가 있다. 왜냐하면 사실 **InnoDB의 잠금은 레코드를 잠그는 것이 아니라 인덱스를 잠그는 방식이다.** 
즉, 변경 대상해야할 레코드를 찾기 위해 사용한 모든 인덱스를 잠근다.

```
SELECT * FROM employees WHERE first_name = 'Georgi'; //253건
SELECT * FROM employees WHERE first_name = 'Georgi' And last_name = 'Klassen'; 1건
...
UPDATE employees SET 
WHERE first_name = 'Gorgi' AND last_name= 'Klassen'
```
위 updateUPDATE 문장이 실행되면 최종적으로 1건의 레코드가 수정업데이트될 것이다. update where 조건하지만 이 1건의 업데이에서 인덱스를 이용할 수 있는 컬럼은 first_name이고 first_name이 'Georgi'인 레코드가 총 253건이기 때문에 이 253건의 레코드가 모두 잠긴다. 이 예제에서는 253건만 잠그지만 update 작업UPDATE 문장을 위해 적절한히 인덱스가 없다면 최악의 경우 테이블을 풀 스캔하면서 모든 레코드를 잠그게 된다. 

준비되어 있지 않다면 각 클라이언트간의 동시성이 상당히 떨어져 한 세션에서 UPDATE 작업을 하고 있는 중에는 다른 클라이언트는 그 테이블을 업데이트하지 못하고 기다려야 하는 상황이 발생할 것이다. 

만약 테이블에 인덱스가 하나도 없다면, 테이블을 풀 스캔하면서 UPDATE 작업을 하는데 이 과정에서 모든 레코드를 잠그게 된다. 따라서 MySQL의 InnoDB에서는 인덱스 설계가 너무나 중요하다. 

위에 불필요한 레코드 잠금 현상은 InnoDB의 넥스트 키 락 때문에 발생하는 것이다. 하지만 InnoDB에서 네스트 키락을 필요하게 만드는 주 원인은 바로 복제를 위한 바이너리 로그 때대문이다. **레코드 기반의 바이너리 로그를 사용하거나 바이너리 로그를 사용하지 않는 경우에는InnoDB의 갭 락이나 넥스트 키 락의 사용을 대폭 줄일 수 있다.** 

다음 조합으로 MySQL 서버가 기동하는 경우에는 InnoDB에서 사용되는 대부분의 갭락이나 넥스트 키락을 제거할 수 있다.

|버전|설정의 조합  |
|--|--|
|MySQL 5.0 이하| innodb_locks_unsafe_for_binglog=1으로 트랜잭션 격리 수준을 READ-COMMITED 설정 |
|MySQL 5.1 이상| 바이너리 로그를 비활성화하고 트랜잭션 격리 수준을 READ-COMMITED 설정. 또는 레코드 기반의 바이너리 로그 사용 innodb_locks_unsafe_for_binglog=1 |

### 레코드 수준의 잠금 확인 및 해제

InnoDB 스토리지 엔진을 사용하는 **테이블의 레코드 수준 잠근은 테이블 수준 잠금보다는 더 복잡하다.** 테이블 잠금에서는 잠금의 대상이 테이블이므로 크기가 커서 문제의 원인이 쉽게 발견되고 해결도 쉽다. 그에 반해 레코드 수준 잠금은 레코드에 걸리므로 **그 레코드가 자주 사용되지 않는 다면 오랜 시간 동안 잠겨진 상태로 남아 있어도 잘 발견되지 않는다.**

MySQL 5.0이하 버전에서는 **레코드 잠금에 대한 메타정보(딕셔너리 테이블)** 을 제공하지 않기 때문에 잠금 확인이 어렵다. 하지만 5.1 InnoDB 플러그인 버전부터는 레코드 잠금과 대기에 대한 조회가 가능하므로 쿼리하나만 실행해보면 특정 레코드의 잠금과 잠금 대기를 바로 확인할 수 있다.

```
SHOW PROCESSLIST; // 현재 실행 중인 프로세스 리스트

SELECTma.innodb_locks; // 어떤 잠금이 존재하는지를 관리한다. 잠금이나 대기가 발생할 경우, InnoDB 스토리지 엔진에서 관련 정보를 계속 테이블로 업데이트.

SELECT
r.trx_id waiting_trx_id,
r.trx_mysql_thread_it waiting_thread,
r.trx_query waiting_query,
b.trx_id blocking_trx_id,
b.trx_mysql_thread_id blocking_thread,
b.trx_query blocking_query
FROM information_schema.innodb_lock_waits w // 어떤 잠금이 대기 상태인지 관리하는 테이블.
INNER JOIN information_schema.innodb_trx b ON b.trx_id = w.blocking_trx_id
INNER JOIN information_schema.innodb_trx r ON r.trx_id = w.requesting_trx_id;
 * FROM information_sche// 어떤 트랜잭션이 어떤 클라이언트(프로세스)에 의해 기동 중이며, 어떤 잠금을 기다리고 있는지를 관리한다.
```
--- 
5.0이하 버전에서 잠금 확인 및 해제를 확인하고 싶다면 SHOW ENGINE INNODB STATUS 쿼리명령어를 실행하면 된다.
 
```
SHOW ENGINE INNODB STATUS; //5.0 이하버전 

TRANSACTION 0 1770, not started, OS thread id 5472
MySQL thread id 5, query id 225 localhost 127.0.0.1 root
show engine innodb status
--- TRANSACTION 0 1770, ACTIVE 1 sec, OS thread id 5148 starting index read
mysql tables in use 1, locked 1
LOCK WAIT 2 lock struct(s), heap size 320
MySQL thread id 3, query id 224 localhost 127.0.0.1 root Updating
UPDATE ...
WHERE ....
...

--- TRANSACTION으로 시작하는 줄 하나가 커넥션에 대한 정보를 의미한다. 바로 뒤의 숫자 2개는 트랜잭션 번호를 의미하고, 그 뒤의 ACTIVE ... sec, not started는 트랜잭션 상태를 표시한다. 그리고 바로 다음 MySQL thread id <숫자> 줄이 있는데 숫자 값이 트랜잭션을 실행하고 있는 프로세스 아이디(커넥션 번호)이다.
```

**중요한 것은 레코드를 오랫동안 잠그고 있는 프로세스가 있는지 여부**이다. 최대한 트랜잭션이 오랫동안 실행되고 있는 줄을 찾으면 되는데 트랜잭션 상태가 ACTIVE이고, 그 뒤 시간값이 최대한 큰 값을 가진 트랜잭션을 찾으면 된다.

이 와 같은 방법으로 문제의 원인으로 예상되는 트랜잭션을 찾으면, 해당 트랜잭션의 프로세스를 KILL 명령으로 종료하자. 만약 근본적인 원인인 트랜잭션을 찾기가 어렵다면 오래 기다리고 있는 트랜잭션을 모두 종료해버리자.












<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyNjk2MDIzMjYsLTg1MDUzNTAxNiw4Nj
c1Njg3MDIsLTkwNTg2ODc2MiwtMjA0NTAyNTgyMCwxNTE4NjQ3
ODIzLDEzODU0MzY4NzMsNTY4Nzk3Nzg0LDE3MDEyMzQ0OTEsMT
UyOTI3OTc3MiwyMDA3NDU0NzUxLC03NTI0MjgzNDQsLTE0MzA2
NDkxNjIsNDMyNTA4ODU4LDE0ODM3OTc3NCwtNTc2OTgwNDg4LC
0xMzkxMDI4Mzk4LDEwMDkwNzM1ODksLTQwMDkyMTY1OSwtMTYx
Mjc4MTk3Nl19
-->