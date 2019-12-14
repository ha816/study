# Spring Framework

> Spring Framework란 무엇인가요?

규모가 있는 엔터프라이즈급 애플리케이션을 개발하는데 유용한 자바기반의 오프소스 프레임워크 입니다. 

## 엔터프라이즈 자바(Enterprise JavaBeans) 

> Java EE? EJB?

Java EE는 커뮤니티([JCP](https://www.jcp.org/), Java Community Process)에서 주도하는 엔터프라이즈 소프트웨어의 표준을 말한다. 이런 Java EE 표준을 구현하기 위한 아키텍처가 바로 EJB(Enterprise JavaBeans)이다. 

EJB는 침투적인(invasive) 기술로 소스코드 측면에서의 강제사항(EJB 관련 클래스를 반드시 사용)과 개발환경 측면에서의 강제사항(EJB 컨테이너 사용) 떄문에 아래와 같은 문제가 발생

-   개발 생산성 저하
-   유지 보수성 저하
-   테스트 용이성이 떨어짐
-   배포의 어려움

그래서 스프링을 EJB에서 문제가 되었던 부분을 해결하기 위해 비 침투적인(non-invasive) 방식을 도입하였고, 그렇게 스프링 프레임워크가 인기를 얻게 되었다.

#### 애플리케이션 컨텍스트
빈에 관한 정의들을 바탕으로 빈들을 엮는다. 
스프링 애플리케이션 컨텍스트는 애플리케이션을 구성하는 객체의 생성과 와이어링을 전적으로 책임진다. 스프링에는 애플리케이션의 여러 구현체가 존재하며, 각각의 주요 차이점은 오직 설정을 로드하는 방법에 있다. 

ex) application context, simple application context, webApplication Context 


### 애스펙트 적용 
애스펙트와 공통 규약을 통한 선언적 프로그래밍 
DI 가 소프트웨어 컴포넌트의 결합도를 낮춰 준다면 애스펙트 지향 프로그래밍은 애플리케이션 전체에 걸쳐 사용되는 기능을 재사용할 수 있는 컴포넌트에 담는다 .

액스펙트 지향 프로그래밍은 소프트웨어 시스템 내부의 관심사들을 서로 분리하는 기술이다. 
관심사의 분리(separation of concerns)

시스템은 보통 특정한 기능을 책임지는 여러개의 컴포넌트로 구성된다. 그러나 각 컴포넌트는 대체로 본연의 기능 외에 로깅이나 트랜잭션 관리, 보안등 시스템 서비스도 수행해야 하는 경우가 많다. 이러한 서비스는 여러 컴포넌트에서 동시에 사용되는 경향이 있어 횡단 관심사라고 한다.(cross-cutting concerns)라고 한다. 

이렇게 관심사가 여러 컴포넌트에 퍼지면 코드는 두가지 관점에서 복잡해진다.
1. 여러 컴포넌트에 같은 코드가 중복외더 나타난다. 만약 코드가 변경되어야 하는 경우 모든 컴포넌트를 변경해야 한다. 
2. 컴포넌트가 본연의 기능과 관련 없는 기능 코드로 지저분해진다. 메소드의 보안이나 로깅 같은 기능을 위해 코드가 지저분해지는것을 피하도록 하자 
 
AOP는 시스템 서비스를 모듈화해서 컴포넌트에 선언적으로 작용한다. 
AOP를 사용하면 시스템 서비스에 대해서는 전혀 알지 못하지만, 본연에 관심사에 집중하는 컴포넌트를 만든다. 

애스펙트를 애플리케이션의 여러 컴포넌트를 덮는 담요처럼 생각하면 도움이 된다. 이 담요에 포함되면 해당 트랜잭션, 로깅, 보안 등에 관리된 기능을 사용할 수 있다. 

코드 1.11 Minstrel를 애스펙트로 선언하기 

스프링의 aop 설정 네임스페이스를 사용해서 빈이 액스펙트라고 선언 
<aop:aspect>
	<aop:pointcut id="embark" expression = "execution(* *.embarkOnQuest(..))" // 포인트컷 정의

embark라는 이름의 포인트 컷을 정의, 어드바이스가 적용될 위치를 expression 어트리뷰트에서 표현; * *.embarkOnQuest(..)는 AspectJ의 포인트커트 표현식언어
	
<aop: before point-cut-ref="embark"
method = "singBeforeQuest"/> //before 어드바이스 정의

<aop: after point-cut-ref="embark"
method = "singAfterQuest"/> // after 어드바이스 정의 

### 템플릿을 이용한 상투적인 코드 제거

상투적인 코드: 뻔하게 늘 반복되는 코드
ex) JDBC 의 try catch finally 같은 코드

이 상투적인 코드를 피하기 위해 스프링은 템플릿에 상투적인 코드를 캡슐화하여 반복적인 코드를 제거하는 방법을 찾는다. 
스프링의 JdbcTemplate은 전통적인 JDBC에서 필요한 모든 형식 없이도 데이터 베이스 작업을 수행하게 한다.


## 빈을 담는 그릇, 컨테이너

객체들의 삶의 터전인 스프링 컨테이너,
객체 생성 및 객체가 의존성 생성, 생명주기 관리 
스프링의 컨테이너는 크게 두가지로 분류된다. 
1. 빈팩토리; 가장 단순한 컨테이너
2. 애플리케이션 컨텍스트; 빈팩토리를 확장해 프로퍼티 파일에 설정을 읽고 이벤트 리스너에 대한 이벤트 발행 같은 애플리케이션 프레임워크 서비를 제공

### 또 하나의 컨테이너, 애플리케이션 컨텍스트

AnnotaionConfingApplicationContext
AnnotaionConfingWebApplicationContext
ClassPathXmlApplicationContext
FileSystemXmlApplicationContext

### 빈의 일생 

보통은 단순히 필요할때 new로 인스턴스화 하고 이를 사용한다. 그리고 나중에 가비지 컬렉션의 후보가 되어 언젠가는 허공속으로 사라진다.

반면에 스프링에서는 좀 더 정교하다. 
빈이 생성될때 스프링이 제공하는 커스터마이징 기회를 얻으려면 스프링의 빈 생명 주기를 이해해야 한다. 

그림에서 있는 것 처럼 특정 인터페이스를 구현하면 그 커스텀 메서드를 사용이 가능한데 이를 커스터마이즈라고 함. 

## 스프링현황

스프링 프레임워크는 DI, AOP, 그리고 상투적 코드의 축소를 통해 자바 개발 간소화에 초점을 둔다. 

하지만 스프링을 넘어서 웹서비스, REST, 모바일 그리고  NoSQL영역으로 스프링을 확장하여 코어 프레임워크를 구축하려는 큰 생태계가 존재 





 





















> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTM2NjIyNzkxMiwtOTcyNjI5NThdfQ==
-->