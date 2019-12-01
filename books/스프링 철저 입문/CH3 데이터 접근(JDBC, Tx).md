# 데이터 소스(Data Source)
데이터 소스는 애플리케이션이 데이터베이스에 접근 하는데 필요한 연결을 제공한다. 즉 추상화된 커넥션을 제공한다. 그리고 스프링에서 제공하는 데이터 소스에서는 세가지 종류가 있다. 

* 애플리케이션 모듈이 제공하는 데이터 소스
	* Commons DBCP나 Tomcat JDBC Connection Pool과 같은 서드파티나 DriverManagerDataSource같이 스프링이 제공하는 데이터 소스를 빈으로 등록해서 사용하는 방식을 말한다. 이런 방식은 데이터베이스에 접속하기 위한 ID와 패스워드, 접속 대상 URL 같은 정보를 애플리케이션이 직접 관리하고 데이터 소스에 설정해야 한다. 
* 애플리케이션 서버가 제공하는 데이터 소스
	* 애플리케이션 서버가 정의한 데이터 소스를 JNDI(Java Naming and Directory Interface)를 통해 가져와서 사용하는 방식이다. 이 방식은 데이터 베이스에 접근하기 위해 필요한 각종 정보를 애플리케이션 서버에서 제공하기 때문에 애플리케이션이 데이터 베이스 정보를 관리할 필요가 없다. 
* 내장형 데이터베이스를 사용하는 데이터 소스
	* HSQDB, H2 내장형 데이터 베이스에 접속하는 데이터 소스를 말한다. 이 방식은 사전에 데이터 베이스를 준비할 필요 없이 애플리케이션 기동시 데이터 소스의 설정과 생성이 자동으로 이루어진다. 데이터 베이스 환경을 쉽게 만들 수 있기 때문에 프로토타입을 만들거나, 업무 비즈니스 성격이 아닌 각종 지원툴이나 툴 성격의 애플리케이션 개발할때 많이 활용된다. 

## 데이터 소스 설정 

데이터 소스를 활용하려면 메이븐의 pom.xml에 의존관계를 정의하면 된다.
```
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-jdbc</artifactId>
</dependency>
```

### 애플리케이션 모듈이 제공하는 데이터 소스

Commons DBCP, DriverManagerDataSource를 데이터 소스로 사용할때 설정 방식을 알아보자. JDBC 접속에 필요한 각종 정보와 커넥션 풀의 설정 등은 jdbc.properties에 기재되어 있다고 가정한다. 
``` //jdbc.properties
database.url = jdbc:mysql://localhost
database.driverClassName = org.mysql.Driver
cp.maxTotal = 100; //connection pool
...
```

자바 기반 설정방식
```
@Configuration
@PropertySoucre("jdbc.properties")
public class PoolingDateSourceCofig {
	@Bean(destoryMethod = "close")
	public DataSource dateSource(
		@Value("${database.driverClassName}") String driverClassName,
		@Value("${database.url}") String url,
		...
		) {
		BasicDataSoucre dataSource= new BasicDataSource();
		dataSource.setDriverClassName(driverClassName);
		...
		return dataSource;
	}
}
```
XML 기반 설정방식
```
<beans xlms =... >
	<context:propery-placeholder location="classpath:META-INF/jdbc.properties"/>

	<bean id = "dataSource" class = "org.apache,commons.dbcp2.BasicDataSource" destory-method="close">
		<property name="driverClassName" value="${database.driverClassName}"/>
		<property name="url" value="${database.url}"/>
		...		
	</bean>
</beans>
```

### 애플리케이션 서버가 제공하는 데이터 소스

애플리케이션 서버에 'jdbc/mydb'라는 JNDI명으로 커넥션 풀이 만들어져 있다고 하자. 

자바 기반 설정방식
```
@Configuration
public class JndiDateSourceCofig {
	@Bean
	public DataSource dateSource(){
		JndiTemplate jndiTemplate = new JndiTempate();
		return jnditemplate.lookup("java:comp/env/jdbc.mydb", DataSource.class)		
	}
}
```
lookup 메서드를 통해서 JNDI 명이 "java:comp/env/jdbc.mydb"인 리소스(데이터소스)를 찾아온다. 
XML 기반 설정방식
```
<beans xlms =... >
	<context:propery-placeholder location="classpath:META-INF/jdbc.properties"/>
	<jee:jndi-lookup id="dataSource" jndi-name="java:comp/env/jdbc/mydb" />
</beans>
```
```<jee:jndi-lookup>```요소로 JNDI를 통해 찾아올 데이터 소스를 정의한다. 

# 스프링 JDBC

