# AOP(Aspect Oriented Programming)

소프트웨어 개발시 규모가 커지다보면 본래의 비즈니스 로직과는 크게 관련 없는 처리 내용이 소스 코드 여기저기에 산재하기 쉽다. 이렇게 구현하고자하는 비즈니스 로직과는 다소 거리가 있지만 여러 모듈에 걸쳐 공통적이고 반복적으로 사용되는 작업을 횡단 관심사(Cross-Cutting Concern)라고 한다. 이런 횡단 관심사에는 대표적으로 로깅, 트랜잭션, 보안등이 있다.

AOP는 공통적으로 사용되는 서비스를 모듈화해서 컴포넌트에 선언적으로 사용할 수 있도록 한다. AOP를 사용하면 공통적인 시스템 서비스에 대해서는 전혀 알지 못하지만, 본연에 관심사에 집중하는 컴포넌트를 만들 수 있다. 즉 관점(액스펙트) 지향 프로그래밍은 본연의 관심사에 집중할 수 있는 프로그래밍이므로, 관심사의 분리(Separation of Concerns)라고도 한다. 

횡단 관심사가 여러 컴포넌트에 퍼지면 코드는 두 가지 관점에서 복잡해진다.
1. 여러 컴포넌트에 같은 코드가 중복되어 나타난다. 만약 코드가 변경되어야 하는 경우 모든 컴포넌트를 변경해야 한다. 
2. 컴포넌트가 본연의 기능과 관련 없는 기능 코드로 지저분해진다. 메소드의 보안이나 로깅 같은 기능을 위해 코드가 지저분해지는것을 피하도록 하자 
 
## AOP 용어

![enter image description here](https://www.baeldung.com/wp-content/uploads/2017/11/Program_Execution.jpg)

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




## 애스펙트 사용하기

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



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTE1Mjk5MDgsLTM3MzUxMTExOSwtMTY2ND
gxMDQ1MSwtMTk5MzIzODk5MSwtODUzMjQ3MDEzLC02ODk2ODIz
MTldfQ==
-->