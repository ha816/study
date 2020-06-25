# Overview

CRUD 명령과 관련된 주요 성질을 알아보자. 

# Atomicity

MongoDB에서, 쓰기 연산은 단일 문서에 대해선 원자성을 보장합니다. 심지어 단일 문서안의 다수의 중첩된 문서에 대한 수정 연산자에 대해서도 보장합니다.  

# Multi-Document Transactions

단일 쓰기 연산이 다수의 문서를 수정할때, 각 문서에 대한 수정은 원자성을 보장합니다. 하지만 **연산 전체가 원자성을 보장하진 않습니다.**

다수 문서에 대한 쓰기 작업을 수행할때, 단일 또는 다수 쓰기 모든 연산이든지, 다른 연산이 교차로 배치될 수 있다. (interleave; 컴퓨터 하드디스크의 성능을 높이기 위해 데이터를 서로 인접하지 않게 배열하는 방식을 말한다.)

복수 문서에 대해서 원자성이 보장되는 읽기와 쓰기를 필요로 하는 상황에 대해서, MongoDB는 복수 문서 트랜잭션을 지원합니다.

For details regarding transactions in MongoDB, see the  [Transactions](https://docs.mongodb.com/manual/core/transactions/)  page.

>주의사항
>대부분의 경우, 다수-문서 트랜잭션은 단일 문서 쓰기 보다 훨씬 성능  손실이 큽니다. 그리고 효과적인 schema 디자인을 해놓았다면, 다수-문서 트랜잭션을 사용할 일이 별로 없습니다.

## Concurrency Control

동시성 제어는 다수의 애플리케이션이 동시 수행될때 데이터 불이치 또는 충돌이 없도록 보장합니다. 
이를 위해 MongoDB의 접근법은 unique한 값만을 가질 수 있는 unique 인덱스 필드를 만드는 것입니다. 인덱스 필드는 중복된 데이터를 생성하는 것으로부터 insertions과 updates를 막습니다. 

# Isolation

## Read Concern(Isolation)

`readConcern` 옵션은 replica sets 그리고 replica shard set으로 부터 데이터를 읽는데 consistency와  isolation을 컨트롤 하도록 합니다. 


### Local

local 레벨의 Read Concern입니다. 

이 쿼리로 반환된 데이터는 그 데이터가 주요 replica sets에 쓰여졌다는 것을 보장하지 않습니다. 운이 나쁘면 해당 데이터는 롤백이 발생하여 데이터가 replica sets에 존재하지 않을 수 있습니다.

기본적으로 primary에서 데이터를 읽습니다. 그러나 causally consistent sessions와 연관된 읽기라면 secondaries에서 읽습니다. 

즉, local은 causally consistent sessions과 transactions이 있든지 없든지 사용이 가능합니다.

### Available

이 쿼리로 반환된 데이터는 그 데이터가 주요 replica sets에 쓰여졌다는 것을 보장하지 않습니다. 운이 나쁘면 해당 데이터는 롤백이 발생하여 데이터가 replica sets에 존재하지 않을 수 있습니다.


**Default for:**  reads against secondaries if the reads are  **not**  associated with  [causally consistent sessions](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions).

**Availability:**  Read concern  `available`  is  **unavailable for use**  with causally consistent sessions and transactions.




## Write Concern

Write Concern(쓰기 고려사항)은 쓰기 작업을 단일 mongd, replica sets, shared clusters에 하기 위해, MongoDB로 부터 요청된 지식 레벨을 묘사합니다. 

```groovy
{ w: <value>, j: <boolean>, wtimeout: <number> }
```

* w는 쓰기 연산이 특정 수의 mongod 객체에 전파되기를 바란다는 요청 옵션
* j는 쓰기 연산이 디스크 저널에 쓰기를 바란다는 옵션
* wtimeout은 다른 쓰기 연산을 막는 제한시간을 지정하는 옵션

## Isolation Guarantees

### Read Uncommitted

Depending on the read concern, clients can see the results of writes before the writes are  [durable](


-   Regardless of a write’s  [write concern](https://docs.mongodb.com/manual/reference/write-concern/), other clients using  [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern.%22local%22 ""local"")  or  [`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern.%22available%22 ""available"")  read concern can see the result of a write operation before the write operation is acknowledged to the issuing client.
-   Clients using  [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern.%22local%22 ""local"")  or  [`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern.%22available%22 ""available"")  read concern can read data which may be subsequently  [rolled back](https://docs.mongodb.com/manual/core/replica-set-rollbacks/)  during replica set failovers.

For operations in a  [multi-document transaction](https://docs.mongodb.com/manual/core/transactions/), when a transaction commits, all data changes made in the transaction are saved and visible outside the transaction. That is, a transaction will not commit some of its changes while rolling back others.

Until a transaction commits, the data changes made in the transaction are not visible outside the transaction.

However, when a transaction writes to multiple shards, not all outside read operations need to wait for the result of the committed transaction to be visible across the shards. For example, if a transaction is committed and write 1 is visible on shard A but write 2 is not yet visible on shard B, an outside read at read concern  [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern.%22local%22 ""local"")  can read the results of write 1 without seeing write 2.

Read uncommitted is the default isolation level and applies to  [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod "bin.mongod")  standalone instances as well as to replica sets and sharded clusters.

### Read Uncommitted And Single Document Atomicity[](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#read-uncommitted-and-single-document-atomicity "Permalink to this headline")

Write operations are atomic with respect to a single document; i.e. if a write is updating multiple fields in the document, a read operation will never see the document with only some of the fields updated. However, although a client may not see a  _partially_  updated document, read uncommitted means that concurrent read operations may still see the updated document before the changes are made  [durable](https://docs.mongodb.com/manual/reference/glossary/#term-durable).

With a standalone  [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod "bin.mongod")  instance, a set of read and write operations to a single document is serializable. With a replica set, a set of read and write operations to a single document is serializable  _only_  in the absence of a rollback.

### Read Uncommitted And Multiple Document Write[](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#read-uncommitted-and-multiple-document-write "Permalink to this headline")

When a single write operation (e.g.  [`db.collection.updateMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.updateMany/#db.collection.updateMany "db.collection.updateMany()")) modifies multiple documents, the modification of each document is atomic, but the operation as a whole is not atomic.

When performing multi-document write operations, whether through a single write operation or multiple write operations, other operations may interleave.

For situations that require atomicity of reads and writes to multiple documents (in a single or multiple collections), MongoDB supports multi-document transactions:

-   **In version 4.0**, MongoDB supports multi-document transactions on replica sets.
-   **In version 4.2**, MongoDB introduces distributed transactions, which adds support for multi-document transactions on sharded clusters and incorporates the existing support for multi-document transactions on replica sets.

For details regarding transactions in MongoDB, see the  [Transactions](https://docs.mongodb.com/manual/core/transactions/)  page.

IMPORTANT

In most cases, multi-document transaction incurs a greater performance cost over single document writes, and the availability of multi-document transactions should not be a replacement for effective schema design. For many scenarios, the  [denormalized data model (embedded documents and arrays)](https://docs.mongodb.com/manual/core/data-model-design/#data-modeling-embedding)  will continue to be optimal for your data and use cases. That is, for many scenarios, modeling your data appropriately will minimize the need for multi-document transactions.

For additional transactions usage considerations (such as runtime limit and oplog size limit), see also  [Production Considerations](https://docs.mongodb.com/manual/core/transactions-production-consideration/).

Without isolating the multi-document write operations, MongoDB exhibits the following behavior:

1.  Non-point-in-time read operations. Suppose a read operation begins at time  _t_1  and starts reading documents. A write operation then commits an update to one of the documents at some later time  _t_2. The reader may see the updated version of the document, and therefore does not see a point-in-time snapshot of the data.
2.  Non-serializable operations. Suppose a read operation reads a document  _d_1  at time  _t_1  and a write operation updates  _d_1  at some later time  _t_3. This introduces a read-write dependency such that, if the operations were to be serialized, the read operation must precede the write operation. But also suppose that the write operation updates document  _d_2  at time  _t_2  and the read operation subsequently reads  _d_2  at some later time  _t_4. This introduces a write-read dependency which would instead require the read operation to come  _after_  the write operation in a serializable schedule. There is a dependency cycle which makes serializability impossible.
3.  Reads may miss matching documents that are updated during the course of the read operation.

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDY1MDM3NzQwLC02MDA3NjE0NywtMjEyOT
QyNDA0NSwtMjA3NDY0Nzg5OSwtMjA2MDg0ODAzMCw1MjU1MTE3
Nyw4NDA1MDM5OTQsNzQ3NDkyMTUyLDg4Nzg0ODM4MSwyMDE5Mz
Y2MzU0LC0xNzA1ODQ4NzE3XX0=
-->