# Spring Framework

> Spring Framework란 무엇인가요?

**규모가 있는 엔터프라이즈급 애플리케이션을 개발하는데 유용한 자바기반의 오프소스 프레임워크.** 

스프링 프레임워크의 핵심은 DI(Dependency Injection), AOP(Aspect Oriendted Programming)이다. 뿐만 아니라 스프링 프레임워크는 추상화를 통해 다양한 모듈을 사용할 수 있고 프레임워크단에서 제공하는 훌륭한 구현체가 많다.

## Java EE

Java EE(Enterprise Edition)는 일종의 **엔터프라이즈 소프트웨어의 표준**을 말한다. JCP 커뮤니티(Java Community Process, [JCP](https://www.jcp.org/))에서 주도한다. 

자바 사양은 JCP에서 표준화 절차를 걸쳐 만들어진다. JCP의 멤버가 자바 사양의 안건이 될 JSR(Java Specification Request)을 작성하고 제안하면 참여자들의 리뷰를 통해 내용이 구체화된다. JCP의 집행 위원회는 다수의 안건을 검토하고 그중에서 최종 사양을 채택한다. 






## 엔터프라이즈 자바빈즈(Enterprise JavaBeans) 

Java EE 표준을 구현하기 위한 아키텍처가 바로 EJB(Enterprise JavaBeans)이다.
EJB는 침투적인(invasive) 기술로 소스코드 측면에서의 강제사항(EJB 관련 클래스를 반드시 사용)과 개발환경 측면에서의 강제사항(EJB 컨테이너 사용) 떄문에 아래와 같은 문제가 발생한다.

-   개발 생산성 저하
-   유지 보수성 저하
-   테스트 용이성이 떨어짐
-   배포의 어려움

그래서 스프링을 EJB에서 문제가 되었던 부분을 해결하기 위해 비 침투적인(non-invasive) 방식을 도입하였고, 그렇게 스프링 프레임워크가 인기를 얻게 되었다.



 





















> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTcyMzY3MDcwMSwzNzI1NzEzMDcsMTI4Nz
kyMDMzNiwtMjEzMDQyMDEyNywtOTcyNjI5NThdfQ==
-->