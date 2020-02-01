# Spring Core

## DI 개요

일반적으로 하나의 애플리케이션이 동작하기 위해선 여러 컴포넌트를 통합해서 사용한다. 특정 컴포넌트를 사용할때 한 클래스에서 구현 클래스를 직접 생성해서 할당하면 두 클래스간의 결합도가 높아진다. 많은 컴포넌트에 의존해야 하는 클래스가 이 같은 방식으로 개발하는 건 큰 문제가 생길 수 있다. 

이 결합도를 낮추는 방법으로 **의존성 주입**이라는 접근방식이 큰 힘을 발휘한다.  클래스의 외부에서 컴포넌트를 생성 한 후, 내부에 주입하여 사용가능하게 만드는 과정을 **의존성 주입 또는 인젝션(injection)**이라 한다.  

사실 DI는 Strategy Pattern을 구현한 **IoC(Inverse of Control)** 이라 하는 디자인 패턴 중에 하나다.  Ioc는 인스턴스를 제어하는 주도권이 역전된다는 의미이다. 컴포넌트를 구성하는 인스턴스의 생성과 의존관계를 해당 클래스가 아닌 DI 컨테이너가 대신해주기 때문이다. 

## DI 컨테이너

**의존성 주입**을 처리하는 주체를 **DI 컨테이너**라고 한다. DI 컨테이너를 통해 각 컴포넌트의 인스턴스를 생성하고 통합 관리하면서 얻을 수 있는 장점은 컴포넌트간의 의존성 해결 뿐만이 아니다. 

어떤 컴포넌트는 싱글턴 객체로 만들어야 하고 어떤 컴포넌트는 매번 필요할때마다 새 인스턴스를 사용하도록 프로토 타입(prototype) 객체를 만들어야 한다. 이러한 인스턴스의 스코프 관리를 DI컨테이너가 대신한다. 심지어 각 인스턴스가 필요로 하는 공통 처리 코드를 외부에서 자동으로 끼워넣는 AOP 기능도 DI 컨테이너가 한다. 

DI 컨테이너에서 인스턴스를 관리하는 방식의 장점 정리

* 인스턴스의 스코프를 관리할 수 있다.
* 인스턴스의 생명 주기를 제어할 수 있다. 
* AOP 방식으로 공통 기능을 집어 넣을 수 있다.
* 컴포넌트 간의 결합도를 낮추어 단위 테스트가 용이하다.

스프링의 DI 컨테이너로는 **빈 팩토리(Bean Factory)** 와 **애플리케이션 컨텍스트(ApplicationContext)** 가 있다. 
빈 팩토리(Bean Factory)는 의존성 주입, 빈 객체 생성, 생명주기를 관리하는 단순한 역할을 한다.
애플리케이션 컨텍스트(ApplicationContext)는 빈 팩토리의 기능 뿐만 아니라 프로퍼티 파일에 설정을 읽거나 이벤트 리스너에 대한 이벤트 발행 같은 추가 기능을 제공한다. **일반적으로 DI 컨테이너라 하면 바로 애플리케이션 컨텍스트를 말한다.**

## Bean

스프링 DI 컨테이너에서 관리하는 객체를 Bean이라 한다. 이 빈에 대한 설정 정보를 빈 정의(Bean Definition). 그리고 DI 컨테이너에서 원하는 빈을 찾아오는 행위를 룩업(look up)이라고 한다.

빈을 설정하는 방법에는 몇 가지 유형이 있다. 
|방법| 설명 |
|--|--|
|자바 기반 설정 방식(Java-based configuration)  | 자바 클래스에 @Configuration 애너테이션을, 메서드에 @Bean 애노테이션을 사용해서 빈을 정의하는 방법이다.|
|XML 기반 설정 방식(XML-based configuration)  |XML 파일을 사용하는 방법으로 <bean> 요소의 클래스 속성에 빈을 기술할 수 있다.|
|애너테이션 기반 설정 방식(annotation-based configuration) |@Componet같은 마커 애너테이션이 부여된 클래스를 탐색하여(Component Scan) 자동으로 DI 컨테이너에 빈을 등록하는 방법이다. |

