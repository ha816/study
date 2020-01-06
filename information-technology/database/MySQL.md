# MySQL

## Partitioning

파티셔닝이란 데이터를 테이블로 분리해서 저장하지만 사용자 입장에서는 여전히 하나의 테이블 사용하는 솔루션이다. 

테이블의 데이터가 많아진다고 무조건 파티션을 적용하는 것이 효율적인 것은 아니다. 하나의 테이블이 너무 커서 인덱스의 크기가 물리적인 메모리 보다 훨씬 크거나, 데이터 특성상 주기적인 삭제 작업이 필요한 경우 등이 파티션이 필요한 대표적인 예다. 

### Partitioning Types 

파티셔닝에는 일반적으로 **수평 파티셔닝(행으로 파티션을 나누는 방식)**과 **수직 파티셔닝(컬럼별로 파티셔닝을 나누는 방법, 특정 컬럼만을 고속 스캔할 때 유용)** 두 가지 방법이 있다. 

Shard는 도자기의 파편이라는 의미로 Sharding은 수평, 수직 파티셔닝을 모두 아우르는 말이다. 

![enter image description here](https://assets.digitalocean.com/articles/understanding_sharding/DB_image_1_cropped.png)


To shard your data, you need to decide a key, called a sharding key, to partition your data on. The shard key is either an indexed field or indexed compound fields that exist in every document in the collection.

There is no general rule to select a sharding key; what key you choose depends on your application. For instance, you may choose userID as the shard key in a social media app.  


### MySQL Partition Method
다른 DBMS와 마찬가지로 MySQL에서도 4가지 기본 파티셔닝 기법을 제공하고 있다. 

* 레인지 파티션
	* 파티션 키의 연속된 범위로 파티션을 정의하는 방법으로 가장 일반적으로 사용된다.
	* 날짜를 기반으로 데이터가 누적되고 특정기간 단위로 분석, 삭제해야 할때
	* 범위기반으로 데이터를 여러 파티션으로 균등하게 나눌수 있을때
* 리스트 파티션
	* 레이진 파티션과 비슷하지만, 리스트 파티션에는 파티션 키 값을 하나하나 리스트로 나열해야 한다.
	* 파티션 키 값이 코드 값이거나 카테고리와 같이 고정적일때
	* 키 값이 연속적이지 않고 정렬 순서와 관계 없이 파티션을 할때
	* 파티션 키값을 기준으로 레코드의 건수가 균일하고, 검색 조건에 파티션 키가 자주 사용될때
* 해시 파티션
	* MySQL에서 정의한 해시 함수에 의해 레코드가 저장될 파티션을 결정하는 방법이다.
	* 해시 파티션에서 파티션의 갯수는 레코드를 각 파티션에 할당하는 알고리즘과 연관되어 파티션을 추가하거나 삭제하는 작업에서는 테이블 전체의 레코드를 재분배하는 작업이 따른다. 
	* 데이터를 균등하게 나누는 것이 어려울때 
	* 테이블의 모든 레코드가 비슷한 사용 빈도를 보이지만 테이블이 너무 커서 파티션을 적용해야 할때
* 키 파티션
	* 키 파티션은 해시 파티션과 거의 비슷하다. 차이점은 키 파티션의 경우 해시 값의 계산을 MySQL 서버가 MD5() 함수를 이용해서 해시 값을 계산하고, 그 값을 MOD하여 각 파티션에 분배한다.
	
## Cluster

클러스터는 NDB(Network DataBase) 클러스터 스토리지 엔진을 사용한다. 이 엔진은 네트워크를 통해 데이터 분산을 지원하는 스토리지 엔진이다. 

### NDB 클러스터의 특징

- 무공유 클러스터링
	- NDB 클러스터는 클러스터 그룹내의 모든 노드가 아무것도 공유하지 않는 무공유 아키텍처로 구현되어 있다. NDB 클러스터는 데이터를 저장하는 스토리지가 분산되어 관리되기 때문에 한 데이터 저장소가 작동을 멈추더라도 서비스에 영향이 없다. 
- 메모리 기반의 스토리지 엔진
- 자동화된 Fail-over
- 분산된 데이터 저장소간의 동기 방식 복제

  
## Replication
데이터베이스의 데이터가 갈수록 대용량화 되어가는 것을 고려하여 확장성(Scalability)는 DBMS에서 아주 중요한 요소이다. MySQL은 확장성을 위해 다양한 기술을 제공하는데 그중 가장 일반적인 방법이 Replication이다. 

Replication은 2대 이상의 MySQL 서버가 동일한 데이터를 가지도록 동기화하는 기술이다. 하지만 Replication은 동기화가 비동기적으로 발생한다. 따라서 어떤 데이터베이스에는 데이터가 업데이트되어 있지만, 다른 데이터베이스에서는 업데이트되지 않을 수도 있다.  

일반적으로 마스터 서버는 반드시 1개이며, 슬래이브는 1개 이상으로 구성한다. 또한, 마스터와 슬레이브로 나누어지기 때문에 데이터를 변경하는쿼리는 단 하나의 데이터베이스에만 요청할 수 있다. 다시 말해서 슬레이브의 데이터를 변경하면, 마스터에 그 변경은 반영되지 않고, 동기화하는 도중 에러를 발생시키기도 한다.  

replication의 가장 큰 장점은 cluster에 비해서 값을 변경하는 쿼리가 매우 빠르게 실행된다. 그래서 주로 실시간 동기화가 필요 없는 경우 cluster대신 replication을 사용한다.

> [Cluster와 Replication의 차이](https://blog.seulgi.kim/2015/05/what-is-mysql-replication.html)
 
MySQL replication은 데이터베이스를 그대로 복사하여 데이터베이스를 한 벌 더 만드는 기능이다. 언뜻 보면 MySQL cluster와 비슷하지만, 말 그대로 분산환경을 만들어서 [single point of failure](http://en.wikipedia.org/wiki/Single_point_of_failure)를 없애려는 cluster와는 달리 MySQL replication은 단순히 데이터를 복제한다.  
  
따라서 모든 데이터가 동기화되는 cluster와는 달리, replication은 동기화가 비동기적으로 발생한다. 따라서 어떤 데이터베이스에는 데이터가 업데이트되어 있지만, 다른 데이터베이스에서는 업데이트되지 않을 수도 있다.  
  
또한, 마스터와 슬레이브로 나누어지기 때문에 데이터를 변경하는 쿼리는 단 하나의 데이터베이스에만 요청할 수 있다. 다시 말해서 슬레이브의 데이터를 변경하면, 마스터에 그 변경은 반영되지 않고, 동기화하는 도중 에러를 발생시키기도 한다.  
  
cluster와 비교하면 replication은 동기화도 보장되지 않고 쿼리를 분산할 수도 없어 cluster 대신 사용할 이유가 없어 보인다. replication은 어떤 용도로 사용될까?  
  
replication이 cluster에 비해서 가지는 가장 큰 장점은 cluster에 비해서 값의 변경이 매우 빠르다는 것이다. cluster는 값을 변경하려고 하면 클러스터 군을 이루는 다른 서버들도 값이 변경되었다는 것을 확인해 주어야 한다. 하지만 replication은 마스터의 값만 변경하면 되기 때문에, 값을 변경하는 쿼리가 매우 빠르게 실행된다.  
  
그래서 주로 실시간 동기화가 필요 없는 경우 cluster대신 replication을 사용한다.


> Statement와 PreparedStatement의 차이
Statement와 PreparedStatement의 차이를 알려면 우선 MySQL 서버가 쿼리를 처리하는 각 단계를 이해해야 한다. 아래 절차는 쿼리를 수행하기 위한 절차를 나타낸다. 

쿼리요청 -> 쿼리 분석 -> 최적화 -> 권한체크 -> 쿼리실행

자바 프로그램에서 Statement로 실행되는 쿼리는 위의 절차를 매번 걸쳐서 쿼리가 실행된다. 
하지만 PreparedStatement를 사용하면 쿼리 분석이나 최적화의 일부작업을 처음 한번만 수행해 별도로 저장해 두고, 다음 부터 요청되는 쿼리는 저장된 분석결과를 재사용한다. 이렇게 하여 매번 쿼리를 실행해야 하는 쿼리분석이나 최적화의 일부 작업을 건너띌 수 있다. 

예를 들어 아래와 같은 쿼리가 100번 실행된다고 해보자.
```
SELECT * FROM employees WHERE emp_no = 10001;
SELECT * FROM employees WHERE emp_no = 10002;
SELECT * FROM employees WHERE emp_no = 10003;
...
```
똑같은 패턴의 쿼리임에도 MySQL 쿼리는 100개의 쿼리 분석 결과를 보관해야 한다. 이런 부분을 보완하고자 PreparedStatement에서는 쿼리에 변수를 사용이 가능하다.

```
SELECT * FROM employees WHERE emp_no = ?;
```
PreparedStatement에서 "?" 바인딩 변수 또는 변수 홀더(variable holder)라고 하는데, 실제 쿼리를 실행할때는 변수 대신에 상수 값을 대입해야 한다.  이렇게 바인딩 변수를 사용하면 쿼리를 템플릿화 할 수 있고, Statement 쿼리에 비해서 쿼리 문장의 수를 대폭 줄일 수 있다. 덕분에 MySQL 서버에서 보관해야하는 정보가 줄어 메모리 사용량을 줄일 수 있다.

이렇게 **변수를 사용하는 쿼리를 바인딩 쿼리** 라고 하며, **바인딩 변수 없이 사용하는 쿼리를 동적 쿼리 또는 다이나믹 쿼리**라고 한다. 

```
conn.prepareStatement("SELECT * FROM employees WHERE emp_no=?")
```
PreparedStatement를 사용할 때는 SQL 쿼리ㅣ 문장을 이용해 PreparedStatement 객체를 준비해야 한다. 위 메서드를 실행하면 Connector는 주어진 SQL 문장을 서버로 전송해서 쿼리를 분석하고 그 결과를 저장해 둔다. 그리고 MySQL 서버는 쿼리의 분석 결과의 포인터와 같은 해시 값을 반환하고 이 값으로 PreparedStatement 객체를 생성한다. 이렇게 생성된 PreparedStatement 바인딩 변수 값만 변경하면서 계속 사용하게 된다. 

결론적으로 PreparedStatement의 성능적 장점은 한번 실행된 쿼리는 매번 쿼리 분석 과정을 거치지 않고 재사용한다는 점이다. SQL 문장의 길이가 길다면 SQL 문장 자체가 네트워크로 전송되지 않고 바인딩할 변수 값만 서버로 전달하기 때문에 네트워크 측면에서 다소 효율적이다. 또 다른 장점으로는 바이너리 프로토콜을 사용한다는 점이다.  MySQL 5.0 전에는 내부적으로MySQL서버에 쿼리를 보내기 위해서 문자열 타입으로 데이터를 변환했다. 그러다 보니 데이터의 크기가 커지는 현상이 있었는데 5.0이상에서는 PreparedStatement를 사용할때 타입변환을 하지않는 바이너리 통신 프로토콜을 사용하기 때문에 좋다. 


<!--stackedit_data:
eyJoaXN0b3J5IjpbNjQyMTE0MTYxLDM5OTgyMjU2MywxMzQ2Mj
M4MDQsLTE2MzQ4NTEzMDIsLTEzODM1OTE4OTAsODUxNDY3MDA4
LDE1MzgwODU1ODgsMTMzODU1MzY1MiwtMTg4ODc1OTk0Myw4NT
E3MjcxNSwxNjM3MzQwOTk0XX0=
-->