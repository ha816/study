# AOP(Aspect Oriented Programming)

소프트웨어 개발시 규모가 커지다보면 본래의 비즈니스 로직과는 크게 관련 없는 처리 내용이 소스 코드 여기저기에 산재하기 쉽다. 이렇게 구현하고자하는 비즈니스 로직과는 다소 거리가 있지만 여러 모듈에 걸쳐 공통적이고 반복적으로 사용되는 작업을 횡단 관심사(Cross-Cutting Concern)라고 한다. 이런 횡단 관심사에는 대표적으로 로깅, 트랜잭션, 보안등이 있다.

AOP는 공통적으로 사용되는 서비스를 모듈화해서 컴포넌트에 선언적으로 사용할 수 있도록 한다. AOP를 사용하면 공통적인 시스템 서비스에 대해서는 전혀 알지 못하지만, 본연에 관심사에 집중하는 컴포넌트를 만들 수 있다. 즉 관점(액스펙트) 지향 프로그래밍은 본연의 관심사에 집중할 수 있는 프로그래밍이므로, 관심사의 분리(Separation of Concerns)라고도 한다. 

횡단 관심사가 여러 컴포넌트에 퍼지면 코드는 두 가지 관점에서 복잡해진다.
1. 여러 컴포넌트에 같은 코드가 중복되어 나타난다. 만약 코드가 변경되어야 하는 경우 모든 컴포넌트를 변경해야 한다. 
2. 컴포넌트가 본연의 기능과 관련 없는 기능 코드로 지저분해진다. 메소드의 보안이나 로깅 같은 기능을 위해 코드가 지저분해지는것을 피하도록 하자 
 
## AOP 용어

![enter image description here](https://www.baeldung.com/wp-content/uploads/2017/11/Program_Execution.jpg)

* 어드바이스(Advice)
	* 특정 조인 포인트에서 실행되는 코드로, 횡당 관심사를 실제로 구현해 처리하는 부분이다.
* 포인트 컷(Pointcut)
	* 수 많은 조인 포인트 중에 실제로 어드바이스를 적용할 곳을 선별하기 위한 표현식. 
* 조인 포인트(Join Point)
	* 어드바이스가 실행될 지점이나 시점(메서드 실행이나 예외 발생 등)을 말한다. 
	* 조인 포인트는 AOP를 구현한 라이브러리에 따라 사양이 다를 수 있는데 스프링에서는 메서드 단위로 조인 포인트를 잡는다.

* 위빙(Weaving)
	* 애플리케이션 코드의 적절한 지점에 애스펙트를 적용하는 것을 말한다. AOP 구현 라이브러리에 따라 시점이 다를 수 있는데, 컴파일 시점이나 클래스 로딩 시점, 실행 시점등 다양한 위빙 시점이 있다. 스프링은 기본적으로 실행 시점에 위빙한다.
* 타깃(Target)
	* AOP 처리에 의해 처리 흐름에 변화가 생긴 객체를 말한다. 

# Spring AOP

스프링에는 AOP를 지원하는 모듈로 **Spring AOP**가 있다. Spring AOP는 개별 현장에서 폭 넓게 사용되어온 **AspectJ**라는 AOP 프레임워크를 포함하고 있다. AspectJ는 애스펙트와 어드바이스를 정의하기 위한 애너테이션이나 포인트컷 표현 언어, 위빙 메커니즘 등을 제공하는 역할을 한다. 
스프링 AOP는 DI 컨테이너에서 관리하는 빈들을 타깃으로 어드바이스를 적용하는 기능이 있는데, 조인 포인트에 어드바이스를 적용하는 방법은 프락시 객체를 만들어서 대체하는 방법을 쓴다. 그래서 어드바이스가 적용된 이후, DI 컨테이너에서 빈을 꺼내보면 **원래의 빈 인스턴스가 아닌 프락시 형태로 어드바이스 기능이 덧입혀진 빈이 나온다.** 

##  Spring Advice Types

스프링에서 제공하는 Advice 실행 시점 유형은 아래와 같다.

|어드바이스 | 설명 |
|--|--|
| Before  | 조인 포인트 전에 실행된다. 예외를 제외하고는 항상 실행된다.|
| After Returning  | 조인 포인트가 정상 종료한 후 실행된다. 예외가 발생하면 실행되지 않는다.|
| After Throwing  | 조인 포인트에서 예외가 발생할 경우 실행된다. 정상적으로 종료되면 실행되지 않는다.|
| After | 조인 포인트 처리 완료 후 실행된다. 예외 발생 정상 종료 여부와 상관 없이 항상 실행된다.|
| Around | 조인 포인트 전후에 실행된다|


## AOP 구현




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


## Pointcut 표현

## 활용되는 AOP 기능

### 캐싱

### 비동기 처리

### 트랜잭션 관리





> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4MjA0Nzk0MjMsLTEyNDg1MTYzODYsMT
A4ODkxMDA1NywtMzczNTExMTE5LC0xNjY0ODEwNDUxLC0xOTkz
MjM4OTkxLC04NTMyNDcwMTMsLTY4OTY4MjMxOV19
-->