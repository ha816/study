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

### Bean Scope

DI 컨테이너는 빈 간의 의존관계 뿐만 아니라 빈의 생존 기간도 관리한다. **빈의 생존 기간을 빈 스코프**라고 한다. 개발자가 직접 빈 스코프를 다루지 않아도 된다는 점이 DI 컨테이너를 사용하는 이유기도 하다. 

스프링에서 제공하는 빈 스코프의 종류는 아래와 같다. 

|Scope  | 설명|
|--|--|
|singleton  | DI 컨테이너 기동시 빈 인스턴스가 하나만 생성되고, 그 인스턴스를 공유하는 방식. 특별한 설정이 없을때는 Singleton을 사용|
|prototype | DI 컨테이너에 빈을 요청할때마다 새로운 빈 인스턴스가 생성된다. 멀티 쓰레드 안전하고 싶다면 Prototype을 사용해야 한다.|
|request | HTTP 요청이 들어올때마다 새로운 인스턴스가 만들어진다. 웹어플리케이션을 만들때만 사용 가능하다.|
|session | HTTP 세션이 만들어질때 마다 새로운 빈 인스턴스가 만들어진다. 웹어플리케이션을 만들때만 사용 가능하다.|

빈을 정의하는 단계에서 Scope를 사용하면 빈의 스코프를 설정할 수 있다.
```
@Bean @Scope("prototype") // 자바 기반
...
<!--XML 기반-->
<bean id="userService" class="com.example.demo.UserServiceImpl" scope="prototype">
```
#### 다른 스코프의 빈 주입(Look Up Injection)

 DI 컨테이너에 의해 주입된 빈은 원래 자신의 스코프와 상관없이 주입받은 빈의 스코프를 따르게 된다.  예를 들어, prototype 스코프의 빈을 singleton 스코프 빈에 주입한다고 생각해보자. 주입된 prototype 빈은 무시되고 주입받은 singleton 스코프의 빈으로 취급된다. 
 singleton 스코프의 빈이 살아 있는 한 DI 컨테이너에서 새로 만들 필요가 없기 때문에 결과적으로 singleton과 같은 수명을 살게 된다. 
```
@Bean @Scope("prototype") // singleton으로 쓰이면 안됨
PasswordEncoder passwordEncoder() { ... }
```
```
@Component @Scope("singleton")
public class UserService {
	@Autowired
	PasswordEncoder passwordEncoder;  // prototype 인코더를 주입
	...
}
```
위와 같은 문제를 해결하는 가장 좋은 방법은 주입 받는게 아니라 필요할때 마다 DI 컨테이너에서 빈을 찾아오면 된다. 이때 DI 컨테이너와 관련된 코드를 남가지 않는 방법이 바로 Look Up injection이다. 
이 기능은 @Lookup 애너테이션을 DI컨테이너의 룩업을 대행하고 싶은 메서드에 붙여주면 된다. 그러면 빈이 DI 컨테이너 등록되는 시점에 LookUp을 하는 실제 코드가 @Lookup 애너테이션이 붙은 메서드로 바뀐다. 구체적으로 설명하자면, DI 컨테이너는 @Lookup을 붙인 메서드를 오버라이드한다. 따라서 private 이나 fianl을 사용하면 안되고 매개변수 역시 지정하면 안된다.

@Lookup 애너테이션에 value 속성에 빈의 이름을 지정할 수 있지만, 별도로 지정하지 않았다면 메서드의 반환 값 타입을 보고 룩업 대상 빈을 찾는다. 

```
@Lookup // 
PasswordEncoder passwordEncoder() { ... }
```
```
<bean id="passwordEncoder" class="com.example.demo" scope="prototype" />
<bean id="userService" class="com.example.demo.UserServiceImpl">
	<lookup-method name="passwordEncoder" bean="passwordEncoder">
</bean>
```
### Bean Life Cycle

빈의 생명 주기는 빈 초기화, 빈 사용단계, 종료단계로 구분할 수 있다.

빈 설정 읽기 및 보완 -> 빈 생성 및 의존 관계 설정 -> 빈 생성 후 초기화 작업

#### 빈 읽기 및 보완
빈을 생성하는데 필요한 정보를 수집한다.이 단계에서는 정보만 불러올뿐 아직 빈을 생성한 것은 아니다. 다음으로 Bean Factory Post Processor(BFPP)를 사용해서 빈의 정보를 보완한다. 

#### 빈 생성 및 의존 관계 설정

#### 빈 생성 후 초기화 작업

빈 의존 관계가 정리되면, 마지막으로 빈 생성 후의 초기화 작업을 수행한다. (Post Construct)
이 작업은 전처리, 초기화 처리, 후처리로 구분된다.

이렇게 빈이 생성된 후에 이뤄지는 초기화는 빈을 생성할때 해주는 초기화와 큰 차이가 있는데, 그것은 바로 의존성 주입이 끝난 필드 값을 초기화에 활용할 수 있다는 점이다. 

@Post






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

별도의 설정이 없는 기본 설정에서는 아래와 같은 애너테이션이 붙은 클래스가 스캔의 탐색 대상이된다. 