스프링 JDBC(Spring JDBC)를 사용할 때 중요한 역할을 하는 JdbcTemplate 클래스를 공부해보자. 스프링 JDBC는 SQL의 내용과는 무관하게 공통적이면서 반복적으로 수행되는 JDBC의 처리를 프레임워크가 대행하는 기능을 제공한다. 여기서 말하는 공통적이고 반복적으로 수행되는 처리에는 아래와 같은 것이 있다. 

* connection 연결과 종료
* SQL문 실행
* SQL문의 실행 결과인 행에 대한 반복 처리
* 예외처리 

스프링 JDBC를 쓰면 위의 공통적이고 반복적인 작업을 거의 모두 처리해준다. 처리된 내용을 제외하면 개발자가 직접 구현할 부분은 아래와 같다. 

* SQL문 정의
* 파라미터 설정
* ResultSet의 결과를 가져온 후, 필요한 처리 

## JdbcTemplate 

스프링 JDBC는 JdbcTemplate, NamedParameterJdbcTemplate와 같이 SQL만 가지고도 데이터베이스를 쉽게 다를 수 있게 도와주는 클래스를 제공한다. 특히 NamedParameterJdbcTemplate는 사실상 데이터를 조작하는 처리를 JdbcTemplate에 위임하게 되는데, 이 둘의 차이는 JdbcTemplate가 데이터 바인딩시 '?'문자를 플레이스 홀더로 쓰는 반면, NamedParameterJdbcTemplate는 바인딩시 파라미터 이름을 쓸수가 있어서 '?'를 좀더 직관적으로 다룰 수 있게 해준다. 

JdbcTemplate을 애플리케이션에서 사용할때는 DI 컨테이너에서 만든 JdbcTemplate을 주입받아 쓰는게 일반적이다. 

JdbcTemplate이 제공하는 주요 메서드

|메서드명| 설명 |
|--|--|
|queryForObject  | 하나의 결과 레코드 중에서 하나의 칼럼 값을 가져올때 사용함; RowMapper아 함께 사용하면 하나의 레코드 정보를 객체에 매핑할 수 있음 |
|queryForMap  | 하나의 결과 레코드 정보를 Map형태로 매핑할 수 있음 |
|queryForList  | 여러개의 결과레코드를 다룬다. List의 한 요소가 레코드에 대응|
|query | ResultSetExtractor, RowCallbackHandler와 함께 조회할때 사용|
|update  | 데이터를 변경하는 SQL(Insert, DELETE, UPDATE)를 실행할때 사용|

### DAO 클래스 구현

데이터배이스에 접근해서 데이터를 다룰 DAO(Data Access Object) 클래스를 정의해보자. 여기선 JdbcTemplate 클래스를 사용하기 위해 빈 컨테이너가 초과해둔것을 @Autowired로 주입받아 사용한다.

```
@Component
public class JdbcRoomDao {
	@Autowired
	JdbcTemplate jdbcTemplate;
	public int findMaxCapacity() {
		String sql = "select MAX FROM ";
		return jdbcTemplate.queryForObject(sql, Integer.class);
	}
}
```

XML 기반 설정방식으로 JdbcTemplate 정의
```
<beans xlms =... >
	<import resource="classpath:jdbc.properties" />
	<context:component-scan base-package="com.example" /> // JdbcRoomDao를 스캐닝하기 위해
	<bean id="jdbcTemplate" class="org.spring.framework.jdbc.core.JdbcTemplate"/>
		<property name="dataSource" ref="dataSource" />
	</bean>
</beans>
```
위 코드를 완료 하면 jdbc.properties에 있는 정보로 dataSource빈이 생성된다. 그 빈을 DAO 인 JdbcRoomDao에 Autowired로 주입한다. 

DAO 클래스응 이용한 데이터 조회
```
	ApplicationContext context = new ClassPathXmlApplicationContext("JdbcTemplateConfig.xml);
	JdbcRoomDao dao = context.getBean("jdbcRoomDao", JdbcRoomDao.class);
	int maxCapacitiy = dao.findMaxCapacity();
```

### SQL 질의 결과를 POJO로 변환

스프링 JDBC는 처리 결과값을 자바가 기본적으로 제공하는 데이터 타입이나 Map, List 같은 컬렉션 타입으로 반환한다. 보통 애프리케이션을 개발할 때는 자바에서 제공하는 타입이 아니라 비즈니스에 맞는 데이터 타입을 POJO형태로 만들어 쓰는 경향이 있기 때문에 반환 값을 가공해야할 수 있다. 

Spring JDBC에서는 기본적인 결과값을 형태 변환하기 쉽도록 아래 세 가지 인터페이스를 제공한다. 

