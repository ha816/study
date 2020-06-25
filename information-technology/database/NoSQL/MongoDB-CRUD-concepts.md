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

# Read Isolation



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4NTg1NDExMzUsODQwNTAzOTk0LDc0Nz
Q5MjE1Miw4ODc4NDgzODEsMjAxOTM2NjM1NCwtMTcwNTg0ODcx
N119
-->