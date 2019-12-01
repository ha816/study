# 데이터 소스(Data Source)
데이터 소스는 애플리케이션이 데이터베이스에 접근하기 위한 추상화된 연결 방식을 제공하는 역할을 한다. 즉 커넥션을 제공한다. 그리고 스프링에서 제공하는 데이터 소스에서는 세가지 종류가 있다. 

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
<beans
	xlms=...
	...
	<context:component-scan base-package="com.example.demo" />
</beans>
```
# 스프링 JDBC

스프링 JDBC(Spring JDBC)를 사용할 때 중요한 역할을 하는 JdbcTemplate 클래스를 공부해보자. 


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbODQ4NzI1NjhdfQ==
-->