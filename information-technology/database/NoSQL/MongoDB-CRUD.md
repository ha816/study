# [Create(Insert)](https://docs.mongodb.com/manual/tutorial/insert-documents/)

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

# [Read(Query)](https://docs.mongodb.com/manual/tutorial/query-documents/)

db.collection.find()
: collection의 문서를 찾는 연산. 특별히 인자를 넣지 않으면 collection에 전체 문서를 찾습니다.

db.collection.findOne()
: collection에 있는 한 문서를 찾는 연산. 내부적으로 db.collection.find() 연산을 호출하며 단지 limit 1 조건을 추가합니다.

## 동등 조건

검색시 동등 조건을 사용하고 싶다면 `<field>:<value>` 표현을 쿼리 필터 문서([query filter document](https://docs.mongodb.com/manual/core/document/#document-query-filter))에 넣어야 합니다.

아래 예제는 `inventory` 컬렉션에서 status필드가 "D" 값을 가지는 모든 문서를 찾습니다.
```groovy
db.inventory.find( { status: "D" } )
```

## 쿼리 연산자를 이용한 조건

쿼리 필터 문서는 쿼리 연산자([query operators](https://docs.mongodb.com/manual/reference/operator/query/#query-selectors))를 사용할 수 있습니다. 

```groovy
{ <field1>: { <operator1>: <value1> }, ... }
```

아래 예제는 `inventory` 컬렉션에서 Status필드가 "A" 또는 "B"인 모든 문서를 찾습니다.
```groovy
db.inventory.find( { status: { $in: [ "A", "D" ] } } )
```

위 예제는 [`$or`](https://docs.mongodb.com/manual/reference/operator/query/or/#op._S_or "$or") 연산자를 이용해서도 같은 표현을 나타낼 수 있습니다. 하지만 [`$in`](https://docs.mongodb.com/manual/reference/operator/query/in/#op._S_in "$in") 연산자가 같은 필드에 대해서 동등 비교를 할때는 더 좋은 성능을 보입니다. 


##  AND Conjunction

다수의 필드를 AND Conjunction으로 조합해서 쿼리를 구성할 수 있습니다. 

아래 예제는 `status` 필드가 "A" 이고 `qty`는 `30`보다 작은 모든 문서를 찾습니다. 

```groovy
db.inventory.find( { status: "A", qty: { $lt: 30 } } )
```

## OR Conjunction

[`$or`](https://docs.mongodb.com/manual/reference/operator/query/or/#op._S_or "$or") 쿼리 연산자를 사용하면 논리적인 OR Conjunction을 사용하는 것과 같은 효과를 낼 수 있습니다. 

```groovy
db.inventory.find( { $or: [ { status: "A" }, { qty: { $lt: 30 } } ] } )
```
위 예제는 status가 "A" 또는 qty가 30보다 작은 모든 문서를 찾습니다. 


# Update

* 만약 update 할려는 문서를 찾지 못하면, update 연산은 새로운 문서를 만들어 삽입합니다. (`upsert  :  true`)
* MongoDB에서 한 문서에 대한 모든 쓰기 작업은 원자성이 보장됩니다. 
* `_id`는 불변적입니다. 한번 `_id`필드에 값이 저장되면, 이 값을 다른 문서로 대체하거나 다른 값을 가질 수 없습니다.

MongoDB는 [`$set`](https://docs.mongodb.com/manual/reference/operator/update/set/#up._S_set "$set") 같은 문서를 update 하기위한 연산자를 제공합니다. 
[`$set`](https://docs.mongodb.com/manual/reference/operator/update/set/#up._S_set "$set")과 같은 어떤 update 연산자는 필드가 존재하지 않으면 필드를 생성합니다. 
update 연산자를 사용하기 위해선 아래와 같은 update 문서를 메서드로 전달해야 합니다. 
```groovy
{
  <update operator>: { <field1>: <value1>, ... },
  <update operator>: { <field2>: <value2>, ... },
  ...
}
```

db.collection.updateOne(filter,  update,  options)
: 조건에 맞는 최상단 문서 하나를 찾아 수정합니다.



db.collection.updateMany(filter,  update,  options)
: 조건에 맞는 문서 전체를 찾아 수정합니다.



updateOne의 경우, item 필드가 paper인 문서 중 제일 앞단의 한 문서에 대해 모든 update 연산을 수행합니다. updateMany의 경우, qty 필드가 50보다 작은 모든 문서를 찾아 모든 update 연산을 수행합니다. 

* $set 연산자를 사용해서 size,uom필드의 값을 cm으로 바꾸고 status를 p로 바꿉니다.
* $currentDate 연산자를 사용해서 lastModified 필드의 최신 날짜로 변경을 합니다. 만약 lastModified 필드가 존재하지 않으면 $currentDate가 새로 필드를 만듭니다.

``` groovy
db.inventory.updateOne(
   { item: "paper" },
   {
     $set: { "size.uom": "cm", status: "P" },
     $currentDate: { lastModified: true }
   }
)

db.inventory.updateMany(
   { "qty": { $lt: 50 } },
   {
     $set: { "size.uom": "in", status: "P" },
     $currentDate: { lastModified: true }
   }
)
```

--- 

db.collection.replaceOne(filter,  update,  options)
: 조건에 맞는 문서를 찾아 새로운 문서로 대체합니다.

`_id`필드를 제외하곤 문서의 모든 컨텐츠를 바꾸고 싶다면, `db.collection.replaceOne()`에 두번째 인자로 완전히 새로운 문서를 넣으면 됩니다. 대체하는 문서는 반드시 field-value 쌍으로 구성되어야 합니다. 즉 update 연산자를 포함하면 안됩니다.

대체하는 문서는 다른 필드를 가질 수 있고, `_id`필드는 불변이기 때문에 남아 있어야 합니다. 만약 대체하는 문서에 `_id` 필드를 포함하고 있으면 기존의 값하고 동일해야만 합니다. 

```groovy
db.inventory.replaceOne(
   { item: "paper" },
   { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 40 } ] }
)
```

### Field Order[](https://docs.mongodb.com/manual/tutorial/update-documents/#field-order "Permalink to this headline")

MongoDB preserves the order of the document fields following write operations  _except_  for the following cases:

-   The  `_id`  field is always the first field in the document.
-   Updates that include  [`renaming`](https://docs.mongodb.com/manual/reference/operator/update/rename/#up._S_rename "$rename")  of field names may result in the reordering of fields in the document.

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTUzMTM4NjE4NiwtMzI5MTIyNTkwLDg2OT
Q3NTY2NiwxMzQwMjAxMzA2LC0xMTkxOTM5MjksMTk0MDE2Nzg1
MywtMTkzMzI0NDU2NV19
-->