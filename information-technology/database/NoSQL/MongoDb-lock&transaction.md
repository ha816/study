# Lock

>Exclusive lock과 Shared lock

멀티 트랜잭션 환경에서 데이터베이스의  **일관성**과  **무결성**을 유지하려면  **트랜잭션의 순차적 진행을 보장할 수 있는 직렬화 장치가 필요**하다.

Exclusive lock (배타적 잠금)
: 쓰기 잠금(Write lock)이라고도 불린다. 왜냐하면 어떤 트랜잭션에서 데이터를 변경하고자 할 때, 쓰이는 잠금이라 그렇다. 배타적 잠금이 걸리면, 트랜잭션이 완료될 때까지 해당 테이블 혹은 레코드(row)를 다른 트랜잭션에서 읽거나 쓰지 못하게 된다. exclusive lock이 특정 데이터에 걸리면 shared lock을 걸 수 없고 exclusive lock도 걸수 없습니다.

Shared lock (공유 잠금)
: 배타적 잠금과는 다르게 다수의 트랜잭션간의 공유가 가능한 락이다. 쓰기가 아닌 읽기에만 국한되어 있다. 즉 리소스를 다른 사용자가 동시에 읽을 수 있게 하되 변경은 불가하게 하는 것이다. 동일한 데이터에 대해서 shared lock 동시 적용이 가능하다. 

## MongoDB 엔진의 잠금

MongoDB에서 제공하는 잠금은 크게 명시적 잠금과 묵시적 잠금으로 나누어 집니다. 또 명시적 잠금은 글로벌 잠금뿐이며 나머지 모든 잠금은 묵시적 잠금이다.  데이터베이스 그리고 컬렉션에 대한 잠금은 모두 묵시적인 잠금이며, 쿼리 실행시 자동적으로 획득됐다가 자동으로 해제되는 잠금이다. 

### 글로벌 잠금(인스턴스 잠금)

MongoDB에서 유일하게 명시적으로 사용할 수 있는 잠금은 글로벌 잠금뿐이다. MongoDB 서버 인스턴스에서 단 하나만 있는 잠금이므로, 이를 인스턴스 잠금이라고 한다.  

```
db.fsyncLock){fsync:1, lock:true}
```

기본적으로 fsync:1으로 설정하면 아직 디스크에 기록되지 못한 데이터(캐시, 메모리) 모두 디스크로 기록합니다. lock:true 옵션이 있으면 글로벌 잠금을 획득하게 되는데, 이는 쓰기 잠금이지 읽기 잠금은 아닙니다. 

### 오브젝트 잠금

MongoDB도 다른 RDBMS와 같이 계층형 오브젝트에 대한 다중 레벨 잠금 방식을 방식을 지원했습니다.

> Multiple Granularity Locking(다중 레벨 잠금방식)
> 계층형 오브젝트에 대한 동시성 처리를 위해서 다중 레벨의 잠금 방식

MongoDB에서는 S(Shared Lock), X(Exclusive Lock), 그리고 IS(Intent Shared Lock)과 IX(Intent Exclusive Lock)을 제공한다. IS와 IX는 의도를 표현하는 잠금인데 묶어서 인텐션 잠금이라도도 한다. 

IS는 컬렉션이나 문서 레벨에 Shared Lock을 걸거이라는 의도를 나타냅니다. 
IX는 마찬가지로 컬렉션이나 문서 레벨에 Exclusive Lock을 걸거이라는 의도를 나타냅니다. 

Intent Lock은 현재 쓰레드가 특정 오브젝트에 대해 Lock을 걸 의도를 가지고 있지만, 다른 변경의도를 가진 락(Intent Lock)도 허용하는 형태의 잠금입니다. 



MongoDB에서는 글로벌 잠금, 데이터베이스, 컬렉션 3개 레벨의 오브젝트에 대해서 잠금을 지원한다.

|| Intent Shared| Intent Exclusive| Shared| Exclusive |
|--|--|--|--|--|
Integer Shared| O| O |O |X|
Integer Exclusive| O| O |X|X|
Shared| O| X | O|X|
Exclusive| X | X | X|X|

위 표는 잠금들 간의 상호 사용 관계를 나타냅니다. 

```
session-1: db.orders.update({user_id:1}, {$set: {order_state:"done"}})
session-2: db.orders.update({user_id:2}, {$set: {order_state:"prepare"}})
```

