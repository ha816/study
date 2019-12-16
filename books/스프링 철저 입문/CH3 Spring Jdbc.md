# 데이터 소스(Data Source)
데이터 소스는 애플리케이션이 데이터베이스에 접근 하는데 필요한 연결을 제공한다. 즉 추상화된 커넥션을 제공한다. 그리고 스프링에서 제공하는 데이터 소스에서는 세가지 종류가 있다. 

* 애플리케이션 모듈이 제공하는 데이터 소스
	* Commons DBCP 나 Tomcat JDBC Connection Pool과 같은 서드파티나 DriverManagerDataSource같이 스프링이 제공하는 데이터 소스를 빈으로 등록해서 사용하는 방식을 말한다. 이런 방식은 데이터베이스에 접속하기 위한 ID와 패스워드, 접속 대상 URL 같은 정보를 애플리케이션이 직접 관리하고 데이터 소스에 설정해야 한다. 
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

DriverManagerDataSource를 데이터 소스로 사용할때 설정 방식을 알아보자. JDBC 접속에 필요한 각종 정보와 커넥션 풀의 설정 등은 jdbc.properties에 기재되어 있다고 가정한다. 
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

스프링 JDBC는 SQL의 내용과는 무관하게 공통적이면서 반복적으로 수행되는 JDBC의 처리를 프레임워크가 대행하는 기능을 제공한다. 여기서 말하는 공통적이고 반복적으로 수행되는 처리에는 아래와 같은 것이 있다. 

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
: DBC의 ResultSet을 순차적으로 읽으면서 원하는 POJO형태로 매핑하고 싶을때 사용한다. ResultSet의 한 행을 읽어 하나의 POJO로 변환할 수 있다. 뒤에 나오는 ResultSetExtractor과 차이는 ResultSet의 다음 해으로 넘어가는 커서를 스프링 프레임워크가 대신한다는 점이다. 즉 특정 행을 자유롭게 이동할수가 없다.

ResultSetExtractor
: RowMapper와 마찬가지로 원하는 POJO형태로 매핑을 하고 싶을때 사용한다. RowMapper와 차이점은 여러 행을 자유롭게 이동할 수 있다는 점이다. 그래서 ResultSetExtractor는 여러 행에서 필요한 값을 꺼내서 POJO로 만들수 있다.

RowCallbackHandler
: 이 인터페이스는의 메서드는 반환값이 없다. 그래서 ResultSet 정보를 처리결과를 반환하기 위해 쓰는것이 아니라 별도의 다른 처리를 하고 싶을때 사용한다. ResultSet에 읽은 데이터를 파일형태나, 조회된 데이터를 검증하는 용도로 활용할 수 있다. 

## 트랜잭션 관리

관계형 DB를 다룰때 트랜잭션의 경계가 어디까지 이고 어떻게 관리되는지 이해하고 있어야 한다. 실제로 트랜잭션을 염두에 두고 관련 코드를 구현하는 것은 상당히 힘들도 번거로울 수 있는데, 다행이 스프링은 이를 도와주는 기능이 있다. 

### PlatformTransactionManager 인터페이스

스프링 트랜잭션 처리에 중심이 되는 인터페이스는 PlatformTransactionManager이다. 이 인터페이스는 트랜잭션 처리에 필요한 API를 제공하며 개발자가 API를 호출하여 트랜잭션 조작이 가능하다. PlatformTransactionManager은 트랜잭션 관리의 구현 방식을 추상화한 인터페이스이기 때문에 다른 종류의 트랜잭션을 사용하더라도 같은 API를 쓸 수 있다. 

스프링 프레임 워크에서는 다양한 환경과 제품에 대응하는 PlatformTransactionManager의 구현 클래스를 제공한다. 대표적인 클래스는 아래와 같다. 

