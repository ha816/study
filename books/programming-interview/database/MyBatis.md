# MyBatis

마이바티스는 개발자가 지정한 SQL, 저장프로시저 그리고 몇가지 고급 매핑을 지원하는 퍼시스턴스 프레임워크이다. 마이바티스는 JDBC로 처리하는 상당부분의 코드와 파라미터 설정및 결과 매핑을 대신해준다. 마이바티스는 데이터베이스 레코드에 원시타입과 Map 인터페이스 그리고 자바 POJO 를 설정해서 매핑하기 위해 XML과 애노테이션을 사용할 수 있다


--- 
> MyBatis에서 #{}과 ${}의 차이는?

https://logical-code.tistory.com/25 [https://www.donnert.net/65](https://www.donnert.net/65)


MyBatis에서는 변수를 바인딩 하는데 #{}, ${}를 활용하는 방법이 있다. 

- #{}
	- PrepareStatment를 활용한다. 
	- 자동적으로 값 앞뒤로`'#{value}'` 생성되어 바인딩 된다. 
	- SQL 인젝션에 안정적이고 일반적으로 MySQL 캐시가 걸려 빠르다. 그래서 거의 항상 사용이 추천된다.
	- `SELECT NAME FROM TEST WHERE NAME=#{name}`
	- `SELECT NAME FROM TEST WHERE NAME='JOHN'`
- ${}	
	- Statment를 활용한다. 
	-  ${} 전체를 완전히 대체 한다.
	- SQL 인젝션에 위험하고 캐시를 사용하지 않기 때문에 느릴 수 있다.  사용자의 입력이 직접 들어오지 못하게 해야하거나 특수 문자들을 체크 해야 한다. 
	- 	`SELECT NAME FROM TEST WHERE SCORE=${score}`
	- 	`SELECT NAME FROM TEST WHERE SCORE= 99`  
  
그러면 언제 ${}를 써야할까? 가끔 수정이 없이 SQL Statement를 그대로 넣고 싶을때가 있다. 
예를 들어, ORDER BY나 LIKE 키워드에 변수를 넣을때는 ''스쿼트가 들어가면 안된다. 
```
LIKE %${word}%, ORDER BY ${orderAS}
```



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3MzUyMDk4MzldfQ==
-->