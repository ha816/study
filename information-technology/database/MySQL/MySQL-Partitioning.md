## Partitioning

파티셔닝이란 데이터를 테이블로 분리해서 저장하지만 사용자 입장에서는 여전히 하나의 테이블 사용하는 솔루션이다. 

테이블의 데이터가 많아진다고 무조건 파티션을 적용하는 것이 효율적인 것은 아니다. 하나의 테이블이 너무 커서 인덱스의 크기가 물리적인 메모리 보다 훨씬 크거나, 데이터 특성상 주기적인 삭제 작업이 필요한 경우 등이 파티션이 필요한 대표적인 예다. 

### Partitioning Types 

파티셔닝에는 일반적으로 **수평 파티셔닝(행으로 파티션을 나누는 방식)**과 **수직 파티셔닝(컬럼별로 파티셔닝을 나누는 방법, 특정 컬럼만을 고속 스캔할 때 유용)** 두 가지 방법이 있다. 

Shard는 도자기의 파편이라는 의미로 Sharding은 수평, 수직 파티셔닝을 모두 아우르는 말이다. 

![enter image description here](https://assets.digitalocean.com/articles/understanding_sharding/DB_image_1_cropped.png)

데이터를 파편화 하려면, 파티션샤딩 키라 불리는 키를 정하고 키를 기준으로 파티션을데이터를 나누어야 한다. **MySQL의 파티션샤딩키는 테이블에서 인덱스는 전부 로컬 인덱스에 해당한다.** 모든 인덱스는 파티션 단위로 생성되며, 파티션에 관계없이 테이블 전체 단위로 글로벌하게 하나의 통합된 인덱스는 지원하지 않는다.[참조](https://12bme.tistory.com/48)

Shard는 도자기의 파편이라는 의미로 Sharding은 수평 파티션을 말한다. 즉 Sharding은 파티션의 일부 개념이다. [참고](https://songsunbi.tistory.com/13) 또는 복합 인덱스가 걸린 필드가 될 수 있다. 
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



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM0NTExNjM1XX0=
-->