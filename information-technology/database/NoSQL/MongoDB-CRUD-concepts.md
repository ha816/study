# Overview

CRUD 명령과 관련된 주요 성질을 알아보자. 

# Atomicity

MongoDB에서, 쓰기 연산은 단일 문서에 대해선 원자성을 보장합니다. 심지어 단일 문서안의 다수의 중첩된 문서에 대한 수정 연산자에 대해서도 보장합니다.  

# Multi-Document Transactions

When a single write operation (e.g.  [`db.collection.updateMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.updateMany/#db.collection.updateMany "db.collection.updateMany()")) modifies multiple documents, the modification of each document is atomic, but the operation as a whole is not atomic.

When performing multi-document write operations, whether through a single write operation or multiple write operations, other operations may interleave.

For situations that require atomicity of reads and writes to multiple documents (in a single or multiple collections), MongoDB supports multi-document transactions:

-   **In version 4.0**, MongoDB supports multi-document transactions on replica sets.
-   **In version 4.2**, MongoDB introduces distributed transactions, which adds support for multi-document transactions on sharded clusters and incorporates the existing support for multi-document transactions on replica sets.

For details regarding transactions in MongoDB, see the  [Transactions](https://docs.mongodb.com/manual/core/transactions/)  page.

IMPORTANT

In most cases, multi-document transaction incurs a greater performance cost over single document writes, and the availability of multi-document transactions should not be a replacement for effective schema design. For many scenarios, the  [denormalized data model (embedded documents and arrays)](https://docs.mongodb.com/manual/core/data-model-design/#data-modeling-embedding)  will continue to be optimal for your data and use cases. That is, for many scenarios, modeling your data appropriately will minimize the need for multi-document transactions.

For additional transactions usage considerations (such as runtime limit and oplog size limit), see also  [Production Considerations](https://docs.mongodb.com/manual/core/transactions-production-consideration/).

## Concurrency Control

Concurrency control allows multiple applications to run concurrently without causing data inconsistency or conflicts.

One approach is to create a  [unique index](https://docs.mongodb.com/manual/core/index-unique/#index-type-unique)  on a field that can only have unique values. This prevents insertions or updates from creating duplicate data. Create a unique index on multiple fields to force uniqueness on that combination of field values. For examples of use cases, see  [update() and Unique Index](https://docs.mongodb.com/manual/reference/method/db.collection.update/#update-with-unique-indexes)  and  [findAndModify() and Unique Index](https://docs.mongodb.com/manual/reference/method/db.collection.findAndModify/#upsert-and-unique-index).

Another approach is to specify the expected current value of a field in the query predicate for the write operations.



-   [Atomicity and Transactions](https://docs.mongodb.com/manual/core/write-operations-atomicity/)
-   [Read Isolation, Consistency, and Recency](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/)
-   [Distributed Queries](https://docs.mongodb.com/manual/core/distributed-queries/)
-   [Linearizable Reads via findAndModify](https://docs.mongodb.com/manual/tutorial/perform-findAndModify-linearizable-reads/)

Query Plan, Performance, and Analysis

-   [Query Plans](https://docs.mongodb.com/manual/core/query-plans/)
-   [Query Optimization](https://docs.mongodb.com/manual/core/query-optimization/)
-   [Analyze Query Performance](https://docs.mongodb.com/manual/tutorial/analyze-query-plan/)
-   [Write Operation Performance](https://docs.mongodb.com/manual/core/write-performance/)

Miscellaneous

-   [Tailable Cursors](https://docs.mongodb.com/manual/core/tailable-cursors/)



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE2NzcwNzc3OTksLTE3MDU4NDg3MTddfQ
==
-->