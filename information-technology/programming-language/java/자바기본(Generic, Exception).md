# 제네릭 이해하기

> 컬렉션 API에서 제네릭을 어떻게 사용하는지 설명하라. 

제네릭은 `매개변수화된 타입`이라고도 알려져 있다. 즉 매개변수로 어떤 타입을 입력받은 타입을 말한다. 만약 컬렉션 클래스에서 매개변수로 특정 타입을 입력 받으면 컴파일러는 그 특정 타입만 포함될 수 있게 컬렉션을 제한한다. 

컬렉션 API에 있는 모든 클래스는 제네리릭을 사용해서 만들었다. List인터페이스와 그 구현은 한 가지 타입의 매개변수만 받는다. Map인터페이스는 두가지 타입 매개변수를 받는다. 

> 타입의 변화는 제네릭에 어떤 영향을 미치는가? 

클래스 B가 A를 확장하면 B는 A의 하위 타입이다. 하지만 `List<B>`는 `List<A>`의 하위 타입이 아니다. 공분산이라 알려진 자바의 제네릭 시스템이는 이에 관한 모델링 방법이 없다. 

제네릭 타입을 다룰때는 때때로 클래스의 하위타입을 받아들여야 하는 경우도 있다. 이럴때 `?`인 와이들 카드를 사용한다. 

예를들어 List<? extends A>는 컴파일러에게 A 클래스를 확장한 모든 인스턴스를 List원소로가지는 매개변수 타입이다.  

> 구상화한다는 건 어떤 의미인가? 

본래 구상화 (reified)는 것은 실행 시에 이용할 수 있다는 것이다. **기본적으로 자바의 제네릭 타입은 구상화가 아니다.** 왜냐하면 .class 파일 정의의 일부가 아닌, 제네릭 매개변수를 직접 사용하는 구현 코드의 모든 타입 정보를 컴파일러가 확인하기 때문이다. 

실제 제네릭을 사용한 코드로 만든 실행 코드를 자바 디컴파일러인 JAD를 이용해서 디컴파일하면 모든 제네릭 타입 정보가 사라진다. 


# 예외처리 하기

자바의 예외처리 구조를 이루는 주요 클래스를 설명하라. 

Error와 Exception은 모두 Throwable하다. 
Error는 프로그램이 실행을 멈추게 되는 치명적이 오류로 대표적으로는 RunTimeError, OutOfMemoryError 등이 있다. Exception은 유연하게 프로그래머가 처리 가능한 예외로 nullPointerException등 이 있다. 

예외는 확인해야하는 명시적 예외(Checked Exception)와 그것이 아닌 예외(Runtime Exception)가 있다. 명시적 예외로 처리된 메서드는 반드시 해당 예외를 try구문으로 처리해야 한다. 

>런타임 예외와 명시적 예외 중 어떤것이 더 좋은가?

명시적 예외에서는 코드에서 무엇이 잘못되었는지 직접 예외 처리를 해줘야 한다. 이때 어떤 동작이 잘못되었는지 정확한 예외를 던져줘야 한다. 

```
public String getHostName() throws Exception
```
위와 같은 명시는 예외가 발생했을때 무엇인 잘못되었는지 단서를 찾을 수 없다. 

런타임 예외의 경우 예외를 정의하는 것이나 다시 처리하는것 모두 선택 사항이다. 

필자는 일반적으로 런타임 예외를 선호한다. 하지만 클라이언트가 해당 메서드를 호출했을때 어떤 예외가 발생하는지를 문서에 꼭 남긴다. 런타임 예외를 선호하는 이유는 명시적 예외에서는 try문을 기본 코드에 추가해야 할 뿐만 아니라 유지보수가 어려워지기 때문이다. 

> try-with resource는 무엇인가?

자바 7에서 추가된 try-with resource는 기존 try-catch-finally를 대체한다. try 뒤에 붙은 자원은 반드시 자원종료를 해주기 때문에 finally를 붙여 구구절절 처리를 해주지 않아도 된다. 


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0NzE2MzMxMF19
-->