|클래스명| 설명  |
|--|--|
|DataSourceTransactionManager  | JDBC, MyBatis등 JDBC 기반 라이브러리로 데이터베이스에 접근하는 경우 사용  |
|HibernateTransactionManager  | 하이버네이트를 이용해 데이터베이스에 접근하는 경우 사용  |
|JpaTransactionManager  | JPA로 데이터베이스에 접근하는 경우 사용  |
|JtaTransactionManager  | JTA로 데이터베이스에 접근하는 경우 사용 |

### 트랜잭션 데이터 저장소 범위

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

**글로벌 트랜잭션**은 여러 데이터 저장소를 걸쳐서 적용되는 트랜잭션이다. 예를 들면 여러 데이터 베이스를 묶되, 각 데이터 베이스에서 각각의 조작을 수행하고 그 조작들을 하나의 트랜잭션으로 묶어 모두 성공하거나 모두 실패할 것으로 처리해야 하는 경우라면 로컬 트랜잭션을 사용할 수 없다. 이런 경우에는 글로벌 트랜잭션은 JTA(Java Transaction API)라는 Java EE 사양으로 표준화 되어 있고 JtaTransactionManager를 사용하면 된다. 

### 선언적 트랜잭션

선언적 트랜잭션은 미리 선언된 룰에 따라 트랜잭션을 제어하는 방법이다. 선언적 트랜잭션의 장점은 정해진 룰을 준수함으로써 **트랜잭션의 시작과 커밋, 롤백 등의 처리를 비즈니스 로직안에 서술할 필요가 없다는 점이다.**

스프링 프레임워크는 선언적 트랜잭션으로 @Transactional 또는 XML설정을 이용하는 두 방법을 제공한다. 

#### @Transactional 이용한 선언적 트랜잭션

@Transactional 에너테이션을 빈의 public 메서드에 추가하는 것으로 대상 메서드의 시작 종료에 맞춰 트랜잭션 시작, 커밋, 롤백이 가능하다. 또한 기본적으로 메서드 안에서 비검사 예외(unchecked exception; runtimeException)가 발생하면 처리가 중단되었을때 자동으로 롤백을 한다. 

@Transactional의 동작방식이나 상세한 설정을 변경하고 싶으면 변경이 가능하다. [옵션 정보](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/annotation/Transactional.html)

대표적인 예제
* @Transactional(readOnly = true, timeout=60)
	* 기본 트랜잭션 관리자를 읽기 전용 트랜잭션으로 이용하고 제한시간은 60초다.
* @Transactional("tx1")
	* "tx1" id를 가지는 트랜잭션 관리자를 사용한다.
* @Transactional(value = "tx2", propagation=Propagation.REQUIRES_NEW)
	* tx2 트랜잭션 관리자를 사용하고 전파방식은 REQUIRES_NEW이다. 

@Transactional의 사용법
@Transactional애너테이션은 클래스와 메서드에 부여할 수 있다. 차이는 애너테이션이 적용되는 범위이다. 

클래스에 적용하면 클래스에 어떤 메서드를 실행해도 기본적으로 트랜잭션이 적용된다.  클래스에 적용하지 않고 특정 메서드에만 붙이면 그 메서드만 트랜잭션이 적용된다. 클래스와 메서드에 모두 붙이면 메서드에 정의한 애너테이션을 따라간다. 

#### XML설정을 이용한 선언적 트랜잭션

XML설정에서는 ```<tx:advice>``` 요소와 같은 **tx로 시작하는 트랜잭션 전용 XML 스키마를 사용한다.**

동작 로직은 @Transactional을 이용한 트랜잭션과 같지만 차이점은 @Transactional 애너테이션이 어이데오 부여되지 않았다는 점이다. @Transactional 과 마찬가지로 TransactionalManager 빈을 정의한다. 