RowMapper
: dJDBC의 ResultSet을 순차적으로 읽으면서 원하는 POJO형태로 매핑하고 싶을때 사용한다. ResultSet의 한 행을 읽어 하나의 POJO로 변환할 수 있다. 뒤에 나오는 ResultSetExtractor과 차이는 ResultSet의 다음 해으로 넘어가는 커서를 스프링 프레임워크가 대신한다는 점이다. 즉 특정 행을 자유롭게 이동할수가 없다.

ResultSetExtractor
: RowMapper와 마찬가지로 원하는 POJO형태로 매핑을 하고 싶을때 사용한다. RowMapper와 차이점은 여러 행을 자유롭게 이동할 수 있다는 점이다. 그래서 ResultSetExtractor는 여러 행에서 필요한 값을 꺼내서 POJO로 만들수 있다.

RowCallbackHandler
: 이 인터페이스는의 메서드는 반환값이 없다. 그래서 ResultSet 정보를 처리결과를 반환하기 위해 쓰는것이 아니라 별도의 다른 처리를 하고 싶을때 사용한다. ResultSet에 읽은 데이터를 파일형태나, 조회된 데이터를 검증하는 용도로 활용할 수 있다. 

## 트랜잭션 관리

관계형 DB를 다룰때 트랜잭션의 경계가 어디까지이고 어떻게 관리되는지 이해하고 있어야 한다. 실제로 트랜잭션을 염두에 두고 관련 코드를 구현하는 것은 상당히 힘들도 번거로울 수 있는데, 다행이 스프링은 이를 도와주는 기능이 있다. 

스프링 트랜잭션 처리에 중심이 되는 인터페이스는 PlatformTransactionManager이다. 이 인터페이스는 트랜잭션 처리에 필요한 API를 제공하며 개발자가 API를 호출하여 트랜잭션 조작이 가능하다. PlatformTransactionManager은 트랜잭션 관리의 구현 방식을 추상화하기 위한 인터페이스이기 때문에 다른 종류의 트랜잭션을 사용하더라도 같은 API를 쓸 수 있다. 

스프링 프레임 워크에서는 다양한 환경가 제품에 대응하는 PlatformTransactionManager의 구현 클래스를 제공한다. 대표적인 클래스는 아래와 같다. 

|클래스명| 설명  |
|--|--|
|DataSourceTransactionManager  | JDBC, MyBatis등 JDBC 기반 라이브러리로 데이터베이스에 접근하는 경우 사용  |
|HibernateTransactionManager  | 하이버네이트를 이용해 데이터베이스에 접근하는 경우 사용  |
|JpaTransactionManager  | JPA로 데이터베이스에 접근하는 경우 사용  |
|JtaTransactionManager  | JTA로 데이터베이스에 접근하는 경우 사용 |

### 트랜잭션 관리자 정의(TransactionManager)

트랜잭션 관리자를 사용할때는 두 가지 작업을 해야 한다. 
* PlatformTransactionManager의 빈을 구현한다.
* 트랜잭션을 관리하는 메서드를 정의한다. 

**로컬 트랜잭션(Local Transaction)** 은 단일 데이터 저장소에 대한 트랜잭션으로 일반적으로 자주 사용되는 트랜잭션이다. 예를 들어, 단일 데이터 저장소에 대한 여러 조작을 하나의 논리적 단위로 처리하고 싶을때 사용한다. 이럴 경우, **DataSourceTransactionManager**를 사용한다. 이때 빈 ID는 transactionManager로 사용하는 것이 좋다. 왜냐하면 스프링 프레임워크에서 기본적으로 트랜잭션 관리자의 빈 ID를 transactionManager로 가정하기 때문이다. 이 관례를 따르면 각종 설정을 간단히 할 수 있다. 

XML 기반 설정 방식을 이용한 빈 정의
```
<bean id="transactionManager"
	class="org.springframework.jdbc.datasource.DateSourceTransactionManager">
	<property name="dataSource" ref="dataSource"
</bean>
// @Transaction 애너테이션을 사용하는 경우
<tx:annotation-driven>
```

글로벌 트랜잭션은 여러 데이터 저장소를 걸쳐서 적용되는 트랜잭션이다. 













> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1NjU3MDA4NTgsNjc5ODc1NDI1LDExOD
A1ODczODgsMTM2MDEzMjkyNCwtNjA3Nzk1MjI4LC00NDQyODI2
NjQsMTU3NzcxNTYyMywtMjAwNzk4NjA4MSwtMjUxOTE5NDM2LC
03ODgwMjAxNDAsMzgyMjU0MjgwLC01NjIzNDU5MDksODA0NjQ1
ODQwLDEwNTg5NTE3MzBdfQ==
-->