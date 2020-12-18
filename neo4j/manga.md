```
MATCH (b:MangaBook)-[:IS_A_BOOK_OF_PRODUCT]->(p:MangaProduct {oid:111850511})-[:HAS_KEYWORD]->(k:TheOneKeyword) // 드래곤볼완전판(111850511; alpha) WHERE toInteger(p.`props.serviceRating`[0]) < 17 AND toInteger(p.`props.productType`[0]) = 0 AND (p.`props.onStatus`[0] = 'true' OR p.`props.firstBookId`[0] = '' ) AND (p.`props.private`[0] = 'true' OR (p.`props.private`[0] = 'false' AND p.`props.periodic`[0] = 'false')) AND b.`props.onStatus`[0] = 'true' AND b.`props.display`[0] = 'true' AND datetime(b.`props.permitStartDateTime`[0]) < datetime() AND datetime() < datetime(b.`props.permitEndDateTime`[0]) WITH p AS product, toBoolean(p.`props.periodic`[0]) AS periodic, p.`props.lastVolume`[0] AS lastVolume ORDER BY periodic, lastVolume DESC LIMIT 1 
```
```
// 2번 단락 - 최신권 순서로 10권 가져오기 
MATCH (b:MangaBook)-[:IS_A_BOOK_OF_PRODUCT]->(product) WHERE b.`props.onStatus`[0] = 'true' AND b.`props.display`[0] = 'true' AND datetime(b.`props.permitStartDateTime`[0]) < datetime() AND datetime() < datetime(b.`props.permitEndDateTime`[0]) WITH b AS pagedBooks, product, toInteger(b.`props.volume`[0]) AS volume ORDER BY volume DESC LIMIT 10 
```
```
// 3번 단락 - 모든 책 가져오기 MATCH (b:MangaBook)-[:IS_A_BOOK_OF_PRODUCT]->(product) WHERE b.`props.onStatus`[0] = 'true' AND b.`props.display`[0] = 'true' AND datetime(b.`props.permitStartDateTime`[0]) < datetime() AND datetime() < datetime(b.`props.permitEndDateTime`[0]) WITH b AS books, pagedBooks, product RETURN apoc.create.virtual.fromNode(product, ['name', 'oid']) AS product, [ (product)-[:AUTHOR_IS]->(author:MangaAuthor) | apoc.create.virtual.fromNode(author, ['name', 'oid']) ] AS authors, [ book IN COLLECT(DISTINCT pagedBooks) | apoc.create.virtual.fromNode(book, ['name', 'oid', 'props.volume']) ] AS pagedBooks, [ book IN COLLECT(books) WHERE toInteger(book.`props.volume`[0]) = 0 | apoc.create.virtual.fromNode(book, ['name', 'oid', 'props.volume']) ][0] AS firstBook, apoc.coll.min([ book IN COLLECT(books) WHERE book.`props.freeEndDateTime`[0] <> '' | datetime(book.`props.freeEndDateTime`[0])]) AS minFreeEndDateTime
```

```
// 1번 단락 - 만화작가의 다른작품 가져오기 (pagination), 가져오면서 작품의 1권 책도 함께(firstBook) 
MATCH (product:MangaProduct {oid:111850511})-[:AUTHOR_IS]->(author:MangaAuthor)<-[:AUTHOR_IS]-(relatedProduct:MangaProduct)<-[:IS_A_BOOK_OF_PRODUCT]-(firstBook:MangaBook) WHERE product <> relatedProduct AND toInteger(firstBook.`props.volume`[0]) = 0 WITH relatedProduct, firstBook, toInteger(relatedProduct.`props.reviewCount`[0]) AS reviewCount ORDER BY reviewCount DESC LIMIT 10 CALL apoc.create.vNode(['MangaProduct', 'TheOne'],{ name:relatedProduct.name, id:relatedProduct.oid, reviewCount:relatedProduct.reviewCount, firstBook:firstBook.oid }) yield node AS result RETURN result
```





> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDI2MDIxMjE0XX0=
-->