session-1은 orders 컬렉션의 데이터를 변경하므로 orders 데이터베이스에 대해 IX 잠금을 필요로 합니다. 그리고 orders  컬렉션에 X잠금을 걸어야 한다. 

마찬가지로 session-2도 동일하게 orders 컬렉션의 데이터를 변경하므로 orders 데이터베이스에 대해서 IX잠금을 걸고 orders 컬렉션에 X잠금을 필요로 한다. 

표를 보면, IX잠금은 동시에 사용 또는 획득할 수 있지만, orders 컬렉션의 X잠금은 동시에 불가능하기 때문에 세션별로 처리를 할수 박에 없다. 

```
session-1: db.orders.find({user_id:1}})
session-2: db.orders.update({user_id:2}})
```

두 데이터를 조회만 하는 경우라면, orders 데이터 베이스에 대해서 IS 잠금이 필요하고 두 컬렉션에 S잠금또한 필요하다. 다행히 S잠금간에는 호완이 가능하기 때문에 두 커넥션은 동시에 수행이 된다. 


# 스토리지 엔진의 잠금

스토리지 엔진이라고 했지만, 기본적으로 WiredTiger 스토리지 엔진에 대한 설명이 메인이다. 

WirtedTiger는 다른 DBMS처럼 문서(레코드) 기반의 잠금을 사용한다. 하지만 다양한 레벨의 DB 오브젝트에 대한 잠금을 위해 다중 레벨의 잠금 방식도 같이 사용한다. (Multiple Granularity Locking) 

MongoDB의 각 DB 오브젝트는 계층 구조를 가지는데, 각 계층 구조에서 하위 오브 젝트에 대한 잠금을 획득하려면, 상위 계층의 인텐션 잠금을 먼저 획득해야 한다. 

MongoDB Instance > DB > Collection > Document

특정 문서를 변경하려면 EX락을 획득해야 하는데, 먼저 글로벌 인텐션 잠금, 데이터베이스 인텐션 잠금 그리고 컬렉션 인텐션 잠금을 가져야 한다 

특정 문서를 읽으려면 동일하게 글로벌 인텐션 잠금, 데이터베이스 인텐션 잠금 그리고 컬렉션 인텐션 잠금을 얻어야 한다. 그런데 다큐먼트를 읽기만 하는 경우는 WiredTiger 스토리지 엔진에서는 별도의 잠금을 이용하지 않는다. 

이는 MongoDB의 MVCC 때문에 가능하다. WiredTiger 스토리지 엔진에서는 문서를 변경할때 기존의 버전은 그대로 두고 새로운 버전을 추가한다. 즉 변경되는 내역을 모두 관리하는데, 따라서 읽기 명령시 현재 트랜잭션이 읽어야할 문서 버전을 찾아 읽기만 하면된다. MVCC를 이용해서 잠금 없이 문서를 읽는다고 하여 이를 잠금없는 일관된 읽기(Consistent non-locking read)라고도 한다. 

즉 실제로는 그 문서가 변경되기 전에 버전을 읽는 것이므로 별도의 잠금이 필요로 하지 않는것이다. 

## 잠금 Yield

쿼리를 실행하는 도중에 잠시 쉬었다가 쿼리 실행을 재개하는 것을 MongoDB에선 양보(Yield)라고 한다. 단순히 쿼리를 멈추고 잠깐 쉬는(sleep) 것이 아니라, 처리 중인 쿼리를 위해서 획득했던 잠금까지 모두 해제하고 일정시간 쉬게 된다.

```
db.users.find({non_indexed_field:"value"})
```

Yield를 실행하는 규칙은 아래와 같다.

* 쿼리가 지정된 건수의 문서를 읽는 경우(128건)
* 쿼리가 지정된 시간동안 수행된 경우(10ms)

기본적으로 잠금 판단을 위해선 db.currentOp() 명령어를 사용하면 알 수 있다. 

# 트랜잭션

WiredTiget 스토리지 엔진이 제공하는 트랜잭션 ACID 속성은 다음와 같은 특징이 있다. 

* 최고 레벨 격리 수준은 Snapshot(Repetable-Read)
* 트랜잭션의 커밋과 체크포인트 두 가지 형태로 영속성(Durability) 보장
* 커밋되지 않은 변경 데이터는 공유 캐시 크기보다 작아야 함