```
<beans ...>
	<import resource="classpath:java.properties">
	<bean id="transactionManager" class="org. ... DataSourceTransactionManager">
		<property name = "dataSource" ref="dataSource" />
	</bean>
	
	<tx:advice id="txAdvice">
		<tx:attributes>
			<tx:method name="get*" readonly="true">
			<tx:method name="*">
		</tx:attributes>
	</tx:advice>

	<aop:config>
		<aop:pointcut id="txPointcut" expression="execution(* com.example.RooServiceXmlImpl.*(..))"/>
		<aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut" />
	</aop:config>
	
	<bean id="roomService class="com.example.RooServiceXmlImpl">
		<property name='roomDao' ref="jdbcRoomDao" />
	</bean>

	<bean id="jdbcRoomDao" class="com.example.RoomServiceXmlImpl">
		<property name="dataSource" ref="dataSource" />
	</bean>
</beans>
```

```<tx:Advice>``` 요소를 이용해서 트랜잭션 정의에 관한 어드바이스를 정의한다. 그리고 ```<tx:attributes>```를 이용해서 트랜잭션 관리 대상이 되는 메서드 정의 및 트랜잭션 설정을 수행한다. 메서드 명이 get으로 시작하는것은 읽기 전용 그 외에는 쓰기 가능한 트랜잭션으로 정의한다. 

```<aop:config>```를 이용해서 포인트컷과 어드바이저를 정의한다. 포인트 컷은 인터페이스 구현 클래스의 메서드를 대상으로 한다. 어드바이저는 앞서 ```<tx:Advice>``` 에서 정의한 어드바이스와 포인트 컷을 조합한다. 

나머지 인터페이스 구현 클래스의 빈을 정의한다.

### 명시적 트랜잭션

명시적 트랜잭션이란 커밋이나 롤백 같은 트랜잭션 처리를 코드에 직접 기술하는 방법이다. 메서드 단위보다 더 작은 단위로 트랜잭션을 제어하고 싶거나 선언전 트랜잭션으로 표현하기 어려운 섬세한 제어를 할때 이 방법을 쓴다. 
스프링 프레임워크는  명시적 트랜잭션을 이용하는 방법으로 PlatfromTransactionManager(TransactionManager들의 인터페이스)와 TransactionTemplate을 사용하는 두가지 방법을 제공한다.

PlatfromTransactionManager을 이용한 명시적 트랜잭션 제어
```
@Autowired
PlatfromTransactionManager txManager;

@Autowired
jdbcDao dao;

public void doTransaction(){
	DefaultTransactionDefinition def = new DefaultTransactionDefinition();
	def.setName("transactionName);
	def.setReadOnly(false);
	def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED)
	TransactionStatus status = txManager.getTransaction(def);
	try {
		// do Something
	} catch (Exception e){
		txManager.rollback(status);
		throw new DateAccessException("error oucucred");
	}
	txManager.commit(status);
}
```
TransactionTemplate을 이용한 명시적 트랜잭션 제어

TransactionTemplate을 이용하면 PlatfromTransactionManager 보다도 구조적으로 트랜잭션 제어를 기술할 수 있다. 

```
@Autowired
TransactionTemplate transactionTemplate;

@Autowired
jdbcDao dao;

public void doTransaction(){
	transactionTemplate.execute(new TransactionCallbackWithoutResult(){
		@Ovveride
		protected void doInTransactionWithoutResult(Transaction status){
			
		}
	}
}
```

TransactionTemplate 자바 기반 설정 방식

```
@Configuration          .    트랜잭션  데이터    

	@Bean
	public TransactionTemplate transactionTemplate(PlatformTransactionManager transactionManager) {
	TransactionTemplate transactionTemplate = new TransactionTemplate(transactionManager);
	transactionTemplate .setIsolationLevel(...)
	return transactionTemplate;
}
```

### 격리 수준과 전파방식(Isolation level, Propagation)

>트랜잭션 격리 수준(Transaction Isolation Level)

참조하는 데이터스프링 프레임 워크에서는 데이터베이스의 기본 설정(DEFAULT)과 4개의 트랜잭션 격리 수준을 이용할수 있다. 스프링 프레임워크에서 지원하는 격리 수준은 아래와 같다. 다만 지원하는 모든 격리 수준이 실제로 사용할 수 있는지는 사용하는 데이터베이스를 어떻게 구현했느냐에 따라 달라질 수 있다. 

