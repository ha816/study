# DI 개요

일반적으로 하나의 애플리케이션이 동작하기 위해선 여러 컴포넌트를 통합해서 사용한다. 특정 컴포넌트를 사용할때 한 클래스에서 구현 클래스를 직접 생성해서 할당하면 두 클래스간의 결합도가 높아진다. 많은 컴포넌트에 의존해야 하는 클래스가 이 같은 방식으로 개발하는 건 큰 문제가 생길 수 있다. 

이 결합도를 낮추는 방법으로 **의존성 주입**이라는 접근방식이 큰 힘을 발휘한다.  클래스의 외부에서 컴포넌트를 생성 한 후, 내부에 주입하여 사용가능하게 만드는 과정을 **의존성 주입 또는 인젝션(injection)**이라 한다.  

DI는 IoC(Inversion of Controll)과 혼용되어 사용되는데 IoC는 인스턴스를 제어하는 주도권이 역전된다는 의미이다. 컴포넌트를 구성하는 인스턴스의 생성과 의존관계를 해당 클래스가 아닌 DI 컨테이너가 대신해주기 때문이다. 즉 DI를 하다보니 IoC 패턴이 따라온 것으로 이해하면 편하다. 

## DI 컨테이너

**의존성 주입**을 처리하는 주체를 **DI 컨테이너**라고 한다. DI 컨테이너를 통해 각 컴포넌트의 인스턴스를 생성하고 통합 관리하면서 얻을 수 있는 장점은 컴포넌트간의 의존성 해결 뿐만이 아니다. 

어떤 컴포넌트는 싱글턴 객체로 만들어야 하고 어떤 컴포넌트는 매번 필요할때마다 새 인스턴스를 사용하도록 프로토 타입(prototype) 객체를 만들어야 한다. 이러한 인스턴스의 스코프 관리를 DI컨테이너가 대신한다. 심지어 각 인스턴스가 필요로 하는 공통 처리 코드를 외부에서 자동으로 끼워넣는 AOP 기능도 DI 컨테이너가 한다. 

DI 컨테이너에서 인스턴스를 관리하는 방식의 장점 정리

* 인스턴스의 스코프를 관리할 수 있다.
* 인스턴스의 생명 주기를 제어할 수 있다. 
* AOP 방식으로 공통 기능을 집어 넣을 수 있다.
* 컴포넌트 간의 결합도를 낮추어 단위 테스트가 용이하다.

스프링의 DI 컨테이너로는 **빈 팩토리(Bean Factory)** 와 **애플리케이션 컨텍스트(ApplicationContext)** 가 있다. 
빈 팩토리(Bean Factory)는 의존성 주입, 빈 객체 생성, 생명주기를 관리하는 단순한 역할을 한다. 애플리케이션 컨텍스트(ApplicationContext)는 빈 팩토리의 기능 뿐만 아니라 프로퍼티 파일에 설정을 읽거나 이벤트 리스너에 대한 이벤트 발행 같은 추가 기능을 제공한다. **일반적으로 DI 컨테이너라 하면 바로 애플리케이션 컨텍스트를 말한다.**

## Bean

빈(Bean)은 스프링 DI 컨테이너에서 관리하는 객체이다. 이 빈에 대한 설정 정보를 빈 정의(Bean Definition). 그리고 DI 컨테이너에서 원하는 빈을 찾아오는 행위를 **룩업(look up)** 이라고 한다.

빈을 설정하는 방법에는 몇 가지 유형이 있다. 
|방법| 설명 |
|--|--|
|자바 기반 설정 방식(Java-based configuration)  | 자바 클래스에 @Configuration 애너테이션을, 메서드에 @Bean 애노테이션을 사용해서 빈을 정의하는 방법이다.|
|XML 기반 설정 방식(XML-based configuration)  |XML 파일을 사용하는 방법으로 <bean> 요소의 클래스 속성에 빈을 기술할 수 있다.|
|애너테이션 기반 설정 방식(annotation-based configuration) |@Componet같은 마커 애너테이션이 부여된 클래스를 탐색하여(Component Scan) 자동으로 DI 컨테이너에 빈을 등록하는 방법이다. |

자바 기반, XML 기반 두 가지 설정 방식은, **모두 DI 컨테이너를 만들면서 필요한 모든 Bean을 세팅한다.** 따라서 애플리케이션에서 사용할 모든 컴포넌트를 자바나 XML로 정의를 해야 하는 번거로움이 있다. 그래서 애너테이션 기반 설정 방법과 병행해서 사용하는 것이 일반적이다. 
 
애너테이션 기반 설정 방식은 DI 컨테이너에서 관리해야할 클래스에 마커 애너테이션을 붙인다. 컴포넌트 스캔(Component Scan)은 이런 **마커 애너테이션이 붙은 클래스를 탐색하여 DI 컨테이너에 자동으로 등록하는 과정**을 말한다.

## ApplicationContext