WiredTiger는 Serializable 수준의 격리 수준은 제공하지 않는다. 
 트랜잭션 로그(저널 로그 또는 리두 로그) 뿐만 아니라 체크포인트로도 영속성이 보장된다. 즉 트랜잭션 로그가 없어도 마지막 체크 포인트 시점의 데이터를 복구 할 수 있다.
트랜잭션이 커밋되기 전에는 트랜잭션 로그를 디스크로 기록하지 않는 다는 것이다. 따라서 
트랜잭션이 변경할 수 있는 데이터의 크기는 WiredTiger 스토리지 엔진이 가진 공유캐시 크기로 제한된다. MongoDB 서버는 기본적으로 다큐먼트 기반의 트랜잭션만 지원한다. 

## 쓰기 충돌(Write Conflict)

데이터를 변경하는 작업 도중에 MongoDB 서버는 쓰기 충돌이 발생할 수 있다. 

MongoDB 서버는 변경하고자 하는 문서가 이미 다른 커넥션에 의해서 잠금이 걸려 있으면 즉시 업데이트 실행을 취소한다. 그리곤 WriteConflict Exception에러을 반환 받아, 같은 업데이트 문장을 재실행한다. 이런 재처리 과정은 MongDB내에서만 작동하며 재처리 과정을 응용 프로그램에서는 모른다. 

이렇게 하나의 다큐먼트에 대해서 변경이 집중되면 순강적으로 재처리 과정이 반복 실행되면서 서버 성능이 떨어질수 있다. 

MongoDB 서버에서 WriteConflict Exception이 얼마나 발생했는지는 db.serverStatus() 명령으로 확인할 수 있다. 

>WirtedTiger의 경우
>WiredTiger  스토리지 엔진에서는 문서를 읽는 쿼리는 WriteConflict와 무관하다. 왜냐하면 MVCC를 통해 읽기 작업에 대한 락을 걸지 않기 때문이다. 

## 단일 문서 트랜잭션

MongoDB 서버는 처음부터 단일 다큐먼트의 트랜잭션만 지원하고 있다. 즉 원자 단위의 처리가 보장된다는 것을 말합니다.