| 격리수준| 설명 | Dirty Read, Unrepeatable Read, Phantom Read |
|--|--|--|
| DEFAULT  | 사용하는 데이터베이스의 기본 격리수준을 사용  |
| READ_UNCOMMITTED| 더티리드(Dirty Read), 반복되지 않은 읽기(Unrepeatable Read), ad)가 발생한다. 이 격리 수준은 커밋되지 않은 변경 데이터를 다른 트랜잭션에서 참조하는 것을 허용한다. 만약 변경 데이터가 롤백된 경우 다음 트랜잭션에서는 무효한 데이터를 조회하게 된다. | O, O, O|
| READ_COMMITTED| 더티리드(Dirty Read)는 방지하지만, 반복되지 않은 읽기(Unrepeatable Read), 팬텀 읽기(Phantom Read)는 발생한다. 이 격리 수준은 커밋되지 않은 변경 데이터를 다른 트랜잭션에서 참조하는 것을 금지한다. |X, O, O|
| REPEATABLE_READ| 더티리드, 반복되지 않은 읽기를 방지하지만 팬텀읽기는 발생한다. |X, X, O|
| SERIALIZABLE| 더티리드, 반복되지 않은 읽기, 팬텀읽기를 방지한다.|X, X, X| 

더티리드(Dirty Read)
: A dirty read occurs when a transaction is allowed to read data from a row that has been modified by another running transaction and not yet committed. Dirty Read also knwo as **write–read conflict**, **reading uncommitted data**.
더티리드는 한 트랜잭션이 아직 커밋하지 않은 상태로 수정 중일때 데이터 row 읽기가 가능하다면 발생한다. 

$$
\begin{bmatrix}T1&T2\\R(A)&\\W(A)&\\&R(A)\\&W(A)\\&R(B)\\&W(B)\\&Com.\\R(B)&\\W(B)&\\Com.&\end{bmatrix}
$$

T2 could read a database object A, modified by T1 which hasn't committed. This is a **dirty read**. T1 may write some value into A which makes the database inconsistent. It is possible that interleaved execution can expose this inconsistency and lead to inconsistent final database state, violating ACID rules.
더티리드는 한 트랜잭션이 아직 커밋하지 않은 상태로 수정 중일때 데이터 row 읽기가 가능하다면 발생한다. 예를 들면, TX1에서 가져온 row 데이터가 있고, TX2에서 커밋하지 않은 상태로 데이터를 수정했다. 커밋되지 않은 상태로 TX1이 동일한 row 데이터를 가져오면 수정된 데이터를 가져온다. 이때 TX2에서 롤백이 발생한다면, TX1에서 가져온 수정된 데이터는 잘못된 데이터가 된다. 

반복되지 않은 읽기(Unrepeatable Read)
: A non-repeatable read occurs, when during the course of a transaction, a row is retrieved twice and the values within the row differ between reads. 
반복되지 않는 읽기는 한 트랜잭션의 과정 중에서, 한 데이터가 두번 읽기가 되고 읽기 작업간에 데이터 값이 달라질때 발생한다. 이는 read-write conflict 라고도 한다.

$$\begin{bmatrix}
T1 & T2 \\
R(A) & \\
&R(A) \\
&W(A) \\
&Commit \\
R(A) &\\
W(A)&\\
Commit&\\
\end{bmatrix}$$

T1 has read the original value of A, and is waiting for T2 to finish. T2 also reads the original value of A, overwrites A, and commits.

However, when T1 r row 복구가 두번이 되고 데이터 row 안에 값들이 읽는 과정에서 달라지는 상황에 발생한다. 
반복되지 않는 읽기 현상은 아마 락 기반 동시성 제어 상황에서 발생할지도 모른다. 읽기 락이 얻어지지 않은 상태에서 select를 실행하거나 또는 복수의 row에 영향을 주는 얻어진 락이 SELECT 동작이 수행되자마자 풀려버린 경우에
반복되지 않는 읽기는 한 트랜잭션이 커밋 컨플릭에 노출되면 반드시 롤백하라는 요구사항이 있다면 이를 편하게 해줄수도 있다.