자바 기반, XML 기반 두 가지 설정 방식은, **모두 DI 컨테이너를 만들면서 필요한 모든 Bean을 세팅한다.** 따라서 애플리케이션에서 사용할 모든 컴포넌트를 자바나 XML로 정의를 해야 하는 번거로움이 있다. 그래서 애너테이션 기반 설정 방법과 병행해서 사용하는 것이 일반적이다. 
 
애너테이션 기반 설정 방식은  DI 컨테이너에서 관리해야할 클래스에 마커 애너테이션을 붙인다. **이 애너테이션이 붙은 클래스를 탐색하여 DI 컨테이너에 자동으로 등록하는데 이를 컴포넌트 스캔(Component Scan)이라고 한다.** 

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


## ApplicationContext

스프링에서는 ApplicationContext가 DI 컨테이너의 역할을 한다. 이제 ApplicationContext를 구현하는 과정을 보도록 하자. ApplicationContext를 생성할때 **설정 클래스(Configuration Class)** 를 전달받아 생성한다. 이러한 설정 클래스를  자바 코드로 구현하는걸 **자바 기반 설정 방식**이라 한다. 

```
@Configuration
public class AppConfig {
	@Bean
	UserRepository userRepository(){
		return new UserRepositoryImpl();
	}
}
```

@Configuration과 @Bean 애너테이션을 사용해서 DI 컨테이너에 컴포넌트를 등록하면 애플리케이션은 DI 컨테이너에 있는 Bean을 DI 컨테이너로 부터 가져 올 수 있다. 이러한 룩업을 하는 방법에는 세 가지 유형이 있다. 
```
UserService userService = context.getBean(UserService.class);
UserService userService = context.getBean("userService",UserService.class);
UserService userService = (UserService) context.getBean("userService");
```
1번 유형은 빈의 타입을 지정하는 방식으로 해당 타입의 객체가 DI 컨테이너에 하나만 있을때 사용한다. 
2번 유형은 빈의 타입과 이름을 지정하는 방식이다. 지정한 **타입 객체가 DI 컨테이너에 여러개 있을때** 구분하기 위해 사용한다. 
3번 유형은 빈의 이름을 지정하는 방식이다. 반환 값이 Object이기 때문에 형변환 해야 원하는 객체를 쓸 수 있다. 


### 의존성 주입

의존성 주입도 명시적으로 설정하는 것이 아니라 **특정 애노테이션이 붙어 있으면 DI 컨테이너가 자동으로 필요로 하는 의존 컴포넌트를 주입하는데 이를 오토 와이어링(Auto Wiring)이라 한다.**

스프링에서 제공하는 의존성 주입은 크게 아래 세 가지 방법이 있다.  설정자 기반 의존성 주입 방식(setter-based dependency injection), 생성자 기반 의존성 주입 방식(constructor-based dependency injection), 필드 기반 의존성 주입 방식(field-based injection).

설정자 기반 의존성 주입 방식(setter-based dependency injection)
: 세터 메서드를 사용해서 필요한 의존성이 주입된 객체를 만드는 방법. 자바 기반 설정 방식으로는 @Bean 애너테이션을 붙은 Bean 생성 메서드에서 필요한 객체를 만들고 세터메서드로 의존 주입을 통한 객체를 반환한다. XML 기반 설정 방식으로는 property 요소에 name 속성으로 대상의 이름을 지정하면 된다. 마지막으로 애너테이션 기반 설정 방식에서는 세터 메서드에다가 @Autowired를 달아주면 된다. 애너테이션 기반 설정 방식은 자바 기반 설정방식과 같이 별도의 설정 파일을 둘 필요가 없다. 

생성자 기반 의존성 주입 방식(constructor-based dependency injection)
: 컨스트런터 인젝션이라고도 하며, XML 기반 설정 방식에서는 <constructor-arg> 요소로 설정하고 애너테이션 기반 방식에서는 생성자에 @Autowired를 부여한다. 

필드 기반 의존성 주입 방식(field-based injection) 

<!--stackedit_data:
eyJoaXN0b3J5IjpbODgxMTA5Nzc1LC0xNzIwNDI2MjMwXX0=
-->