# Overview

JPA는 Java Persistence API의 약자로 자바 객체와 DB 테이블 간의 매핑을 처리하기 위한 ORM(Object-Relational Mapping) 표준이다. 

JPA 스펙 구현체를 JPA 프로바이더라고 하며, 하이버네이트, 이클립스링크 등이 있다. 그 중 하이버네이트를 많이 들 사용하는데 그 이유는 인기가 많기 때문이다. 

#  Features

가장 큰 특징은 자바 객체와 DB 테이블 사시의 매핑 설정을 이용해서 SQL을 사용한다는 점이다. 개발자가 SQL을 작성하지 않으면서 얻을 수 있는 장점은 아래와 같다. 

* DB 컬럼과 객체의 매핑이 변경되면 설정만 변경하면 된다. 

예를 들어, member 테이블에 password를 추가했다고 하자. 
```
public class Member {
	@Column(name = "password")
	private String password;
}
```
그것을 반영하려면 위처럼 매핑 설정만 추가하면 된다. SQL 쿼리를 사용했다고 하면 member 테이블에 접근하는 INSERT, SELECT, UPDATE 쿼리를 찾아서 컬럼 추가로 인한 변경이 필요한지 확인하고 수정해야 한다. 결과적으로 SQL을 사용하는 것과 비교하면 태이블과 객체 사이의 매핑 변경이 쉽기 때문에 상대적으로 유지보수가 편하다. 

또 다른 특징으로는 객체를 위한 기능이 있다. 테이블 컬럼간의 참조 관계를 객체간의 연관으로 매핑하는 기능이 있고, 밸류 타입을 위한 매핑, 클래스 상속에 대한 매핑도 지원한다. 이는 객체 모델을 중심으로 사고하는데 도움을 준다. 

그 밖에 JPQL(Java Persistence Query Language)를 지원한다. JPQL은 SQL과 유사하지만 좀 더 객체에 초점을 둔다. 

JPA는 성능 향상을 위해 지연 로딩이나 즉시 로딩과 같은 몇 가지 기법을 제공한다.  







> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTE1OTY1OTEzMF19
-->