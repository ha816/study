# Spring Core

스프링 코어 중에서 가장 중요한 것은 **DI(dependency injection)** 과 **AOP(aspect oriented programming)** 이다.

DI의 경우, 하나의 애플리케이션을 동작하기 위해 여러개의 컴포넌트를 통합해서 사용한다. 이때 의존성 주입이라는 접근방식이 큰 힘을 발휘한다. 

일반적으로 특정 컴포넌트를 사용할때 한 클래스에서 구현 클래스를 직접 생성해서 할당하면 두 클래스간의 결합도가 높아진다. 많은 컴포넌트에 의존해야 하는 클래스가 이 같은 방식으로 개발하는 건 큰 문제가 생길 수 있다. 이 결합도를 낮추는 방법으로 클래스의 외부에서 컴포넌트를 생성 한 후, 내부에 주입하여 사용가능하게 만드는 과정을 `의존성 주입` 또는 인젝션(injection)이라 한다. 그리고 이러한 의존성 주입을 자동으로 처리하는 기반을 DI 컨테이너라고 한다. 

스프링 프레임워크가 제공하는 기능 중 가장 중요한것이 바로 이 DI 컨테이너의 기능이다. 스프링의 DI 컨테이너로는 빈 팩토리과 애플리케이션 컨텍스트가 있다. 빈 팩토리는 빈 객체 생성, 의존성 주입, 생명주기를 하는 아주 단순한 역할을 한다. 그에 반해 애플리케이션 컨텍스트는 빈 팩토리를 확장해 프로퍼티 파일에 설정을 읽고 이벤트 리스너에 대한 이벤트 발행 같은 추가 기능을 제공한다. 앞으로 DI 컨테이너라 하면 바로 애플리케이션 컨텍스트를 뜻한다.

```
ApplicationContext context = ...; //DI 컨테이너
UserService service = context.getBean(UserService.class);
...
```
이렇게 DI 컨테이너를 통해 각 컴포넌트의 인스턴스를 생성하고 통합 관리하면서 얻을 수 있는 장점은 컴포넌트간의 의존성 해결 뿐만이아니다. 어떤 컴포넌트는 싱글턴 객체로 만들어야 하고 어떤 컴포넌트는 매번 필요할때마다 새 인스턴스를 사용하도록 프로토 타입(prototype) 객체를 만들어야 한다. 이러한 인스턴스의 스코프 관리를 DI컨테이너가 대신한다. 심지어 각 인스턴스가 필요로 하는 공통 처리 코드를 외부에서 자동으로 끼워넣는 AOP 기능도 DI 컨테이너가 한다. 

## DI 개요

DI는 의존성 주입, **IoC(Inverse of Control)** 이라 하는 디자인 패턴 중에 하나다.  Ioc는 인스턴스를 제어하는 주도권이 역전된다는 의미이다. 컴포넌트를 구성하는 인스턴스의 생성과 의존관계를 해당 클래스가 아닌 DI 컨테이너가 대신해주기 때문이다. 

DI 컨테이너에서 인스턴스를 관리하는 방식으로 하면 아래와 같은 장점이 있다. 
* 인스턴스의 스코프를 관리할 수 있다.
* 인스턴스의 생명 주기를 제어할 수 있다. 
* AOP 방식으로 공통 기능을 집어 넣을 수 있다.
* 의존하는 컴포넌트 간의 결합도를 낮추어 단위 테스트하기가 쉬워진다. 

### ApplicationContext와 Bean 정의

스프링 프레임 워크에서는 ApplicationContext가 DI 컨테이너의 역할을 한다. ApplicationContext를 생성할때 설정 클래스(configuration class)를 전달받아 생성한다. 이러한 설정 클래스를  자바 코드로 구현하는걸 자바 기반 설정 방식이라 한다. 

```
@Configuration
public class AppConfig {
	@Bean
	UserRepository userRepository(){
		return new UserRepositoryImpl();
	}
}
```

@Configuration과 @Bean 애너테이션을 사용해서 DI 컨테이너에 컴포넌트를 등록하면 애플리케이션은 DI 컨테이너에 있는 Bean을 DI 컨테이너로 가져 올 수 있다. 

**스프링 프레임워크에서는 DI 컨테이너에 등록하는 컴포넌트를 Bean이라고 하고, 이 빈에 대한 설정 정보를 빈 정의(Bean Definition). 또한 DI 컨테이너에서 빈을 찾아오는 행위를 룩업(look up)이라고 한다.** 

