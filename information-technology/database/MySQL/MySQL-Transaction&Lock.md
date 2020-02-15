# Overview

이 장에서는 MySQL의 동시성에 영향을 미치는 잠금(Lock)과 트랜잭션, 그리고 트랜잭션의 격리 수준(Isolation Level)을 공부한다.

Lock과 트랜잭션은 서로 비슷해 보이지만 사실 **잠금은 동시성을 제어하기 위한 기능**이고 **트랜잭션은 정합성을 보장하기 위한 기능**이다. 잠금은 여러 커넥션에서 동시에 동일한 자원(레코드나 테이블)을 요청할 경우 순서대로 한 시점에는 하나의 커넥션에서만 변경할 수 있도록 한다. 
격리수준은 하나의 트랜잭션 이나 여러 트랜잭션 간의 작업 내용을 어떻게 공유하고 차단할 것인지를 결정한다.

## MySQL Transaction

트랜잭션은 작업의 완전성을 보장한다고 말한다. 이 말은 작업의 일부만 적용되는 현상(partial update)가 없다는 말이다. 

MySQL에서 트랜잭션을 지원하는 스토리지 엔진은 대표적으로 InnoDB다. MyISAM은 지원하지 않는다.

트랜잭션은 DBMS 커넥션과 동일하게 꼭 필요한 최소의 코드에만 적용하는게 좋다. 이는 프로그램 코드에서 트랜잭션의 범위를 최소화하라는 의미다. 

## MySQL 잠금(Lock)

MySQL에서 사용되는 잠금은 크게 **MySQL엔진 레벨**과 **스토리지 엔진 레벨**로 나눌 수 있다. MySQL 엔진 레벨의 잠금은 모든 스토리지 엔진에 영향을 미치지만 스토리지 엔진 레벨의 잠금은 스토리지 간 상호 영향을 미치지 않는다. 

MySQL 엔진에서는 테이블 데이터 동기화를 위한 테이블 락 말고도 사용자의 필요에 맞게 유저 락과 테이블 명에 대한 잠금을 위한 네임 락이라는 것을 제공한다. 

### Global Rock(글로벌 락)

글로벌 락은 "FLUSH TABLES WITH READ LOCK"이라는 명령으로만 획들 할 수 있으며, MySQL에서 제공하는 잠금 가운데 가장 범위가 크다. 한 세션에서 글로벌 락을 얻으면 다른 세션에서는 SELECT를 제외한 대부분의 DDL 문장이나 DML문장을 실행할 경우, 락이 풀릴때 까지 대기로 남는다.

글로벌 락의 영향은 MySQL 전체 서버이며, 테이블이나 데이터베이스가 다르더라도 동일하게 영향을 미친다. 여러 데이터 베이스에 존재하는 테이블에 대해서 mysqldump로 일괄된 백업을 받아야 할때 글로벌 락을 사용한다.

### Table Rock(테이블 락)