>복수 문서 트랜잭션(Multi-Document Transactions)?
>다행이도 MongoDB는 복수 문서에 대한 트랜잭션을 지원한다고 합니다. ([Transactions](https://docs.mongodb.com/manual/core/transactions/))
>주의사항으로는 서로 다른 연산이 교차로 배치될 수 있습니다. (interleave; 컴퓨터 하드디스크의 성능을 높이기 위해 데이터를 서로 인접하지 않게 배열하는 방식을 말한다.)
>복수 문서 트랜잭션은 단일보다 성능이 훨씬 안좋기 때문에 스키마 디자인 자체를 잘해서 복수 문서 트랜잭션을 최대한 사용하지 않는게 좋다고 합니다.

# 격리 수준

MongoDB에는 MMAPv1에서 사용가능한 READ-COMMITED과 WiredTiget에서 사용가능한 SNAPSHOT(RPEATABLE-READ) 격리 수준이 있습니다.

>READ-COMMITED
>다른 세션의 변경 내용이 커밋되어야 다른 세션에서도 영향을 미친다. 

>SNAPSHOT
>하나의 트랜잭션에서는 반복된 읽기를 해도 항상 같은 결과가 나온다.

## MongoDB 서버의 격리 수준


WT



# Read & Write Concerns





# Concurrency Control

동시성 제어란  DBMS가 다수의 사용자 사이에서 동시에 작용하는 다중 트랜잭션의 상호간섭 작용에서 DB의 일관성과 동시성을 적절히 조절하는 것을 말합니다. 일반적으로 동시성을 허용하면 일관성이 낮아지게 됩니다.

![](https://k.kakaocdn.net/dn/YYwAw/btqAhqpozl2/IMdpfbgguBj897K4VkEwy1/img.png)

동시성 제어의 목표는 동시에 실행되는 트랜잭션 수를 최대화 하면서 입력, 수정, 삭제, 검색 시 데이터의 무결성을 유지하는데 있습니다. 

원하는 동시성 제어를 위해선 Read Concern과 Write Concern을 사용할 수 있습니다. 

## Read Concern

`readConcern` 옵션은 replica sets 그리고 replica shard set으로 부터 데이터를 읽는데 그 데이터의 LATEST, SAFE, FAST을 고려하여 가져온다. 

![](http://postfiles10.naver.net/MjAxODA1MjZfMTYx/MDAxNTI3MzQzMzYwOTIx.UWOcjWVn6qw1XhMrWubt4Kr4BL9yjzqkshcjPVxZWFog._OW_LnzY41867zdVE5h3JpB-Jox1aAtV8jllOsN9SMcg.PNG.ijoos/image.png?type=w773)

### local & available

가장 최근 데이터를 빠르게 가져오는 Read Concern 입니다. 이 쿼리로 반환된 데이터는 그 데이터가 주요 replica sets에 쓰여졌다는 것을 보장하지 않습니다. 운이 나쁘면 해당 데이터는 롤백이 발생하여 데이터가 replica sets에 존재하지 않을 수 있습니다.

샤딩된 클러스터에선, `"available"` 을 사용하면 모든 read concerns(local 포함) 중에서 가장 빠르게 데이터를 가져옵니다. 그러나 샤딩된 컬렉션을 읽는 과정에서 [orphaned documents](https://docs.mongodb.com/manual/reference/glossary/#term-orphaned-document) 문서를 읽을 수도 있습니다. 

샤딩된 컬렉션에서 orphaned document를 읽는 상황을 피하기 위해서, `local` read concern과 같은 다른 concern level을 씁시다.

> orphaned document
> 샤딩된 클러스터에서, 비정상 종료 때문에 실패하거나 불완전한 마이그레이션으로 발생하는 다른 샤드의 문서입니다. 이 문서를 제거하기 위해 `cleanupOrphaned`을 사용할 수 있습니다.

### majority

저장된 데이터를 빠르게 가져오는 Read Concern입니다. 
이 쿼리로 반환된 데이터는 그 데이터가 주요 replica sets에 최근에 쓰여졌다면, 가져온 데이터가 최신인것을 보장하지 않습니다. 

majority는 실패 이벤트에서도, 성공적으로 문서를 가져옵니다. majority-commit point시점에 인-메모리 데이터를 가져오기 때문에 최신인것은 보장하지 않지만 데이터는 온전하게 있습니다. 

majority를 사용하려면 replica sets가 반드시 [WiredTiger storage engine](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger)을 사용해야 합니다.

### linearizable

최신 데이터를 포함하여 저장된 데이터를 가져옵니다.

읽기 연산 시작 이전에 성공적으로 인정된 모든 데이터를 반영한 최시 데이터를 가져옵니다. 결과를 반환하기 전에 모든 replica set에 쓰기 작업을 전파하기 때문에 모든 read concern 중 제일 느립니다.

linearizable은 읽기 연산이 단일 문서에 국한된 필터를 썻을때만 성공적으로 read concern을 보장합니다.

## Write Concern

Write Concern이란 MongoDB가 Client의 요청으로 데이터를 기록할 때, 해당 요청에 대한 Response를 어느 시점에 주느냐에 대한 동작 방식을 지정합니다.

![](https://k.kakaocdn.net/dn/bqWzBJ/btqvCK0MrG1/kkQXAqikXxDAA9h0iKlya1/img.png)

기본적으로 MongoDB는 Client 가 보낸 데이터를 Primary에 기록하고, 이에 대한 Response를 즉시Client에게 보냅니다. (By Default)

Write 작업을 Primary에서 먼저 수행하고, 이후 Secondaries가 같은 Write 작업을 진행합니다. 이때 **Primary와 Secondary 간 동기화 되는데 시간차**가 있습니다.
즉 클라이언트가 Response를 받은 시점과 Primary 에서 Secondary로 Sync가 되는 시점에는 데이터 일관성이 보장되지 않는 위험 구간이 존재합니다.

![](https://k.kakaocdn.net/dn/48NyH/btqvBas04QL/0vTCbpKEAIpFnarAI2FjqK/img.png)

만약 이 사이에 Primary에 장애가 발생 했다고 가정해 보면, 아직 최신 데이터를 Sync 하지 못한 Secondary 멤버가 Primary 로 승격되게 되고 Client 는 이를 알아차리지 못한채 이미 작업이 완료된 Response 를 받았기 때문에 Client가 알고 있는 데이터와 DB 의 데이터가 unmatch 되는 상황이 발생합니다.

이러한 문제를 해결하기 위해 **Client 쪽에 보내는 response 시점을 Primary 와 Secondary 가 동기화 된 이후로 설정이 가능하며 이것이 바로 Write concern 설정의 핵심이다.**

![](https://k.kakaocdn.net/dn/daSduH/btqvErfiQPr/YnveydIHjt1YdgInTK1VxK/img.png)

Write Concern 을 설정하게 되면, **Primary 가 데이터 쓰기를 처리한 이후 바로 Client 에게 response 를 보내는 것이 아니라 Secondary 쪽으로 데이터를 동기화 작업을 완료한 이후에 Client 에게 response 를 보내게 된다.** 이렇게 되면 Client 와 Primary, Secondary 간에 데이터 일관성을 유지할 수 있게 된다.

Write Concern 을 지정하는데는 크게 w / j / wtimeout options를 설정 할 수 있습니다.

w option
: ReplicaSet 에 속한 멤버중 지정된 수만큼의 멤버에게 데이터 쓰기가 완료되었는지 확인합니다.
만약 Primary/Secondary 가 총 3대로 구성된 ReplicaSet 일 경우, w = 3 으로 설정시 3대의 멤버에 데이터 쓰기가 완료 된 것을 확인하고 response를 반환합니다.
w = 1 이 Default 설정이며, 이런 경우 Primary 에만 기록 완료되면 response 합니다. 
w = majority 로 설정할 경우, 멤버의 과반수 이상을 자동으로 설정하게 된다.

 j option
 : 데이터 쓰기 작업이 디스크상의 journal 에 기록된 후 완료로 판단하는 옵션입니다. 
 만약, Replicaset 의 멤버가 3대인 경우 w = majority, j = true 로 설정시 Primary 1 대 Secondary 1대 총 2대의 멤버에서 디스크의 journal 까지 기록이 완료 된 후 response 하게 됩니다.

wtimeout option
: Primary 에서 Secondary 로 데이터 동기화시 timeout 값을 설정하는 옵션이다. 
만약 wtimeout 의 limit 을 넘어가게 되면 실제로 데이터가 Primary에 기록되었다고 해도 error 를 리턴하게 됩니다.
설정 단위는 milisecond 이다.

## Isolation

### Read Uncommitted

클라이언트는 쓰기 작업의 결과를 read concern에 따라 볼 수 있습니다.

* local 또는 available을 쓰는 경우, 쓰기 작업이 완료되지 않았어도 write concern과는 관계 없이 결과를 받을 수 있습니다. 이 결과는 replica set failovers 중인 잘못된 데이터 일수도 있습니다. 

한 트랜잭션이 커밋을 하면, 트랜잭션에서 모든 변경점을
[multi-document transaction](https://docs.mongodb.com/manual/core/transactions/)의 연산자들로 볼 수 있습니다. 즉 한 트랜잭션은 다른 것을 롤백하는 중에는 변경점을 커밋하지 않을 것 입니다. 

한 트랜잭션이 커밋하기 전에, 트랜잭션 내에서 데이터 변경점은 외부 트랜잭션에선 볼 수 없습니다.

그러나 한 트랜잭션이 다수의 샤드에 쓰기 작업을 할땐, 샤드들을 가로질러 커밋된 트랜잭션의 결과를 모든 다른 읽기 연산이 기다릴 필요가 없습니다. 

Read uncommitted 설정은 기본 isolation level으로 샤딩된 클러스터, replica sets, 그리고 standalone mongd에 적용되어 있습니다. 


### Read Uncommitted And Single Document Atomicity

단일 문서에 대해선 쓰기 작업은 원자성을 보장합니다. 이 말은 한 쓰기 작업이 문서의 다수 필드를 수정하고 있다면, 읽기 연산은 아주 일부 필드가 수정된 문서는 보지 못합니다. 
비록 클라이언트가 일부만 수정된 문서를 볼순 없지만, read uncommitted는 변화 결과
가 반영되기 전에 문서를 보아 읽기 연산이 가능합니다. 

만약 standalone 	구성을 하고 있다면, 한 문서에 대한 읽기와 쓰기 작업은 serializable합니다. replica set을 구성하고 있다면, 한 문서에 대한 읽기와 쓰기 작업들은 rollback이 없는 경우만 serializable이 됩니다. 

### Read Uncommitted And Multiple Document Write

한 쓰기 작업이 많은 문서를 수정할때, 각 문서의 수정은 원자성이 보장됩니다. 그러나 전체 연산은 원자성이 보장되지 않습니다.

다수-문서 쓰기 작업을 수행할때, 다른 연산은 교차 수행될지도 모릅니다. 

다수의 문서에 대해서 읽기와 쓰기 원자성 보장이 필요할때, MongoDB는 다수 문서 트랜잭션을 지원합니다. 

> 주의사항
> 대부분의 multi-document transaction은 굉장히 나쁜 성능을 보이고, 좋은 스키마 디자인을 대체할 수는 없습니다. 좋은 스키마 또는 모델링을 통해서 최대한 multi-document transaction 사용을 줄이도록 합시다. 





# Consistency

전통적인 DB 시스템에서 일관성이란 휘발성 저장장치와 비휘발성 저장장치간의 데이터의 일관성 유지를 말합니다. Strict Consistency는 가장 기본사항으로 기존 RDBMS에서 사용되어 왔습니다. 하지만 분산화된 상태에서 strict consistency를 만족하는 것은 매우 어려운 일이며, 또한 DB의 속도를 느리게 만듭니다. 이런 문제를 개선하기 위해 나온것이 Causal Consistency입니다.

## Causal Consistency(Sequential Consistency)

Causal consistency는 strict consistency보다 한단계 완화된 consistency입니다. 
Causal consistency를 사용하기 위해선 아래 두 가지 물리적 요구사항을 지켜야 합니다.

-   Ordering 유지
-   Atomic read/write operation

공유 데이터를 접근할 때, single operation queue가 있어 그 큐에는 write operation과 read operation들이 쌓여있다. 그리고 이 operation들은 in-order로 처리되야 한다. 그리고 operation이 수행되고 있으면 single operation queue에 들어있는 다른 operation들은 대기해야 한다.

이는 strict consistency보다는 빠른 성능을 보인다. (Network 속도에 클럭이 병목될 정도는 아니다.)  **하지만 operation들이 sequential하게 수행되어야 하므로 성능이 만족할정도로 나오지 않는다.**  (previous operation이 완료 될 때까지 기다려야 하므로 느리다.)

causal consistency는 4가지 session guarantees로 재정의 될 수 있습니다. 

Read own Writes
: 만약 한 프로세스가 쓰기 작업을 수행 했다면,  나중에 같은 프로세스가 이 쓰기 작업의 결과를 관찰한다. 
If a process performs a write, the same process later observes the result of its write.

Monotonic Reads
: 한 프로세스에 의해서 관찰된 쓰기 작업들은 단조롭게 감소하는 것이 보장됩니다.

Writes Follow Reads
: 몇몇의 프로세스가 쓰기 작업에 의해 읽기 작업을 수행한고 또 다른 프로세스가 그 쓰기 작업의 결과를 관찰한다면, 그 프로세스도 읽기 작업을 관찰 할 수 있다.
if some process performs a read followed by a write, and another process observes the result of the write, then it can also observe the read (unless it has been overwritten).

Monotonic Writes
: 만약 몇몇의 프로세스가 한 작업을 수행한다면, 후속 쓰기 작업이 추가로 있는, 다른 프로세스가 그것들을 같은 순서로 관찰할 것입니다. 

# Causal Consistency

만약 한 연산이 논리적으로 앞전 연산에 의지하고 있다면, 두 연산자 간의 causal한 관계가 있다고 합니다. 예를 들어, 한 쓰기 연산이 모든 문서를 특정 컨디션에 지우고 있다고 하면, 특정 컨디션을 위한 읽기 연산 때문에 둘의 관계는 causal 관계를 이루고 있씁니다.

causally consistent sessions으로, MongoDB는 그들의 causal 관계십에 기반한 순서대로 causal 연산을 수행합니다. 그리고 클라이언트는 casual 관계들에서 일치성이 맞는지 관찰합니다. 

Causal Consistency을 제공하기 위해선, MongoDB 3.6에서 클라이언트 세션에 가능합니다. A causally consistent session은 `majority` 읽기 연산을 가진것과  `majority` 쓰기 연산의 연속들로 구성됩니다.  애플리케이션은 반드시 클라이언트 세션에서 오직 하나의 쓰레드에서 이런 연산이 수행되도록 해야합니다.
 


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTMwNTQ3MTUxNyw2MjI4MzUxOTgsLTY5MT
I2Mzc5NiwxNzczMTkxOTY5LC01ODA4ODk4MjUsNzMyNzExNTI4
LC05NjEzNjUzNiwxMTU3NDg2ODQ4LDE4NjQ5MzY2OTMsLTY5ND
UzOTMxOSwxNTIyOTYxMTE2XX0=
-->