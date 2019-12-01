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
|queryForObject  |  |





> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTgwODM1MDA3NCwtMjUxOTE5NDM2LC03OD
gwMjAxNDAsMzgyMjU0MjgwLC01NjIzNDU5MDksODA0NjQ1ODQw
LDEwNTg5NTE3MzBdfQ==
-->