개별 테이블 단위로 설정되는 잠금이다. 
명시적이나 또는 묵시적으로 특정 테이블의 락을 얻을 수 있다. 
명시적 방법은 아래와 같다. 명시적 테이블 락도 특별한 상황이 아니면 거의 사용할 필요가 없다. 
```
LOCK TABLES table_name [ READ | WRITE ]
UNLOCK TABLES
```
묵시적인 테이블 락은 테이블에 **데이터를 변경하는 쿼리를 실행**하면 발생한다. MySQL 서버가 데이터가 변경되는 테이블에 잠금을 설정하고 데이터 변경 후, 해제를 한다. 즉 묵시적 테이블 락은 자동적으로 얻고 해제 된다. InnoDB에서는 레코드 기반의 잠금을 제공하기 때문에 데이터 변경 쿼리로 테이블 락이 발생하지는 않는다. 더 정확히는 InnoDB 테이블에도 테이블 락이 설정되지만, 데부분의 데이터 변경 쿼리(DML은 무시되고 스키마를 변경하는 쿼리(DDL)에만 영향을 미친다.

### 유저 락(USER-LEVEL LOCK)

유저락(USER-LEVEL LOCK)은 잠금 대상이 테이블이나 레코드와 같은 데이터 베이스 컴포넌트가 아니다. 유저락(USER-LEVEL LOCK)은 단순히 사용자가 지정한 문자열(String)로 락을 획득하고 반납한다. 즉 String으로 주어진 특정 이름으로 락을 얻고 해제한다. 

한 세션이 락을 가지고 있으면, 다른 세션은 동일한 이름의 락을 걸지 못한다.
GET_LOCK 함수를 이용해 임의로 잠금을 설정할 수 있다. 유저락은 동기화 문제를 해결하는데 사용될 수 있다. 

### 네임 락(Name Lock)

데이터 베이스 객체(테이블이나 뷰)의 이름을 변경하는 경우 획득하는 잠금.
네임 락은 명시적으로 획득하거나 해제하는 게 아니다.
```
RENAME TABLE tab_a TO tab_b
```
위와 같이 테이블의 이름을 변경하는 경우 자동으로 획득하는 잠금이다.


## InnoDB 잠금(스토리지 엔진 레벨)

비관적 잠금(Pessimistic locking)
: 현재 트랜잭션에서 변경하고자 하는 레코드에 대해 잠금을 획득하고 작업을 처리하는 방식. 현재 변경하고자 하는 레코드를 다른 트랜잭션에서도 변경할 수 있을 거라는 비관적인 가정을 한다는 의미다. 일반적으로 높은 동시성 처리에서는 비관적 잠금이 유리하다고 하며 InnoDB는 비관적 잠금 방식을 쓴다.

낙관적 잠금(Optimistic locking)
: 기본적으로 각 트랜잭션이 같은 레코드를 변경할 가능성이 희박할것이라고 낙관적으로 생각한다. 그래서 변경 작업을 수행하고 마지막에 잠금 충돌이 있었는지 확인해보고 있으면 Rollback 처리를 한다. 

###  레코드 락(Record lock, Record only lock)
레코드 자체만 잠그는 락 InnoDB에서는 레코드 자체가 아니라 인덱스를 잠. 만약 인덱스가 하나도 없는 테이블이라도 자동 생성된 클러스터 인덱스를 이용해 잠금을 수행한다.

###다.

### 넥스트 키 락(Next key lock)
*레코드 락과 갭락을 합쳐 놓은 형태의 잠금이다.넥스트 키락은 바이너리 로그에 기록되는 쿼리가 슬레이브에서 실행될때 마스터에서 만들어낸 결과와 동일한 결과를 만들도록 보장하는 것이 목적이이다.

![enter image description here](https://letmecompile.s3.amazonaws.com/wp/wp-content/uploads/2018/06/next_key_lock.png)

 자동증가 락(Auto increment lock)
자동 증가하는 숫자 값을 추출하기 위해 AUTO_INCREMENT라는 컬럼 속성이 존재한다. AUTO_INCREMENT컬럼이 사용된 테이블에 동시에 여러 INSERT가 되는 경우, 저장되는 각 레코드는 중복되지 않고 저장된 순서대로 증가한 일련번호를 가져야한다. 이를 위해 InnoDB 스토리지 엔진에서는 내부적으로 AUTO_INCREMENT락이라고 하는 테이블 수준의 잠금을 사용한다.

**AUTO_INCREMENT 락은 INSERT와 REPLACE 쿼리 문장과 같이 새로운 레코드를 저장하는 쿼리에서만 필요**하며, **UPDATE나 DELETE 쿼리에서는 걸리지 않는다.** 

다른 잠금과는 달리, 트랜잭션 관계없이 INSERT나 REPLACE 문장에서 AUTO_INCREMENT 값을 가져오는 순간만 AUTO_INCREMENT 락이 걸렸다가 즉시 해제된다. 

AUTO_INCREMENT 락은 태이블에서 하나만 존재하기 때문에, 두개의 쿼리가 실행되는 경우, 하나의 쿼리가 락을 걸게 되면 나머지 쿼리는 락 해제를 기다려야 한다. 

ATUOㅆ_INCREMENT 락은 명시적으로 획득하고 해제하는 방법은 없다. 하지만 AUTO_INCREMENT 락은 아주 짧은 시간만 걸렸다가 해제가 되기 대문에 보통 큰 문제가 되지 않는다.

### InnoDB 인덱스와 잠금

InnoDb의 잠금과 인덱스는 상당히 중요한 연관관계가 있다. **InnoDB의 잠금은 레코드를 잠그는 것이 아니라 인덱스를 잠그는 방식이다.**
즉, 변경해야할 레코드를 찾기 위해 검색한 인덱스의 레코드를 모두 잠가야 한다. 

```
SELECT * FROM employees WHERE first_name = 'Georgi'; //253건
SELECT * FROM employees WHERE first_name = 'Georgi' And last_name = 'Klassen'; 1건
...
UPDATE employees SET 
WHERE first_name = 'Gorgi' AND last_name= 'Klassen'
```
UPDATE 문장이 실행되면 1건의 레코드가 업데이트될 것이다. 하지만 이 1건의 업데이트를 위해 몇개의 레코드에 락을 걸어야 할까?...

위 update 쿼리에서 인덱스를 이용할 수 있는 것은 first_name이다. 따라서 253건의 레코드가 모두 잠긴다. 이 예제에서는 253건만 잠그지만 UPDATE 문장을 위해 적절히 인덱스가 준비되어 있지 않다면 각 클라이언트간의 동시성이 상당히 떨어져 한 세션에서 UPDATE 작업을 하고 있는 중에는 다른 클라이언트는 그 테이블을 업데이트하지 못하고 기다려야 하는 상황이 발생할 것이다. 

만약 테이블에 인덱스가 하나도 없다면, 테이블을 풀 스캔하면서 UPDATE 작업을 하는데 이 과정에서 모든 레코드를 잠그게 된다. 따라서 MySQL의 InnoDB에서는 인덱스 설계가 너무나 중요하다. 

위에 불필요한 레코드 잠금 현상은 InnoDB의 넥스트 키 락 때문에 발생하는 것이다. 하지만 InnoDB에서 네스트 키락을 필요하게 만드는 주 원인은 바로 복제를 위한 바이너리 로그 대문이다. 레코드 기반의 바이너리 로그를 사용하거나 바이너리 로그를 사용하지 않는 경우에는 바이너리 로그를 사용하지 않는 경우에는 InnoDB의 갭 락이나 넥스트 키 락의 사용을 대폭 줄일 수 있다. InnoDB의 갭락이나 넥스트 키락을 줄일 수 있다는 것은 사용자의 쿼리 요청을 동시에 더 많이 처리할 수 있다는 것을 말한다. 

다음 조합으로 MySQL 서버가 기동하는 경우에는 InnoDB에서 사용되는 대부분의 갭락이나 넥스트 키락을 제거할 수 있다.

|버전|설정의 조합  |
|--|--|
|MySQL 5.0| innodb_locks_unsafe_for_binglog=1 트랜잭션 격리 수준을 READ-COMMITED 설정 |
|MySQL 5.1 이상| 바이너리 로그를 비활성화, 트랜잭션 격리 수준을 READ-COMMITED 설정; 또는 레코드 기반의 바이너리 로그 사용 innodb_locks_unsafe_for_binglog=1 |

### 레코드 수준의 잠금 확인 및 해제

InnoDB 스토리지 엔진을 사용하는 **테이블의 레코드 수준 잠근은 테이블 수준 잠금보다는 더 복잡하다.** 테이블 잠금에서는 잠금의 대상이 테이블이므로 단위와 크기가 커서 문제의 원인이 쉽게 발견되고 해결도 쉽다. 그에 반해 레코드 수준 잠금은 레코드에 걸리므로 **그 레코드가 자주 사용되지 않는 다면 오랜 시간 동안 잠겨진 상태로 남아 있어도 잘 발견되지 않는다.**

MySQL 5.0이하 버전에서는 **레코드 잠금에 대한 메타정보(딕셔너리 테이블)**을 제공하지 않기 때문에 잠금 확인이 어렵다. 하지만 5.1 InnoDB 플러그인 버전부터는 레코드 잠금과 대기에 대한 조회가 가능하므로 쿼리하나만 실행해보면 잠금과 잠금 대기를 바로 확인할 수 있다.

```
SHOW PROCESSLIST;
SHOW ENGINE INNODB STATUS;
```



SHOW ENGINE INNODB STATUS 명령어를 실행 
```
--- TRANSACTION 0 1770, not started, OS thread id 5472
MySQL thread id 5, query id 225 localhost 127.0.0.1 root
show engine innodb status
--- TRANSACTION 0 1770, ACTIVE 1 sec, OS thread id 5148 starting index read
mysql tables in use 1, locked 1
LOCK WAIT 2 lock struct(s), heap size 320
MySQL thread id 3, query id 224 localhost 127.0.0.1 root Updating
UPDATE ...
WHERE ....
...  생략
```
---TRANSACTION으로 시작하는 줄 하나가 커넥션에 대한 정보를 의미한다. 바로 뒤의 숫자 2개는 트랜잭션 번호를 의미하고, 그 뒤의 ACTIVE ... sec, not started는 트랜잭션 상태를 표시한다. 그리고 바로 다음 MySQL thread id <숫자> 줄이 있는데 숫자 값이 트랜잭션을 실행하고 있는 프로세스 아이디(커넥션 번호)이다.

중요한 것은 레코드를 오랫동안 잠그고 이














<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc3Njc5NzU0MCwtMTUzMzQ4Nzk2NywtMT
UxMTM3MTE0MSwyMTE1MzAxMTc0LC0xMzk1ODU2MDA3LC0yNjAy
OTE1OSwtMjA4NTYwNzIwNCwtMTU1MjYwNTE4MiwtMTYzNTU1ND
MzMSwxODgyMjUxODAzLDg4NzMyNjc1LDE1NTg5NzE1OTgsLTE4
ODcyMTc3OTMsMTMzNzY5MjA5MSwtODk2MTgzNDkyLDE2NjQzOT
Q4NywtMTI2OTMwOTE5NSw4NTIwNjY1NDcsMjA4ODY3MjM4Nywt
MTQzNDExODUyM119
-->