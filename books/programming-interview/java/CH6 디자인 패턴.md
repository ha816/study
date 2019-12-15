# 빌더 패턴

* 다루고자 하는 객체 생성시 객체가 가지는 필드가 많을 경우 유용한 패턴.
* 객체 생성시 필요한 필드가 많아지면 가독성이 저하되고 필드 관리가 안된다. 
* 일반적으로 생성하고자 하는 객체 내부에 중첩 클래스(inner class)로 빌더 클래스를 만든다.
* 빌더는 세터메서드 호출마다 빌더 자신을 반환한다(메서드 연쇄;method chaining). 또한 아직 완성되지 않은 객체를 build 메서드에서 판별한다.
* 단점은 builder라는 객체를 추가로 생성해야 한다. 

# 컴포지트 패턴

* 객체들의 관계를 트리 구조로 구성하며, 사용자가 단일 객체와 복합 객체 모두 동일하게 다루도록 한다. 
* 대표적인 예는 일반적인 파일시스템의 파일과 디렉토리가 있다. File(단일)-Directory(복합)

![enter image description here](https://t1.daumcdn.net/cfile/tistory/99E9FF455C84AF1E20)


# 스트레티지 패턴

* 스트레티지 패턴은 **전략을 쉽게 바꿀 수 있도록 해주는 디자인 패턴** 
* 스트레티지(전략)이란 어떤 목적을 달성하기 위한 작업 방식, 문제를 해결하는 알고리즘 등을 뜻한다.
* 세부 구현을 변경할 필요 없이 실행 중이라도 교환할 수 있어 의존성 주입(DI; Dependency Injection)에 자주 사용된다.  

# 템플릿 메서드 패턴

* 템플릿 메서드 패턴은 알고리즘의 일부 또는 전부를 하위 클래스에서 구현하거나 위임하는데 사용한다. 
* 공통적으로 여러 객체가 사용할 메서드를 미리 템플릿으로 정의해두고 사용해도 되고, 재정의를 통해 원하는 기능을 구현해도 된다. 

# 데코레이터 패턴

* **다수의 데코레이터 객체가 작업에 관여하여 원하는 작업을 완성하는 패턴.**(다수 데코레이터의 조합을 통해 기능을 동적으로 유연하게 확장 할 수 있게 해주는 패턴)
* 기본 기능(Component)에서 필요한 기능이 너무 많은 경우, 데코레이터 클래스로 정의 한 후 필요한 데코레이터 객체를 조합하여 사용한다.
* 대표적인 예로 Java Stream 클래스가 데코레이터 패턴을 사용한다.

Component
: 기본 기능은 Component를 통해 구현한다. 즉, 클라이언트는 Component를 통해 실제 객체를 사용함

Decorator
: 기본 Component를 확장하여 추가 기능을 정의 한 후 데코레이터를 정의한다.

## Stream 클래스

JVM 외부 소스를 읽고 저장하는 자바의 기본 입.출력 클래스는 데커레이터 패턴을 사용한다. InputStream, OutputStream 클래스 그리고 하위 그 클래스는 구현 클래스에서 정의한 방법으로 데이터를 읽고 저장하는데 이를 상황에 따라 효율적으로 하기 위해서 함께 조합하여 사용한다. 

OutputStream은 클래스에서 구현한 메서드 대부분은 필요한 수행을 동작 후, 다른  OutputStream 클래스에 수행을 위임한다. 이 위임할 대상은 이미 OutputStream객체를 만들때 매개변수로 받는다.  

반면에 FileOutputStream이나 SocketOutputStream 같은 클래스의 실제 데이터를 저장하는 OutputStream 클래스는 write 메서드를 다른 클래스에 위임하지 않는다. 

```
@Test
public void decoratorPattern() throws IOException
File f = new File("File.txt");
FileOutputStream fos = new FileOutputStream(f);
BufferedOutputStream bos = new BufferedOutputStream(fos);
ObjectOutputStream oos = new ObjectOutputStream(fos);
```

FileOutputstream 클래스는 디스크에 파일을 저장하고,
BufferedOutputStream 클래스는 파일을 저장하는데 필요한 작업을 버퍼에 쌓아두고 한번에 여러 바이트씩 저장한다. 이렇게 하면 디스크에 파일을 저장할대 효율이 크게 향상된다. 

ObjectOutputStream은 자바에 내장된 객체나 primitive 타입을 스트림에 저장하는 직렬화 클래스다. 이 클래스는 파일이 어디에 기록되는 지 모른다. 다른 OutputStream클래스에 간단히 저장 권한을 위임할 뿐이의 데코레이터 객체가 작업에 관여하여 원하는 작업을 완성하는 패턴.
* 데코레이터 객체는 내부 필드로 다른 데코레이터 객체를 전달 받는다. 전달 받은 객체의 필요 메서드를 호출하면서 자신이 해야하는 작업을 수행한 다음 데코레이터 객체를 반환한다. 


# 플라이웨이트 패턴

> 플라이웨이트 패턴의 구현 방법을 설명할 수 있는가?

* 플라이웨이트 패턴은 불필요한 객체 생성을 줄이고 불변 객체의 재사용성을 높여 성능 향상을 꾀하는 패턴이다. 
* 대표적인 예로 Integer.valueOf 메서드()가 있다. 
* valueOf 메서드는 매개변수의 값을 보고 이전에 캐시된 값이면 새로운 인스턴스를 만들지 않고 이전해 생성해둔 인스턴스를 반환한다. 이 캐시의 기본범위는 -128 ~ 127이까지플라이 웨이트 패턴은 불필요한 객체의 생성을 피하고자 하는 패턴이다.
* 캐시를 써서 이미 생성된 객체가 있다면 그 객체를 재사용하여 성능 향상을 꾀한다.  

# 싱글턴 패턴

> 싱글턴 패턴은 어떻게 사용하는가?

* 싱글턴 패턴은 클래스가 오직 하나인 인스턴스만 생성한다는 것을 보장하는 패턴이다. 
* 하나의 인스턴스만 있기 때문에 관리하기가 편하고 성능 향상이 있을 수 있다.

싱글턴의 구현 방법에 따라 다르지만 쓰레드 세이프하지 않을 수 있기 때문에, 자바 5에서 공개된 Enum 타입으로 싱글턴 패턴을 구현하는 것이 좋다. 싱글턴 패턴을 적용할 인스턴스를 하나의 원소만 가지는 Enum 타입으로 생성하면 JVM 에서 싱글턴을 보장한다. > 

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTcwNzUzMzUyNywxMjIxNjc1MzI0LC0yMD
M4MTQwOCwtNTY0MDgxNiwtMzc2Nzc4MTY3LC00MDc0OTgxNCwt
MTExMTg1MTQ3OV19
-->