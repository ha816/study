# JAVA 

>**자바의 원시 타입은?** 
>자바에는 8가지 원시 타입이 있다. boolean(1byte), byte(1), char(2), short(2), int(4), long(8), float(4), double(8). 

>**자바의 Wrapper 클래스는? 그리고 AutoBoxing이란?**
> Wrapper 클래스는 원시 타입에 대응하는 클래스이다.  객체 생성시 내부 필드에 final 키워드가 붙어있다. 즉 불변 객체이다. 원시 타입을 대응하는 Wrapper로 만들어 사용해야 할때가 있는데 이를 자동으로 해주는 것이 AutoBoxing이다. 반대는 UnBoxing이다. 

>**자바의 변수의 종류는?**
> 변수가 가지는 값의 종류에 따라 참조 변수(reference variable), 원시 변수(primitive variable)가 있다. 그리고 클래스에 선언한 위치에 따라서 멤버 변수, 로컬 변수로 나뉜다. 멤버 변수는 다시 인스턴스 변수와 클래스변수로 나뉘는데 클래스 변수는 Static이 붙은 정적 변수를 말한다. 

>**자바의 클래스와 객체의 차이는?**
>클래스는 생성할 객체의 일종의 명세서이다. 그리고 객체는 그러한 명세에 따라 생성되고 다른 객체와 상호작용하여 필요한 동작을 하는 주체다. 

>**JVM의 역할은? 그리고 GC(가비지 컬렉션)은  어떤 역할을 하는가?**
>JVM은 Java Virtual Machine의 약자로, 자바 프로그램이 실행되는 곳이다. JVM은 운영체제 OS와 애플리케이션 사이에 위치하면서 애플리케이션이 특정 플랫폼에 상관없이 독립적으로 실행되도록 한다. 또한 JVM은 메모리 관리를 위해 GC를 수행한다. 

>자바의 메모리 구조는? 변수별로 저장되는 위치는?
>

>불변객체란? 왜 사용하는지?

>final 키워드는? 어디에 사용하는지?

>static 키워드는? 

>접근 권한 수정자(private, package-private, protected, public)에 대해서 설명해보자.

>다형성과 상속이란 무엇인가?

>Overloading과 Override의 차이? 그리고 Override 애노테이션은 어떤 역할을 하는가?

>String은 메모리에 어떻게 저장되는가? String 객체의 값을 변경할 수 있는가? 인터닝이란? 

>제네릭이란? 구상화(reified)한다는 건 어떤 의미인가? 

>자바의 예외처리 구조를 이루는 주요 클래스를 설명하라. Error와 Exception의 차이는?

>Comparable과 Comparator 인터페이스의 차이는?

# DataStrucutre

>ArrayList와 LinkedList의 차이

>HashMap은 무엇이고 성능은? 최악의 경우를 가정하면 어떤가?

>Collection에 대해서 아는 클래스를 최대한 설명해보라

>HashMap과 LinkedHashMap의 차이, ConcurrentHashMap의 차이는?

>Tree란? HashMap과 TreeMap의 차이는?

# DesignPattern

>Singleton, Decorator, Composite, Strategy, Builder, Template method, Fly-weight
>Singleton: Singleton 클래스는 시스템에서 유일무이한 객체를 말한다. 
>Decorator: Decorator 객체는 본연의 기능을 수행하는 Component 객체를 주입받아 클라이언트 입장에서는 같은 기능을 사용하지만 내부적으로 본연의 기능 뿐만 아니라 상황에 맞는 추가 기능을 사용할 수 있게 된다.
>Composite: 클라이언트 입장에서 단일-복합 객체를 모두 공통적인 객체로 사용할 수 있는 패턴. FileSystem의 구조가 대표적인 Composite 패턴의 예이다. Directory-File(복잡, 단일).
>Strategy: 객체간 의존성을 줄이기 위해 사용하는 패턴으로 사용할 객체를 주입 받아 사용한다. IoC이라고도 불린다.
>Builder: 한 객체에 필드가 너무 많을 경우 사용하는 패턴이다. 보통 클래스 내부에 builer 클래스를 정의해서 사용하며, 메서드 채인 방식으로 필요한 필드를 채워 최종적으로 build 메서드를 호출해 필요한 객체를 생성한다.
>Template method : 공통적으로 사용한 메서드를 미리 상위 클래스에 정의를 하고 하위 클래스에서 정의해둔 메서드를 사용하거나 재정의해서 사용한다.
>Fly-weight : 자주 사용되는 객체를 캐시 해두어, 필요시 캐시에 있던 객체를 재사용하는 패턴.

# MultiThread

> 자바 4에서 도입된 동시성 프레임워크(Executor Framework)가 있다.  Executor 프레임워크은 어떤 특징이 있는가?

>volatile 키워드는?

> Atomic 클래스는 무엇을 제공하는가? 

# Framework & Application

>프레임워크와 라이브러리의 차이

> 스프링이 유명한 이유는?

>DI는?AOP는?

>Spring의 객체 스코프는?

>Spring의 MVC패턴은? 

>Servelet Container는 무엇이고 하는 역할은?

>Servelet은 무엇이고 하는 역할은? 

> WAR 파일이란 무엇인가?

>Filter와 Interupt의 차이

# Database

>**Indexing은? 그리고 Indexing 방식은?**

>**트랜잭션이란? 트랜잭션의 성질?**

> **트랜잭션 격리수준(isolation level)이란?**.

>**PreparedStatement와 Statement의 차이는?**

>트랜잭션이란? 

>PreparedStatement와 Statement의 차이는?

>**클러스터와링과 리플레케이션의 차이**

>**샤딩(Sharding)과 파티셔닝(Partitioning)의 차이는?**



# Network & Protocol

>**TCP(Transmission Control Protocol)와 UDP(User Datagram Protocol)의 차이는?**

>**HTTP? 그리고 HTTP 1.1과 HTTP 2.0간의 차이는?**

>**REST란?**

>**URI(Uniform Resource Identifier)과 URL(Uniform Resource Locator)의 차이는?**

# OperationSystem

> **32비트 컴퓨터와 64비트 컴퓨터의 차이**
  
> **프로세스와 쓰레드의 차이**

> **데드락이란 무엇이고 발생하는 조건은?** 


>**데드락 처리는?**


# Server

>**WAS와 Web Server의 차이는?**

>**WebContainer(Servelet Container)의 역할은?**


# ETC

>**Scaling out과 Scaling up의 차이?**

>**동기 처리와 비동기 처리의 차이** 

# Architecture

>**MSA(Micro Service Architecture)는?**

> **Docker는?**

# TOOL

> MAVEN?

> GIT 형상관리 툴?

> Written with [StackEdit](https://stackedit.io/).



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA4NDE3NDgzN119
-->