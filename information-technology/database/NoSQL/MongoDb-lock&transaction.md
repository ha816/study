# Lock

>Exclusive lock과 Shared lock

멀티 트랜잭션 환경에서 데이터베이스의  **일관성**과  **무결성**을 유지하려면  **트랜잭션의 순차적 진행을 보장할 수 있는 직렬화 장치가 필요**하다.

Exclusive lock (배타적 잠금)
: 쓰기 잠금(Write lock)이라고도 불린다. 왜냐하면 어떤 트랜잭션에서 데이터를 변경하고자 할 때, 쓰이는 잠금이라 그렇다. 배타적 잠금이 걸리면, 트랜잭션이 완료될 때까지 해당 테이블 혹은 레코드(row)를 다른 트랜잭션에서 읽거나 쓰지 못하게 된다. exclusive lock이 특정 데이터에 걸리면 shared lock을 걸 수 없고 exclusive lock도 걸수 없습니다.

Shared lock (공유 잠금)
: 배타적 잠금과는 다르게 다수의 트랜잭션간의 공유가 가능한 락이다. 쓰기가 아닌 읽기에만 국한되어 있다. 즉 리소스를 다른 사용자가 동시에 읽을 수 있게 하되 변경은 불가하게 하는 것이다. 동일한 데이터에 대해서 shared lock 동시 적용이 가능하다. 

# MongoDB 엔진의 잠금

MongoDB에서 제공하는 잠금은 크게 명시적 잠금과 묵시적 잠금으로 나누어 집니다. 또 명시적 잠금은 글로벌 잠금뿐이며 나머지 모든 잠금은 묵시적 잠금이다.  데이터베이스 그리고 컬렉션에 대한 잠금은 모두 묵시적인 잠금이며, 쿼리 실행시 자동적으로 획득됐다가 자동으로 해제되는 잠금이다. 

## 글로벌 잠금(인스턴스 잠금)

MongoDB에서 유일하게 명시적으로 사용할 수 있는 잠금은 글로벌 잠금뿐이다. MongoDB 서버 인스턴스에서 단 하나만 있는 잠금이므로, 이를 인스턴스 잠금이라고 한다.  

```
db.fsyncLock){fsync:1, lock:true}
```

기본적으로 fsync:1으로 설정하면 아직 디스크에 기록되지 못한 데이터(캐시, 메모리) 모두 디스크로 기록합니다. lock:true 옵션이 있으면 글로벌 잠금을 획득하게 되는데, 이는 쓰기 잠금이지 읽기 잠금은 아닙니다. 

## 오브젝트 잠금

MongoDB도 다른 RDBMS와 같이 계층형 오브젝트에 대한 다중 레벨 잠금 방식을 방식을 지원했습니다.

> Multiple Granularity Locking(다중 레벨 잠금방식)
> 계층형 오브젝트에 대한 동시성 처리를 위해서 다중 레벨의 잠금 방식

MongoDB에서는 S(Shared Lock), X(Exclusive Lock), 그리고 IS(Intent Shared Lock)과 IX(Intent Exclusive Lock)을 제공한다. IS와 IX는 의도를 표현하는 잠금인데 묶어서 인텐션 잠금이라도도 한다. 

IS는 컬렉션이나 문서 레벨에 Shared Lock을 걸거이라는 의도를 나타냅니다. 이는 현재 쓰레드가 특정 오브젝트에 대해 Lock을 걸 의도를 가지고 있

IX는 마찬가지로 컬렉션이나 문서 레벨에 Exclusive Lock을 걸거이라는 의도를 나타냅니다. 

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

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1MjY1NTg5NDAsLTk2MTM2NTM2LDExNT
c0ODY4NDgsMTg2NDkzNjY5MywtNjk0NTM5MzE5LDE1MjI5NjEx
MTZdfQ==
-->