스프링에서는 ApplicationContext가 DI 컨테이너의 역할을 한다. 이제 ApplicationContext를 구현하는 과정을 보도록 하자. ApplicationContext를 생성할때는 자바 기반 설정 방식과 XML 기반 설정 방식이 있다. 
```
@Configuration //자바 기반 방식
@ComponentScan("com.example.demo")
public class AppConfig {
	@Bean UserRepository userRepository(){ return new UserRepositoryImpl();}
}
```
@Configuration과 @Bean 애너테이션을 사용해서 DI 컨테이너에서 관리할 빈 컴포넌트를 등록할 수 있다.  또 @ComponentScan("com.example.demo") 애노테이션을 부여하면 해당 패키지(com.example.demo) 이하의 범위에서 애너테이션이 붙은 클래스를 스캔하여, 애플리케이션 컨텍스트에 자동으로 등록한다. 
```
<beans xlms=...> <!--XML 기반 설정 방식-->
	<bean id="userDao" class="com.example.demo.UserDao"></bean>
	<context:component-scan base-package="com.example.demo" />
</beans>
```
XML기반은 설정 방식은 `<bean>` 태그로 사용하려는 빈을 DI 컨테이너에 등록한다. 
 그리고 컴포넌트 스캔 범위는 `<context:component-scan>` 태그요소의 base-packages 속성으로 결정한다. 

참고로 애플리케이션 컨텍스트에 있는 빈을 가져오는 방법(룩업)은  아래와 같이 세 가지 방식이 있다.
```
UserService userService = context.getBean(UserService.class);
UserService userService = context.getBean("userService",UserService.class);
UserService userService = (UserService) context.getBean("userService");
```
1번 유형은 빈의 타입을 지정하는 방식으로 해당 타입의 객체가 DI 컨테이너에 하나만 있을때 사용한다. 
2번 유형은 빈의 타입과 이름을 지정하는 방식이다. 지정한 **타입 객체가 DI 컨테이너에 다수가 있을때** 구분하기 위해 사용한다. 
3번 유형은 빈의 이름을 지정하는 방식이다. 반환 값이 Object이기 때문에 형변환 해야 원하는 객체를 쓸 수 있다. 

## Component Scan

DI 컨테이너가 관리하는 빈은 크게 명시적으로 설정된 빈과 컴포넌트 스캔으로 자동 등록된 빈으로 나뉜다. 컴포넌트 스캔은 클래스 로더를 스캔하면서 특정 클래스를 찾아 DI컨테이너에 등록한다.

### 기본 설정


### 필터 적용 설정



## Spring DI 

스프링단에서 사용하려는 빈을 주입받는 방법은 세 가지 방법이 있다. 

### 설정자 기반 의존성 주입 방식(setter-based dependency injection)

세터 메서드를 사용해서 필요한 의존성을 주입한다. 이는 다시 자바 기반 설정 방식, XML 기반 설정 방식, 애노테이션 기반 설정 방식으로 나누어진다. 
```
@Bean //세터 인젝션을 자바 기반 설정 방식으로 ... 
UserService userService(UserRepository repository){
	UserServiceImpl userService = new UserServiceImpl();
	userServce.setUserRepository(repository);
	return userService;
}
```
@Bean 애너테이션이 붙은 Bean 생성 메서드로 필요한 객체를 만드는 과정에서, 객체의 세터 메서드로 의존 주입을 통해 의존성 주입이 완료된 객체를 반환한다. 

```
<bean id="userSerivce" class="com.example.demo.UserServiceImpl">
	<property name="userRepository" ref="userRepository" />
</bean>	
```
XML 기반 설정 방식으로는 property 요소에 name 속성으로 대상의 이름을 지정하면 된다. 여기서 프로퍼티 이름은 기준으로 자바빈즈의 관례에 따라 프로퍼티의 이름과 메서드의 이름을 정한다. 위의 예에선 setUserRepository 메서드가 생성된다. 
```
@Autowired //애너테이션 기반 설정 방식으로 ... 
public void setUserRepository(UserRepository repository){
	this.userRepository = userRepository;
}
```
마지막으로 애너테이션 기반 설정 방식에서는 **구현체의 세터 메서드에다가 @Autowired**를 달아주면 된다. 애너테이션 기반 설정 방식은 자바 기반 설정 방식과 같이 별도의 설정 파일을 둘 필요가 없다. 

### 생성자 기반 의존성 주입 방식(constructor-based dependency injection)

컨스트런터 인젝션이라고도 하며, 자바 기반 설정 방식에서는 생성자에 의존 컴포넌트를 직업 설정한다.  XML 기반 설정 방식에서는 <constructor-arg> 요소로 설정하고 애너테이션 기반 방식에서는 생성자에 @Autowired를 부여한다. 

### 필드 기반 의존성 주입 방식(field-based injection) 

필드 기반 의존성 주입은 생성자나 설정자 메서드를 쓰지 않고 DI 컨테이너의 힘을 빌려 의존성을 주입한다. 간단히 의존성을 주입하고 싶은 필드에 @Autowired를 달아준다. 필드기반 의존성 주입을 사용하여 생성자나 세터 메서드를 쓰지 않아도 되서 코드가 간결해 보이는 효과가 있다. 

## AutoWiring

오토 와이어링(Auto Wiring)은 DI 컨테이너의 빈을 자동으로 주입하는 방식이다. 오토 와이어링을 사용하는 방식에는 타입을 이용하는 방식(autowiring by type)과 이름을 이용하는 방식(autowiring by name) 두가지가 있다. 

### Autowiring by Type

### Autowiring by Name


### Bean Scope


### Bean Life Cycle







<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE2NjQ2MjA4ODEsLTg4MTM0OTkyMywtNj
U2ODY0NTgxLDE1MDQ3NTIwNjUsMTg1ODMzMjI3NSwyMDYxMDMx
OTAyLDE5MTg4NzI4NTgsLTIzMzYxNzk5NiwxMDM4MTA5MTE2LC
0xNzIwNDI2MjMwXX0=
-->