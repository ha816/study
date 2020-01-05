# MySQL



 
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
똑같은 패턴의 쿼리임에도 MySQL 쿼리는 100개의 쿼리 분석 결과를 보관해야 한다. 이럴 
부분을 보완하고자 PreparedStatement에서는 쿼리에 변수를 사용이 가능하다.

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
eyJoaXN0b3J5IjpbLTE0NzM2Njk5OTUsMTYzNzM0MDk5NF19
-->