룩업을 하는 방법에는 몇 가지 유형이 있다. 
```
UserService userService = context.getBean(UserService.class);
UserService userService = context.getBean("userService",UserService.class);
UserService userService = (UserService) context.getBean("userService");
```
1번 유형은 빈의 타입을 지정하는 방식으로 해당 타입의 객체가 DI 컨테이너에 하나만 있을때 사용한다. 
2번 유형은 빈의 타입과 이름을 지정하는 방식이다. 지정한 타입 객체가 DI 컨테이너에 여러게 있을때 구분하기 위해 사용한다. 
3번 유형은 빈의 이름을 지정하는 방식이다. 반환 값이 Object이기 때문에 형변환 해야 원하는 객체를 쓸 수 있다. 

빈을 설정하는 방법에도 몇가지 유형이 있다. 
|방법| 설명 |
|--|--|
|자바 기반 설정 방식(Java-based configuration)  | 자바 클래스에 @Configuration 애너테이션을, 메서드에 @Bean 애노테이션을 사용해서 빈을 정의하는 방법이다.|
|XML 기반 설정 방식(XML-based configuration)  |XML 파일을 사용하는 방법으로 <bean> 요소의 클래스 속성에 빈을 기술할 수 있다.|
|애너테이션 기반 설정 방식(annotation-based configuration) |@Componet같은 마커 애너테이션이 부여된 클래스를 탐색해서(Component Scan) DI 컨테이너에 빈을 자동으로 등록하는 방법이다. |

자바 기반, XML 기반 모두 DI 컨테이너를 만들면서 필요한 모든 Bean을 세팅한다. 따라서 애플리케이션에서 사용할 모든 컴포넌트를 자바나 XML로 정의를 해야 하는 번거로움이 있다. 그래서 애너테이션 기반 설정 방법과 병행해서 사용하는 것이 일반적이다. 

**애너테이션 기반 설정 방식**
 
 이 방법은 DI 컨테이너에서 관리해야하는 클래스에 애너테이션을 붙인다. **이 애너테이션이 붙은 클래스를 탐색해서 DI 컨테이너에 자동으로 등록하는데 이를 컴포넌트 스캔(Component Scan)이라고 한다.** 또한 의존성 주입도 이제까지 처럼 명시적으로 설정하는 것이 아니라 **애노테이션이 붙어 있으면 DI 컨테이너가 자동으로 필요로 하는 의존 컴포넌트를 주입하며 이를 오토 와이어링(Auto Wiring)이라 한다.**

컴포넌트 스캔을 수행할때는 스캔할 범위를 지정해야 하는데 자바 기반 설정 방식과 XML 기반 설정 방식을 사용할 수 있다. 
```
@Configuration
@ComponentScan("com.example.demo")
public class AppConfig { 
... 
}
```

애플리케이션 컨텍스트 생성시 사용할 Configuration에 @ComponentScan("com.example.demo") 애노테이션을 부여하면 해당 패키지(com.example.demo) 이하의 범위에서 애너테이션이 붙은 클래스를 스캔하여, 애플리케이션 컨텍스트에 자동으로 등록한다. 

```
<beans xlms=...>
	<context:component-scan base-package="com.example.demo" />
</beans>
```
XML기반은 `<context:component-scan>` 태그요소의 base-packages 속성으로 스캔범위를 결정한다. 


### 의존성 주입

스프링에서 제공하는 의존성 주입은 크게 아래 세 가지 방법이 있다.  설정자 기반 의존성 주입 방식(setter-based dependency injection), 생성자 기반 의존성 주입 방식(constructor-based dependency injection), 필드 기반 의존성 주입 방식(field-based injection).

설정자 기반 의존성 주입 방식(setter-based dependency injection)
: 세터 메서드를 사용해서 필요한 의존성이 주입된 객체를 만드는 방법. 자바 기반 설정 방식으로는 @Bean 애너테이션을 붙은 Bean 생성 메서드에서 필요한 객체를 만들고 세터메서드로 의존 주입을 통한 객체를 반환한다. XML 기반 설정 방식으로는 property 요소에 name 속성으로 대상의 이름을 지정하면 된다. 마지막으로 애너테이션 기반 설정 방식에서는 세터 메서드에다가 @Autowired를 달아주면 된다. 애너테이션 기반 설정 방식은 자바 기반 설정방식과 같이 별도의 설정 파일을 둘 필요가 없다. 

생성자 기반 의존성 주입 방식(constructor-based dependency injection)
: 컨스트런터 인젝션이라고도 하며, XML 기반 설정 방식에서는 <constructor-arg> 요소로 설정하고 애너테이션 기반 방식에서는 생성자에 @Autowired를 부여한다. 

