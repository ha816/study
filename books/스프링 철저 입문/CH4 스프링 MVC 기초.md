# 스프링 MVC

스프링 MVC는 자바 기반의 웹 어플리케이션을 개발할때 사용하는 프레임워크의 하나로 프레임워크 아키텍처로 MVC 패턴을 채택했다. MVC 패턴을 적용한 웹 애플리케이션은 모델(Model), 뷰(View), 컨트롤러(Controller)와 같은 세 가지 역할의 컴포넌트로 구성되어 클라이언트의 요청을 처리한다. 

>모델(Service)
>애플리케이션 상태(데이터)나 비즈니스 로직을 제공하는 컴포넌트

>뷰
>모델이 보유한 애플리케이션 상태(데이터)를 참조하고 클라이언트에 반환할 응답 데이터를 생성하는 컴포넌트.

>컨트롤러
>요청을 받아서 모델과 뷰의 호출을 제어하는 콤포넌트. 컨트롤러라는 이름처럼 요청과 응답의 처리 흐름을 제어한다. 

## 스프링의 MVC를 이용한 웹 어플리케이션 개발시 특징 

* POJO 구현
* 애너테이션을 이용한 정의 정보실정
* 유연한 메서드 시그니처 정의
* Servelet API 추상화
* 뷰 구현 기술의 추상화
* 스프링의 DI 컨테이너와 연계

## MVC 프레임워크로서의 특징

* 풍부한 확장 포인트 제공
* 엔터프라이즈 애플리케이션에 필요한 기능 제공
* 서드파티 라이브러리와의 연계 지원

## 스프링의 아키텍처

스프링 MVC는 프론트 컨트롤러 패턴(front controller)라는 아키텍처를 채택하고 있다. 프런트 컨트롤러 패턴은 클라이언트 요청을 프런트 컨트롤러 컴포넌트가 받아 요청 내용에 따라 수행하는 핸들러를 선택하는 아키텍처다. 

![enter image description here](https://www.tutorialspoint.com/design_pattern/images/frontcontroller_pattern_uml_diagram.jpg)

이 프론트 컨트롤러 패턴은 공통적인 처리를 하는 프런트 컨트롤러에서 통합 할 수 있어 핸들러에서 처리하는 내용을 줄일 수 있다. 스프링에서는 아래와 같은 기능을 프론트 컨트롤러가 대행하고 있다. 

* 클라이언트의 요청 접수, 요청 결과 응답
* 요청 데이터를 자바 객체로 변환
* 입력값 검사(Bean Validation)
* 핸들러 호출
* 뷰 선택
* 예외처리

스프링의 MVC 프론트 컨트롤러는 DispatcherServelet 클래스로 구현한다. 그리고 일반적인 처리 흐름은 아래와 같다. 

![enter image description here](https://howtodoinjava.com/wp-content/uploads/2015/02/Spring-dispatcher-servlet.png)

Servlet은 Handler Mapping 이너페이스의 getHandler 

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQ2OTY0OTc5NCwtMzM1NDA2MzU1LC05ND
k3ODE4NzcsMTgzNzk3ODgwMywxMzI5NDMzMjMyLC0yMDcxMjM1
ODFdfQ==
-->