예를 들어 TX1에서 과거 한 row의 데이터 

Transaction 2 commits successfully, which meadns from A, it discoverthat its changes two different versions of A, anthe row with id T1 wshould be forced to  abort, because T1 would not know what to do.


팬텀 읽기(Phantom Read)
: A  phantom read  occurs when, in the course of a transaction, new rows are added or removed by another transaction to the records being read. The phantom reads anomaly is a special case of **Non-repeatable reads** when Transaction 1 repeats a ranged  _SELECT ... WHERE_  query and, between both operations, Transaction 2 creates Insert new rows (in the target table) which fulfil that  WHERE  clause.
 
$$\begin{bmatrix}
T1 & T2 \\
Select(T) & \\
&Insert(T) \text{ or } Delete(T) \\
Select(T) &\\
\end{bmatrix}$$

T1이 Select 쿼리로 테이블 T에서 가져온 결과가 있는데, T2가 같은 테이블에 Insert 또는 Delete 쿼리를 한다면, 두 번재 Select 쿼리는 결과가 달라질 수 있다. 

--- 

>트랜잭션 전파 방식(Propagation)
> 트랜잭션 전파 방식은 트랜잭션 경계에서 트랜잭션에 참여하는 방법을 결정한다. 

트랜잭션 전파 방식을 고려해야하는 경우는 트랜잭션 경계가 중첩될때다. **여러개의 트랜잭션 경계가 중첩되지 않았다면 트랜잭션을 순차적으로 제어만 하면 되기 때문에 굳이 전파 방식을 의식할 필요는 없다.** 

트랜잭션 관리 대상이 되는 메서드 안에서 또 다른 트랜잭션 관리 대상 되는 메서드를 호출한 경우에는 트랜잭션의 전파 방식을 고려해야 한다. 예를 들어, 두 메서드가 독립적으로 처리될지 혹은 같은 트랜잭션의 관리 범위에 들어가는지는 설정한 전파 방식에 따라 좌우된다. 

스프링 프래임 워크에서는 7가지 트랜잭션 전파 방식을 이용할 수 있다. 기본적인 전파 방식은 REQUIRED이고 바꾸고 싶다면 @Transactional에서 propagtion의 속성을 바꾸면 된다. 

|전파방식|설명|
|--|--|
|REQUIRED | 이미 만들어진 트랜잭션이 존재하면 해당 트랜잭션 안으로 들어가 함께 처리한다. 이미 만들어진 트랜잭션이 없다면 새로운 트랜잭션을 만든다.|
|REQUIRES_NEW| 이미 만들어진 트랜잭션에 들어가지 않고 반드시 새로운 트랜잭션을 만든다. 이미 만들어진 트랜잭션은 보류가 되어 새로운 트랜잭션의 작업이 끝날때 까지 기다려야 한다. |
| MANDATORY| 이미 만들어진 트랜잭션 범위 안에 들어가야 한다. 만약 기존에 만들어진 트랜잭션이 없다면 예외가 발생한다. |
| NEVER | 트랜잭션 관리를 하지 않는다. 이미 만들어진 트랜잭션이 있으며 예외가 발생  |
|NOT_SUPPORTED| 트랜잭션을 관리하지 않는다. 만약 이미 만들어진 트랜잭션이 있다면 이전 트랜잭션이 끝나는 것을 기다려야 한다.|
|SUPPORTS| 이미 만들어진 트랜잭션이 있다면 그 범위 안에 들어가고, 만약 트랜잭션이 없다면 트랜잭션 관리를 하지 않는다.|
|NESTED|REQUIRED와 마찬가지로 트랜잭션이 존재하지 않으면 새로 만들고 이미 존재하면 계속 이용한다. 하지만 NESTED가 적용된 구간은 중첩된 트랜잭션 처럼 취급한다. NESTED안에서 롤백이 발생한 경우 NESTED 구간 안의 처리 내용은 모두 롤백 되지만 NESTED 구간 밖에 실행된 내용은 롤백 되지 않는다. 단 부모 트랜잭션에서 롤백되면 NESTED 구간의 트랜잭션은 모두 롤백된다.|