필드 기반 의존성 주입 방식(field-based injection) 
: 


## AOP(aspect oriented programming)

관점(액스펙트) 지향 프로그래밍은 관심사의 분리(separation of concerns)라고도 한다. 시스템은 보통 특정 기능을 책임지는 여러 컴포넌트로 구성된다. 그러나 각 컴포넌트는 대체로 **본연의 기능 외에 로깅, 트랜잭션 관리, 보안 등 다른 서비스도 수행해야 하는 경우가 많다.** 이러한 서비스는 여러 컴포넌트에서 동시에 사용되는 경향이 있어 횡단 관심사(cross-cutting concerns)라고 한다

이렇게 관심사가 여러 컴포넌트에 퍼지면 코드는 두가지 관점에서 복잡해진다.
1. 여러 컴포넌트에 같은 코드가 중복되어 나타난다. 만약 코드가 변경되어야 하는 경우 모든 컴포넌트를 변경해야 한다. 
2. 컴포넌트가 본연의 기능과 관련 없는 기능 코드로 지저분해진다. 메소드의 보안이나 로깅 같은 기능을 위해 코드가 지저분해지는것을 피하도록 하자 
 
AOP는 시스템 서비스를 모듈화해서 컴포넌트에 선언적으로 작용한다. 
AOP를 사용하면 시스템 서비스에 대해서는 전혀 알지 못하지만, 본연에 관심사에 집중하는 컴포넌트를 만든다. 

애스펙트를 애플리케이션의 여러 컴포넌트를 덮는 담요처럼 생각하면 도움이 된다. 이 담요에 포함되면 해당 트랜잭션, 로깅, 보안 등에 관리된 기능을 사용할 수 있다. 

### 대표적 용어

* 애스펙트(Aspect)
	* Aop의 단위가 되는 횡단 관심사에 속한다. 예를 들어 "로그를 출력한다", "예외를 처리한다", "트랜잭션을 관리한다" 와 같은 관심사가 애스펙트다.
* 조인 포인트(Join Point)
	* 횡단 관심사가 실행될 지점이나 시점(메서드 실행이나 예외 발생 등)을 말한다. 조인 포인트는 AOP를 구현한 라이브러리에 따라 사양이 다를 수 있는데 스프링에서는 메서드 단위로 조인 포인트를 잡는다.
* 어드바이스(Advice)
	* 특정 조인 포인트에서 실행되는 코드로, 횡당 관심사를 실제로 구현해 처리하는 부분이다.
* 포인트 컷(Pointcut)
	* 수 많은 조인 포인트 중에 실제로 어드바이스를 적용할 곳을 선별하기위한 표현식. 일종의 조인 포인트의 그룹이라 볼 수도 있다.
* 위빙(Weaving)
	* 애플리케이션 코드의 적절한 지점에 애스펙트를 적용하는 것을 말한다. AOP 구현 라이브러리에 따라 시점이 다를 수 있는데, 컴파일 시점이나 클래스 로딩 시점, 실행 시점등 다양한 위빙 시점이 있다. 스프링은 기본적으로 실행 시점에 위빙한다.
* 타깃(Target)
	* AOP 처리에 의해 처리 흐름에 변화가 생긴 객체를 말한다. 

### 애스펙트 사용하기

스프링의 aop 설정 네임스페이스를 사용해서 빈이 액스펙트라고 선언 
```
<aop:aspect>
	<aop:pointcut id="embark" expression = "execution(* *.embarkOnQuest(..))" // 포인트컷 정의
```

embark라는 이름의 포인트 컷을 정의, 어드바이스가 적용될 위치를 expression 어트리뷰트에서 표현; .embarkOnQuest(..)는 AspectJ의 포인트커트 표현식언어

```	
<aop: before point-cut-ref="embark"
method = "singBeforeQuest"/> //before 어드바이스 정의

<aop: after point-cut-ref="embark"
method = "singAfterQuest"/> // after 어드바이스 정의 
```

<!--stackedit_data:
eyJoaXN0b3J5IjpbNzcwMzY1MzY5LC0xNzAxNzc5ODgwLDY3OT
c0NDA0MiwxNDM0MjYxMTIyLDExNTMyNTcwMzIsNDMxMDIxNjU0
LC0yMDU2ODQyMzE2LC0xOTEyODk2NDE2LDU3MDA2MTUwMCwtMT
UzMTYyMzE3MSwzMTM4NjI5NjcsMTc5NTg5MjAxNSwtMzEwMzY3
ODU5LC0xOTI1MTUwNzI3XX0=
-->