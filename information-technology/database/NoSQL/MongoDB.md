# MongoDB  

MongoDB는 [NoSQL](https://namu.wiki/w/NoSQL "NoSQL")  [DBMS](https://namu.wiki/w/DBMS "DBMS")의 한 종류입니다.

MongoDB는 오픈소스 문서지향(Document-Oriented)적 Cross-platform 데이터베이스이며, 뛰어난 확장성과 성능을 자랑합니다. 또한, 현존하는 NoSQL 데이터베이스 중 인지도 1위를 유지하고있습니다.

[MySQL](https://namu.wiki/w/MySQL "MySQL") 처럼 전통적인 테이블-관계 기반의 [RDBMS](https://namu.wiki/w/RDBMS "RDBMS")가 아니며 [SQL](https://namu.wiki/w/SQL "SQL")을 사용하지 않습니다. 참고로 mongo는 humongous의 줄임말이라고 합니다. 즉 거대한 DB라는 뜻입니다.

# Basic Concept

## Document

Document는 RDMS의 record, row에 대응하는 데이터 구조입니다. 한 개이상의  key-value pair 로 구성됩니다.

아래 Document 예제를 한번 봅시다.

{
    "id": ObjectId("5099803df3f4948bd2f98391"),
    "username": "velopert",
    "name": { first: "M.J.", last: "Kim" }
}

여기서 id, username, name 은 key이고 대응하는 오른쪽에 값들은 value 입니다.

id 값은 12bytes의 hexadecimal 값으로서, 각 document의 유일함(uniqueness)을 제공합니다.  
첫 4bytes는 현재 timestamp, 다음 3bytes는 machine id, 다음 2bytes는 MongoDB 서버의 프로세스id, 마지막 3bytes는 순차번호입니다.

Document는 기본적으로 추가될때마다 값이 높아집니다.

Document는 동적(dynamic) schema입니다. 즉, 같은 Collection 안에 있는 Document 임에도 서로 다른 schema를 갖고 있을 수 있습니다.

## Collection

Collection은 Document의 집합입니다. RDMS측에 비유하자면, Table에 대응합니다. 하지만 RDMS와 달리 schema를 따로 가지고 있지 않습니다.

## Database

Database는 Collection들의 물리적인 컨테이너입니다. 각 Database는 파일 시스템에 맞게 최적으로 저장됩니다.

# Operations

`launchd` 런치를 위한 데몬에게 `mongod` 몽고 DB를 위한 데몬을 시작 또는 다시 시작하게 하고 싶다면 아래 명령어를 사용합시다. 

```
brew services start mongodb-community
```
반대로 서버 인스턴스를 멈추고 싶다면 아래 명령어를 수행합시다. 
```
$ brew services stop mongodb-community
```

## CRUD Operations

Insert(Create)에 해당하는 문서를 보자. [https://docs.mongodb.com/manual/tutorial/insert-documents/](https://docs.mongodb.com/manual/tutorial/insert-documents/)


`db.collection.insertOne()`을 사용하면 단일 문서를 추가합니다. 아래 예제에서는 `inventory`  collection 안에 한 문서를 추가합니다. 만약 문서가 

The following example inserts a new document into the  `inventory`  collection. If the document does not specify an  `_id`  field, MongoDB adds the  `_id`  field with an ObjectId value to the new document. See  [Insert Behavior](https://docs.mongodb.com/manual/tutorial/insert-documents/#write-op-insert-behavior).

copy

copied

db.inventory.insertOne(
   { item: "canvas", qty: 100, tags: ["cotton"], size: { h: 28, w: 35.5, uom: "cm" } }
)




> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwMTQ0OTE0NzAsLTIwNTcxNzA3OTAsNz
MwOTk4MTE2XX0=
-->