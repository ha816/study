# Spring Framework

**규모가 있는 엔터프라이즈급 애플리케이션을 개발하는데 유용한 자바기반의 오프소스 프레임워크.** 

스프링 프레임워크의 핵심은 DI(Dependency Injection), AOP(Aspect Oriendted Programming)이다. 뿐만 아니라 스프링 프레임워크는 추상화를 통해 다양한 모듈을 사용할 수 있고 프레임워크단에서 제공하는 훌륭한 구현체가 많다.

# Java EE

Java EE(Enterprise Edition)는 일종의 **엔터프라이즈 소프트웨어의 표준**을 말한다. JCP 커뮤니티(Java Community Process, [JCP](https://www.jcp.org/))에서 주도한다. 

자바 사양은 JCP에서 표준화 절차를 걸쳐 만들어진다. JCP의 멤버가 자바 사양의 안건이 될 JSR(Java Specification Request)을 작성하고 제안하면 참여자들의 리뷰를 통해 내용이 구체화된다. JCP의 집행 위원회는 다수의 안건을 검토하고 그중에서 최종 사양을 채택한다. 

JavaEE는 자바 서블릿, JSP, EJB, JDBC등 서버측 애플리케이션 개발에 필요한 기능을 가지고 있다. 
JavaEE는 사양이며, 여러 소프트웨어 벤더가 이 사양에 맞춰 서버를 개발한다. 스프링 프레임워크도 JavaEE와 완전히 호완되는 것은 아니지만 일부 기능은 지원한다. 

스프링 프레임워크는 사실 JavaEE의 안티테제이자 경쟁 프레임워크로 개발됐지만, 시간이 지남에 따라 서로의 장점을 받아들여 차이가 출어드는 추세다. 그렇지만 Java EE를 구현한 프레임워크를 사용하는게 좋은가라고 한다면 꼭 그렇지만은 않다. 왜냐하면 JavaEE는 사양을 결정하는데 2년, 서버 제품을 구현하는데 2년 즉 시간이 오래걸린다. 그에 반해 스프링 진영은 신기술을 도입하는 속도가 더 빠르다. 만약 애플리케이션 개발시 새로운 기능과 아키텍처가 필요한 경우 스프링을 사용하는 것이 최상의 선택일 것이다. 

## 엔터프라이즈 자바빈즈(Enterprise JavaBeans) 

Java EE 표준을 구현하기 위한 아키텍처가 바로 **EJB(Enterprise JavaBeans)**이다. 태생적인 이유로 EJB는 Spring과 자주 비교가 된다. 

EJB는 보안, 트랜잭션등 EJB 컨테이너의 다양한 서비스를 제공 받기 위해서는 EJB 스펙을 지켜야 했는데, 그 결과 **서비스가 구현해야 하는 실제 비즈니스 로직보다 EJB 컨테이너를 사용하기 위한 상투적인 코드들이 많아지는 문제**가 발생했다. 또한 벤더 사마다  EJB 컨테이너를 구현한 내용이 다르기 때문에 다른 벤더 사의 컨테이너로의 변경에 어려움이 있었고, 설정이 너무 복잡하다는 문제점이 부각되기 시작했다.

이런 문제들이 발생한 이유는 **비즈니스 로직이 특정 기술에 종속되어 있다는 것**이다. 이를 기술 침투(invasive)라고 하는데, 이것이 EJB의 가장 큰 문제점이다. 

그래서 스프링은 EJB에서 문제가 되었던 부분을 해결하기 위해 비 침투적인(**non-invasive**) 방식을 도입하였다.

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTkwODE5MjkwNCwtNzA3MTM1NjE4LDM3Mj
U3MTMwNywxMjg3OTIwMzM2LC0yMTMwNDIwMTI3LC05NzI2Mjk1
OF19
-->