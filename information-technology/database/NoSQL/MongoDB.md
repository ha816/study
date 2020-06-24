# MongoDB  

MongoDB는 [NoSQL](https://namu.wiki/w/NoSQL "NoSQL")  [DBMS](https://namu.wiki/w/DBMS "DBMS")의 한 종류입니다.

MongoDB는 오픈소스 문서지향(Document-Oriented)적 Cross-platform 데이터베이스이며, 뛰어난 확장성과 성능을 자랑합니다. 또한, 현존하는 NoSQL 데이터베이스 중 인지도 1위를 유지하고있습니다.

[MySQL](https://namu.wiki/w/MySQL "MySQL") 처럼 전통적인 테이블-관계 기반의 [RDBMS](https://namu.wiki/w/RDBMS "RDBMS")가 아니며 [SQL](https://namu.wiki/w/SQL "SQL")을 사용하지 않습니다. 참고로 mongo는 humongous의 줄임말이라고 합니다. 즉 거대한 DB라는 뜻입니다.

# Component

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

# BSON

BSON은 문서를 저장하거나 MongoDB에 원격처리 호출을 하는데 사용되는 이진 시리얼 포맷입니다. 

## ObjectId[](https://docs.mongodb.com/manual/reference/bson-types/#objectid "Permalink to this headline")

ObjectIds 작고, 빠르게 생성하고, 거의 unique하고 순서가 정해집니다. ObjectId 값은 12bytes 길이로 이 12 bytes으 아래로 구성됩니다.

-   a 4-byte  _timestamp value_, representing the ObjectId’s creation, measured in seconds since the Unix epoch
-   a 5-byte  _random value_
-   a 3-byte  _incrementing counter_, initialized to a random value

While the BSON format itself is little-endian, the  _timestamp_  and  _counter_  values are big-endian, with the most significant bytes appearing first in the byte sequence.

MongoDB 클라이언트는 `_id` 필드를 유일한 ObjectId 로 추가해야 합니다. ObjectIds를 사용하는 것은 아래와 같은 이점들이 있습니다.

* mongo shell에서,  `ObjectId.getTimestamp()` 메서드를 사용하면 ObjectId의 생성 시간을 알 수 있습니다. 
* ObjectId를 저장하면 `_id`필드가 거의 생성 시간으로 정렬된 것과 같습니다. 

> objectid값이 시간에 따라 커지는 것은 일반적으로 하는 것은 맞지만, 필수불가결하게 단조롭게 모든 objectid값이 항상 커지는 것은 아닙니다. 왜냐하면 같은 초에 생성된 값은 순서를 보장하지 않고, 클라이언트가 objectId를 만들어 시스템 시간이 다를지도 모르기 때문입니다.   
    
    
# Operations

`launchd` 런치를 위한 데몬에게 `mongod` 몽고 DB를 위한 데몬을 시작 또는 다시 시작하게 하고 싶다면 아래 명령어를 사용합시다. 

```
brew services start mongodb-community
```
반대로 서버 인스턴스를 멈추고 싶다면 아래 명령어를 수행합시다. 
```
brew services stop mongodb-community
```

## CRUD Operations

### [Create(Insert)](https://docs.mongodb.com/manual/tutorial/insert-documents/)

* 만약 생성할 문서가 들어갈 collection이 현재 없다면, insert 연산 수행시 자동으로 생성합니다.
* MongoDB에선, 컬렉션에 저장된 각 문서는 유니크한 `_id`	필드를 primary key	처럼 가져야 합니다. 만약 삽입하는 문서에 `_id`필드가 빠져있다면, MongoDB 드라이버가 자동으로 Objectid로 `_id`필드를 생성합니다.
* [upsert: true](https://docs.mongodb.com/manual/reference/method/db.collection.update/#upsert-parameter)로 동작하는 update 연산에서도 동일하게 적용됩니다. 
* MongoDB의 한 문서에 대한 모든 쓰기 작업은 원자성을 보장합니다. 
	* [Atomicity and Transactions](https://docs.mongodb.com/manual/core/write-operations-atomicity/)

db.collection.insertOne()
: collection에 단일 문서를 추가합니다. 만약 문서가 `_id`  필드 값을 가지지 않으면, MongoDB에서 자동적으로 ObjectId 값으로  `_id` 필드를 추가합니다. 

아래 예제에서는 `inventory`  collection 안에 한 문서를 추가합니다. 

```
db.inventory.insertOne(
   { item: "canvas", qty: 100, tags: ["cotton"], size: { h: 28, w: 35.5, uom: "cm" } }
)
```

`insertOne()`의 결과로 새롭게 삽입된 문서의 `_id` 필드 값을 포함한 하나의 문서를 반환합니다.  간단하게 막 삽입한 문서를 보고 싶다면 아래 명령을 수행합니다.

```
db.inventory.find( { item: "canvas" } )
```

db.collection.insertMany()
: 한 컬렉션에 다수의 문서를 삽입할 수 있습니다.  문서들의 배열을 메서드로 보냅니다. 

아래 예제에선 `inventory`  collection에 3건의 새로운 문서를 삽입합니다. `_id`  필드가 없다면, MongoDB가 자동적으로 각 문서에 대해 값을 만듭니다.

```
db.inventory.insertMany([
   { item: "journal", qty: 25, tags: ["blank", "red"], size: { h: 14, w: 21, uom: "cm" } },
   { item: "mat", qty: 85, tags: ["gray"], size: { h: 27.9, w: 35.5, uom: "cm" } },
   { item: "mousepad", qty: 25, tags: ["gel", "blue"], size: { h: 19, w: 22.85, uom: "cm" } }
])
```
`insertMany()` 는 새로 삽입된 문서의 `_id`값을 포함한 문서를 반환합니다. 

### [Read(Query)](https://docs.mongodb.com/manual/tutorial/query-documents/)

db.collection.find()
: collection의 문서를 찾는 연산. 특별히 인자를 넣지 않으면 collection에 전체 문서를 찾습니다.

db.collection.findOne()
: collection에 있는 한 문서를 찾는 연산. 내부적으로 db.collection.find() 연산을 호출하며 단지 limit 1 조건을 추가합니다.

## Query 

A  [query filter document](https://docs.mongodb.com/manual/core/document/#document-query-filter)  can use the  [query operators](https://docs.mongodb.com/manual/reference/operator/query/#query-selectors)  to specify conditions in the following form:

copy

copied

{ <field1>: { <operator1>: <value1> }, ... }

The following example retrieves all documents from the  `inventory`  collection where  `status`  equals either  `"A"`  or  `"D"`:

copy

copied

db.inventory.find( { status: { $in: [ "A", "D" ] } } )

NOTE

Although you can express this query using the  [`$or`](https://docs.mongodb.com/manual/reference/operator/query/or/#op._S_or "$or")  operator, use the  [`$in`](https://docs.mongodb.com/manual/reference/operator/query/in/#op._S_in "$in")  operator rather than the  [`$or`](https://docs.mongodb.com/manual/reference/operator/query/or/#op._S_or "$or")  operator when performing equality checks on the same field.

The operation corresponds to the following SQL statement:

copy

copied

SELECT * FROM inventory WHERE status in ("A", "D")

Refer to the  [Query and Projection Operators](https://docs.mongodb.com/manual/reference/operator/query/)  document for the complete list of MongoDB query operators

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIxMDQxMTUyNzMsLTEyMTk5MTk4MjQsOD
E1ODU0NzQxLDExODc5MTQzLC0yMDU3MTcwNzkwLDczMDk5ODEx
Nl19
-->