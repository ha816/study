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

1. 서블릿은 Handler Mapping 인터페이스의 getHandler 메서드를 호출해서 실제 요청을 처리하는 Handler 객체(컨트롤러)를 가져온다. 
2. Servlet은 HandlerAdapter 인터페이스의 handle 메서드를 호출해서 Handler(컨트롤러) 객체의 메서드 호출을 의뢰한다.
3. HandlerAdapter 인터페이스의 구현 클래스인 Handler객체(컨트롤러)에 구현된 메서드로 요청 처리를 한다.
4. 서블릿은 ViewResolver 인터페이스의 resolveViewName 메서드를 호출해서 Handler 객체에서 반환된 뷰 이름에 대응하느 View 인터페이스 객체를 가져온다.
5. 서블릿은 View 인터페이스의 render 메서드를 호출해서 응답 데이터에 대한 렌더링을 요청한다. View인터페이스의 구현 클래스는 JSP와 같은 렌더링 데이터를 생성한다.
6. 마지막으로 서블릿은 클라이언트에 응답을 반환한다. 

흐름을 보면 알겠지만, 처리 내용 대부분이 인터페이스를 통해 실행되고 이는 프레임워크의 기능을 확장하기 쉽게 한다. 
이제 프론트 컨트롤러를 구성하는 컴포넌트에 대해 알아보자.

### DispatcherServlet

프런트 컨트롤러이자 기본적인 처리 흐름을 제어하는 사령탑 역할을 한다. 그림에서는 표현되지 않았지만 아래와 같은 인터페이스와도 연동되어 프레임워크 전체 기능을 수행한다. 모두 기본적으로 적용되어 있다.

|인터페이스명|역할|
|--|--|
|HandlerExceptionReolver  |예외처리를 위한 인터페이스, 스프링 MVC가 제공하는 기본 구현 클래스가 적용되어 있다.  |
|LocalResolver, LocaleContextResolver  |클라이언트의 로컬 정보를 확인하기 위한 인터페이스, 기본적으로 적용 되어있다. |
|ThemeResolver  |클라이언트의 테마를 결정하기 위한 인터페이스|
|FlashMapManager|FlashMap이란 객체를 관리하기 위한 인터페이스. FlashMap은 PRG 패턴의 Redirect와 Get사이에 모델을 공유하기 위한 Map객체다.|
|RequestToViewNameTranslator|핸들러가 뷰이름과 뷰를 제공하지 않는 경우 뷰이름을 해결하기 위한 인터페이스|
|HandlerInterceptor|핸들러 실행 전후에 공통 처리를 구현하기 위한 인터페이스|
|MultipartResolver|멀티파트 요청 처리를 위한 인터페이스, 예외적으로 기본 적용이 되어 있지 않다.|

### Handler(Controller)

핸들러 객체가 하는 일은 받은 요청에 따라 필요한 처리를 수행하는 것이다. 용어가 헷갈릴수 있는 데 짚고 가자면, 프레임 워크 관점에서는 핸들러라 부르지만 개발자가 작성하는 클래스의 관점에서는 컨트롤러라고 부른다. 

핸들러는 @Controller 애너테이션을 클래스에 지정하고 요청 처리를 수행하는 메서드에 @RequestMapping을 지정하면 만들 수 있다. 

### HandlerMapping 인터페이스

요청에 대응하는 핸들러를 선택하는 역할을 담당한다. 구현 클래스로는 RequestMappingHandlerMapping 클래스로@RequestMapping 애너테이션에 정의된 정보로 실행할 핸들러를 선택한다. 

@RequestMapping 애너테이션이 붙은 메서드는 핸들러 메서드이다. 

|요청매핑 정보| 핸들러 |
|--|--|
|/hello  | 핸들러 메서드1  |
|/goodbye  | 핸들러 메서드2 |

### HandlerAdapter

핸들러 메서드를 호출하는 역할을 한다. 앞서 RequestMappingHandlerMapping에서 선택되어 가져온 핸들러 메서드를 호출할 때는 RequestMappingHandlerAdapter 클래스를 사용한다.  RequestMappingHandlerAdapter클래스에는 핸들러 메서드에 매개변수를 전달하고 메서드의 처리결과를 반환 값으로 돌려 보내는 것과 같은 스프링의 상당히 중요한 역할을 담당한다. 핸들러 메서드에 매개변수를 전달할때는 요청 받은 데이터를 자바 객체로 변환하고, 입력값이 올바른지 검사(Bean Validation)하는 것 까지 한꺼번에 이뤄진다. 

파라미터나 








> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTgzODAwMjkyMywtNjg1MTY3NzAzLDczNT
AzMzQ1MCwxODE1NzA1NzI2LC0zMzU0MDYzNTUsLTk0OTc4MTg3
NywxODM3OTc4ODAzLDEzMjk0MzMyMzIsLTIwNzEyMzU4MV19
-->