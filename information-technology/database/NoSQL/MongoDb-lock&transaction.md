# Lock

>Exclusive lock과 Shared lock

멀티 트랜잭션 환경에서 데이터베이스의  **일관성**과  **무결성**을 유지하려면  **트랜잭션의 순차적 진행을 보장할 수 있는 직렬화 장치가 필요**하다.

Exclusive lock (배타적 잠금)
: 쓰기 잠금(Write lock)이라고도 불린다. 
어떤 트랜잭션에서 데이터를 변경하고자 할 때, 해당 트랜잭션이 완료될 때까지 해당 테이블 혹은 레코드(row)를 다른 트랜잭션에서 읽거나 쓰지 못하게 하기 위해 Exclusive lock을 걸고 트랜잭션을 진행시키는 것이다.

exclusive lock이 특정 데이터에 걸리면 shared lock을 걸 수 없고 exclusive lock도 걸수 없습니다.

Shared lock (공유 잠금)
: 읽기 잠금(Read lock)이라고도 불린다. 
리소스를 다른 사용자가 동시에 읽을 수 있게 하되 변경은 불가하게 하는 것이다.
어떤 데이터에는 shared lock이 동시에 여러개 적용될 수 있다.
어떤 자원에 shared lock이 하나라도 걸려있으면 exclusive lock을 걸 수 없다.


* Lock은 DBMS가 자동으로도 적용하기도 하고 수동으로도 줄 수 있다.  

* Lock은 잠금 비용과 동시성비용을 고려해야한다.

만약 lock을 걸어야할 페이지가 많다면, 그럴바에 테이블 전체에 lock을 걸어버리는 편이 한번에 처리하니까 잠금 비용에 낮아져 효율적이다.

하지만 lock의 범위가 넓어질수록 동시에 접근할 수 없는 자원이 많아지므로 동시성 비용이 높아져 효율이 떨어진다.

* Lock이 엄청나게 다양하다.

row lock / table lock 부터 시작해서 그 안에 RX, RS, S, SRX, X등이 있다.

* 격리수준에 따라 Lock종류가 많아진다. 예를 들면 SQL select문에서 Shared lock을 걸지 않게하는 것도 있다.

SELECT * FROM TABLENAME WITH (READUNCOMMITTED) WHERE PK = 5

  
  
출처: [https://jeong-pro.tistory.com/94](https://jeong-pro.tistory.com/94) [기본기를 쌓는 정아마추어 코딩블로그]  
  
출처: [https://jeong-pro.tistory.com/94](https://jeong-pro.tistory.com/94) [기본기를 쌓는 정아마추어 코딩블로그]  
  
출처: [https://jeong-pro.tistory.com/94](https://jeong-pro.tistory.com/94) [기본기를 쌓는 정아마추어 코딩블로그]


# MongoDB 엔진의 잠금

MongoDB에서 제공하는 잠금은 크게 명시적 잠금과 묵시적 잠금으로 나누어 집니다. 또 명시적 잠금은 글로벌 잠금뿐이며 나머지 모든 잠금은 묵시적 잠금이다.  데이터베이스 그리고 컬렉션에 대한 잠금은 모두 묵시적인 잠금이며, 쿼리 실행시 자동적으로 획득됐다가 자동으로 해제되는 잠금이다. 

## 글로벌 잠금

MongoDB에서 유일하게 명시적으로 사용할 수 있는 잠금은 글로벌 잠금뿐이다. MongoDB 서버 인스턴스에서 단 하나만 있는 잠금이므로, 이를 인스턴스 잠금이라고 한다.  

## 오브젝트 잠금

# 스토리지 엔진의 잠금


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTAzMzU2Njg2LDE4NjQ5MzY2OTMsLTY5ND
UzOTMxOSwxNTIyOTYxMTE2XX0=
-->