호출자 -> 트랜잭션 경계 -> 트랜잭션 메서드1 -> 트랜잭션 경계 -> 트랜잭션 메서드2

각 트랜잭션 메서드는 자신의 트랜잭션 경계에서 전파 설정에 따라 트랜잭션을 진행한다. 트랜잭션 메서드1 과 2 모두 REQUIRED라면 메서드 1에서 새로운 트랜잭션을 만들고 트랜잭션 2의 트랜잭션 경계에서는 만들어진 트랜잭션이 있다는 걸을 알고 같은 트랜잭션으로 묶여 처리가 된다. 

만약 트랜잭션 메서드1 과 2 모두 REQUIRED_NEW라면 메서드1에서 새로운 트랜잭션을 만들고 트랜잭션 2 경계에서도 새로운 트랜잭션을 만들어 처리한다. 둘다 독립적인 트랜잭션이기 때문에 트랜잭션 2에서 예외가 발생해도 트랜잭션 메서드 1은 롤백이 되지 않는다. REQUIRED_NEW는 메서드가 항상 새로운 트랜잭션에서 동작하도록 보장할때 쓴다. 

MANDATORY의 경우, 만들어진 트랜잭션 메서드 1의 경계에서 부터 이미 만들어진 트랜잭션이 없기 때문에 예외가 발생한다. (의무적인, 위임된, 강압적인)

NEVER, NOT_SUPPORTED는 모두 트랜잭션을 관리하지 않는다. 트랜잭션 메서드1의 경계에서 만들어진 트랜잭션이 존재하고 트랜잭션 메서드 2가 NEVER라면 트랜잭션을 절대 쓰지 않는다로 설정된 트랜잭션 메서드2를 호출하였으므로 예외가 발생한다. 

NOT_SUPPORTED일 경우,  트랜잭션처리를 하지 않는 메서드라는 의미이다. 만약 이미 트랜잭션이 있다면 그 트랜잭션은 NOT_SUPPORTED 메서드를 호출할때 보류된다. 호출한 메서드가 끝나면 원래 트랜잭션은 재개 된다. NOT_SUPPORTED는 트랜잭션이 불필요할때 사용하는데, 트랜잭션이 오버헤드를 가지기 때문에 이것이 성능을 개선할수도 있기 때문이다. 

SUPPORTED 메서드라면 만들어진 트랜잭션이 존재하면 그 트랜잭션과 함께 처리되고 아니라면 트랜잭션 처리가 되지 않는다. 메서드의 트랜잭션 행위는 매우 다양하기 때문에, SUPPORTED는 매우 조심히 써야한다. 

NESTED 메서드라면 REQUIRED와 매우 유사하다. 하지만 NESTED 메서드 구간안에서는 중첩된 트랜잭션 취급하게 된다. NESTED 구간안에서 롤백이 되면 NESTED 구간안의 모든 내용은 롤백 되지만 구간 밖에 내용은 롤백 되지 않는다. 즉 REQUIRED는 어떤 구간에서 예외가 발생하든 모두 롤백이 되지만, NESTED는 come visible. However, Transaction 1 has already seen a different value for _age_ in that row. At the SERIALIZABLE and REPEATABLE READ isolation levels, the DBMS must return the old value for the second SELECT. At READ COMMITTED and READ UNCOMMITTED, the DBMS may return the updated value; this is a non-repeatable read.


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjg5MzU0MTMzLDg4OTcyMTYxNywtNzE0OT
Q0ODgyLC0xMDI0NDg4NjgyXX0=
-->