|애너테이션  | 설명 |
|--|--|
|@Controller  | MVC 패턴에서 컨트롤러 역할을 하는 컴포넌트에 붙는 애너테이션 |
|@Service  | 비즈니스 로직을 처리하는 컴포넌트에 붙은 애너테이션|
|@Repository  | 영속적인 데이터 처리를 수행하는 컴포넌트에 붙이는 애너테이션|
|@Component  | 위 경우에 해당하지 않는 컴포넌트에 붙이는 애너테이션|


컴포넌트 스캔을 할때는 클래스 로더에서 위와 같은 애너테이션이 붙은 클래스를 찾아야 하기 때문에, 이 과정에서 애플리케이션 기동 시간을 느리게 만드는 원인이 되기도 한다. 

가능한한 광범위한 범위 설정을 피해야 하는데, **통상 애플리케이션의 최상위나 한 단계 아래 패키지까지를 스캔 대상으로 하는게 적절하다.** 

### Filter

추가로 다른 컴포넌트를 포함하고 싶다면 필터를 적용하는 방법으로 스캔 범위를 커스터마이징할 수 있다. 스프링에서는 아래와 같은 필터를 제공한다.

* 애너테이션 필터(ANNOTATION)
* 할당 가능 타입을 이용 필터(ASSIGNABLE_TYPE)
* 정규 표현식 필터(REGEX)
*  ASPECTJ 패턴을 이용한 필터(ASPECTJ)

```
@ComponentScan(BasePackages = "com.example.demo", useDefaultFilters = false,
includeFilters = { @Component.Filter(type = FilterType.REGEX, pattern = {".+DomainService$"}) },
excludeFilters = { @Component.Filter(type = FilterType.ANNOTATION, pattern = { Exclude.class }
```
```
<context:component-scan base-package="com.example.demo" use-default-filters = "false">
	<context:include-filter type="regex" expression=".+DomainService$" />
	<context:exclude-filter type="annotation" expression="com.example.demo.Exclude"$	
</context:component-scan>
```

한 가지 주위할 점은 필터로 스캔할때는 기존 애너테이션이 붙은 스캔 대상도 포함된다.  필터시 애너테이션이 붙은 기존 스캔 대상을 무시하고, 순수하게 필터를 적용해서 탐색되는 컴포넌트만 사용하고 싶다면, **userDefaultFilters**를 false로 설정하자.

추가로 include필터와 exclude 필터가 모두 해당하는 컴포넌트가 있는 경우, **제외하는 필터가 우선순위가 높아 해당 컴포넌트는 스캔 대상에서 제외된다.**

## Spring DI 

스프링에서 사용하려는 빈을 주입받는 방법은 세 가지 방법이 있다. 

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

### AutoWiring

DI 컨테이너의 빈을 자동으로 의존성 주입하는 방법이다. 자세히는 빈 컨테이너가 제공하는 빈을 특정 클래스에서 사용하고 싶을때 사용하는 방법이다. `@Autowired`을 세터 메서드, 생성자, 필드에 붙이면 빈 컨테이너로부터 필요한 객체를 주입받을 수 있다. 세터 인젝션, 컨스트럭터 인젝션, 필드 인젝션 모두에서 활용가능하다. 

기본적으로 의존성 주입이 반드시 성공한다고 가정하기 때문에, 주입할 타입에 해당하는 빈을 DI 컨테이너에서 찾지 못한다면 NoSuchBeanDefinitionException 예외가 발생한다. 
이런 필수 조건을 완하하고 싶다면 @Autowried 애너테이션의 required를 false로 설정하면 된다. `requlare = false`로 하면 해당 타입의 빈을 찾지 못하더라도 예외가 발생하지 않는다. 그렇다고 하더라도 의존성 주입은 실패했기 때문에 해당 필드의 값은 null이 된다.

DI 컨테이너에 같은 타입의 빈이 여럿 발견된다면 그 중 어떤 것을 써야할지 알 수 없다. 이럴 경우 NoUniqueBeanDefinitionException이 발생한다. 이럴때 `@Qualifier` 애너테이션으로 추가하려는 빈 이름을 지정하면 원하는 빈을 설정할 수 있다. 





<!--stackedit_data:
eyJoaXN0b3J5IjpbNjM5NTY4NjE3LC0zOTc0MzQ2NjksLTE2Nj
k0MzY2ODksLTQzNzA0ODY2NCwxMTUxMTE5MzQwLDc5ODYyMjA0
MSwtMTkyNTIyMTA5MSwtMTQyMzc5NDE0NCwxNjY2MzU2Njc2LC
0yMTE4NTQ5MDY4LDE3OTg1NzM0NTgsOTg2OTUxNTQsLTY5Nzgz
Nzg1MiwtMTM4Mjc3NDk0OCwtODM4MjU2NjM2LDIwOTA0OTE4MT
YsLTQ1ODk4MjE3OSwxNTc0OTYyODI5LDUyMDMyNjQ3OSwxNDE3
MDI1NjEwXX0=
-->