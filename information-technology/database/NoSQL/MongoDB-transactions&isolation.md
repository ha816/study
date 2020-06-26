# Transaction

## Atomicity

MongoDB에서, 쓰기 연산은 단일 문서에 대해선 원자성을 보장합니다. 심지어 단일 문서안의 다수의 중첩된 문서에 대한 수정 연산자에 대해서도 보장합니다.  

# Multi-Document Transactions
단일 쓰기 연산이 다수의 문서를 수정할때, 각 문서에 대한 수정은 원자성을 보장합니다. 하지만 **연산 전체가 원자성을 보장하진 않습니다.**

다수 문서에 대한 쓰기 작업을 수행할때, 단일 또는 다수 쓰기 모든 연산이든지, 다른 연산이 교차로 배치될 수 있다. (interleave; 컴퓨터 하드디스크의 성능을 높이기 위해 데이터를 서로 인접하지 않게 배열하는 방식을 말한다.)

복수 문서에 대해서 원자성이 보장되는 읽기와 쓰기를 필요로 하는 상황에 대해서, MongoDB는 복수 문서 트랜잭션을 지원합니다.

For details regarding transactions in MongoDB, see the  [Transactions](https://docs.mongodb.com/manual/core/transactions/)  page.

>주의사항
>대부분의 경우, 다수-문서 트랜잭션은 단일 문서 쓰기 보다 훨씬 성능  손실이 큽니다. 그리고 효과적인 schema 디자인을 해놓았다면, 다수-문서 트랜잭션을 사용할 일이 별로 없습니다.

# Concurrency Control

동시성 제어란  DBMS가 다수의 사용자 사이에서 동시에 작용하는 다중 트랜잭션의 상호간섭 작용에서 DB의 일관성과 동시성을 적절히 조절하는 것을 말합니다. 일반적으로 동시성을 허용하면 일관성이 낮아지게 됩니다.

![](https://k.kakaocdn.net/dn/YYwAw/btqAhqpozl2/IMdpfbgguBj897K4VkEwy1/img.png)

동시성 제어의 목표는 동시에 실행되는 트랜잭션 수를 최대화 하면서 입력, 수정, 삭제, 검색 시 데이터의 무결성을 유지하는데 있습니다. 하지만 읽기 작업에 공유 Lock을 사용하는 일반적인 Locking 메커니즘은 구현이 간단한 반면에 아래와 같은 문제점을 가

동시성 제어는 다수의 애플리케이션이 동시 수행될때 데이터 불이치 또는 충돌이 없도록 보장합니다. 이를 위해 MongoDB의 접근법은 unique한 값만을 가질 수 있는 unique 인덱스 필드를 만드는 것입니다. 인덱스 필드는 중복된 데이터를 생성하는 것으로부터 insertions과 updates를 막습니다. 


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

Write Concern이란 MongoDB 가 Client 의 요청으로 데이터를 기록할 때, 해당 요청에 대한 Response를 어느 시점에 주느냐에 대한 동작 방식을 지정합니다.

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

<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA2NDExNTkxOCwtMTUwMzA1NTE5OSwtMT
kwMDE2NTA3MywtMTQ0NzAwNTU4NSwtMTM0NzY2Njk4XX0=
-->