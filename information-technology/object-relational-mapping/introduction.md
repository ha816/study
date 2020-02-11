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

JPA는 성능 향상을 위해 지연 로딩이나 즉시 로딩과 같은 몇 가지 기법을 제공한다. 하지만 JPA가 항상 정답은 아니다. JPA를 잘못 사용하면 실행속도가 느려질 수 도 있다. 예를 들어, DBMS에 특화된 기능을 필요로 한다면, SQL 쿼리를 직접 사용하는 것이 좋을 때도 있다. 경험상 통계, 데이터 집계, 배치와 같은 작업은 JPA보다 SQL을 사용하는 것이 유리했다. 

# 클래스 설정

```
@Entity
@Table(name = "user")
public class User {
	@Id
	private String email;
	private String name;

	@Temporal(TemporalType,TIMESTAMP)
	@Column(name = "create_date")
	private Date createDate;
	
	protected User() {}
	...
}
``` 

흔히 보이는 모델 클래스와 비교해보자면 한 가지 차이가 있다 바로 JPA 애노테이션을 사용해서 DB 테이블과 매핑 정보를 설정하고 있다는 것이다. 

@Entitiy는 해당 클래스가 JPA의 엔티티임을 의미한다. JPA에서 엔티티는 DB 테이블과 매핑되는 기본 단위다. 

@Table 애노테이션은 그 클래스가 어떤 테이블과 매핑되는지 설정한다. 

DB 테이블에 프라이머리키가 있다면, JPA 엔티티에서 식별할 수 있다. @Id 애노테이션은 엔티티를 식별할 때 사용할 프로퍼티를 말한다. 대부분의 엔티티 클래스의 식별자는 DB 테이블의 프라이머리키와 매핑된다. 

@Java.Util.Date 타입을 매핑할때는 @TimeStamp 애노테이션을 사용한다. 

JPA 프로바이더는 테이블에서 읽어온 데이터로 자바 객체를 생성할때 매핑 정보를 이용한다. 
```
User user = entityManager.find(User.class, "madvirus@..."

SELECT email, name, create_date FROM user WHERe email.'madvirus@...'
```

위 코드는 JPA가 제공하는 EntitiyManager를 이용해서 User 객체를 찾는다. @Table 애노테이션을 이용해서 User 테이블을 찾고 식별자를 지정하는 @id로 Where 절에 프라이머리키를 사용한다. 

위 쿼리 결과가 나오면, JPA는 다음 과정을 거쳐 User 객체를 생성한다.

User 클래스의 인자가 없는 기본 생성자를 이용해서 User 객체를 생성한다. 

* User 클래스의 인자가 없는 기본 생성자를 이용해서 User객체 생성
* @Column 애노테이션이 없는 필드를 보고 동일한 이름을 갖는 컬럼 값을 할당한다.
	* @Temporal 애노테이션이 붙어 있으면 해당 타입으로 객체를 불어온 후, Java.Util.Date로 변환한다.


# 영속 컨텍스트와 영속 객체 개요











> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA1MzA1MTg4NCwtNDUyMTY2MTI3LC0yMT
Q2NzM2MTQ3LC0zMzM5Njg2NjBdfQ==
-->