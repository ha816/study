# JAVA 

>**자바의 원시 타입은?** 
>자바에는 8가지 원시 타입이 있다. boolean(1byte), byte(1), char(2), short(2), int(4), long(8), float(4), double(8). 

>**자바의 Wrapper 클래스는? 그리고 AutoBoxing이란?**
> Wrapper 클래스는 원시 타입에 대응하는 클래스이다.  객체 생성시 내부 필드에 final 키워드가 붙어있다. 즉 불변 객체이다. 원시 타입을 대응하는 Wrapper로 만들어 사용해야 할때가 있는데 이를 자동으로 해주는 것이 AutoBoxing이다. 반대는 UnBoxing이다. 

>**자바의 변수의 종류는?**
> 변수가 가지는 값의 종류에 따라 참조 변수(reference variable), 원시 변수(primitive variable)가 있다. 그리고 클래스에 선언한 위치에 따라서 멤버 변수, 로컬 변수로 나뉜다. 멤버 변수는 다시 인스턴스 변수와 클래스변수로 나뉘는데 클래스 변수는 Static이 붙은 정적 변수를 말한다. 

>**자바의 클래스와 객체의 차이는?**
>클래스는 생성할 객체의 명세서이다. 그리고 객체는 그러한 명세에 따라 생성되고 다른 객체와 상호작용하여 필요한 동작을 하는 주체다. 객체는 상태에 해당하는 필드와 메소드를 가진다. 

>**JVM의 역할은? 그리고 GC(가비지 컬렉션)은  어떤 역할을 하는가?**
>JVM은 Java Virtual Machine의 약자로, 자바 프로그램이 실행되는 곳이다. JVM은 운영체제 OS와 애플리케이션 사이에 위치하면서 애플리케이션이 특정 플랫폼에 상관없이 독립적으로 실행되도록 한다. 또한 JVM은 메모리 관리를 위해 GC를 수행한다. 

>**자바의 메모리 구조는? 변수별로 저장되는 위치는?**
> 자바 메모리는 쓰레드 전체가 공유하는 메모리 영역(Shared Memory)과 개별 쓰레드가 가지는 고유 메모리 영역(Non-Shared Memory) 두 가지로 나뉜다. 공유 메모리 영역에는 Method 영역, Heap 영역이 있다. Method 영역은 클래스 영역이라고도 불리는데 클래스 정보(이름, 필드, 메서드), 클래스 변수, Run-Time Constant pool 이 저장되는 영역이다. Heap 영역은 객체들이 저장되는 공간이다. 

>**불변객체란? 왜 사용하는지?**
>불변 객체는 객체의 상태가 절대 변하지 않는 것을 보장하는 객체를 말한다. 대표적인불변객체는 String이 있다. 불변 객체를 사용하면 Thread-Safe한 장점이 있고 방어적 복사본 코드를 만들 필요가 없다.

>**final?**
>final 키워드는 변수, 클래스, 메서드에 붙일 수 있다. 원시타입변수에 붙이면, 값을 바꿀 수 없고, 참조변수에 붙이면, 주소값을 변경할 수 없다. 클래스에 붙으면 해당 클래스를 상속받을 수 없다는 것을 말한다. 메서드에 붙이면, 메서드를 오버라이딩(재정의)할 수 없다는 것을 말한다. 

>**static?** 
>static 키워드는 변수, 메서드, 클래스에 붙일 수 있다. Static이 붙은 변수, 메서드는 해당 클래스내에서 공유되는 자원으로 JVM 동작시 Method Area에서 이미 존재한다. 일반 클래스에는 static 키워드를 붙일 수 없고, 내부 클래스(inner class)에는 붙일 수 있다. static이 붙은 내부 클래스는 마찬가지로 공유되는 자원으로 이미 존재하고 있는 객체다.

>**접근 권한 수정자(private, package-private, protected, public)?**
> 접근 권한 수정자는 클래스, 변수, 메서드에 붙일 수 있다. private이 붙으면 해당 클래스에서만 접근이 가능하다. package-private은 디폴트 값으로 해당 패키지내에서 접근이 가능하다. protected는 같은 패키지내에서 또는 상속을 받아 접근이 가능하다. protected 부터는 사실상 다른 클래스에 공유된다고 봐도 무방하다. public은 어떤 곳에서도 접근이 가능하다.

>**다형성(Polymorphsim)과 상속(Inheritance)**
>다형성은 하나의 객체가 다른 역할을 하는 객체로 사용될 수 있다는 의미이다. 항상 가능한 것은 아니고 특정 상위 클래스를 확장한 하위 클래스는 상위 클래스를 대신할 수 있다. 
>상속은 상위 클래스의 기능, 설계를 그대로 하위 클래스에 가져오는 것을 말한다. 따라서 하위 클래스는 온전히 상위 클래스의 역할을 할 수 있으면 추가 기능을 추가할 수 도 있다. 

>**abstract와 interface의 차이는?**
>Java에서 abstract 클래스와 interface는 모두 그 자체로는 객체가 될 수 없다. abstract 클래스는 실제 구현된 메서드와 구현되지 않은 추상 메서드를 모두 가질 수 있다. 객체화할 하위 클래스에서 abstract 클래스를 상속받으면 해당 메서드를 반드시 구현해야 한다. 마찬가지로, interface를 상속 받아 객체화할 하위 클래스에서도 interface에서 정의한 메서드를 구현해야 한다. 
>interface는 access modifier가 없고 기본적으로 모두 public이다.

>**Overloading과 Override의 차이? 그리고 Override 애노테이션은 어떤 역할을 하는가?** 
>Overloading(과적합)은 하나의 메서드 이름을 여러 메서드에서 사용하는 방법이다. 
>Override(재정의)은 상위 클래스의 메서드를 하위 클래스에서 재정의하는 것을 말한다. 
>@Override 에너테이션은 코드 가독성과 개발자의 실수를 방지해주는 역할을 한다. @Override가 붙은 메서드는 상위 클래스나 인터페이스에 있는 특정 메서드를 재정의했다는 의미이다. 현재 다른 메서드와 차이를 명확히 밝혀 가독성을 높이고, 상위 메서드가 개발자의 실수로 변경이 일어나면 컴파일 에러를 낸다.

>**제네릭? 구상화(reified)한다는 건 어떤 의미인가?** 
>Generic이란 매개변수화된 타입이라고 한다. 즉 사용하려는 타입을 매개변수로 받는것을 말한다. 예를 들어 List는 담을 원소의 타입을 제네릭을 이용해서 정할 수 있다. 그러면 컴파일러는 list에 특정 타입만 포함되도록 컬렉션을 제한한다. 
>실체화는(reified) 실행 시에 이용할 수 있다는 것인데, 기본적으로 제네릭은 실체화가 아니다. 컴파일시에는 존재하지만 실제 동작하는 코드에는 존재하지 않는다.

>**String은 어느 메모리 공간에 올라가는가? 인터닝(interning)이란?** 
>String 객체는 대표적인 불변객체로, new 키워드로 생성하지 않는한 String Constant Pool에 올라간다. 
>String Interning이란 불변의 독립적인 문자열을 저장하는 방법이다. String은 intern메서드를 가지는데, 상수 풀에 해당 문자열이 있으면 풀에 문자열을 가져오고 아니라면, 이 문자열은 풀에 추가되고 반환된다. 

> **StringBuilder와 StringBuffer의 차이는?**
> StringBuilder는 Thread Safe하지 않지만 StringBuffer는 그러하다. 따라서 StringBuilder가 더 성능상 빠르다. 

>**Error와 Exception의 차이는?**
>Error는 발생 시 더 이상의 작업을 수행할 수 없는 치명적인 문제를 말한다. Error가 발생하면 JVM이 중단되며 대표적인 Error로는 StackOverFlow, HeapOutOfMemory이 있다. Exception은 발생해도 개발자가 추가 처리 코드를 통해 처리가 가능하다. 

>**Exception예외처리의 주요 클래스를 설명하라**
>RuntimeException -> Exception -> Throwable
>예외는 크게 RuntimeException(unchecked Exception)과 checked과 명시적 Exception으로 나뉜다. RuntimeException은 런타임시 발생하는 예외로 대표적으로는 NullPointException, ArrayOutofBound 등이 있다. 명시적 예외는 코드 상에 예외처리를 위한 코드를 작성해야 하며, 대표적으로 IO Excetpion이 있다. 

>**Comparable VS Comparator**
>Comparable과 Comparator 모두 객체들의 순서를 정하기 위해 사용한다. Collections.sort와 같이 정렬 메서드를 사용하고 싶다면 객체들이 Comparable 인터페이스를 구현해야 한다. Comparable은 자연스러운 순서를 정할때 사용하고 Comparator는 원하는 임의로 순서를 정하고 싶을 때 사용한다. 

# DataStrucutre

>**Collection 인터페이스?**
>Java에서 Collection 인터페이스를 상속하는 클래스는 List, Set이 대표적이다. Collection 인터페이스에는 선형 구조의 데이터 공간에 원소를 추가, 삭제하는 메서드가 정의되어 있다. 

>**List, Set?**
> 모두 선형 구조 데이터 구조로, List는 가지고 있는 원소들의 중복을 허용하고, 순서도 기억한다. 그에 반해 Set은 원소들의 중복을 허용하지 않고, 순서도 기억하지 않는다.

>**ArrayList와 LinkedList의 차이**
>ArrayList는 array기반이기 때문에 랜덤 엑세스로 특정 인덱스를 찾는데 빠르다. 하지만 가장 끝에 원소를 추가하는 것을 제외하고 특정 자리에 원소를 추가할때는 자리 뒤쪽 원소를 모두 뒤로 보내야하기 때문에 추가 연산이 있다. 그리고 배열이 가득 차면 더 넓은 공간의 배열을 새로 만들어야 한다. LinkedList는 다음 원소를 가르키는 추가 공간을 사용한다. 하지만 어떤 자리에 원소를 추가해도 비교적 적은 연산으로 추가가 가능하다. 정리하자면, 처음과 중앙에 원소를 추가, 삭제하는 경우가 적고, 대량의 원소를 다룬다면 ArrayList를 사용하는게 유리하다. 그 외라면 LinkedList가 유리하다. 인의 차이는?
 rrrr ke HaMp은 은? 최악의 상황은?**
>HashMap은 Hash를 이용하여 Key-Value 쌍을 가지는 데이터 구조이다. HashMap은 주어진 키에 대응하는 값을 찾는데 상수시간만에 찾는다. HashMap은 성능은 Hash함수에 달려있는데, 극단적으로 Hash함수가 같은 값만을 반환한다고 가정하면, 항상 Hash Collision이 발생하는 최악의 상황이 발생한다. 이런 최악의 상황은 HashMap의 성능을 떨어트린다.

>**LinkedHashMap, TreeMap?** 
>LinkedHashMap은 입력 받은 Key-Value 쌍의 입력 순서를 기억한다. TreeMap은 Map 상단에 key-value 노드로 구성된 Tree를 만들어 키를 기준으로 정렬이 가능해진다.

>**SynchronizedMap과 ConcurrentHashMap의 차이는?**
> SynchronizedMap과 ConcurrentHashMap 모두 Thread-Safe를 보장한다. SynchronizedMap은 데이터 수정이 일어날때 완전히 모든 데이터에 락을 걸고, 다른 쓰레드들은 락이 풀렸을 경우에만 접근이 가능하다. ConcurrentHashMap은 데이터를 추가하거나 수정할때만 segment라는 일부의 맵만 락이 걸린다. 그래서 다른 쓰레드들은 그 일부의 맵을 제외하고는 읽기 및 수정이 가능하다. 

>**Tree?**
> Node와 Edge로 이루어진 자료 구조이며 사이클이 존재하지 않는다. 대표적인 Tree로는 Binary Tree가 있다. 

>**Heap**
>Heap은 최소값이나 최대값을 찾는데 적합한 자료구조이다. 최소값에 적합한 Heap을 MinHeap, 최대값에 적합한 Heap을 MaxHeap이라 한다. 
>Heap은 Complete Binary Tree(완전 이진 트리)이어야 한다. 완전 이진 트리란 최하단과 바로 그 위의 레벨 노드들을 제외한 모든 노드들이 온전히 2개의 자식 노드를 갖는 트리를 말한다.
>minHeap일 경우 부모 노드가 자식 노드보다 항상 작아야 한다. 반대로 maxHeap일 경우는 부모 노드가 자식 노드보다 항상 커야 한다. 



# DesignPattern

>**Singleton, Decorator, Composite, Strategy, Builder, Template method, Fly-weight, Proxy**
>Singleton: Singleton 클래스는 시스템에서 유일무이한 객체를 말한다. 
>Decorator: Decorator 객체는 본연의 기능을 수행하는 Component 객체를 주입받아 클라이언트 입장에서는 같은 기능을 사용하지만 내부적으로 본연의 기능 뿐만 아니라 상황에 맞는 추가 기능을 사용할 수 있게 된다.
>Composite: 클라이언트 입장에서 단일-복합 객체를 모두 공통적인 객체로 사용할 수 있는 패턴. FileSystem의 구조가 대표적인 Composite 패턴의 예이다. Directory-File(복잡, 단일).
>Strategy: 객체간 의존성을 줄이기 위해 사용하는 패턴으로 사용할 객체를 주입 받아 사용한다. IoC이라고도 불린다.
>Builder: 한 객체에 필드가 너무 많을 경우 사용하는 패턴이다. 보통 클래스 내부에 builer 클래스를 정의해서 사용하며, 메서드 채인 방식으로 필요한 필드를 채워 최종적으로 build 메서드를 호출해 필요한 객체를 생성한다.
>Template method : 공통적으로 사용한 메서드를 미리 상위 클래스에 정의를 하고 하위 클래스에서 정의해둔 메서드를 사용하거나 재정의해서 사용한다.
>Fly-weight : 자주 사용되는 객체를 캐시 해두어, 필요시 캐시에 있던 객체를 재사용하는 패턴.
>Proxy : Proxy는 우리말로 대리자, 대변인 이라는 뜻이다. 원래 작업을 해야할 객체가 아닌 Proxy가 대신해서 작업을 수행한다. Proxy는 실제 작업해야할 객체를 참조 변수로 가지고 있다. 실제 필요한 작업은 참조하던 객체의 메서드를 호출하는데 호출 전후로 별도의 로직을 수행할 수 있다.
>Adapter : 

# MultiThread

> **Executor Framework는? 어떤 특징이 있는가?**
> 자바 4에서 추가된 프레임 워크이다. 

>**volatile 키워드는?**
> volatile 키워드가 붙은 변수는 읽거나 쓸때, CPU 캐시가 아니라 메인 메모리에서 직접 활용한다. 멀티 쓰레드 환경에서 CPU 캐시마다 변수의 상태가 다를 수 있기 때문에 변수값이 불일치하는 현상이 생긴다. 이럴 때 volatile을 사용할 수 있다. volatile 변수에 쓰기 명령이 끝나면, 해당 변수를 사용하는 모든 쓰레드 값을 가장 최근 값으로 바꾼다. 
> 하지만 volatile이 항상 최선은 아니다. 하나의 Thread가 아닌 여러 Thread가 write하는 상황에서는 적합하지 않다. 그리고 cache가 아닌 메모리에 접근하기 때문에 느리다.

> **Atomic 클래스는 무엇을 제공하는가?** 
> 패키지 중 java.concurrent.atomic 을 살펴보면 원자적 연산을 수행할 수 있는 유용한 클래스들을 확인할 수 있다. Synchronized 키워드 없이도 쓰레드 세이프한 처리가 가능하다. 대기 상태에 들어가지 않는 넌블로킹 알고리즘은 비교 후 치환(compare-and-swap)과 같은 저수준의 명령을 활용한다. CAS 연산은 일단 성공적으로 치환할 수 있을 것이라고 희망하는 상태에서 연산을 실행해보고,  값을 마지막으로 확인한 이후에 다른 스레드가 해당하는 값을 변경했다면 그런 사실이 있는지를 확인이나 하자는 의미이다.

# Framework & Application

>**프레임워크와 라이브러리의 차이**
>라이브러리는 단순히 활용 가능한 도구들의 집합을 말한다.
>프레임워크는 프레임워크는 뼈대나 기반구조를 뜻하고, 제어의 역전 개념이 적용된 대표적인 기술입니다. 소프트웨어에서의 프레임워크는 '소프트웨어의 특정 문제를 해결하기 위해서 상호 협력하는 클래스와 인터페이스의 집합' 이라 할 수 있으며, 완성된 어플리케이션이 아닌 프로그래머가 완성시키는 작업을 해야합니다.
>주된 차이는 바로 프레임워크가 **애플리케이션 제어 흐름** 가진다는게 중요합니다. 프레임워크는 전체적인 흐름을 스스로가 쥐고 있으며 사용자는 그 안에서 필요한 코드를 짜 넣으며 반면에 라이브러리는 사용자가 전체적인 흐름을 만들며 라이브러리를 가져다 쓰는 것이라고 할 수 있습니다.

> **스프링이 유명한 이유는?**
>엔터프라이즈 시스템은 기본적으로 복잡한 시스템이다. 그래서 스피링이 나타나기 전에 무수히 많은 엔터프라이즈 프로젝트가 실패했다. 그러나 스프링 복잡한 시스템의 요구사항을 DI, AOP, PSA등을 통해 효과적으로 대응했고 살아남아 유명해졌다.

>**DI?**
>DI(Depenceny Injection)는 의존성 주입, **IoC(Inverse of Control)**라고 한다. 애플리케이션을 동작하기 위해 여러개의 컴포넌트를 통합해서 사용한다. 일반적으로 특정 컴포넌트를 사용할때 클래스 내부에서 구현 클래스를 직접 생성해서 사용하면 두 클래스간의 결합도가 높아진다.  결합도를 낮추는 방법으로 클래스의 외부에서 컴포넌트를 생성 한 후, 내부에 주입하여 사용한다. 

>**DI 컨테이너?**
>

>**AOP**
>AOP는 Aspect Oriendted Programing의 약자로, 시스템은 보통 특정 기능을 책임지는 여러 컴포넌트로 구성된다. 그러나 각 컴포넌트는 대체로 **본연의 기능 외에 로깅, 트랜잭션 관리, 보안 등 다른 서비스도 수행해야 하는 경우가 많다.** 이러한 서비스는 여러 컴포넌트에서 동시에 사용되는 경향이 있어 횡단 관심사(cross-cutting concerns)라고 한다. AOP는 공통적으로 사용되는 서비스를 모듈화해서 컴포넌트에 선언적으로 사용할 수 있도록 한다. AOP를 사용하면 본연에 관심사에 집중하는 컴포넌트를 만들 수 있다. 

>**Spring MVC**의 객체 스코프는?

>Spring이 채택한의 MVC패턴은 사실 프론트 컨트롤러(FrontController)패턴이다. 컨트롤러 패턴에서 프론트 컨트롤러는 요청을 처리하는 과정 전체의 제어 흐름을 담당한다. Spring에서는 DispatcherServelet이 대표적인 프론트 컨트롤러이다. 
  
>Spring의 빈 스코프는?
>애플리케이션 컨텍스트는 모든 빈의 생존기간을 관리한다. 
>singleton은 컨텍스트 기동시 빈 인스턴스가 하나만 생성되고, 그 빈을 공유한다. 
>prototype은 컨텍스트에 빈을 요청할때마다 새로운 빈이 생성된다.
>request는 HTTP 요청이 들어올때마다 새로운 빈이 생성된다.
>session은 HTTP 세션이 만들어질때 마다 새로운 빈이 생성된다.let은 무엇이고 하는 역할은? 



>Filter와 Interupt의 차이
>

# Database

>**Indexing은? 그리고 Indexing 방식은?**
>DB에서 원하는 값을 찾는데 모든 데이터를 검색하려면 오래걸린다. 그래서 indexing을 한다. DBMS에서 인덱스는 검색을 위해 저장, 수정 기능을 희생한다. 
>인덱싱 기법에는 B-Tree 인덱싱과 Hash 인덱싱이 있다. Hash 인덱싱은 해시 값으로 변경해서 저장하기 때문에 값의 일부만 검색할때는 사용할 수 없다. 하지만 검색 속도는 매우 빠르다. 
>B-Tree(Balanced-Tree)는 가장 범용적으로 사용되는 알고리즘이다. B-Tree는 Root, Branch, Leaf 노드로 구성된다. Leaf 노드는 실제 저장된 레코드를 가리킨다. 

>**트랜잭션이란? 트랜잭션의 성질?**
>Transaction은 여러 작업을 묶어 하나의 작업 단위로 만든 것을 말한다. 트랜잭션이 성립하려면 ACID성질을 만족해야 한다. 
>원자성 : 트랜잭션 내용은 모두 적용되거나 아니면 하나도 적용되지 않아야 한다. 일부만 적용될수 없다. 
>일관성 : 트랜잭션 적용 후에 데이터베이스에 모든 데이터는 일관 되어야 한다. 
>독립성 : 다수의 트랜잭션이 동시에 동작할때, 트랜잭션이 순차적으로 동작해야 한다.
>견고성 : 트랜잭션이 커밋되면 그 내용은 영구히 남아야 한다. 심지어 시스템 레벨의 재해라도 남아야 한다. 

> **트랜잭션 격리수준(isolation level)이란?**
> READ_UNCOMMITTED, READ_COMMITED, REPEATABLE_READ, SERIALIZABLE 강도 세기 순서대로 총 4가지가 있다. 
> READ_UNCOMMITTED : 커밋되지 않는 읽기가 가능하다. 즉 커밋되지 않은 변경 데이터를 다른 트랜잭션에서 읽을 수 있다.
> READ_COMMITED : 커밋 후 읽기, 커밋된 변경 데이터만 다른 트랜잭션에서 읽을 수 있다. 
> REPEATABLE_READ : 반복적인 읽기, Repeatable Read를 허용한다. 즉 Unrepeatable Read 현상을 방지한다. 
> SERIALIZABLE  : 시리얼화 가능, Phantom Read를 방지한다.

>**PreparedStatement와 Statement의 차이는?**
>Statement는 사용하는 쿼리문 자체를 DB서버에 보낸다. 때문에 SQL Injection에 취약하며 사용하는 쿼리가 비슷하더라도 성능 이점을 얻을 수 없다. PreparedStatement 쿼리에는 바인딩 변수를 사용할 수 있다. 이렇게 템플릿화 된 쿼리를 DB서버에 미리 알려주어 쿼리 작업의 일부 결과를 저장한다. 해당 쿼리를 반복적으로 요청시 저장해둔 결과를 재사용하여 쿼리 수행시 성능상 이득이 있다. 또한 SQL Injection의 예방할 수 있다.

>**클러스터와링과 리플레케이션의 차이**
>Cluster는 모든 데이터베이스를 실시간으로 동기화하고 데이터 변경을 확인한다. 
>Replication도 마스터 데이터베이스를 슬레이브 데이터베이스에 동기화를 하지만 비동기적으로 수행한다. 따라서 어떤 데이터베이스에는 변경되어 있지만, 다른 데이터베이스에서는 변경되지 않을 수도 있다. 데이터를 변경하는 쿼리는 마스터 데이터베이스에만 요청할 수 있다. Replication은 Cluster에 비해 변경이 매우 빠르기 때문에 주로 실시간 동기화가 필요 없는 경우 Replication을 사용한다.에 대해서

>**샤딩(Sharding)과 파티셔닝(Partitioning)의 차이는?**
>파티셔닝이란 데이터를 테이블로 분리해서 저장하지만 사용자 입장에서는 여전히 하나의 테이블 사용하는 솔루션이다. 파티셔닝에는 일반적으로 레코드 별로 파티션을 나누는 방식인 수평 파티셔닝과 컬럼 별로 파티션을 나누는 수직 파티셔닝이 있다. 
>Sharding은 수평 파티셔닝을 일컫는 말이다. Shard를 만들려면 Sharding key를 정하고 키를 기준으로 샤딩을 나누어야 한다. **MySQL의 파티션 테이블에서 인덱스는 전부 로컬 인덱스에 해당한다.** 모든 인덱스는 파티션 단위로 생성되며, 파티션에 관계없이 테이블 전체 단위로 글로벌하게 하나의 통합된 인덱스는 지원하지 않는다


# Network & Protocol

>**TCP(Transmission Control Protocol)와 UDP(User Datagram Protocol)의 차이는?**
>TCP는 발신지와 수신지를 연결하여 패킷을 전송하기 위한 논리적 경로를 만든다. 3-way handshaking은 목적지와 수신지를간 정확한 전송을 보장을 위해 세션을 수립하는 과정이다.  TCP는 패킷의 전송 순서를 지키고, 패킷의 손실이 없기 때문에 신뢰성있는 데이터 전송이 필요할때 사용한다.
>UDP는 발신지와 수신지간의 논리적인 경로가 없다. 각 데이터 그램은 서로 독립적으로 다른 경로로 전송된다. UDP는 전송순서를 지키지 않고, 손실이 있을수 있기 때문에 신뢰성있는 데이터의 전송을 보장하지는 못한다. 대신 TCP보다 속도가 빠르며 네트워크 부하가 적다는 장점이 있다.은?

>**HTTP? 그리고 HTTP 1.1과 HTTP 2.0간의 차이는?**
>HTTP는 HyperText Transfer Protocol의 약자로 WWW에서 주로 사용한다. 
>HTTP 1.1은 기본적으로 연결당 하나의 요청과 응답을 처리하기 때문에 동시전송 문제와 많은 리소스를 처리하기에 문제가 있다. HTTP2는 성능과 속도 모두 잡기 위해 새로운 기능 4가지를 제공한다. 
>Multiplexing: 요청과 응답시 멀티플렉싱을 통하여 레이턴시를 감소.
>Prioritization: 요청 우선순위 설정이 가능.
>Header Compression: HTTP header필드를 압축하여 프로토콜 오버헤드를 감소.
>Server Push: 필요한 리소스를 클라이언트 요청 없이 서버에서 전송.

>**REST란?**
>REpresentional State Transfer의 약자로 시스템간 API를 만드는데 활용하는 아키텍처 스타일의 일종이다. REST에서 가장 중요한 것은 리소스로, 리소스는 클라이언트에 공개할 가공된 정보를 말한다. 이런 리소스에 CRUD 조작을 위한 수단이 바로 REST API가 된다. 

>**URI(Uniform Resource Identifier)과 URL(Uniform Resource Locator)의 차이는?**
>URI는 클라이언트에 공개하는 리소스의 고유하게 식별자다.  따라서 URI를 안다면 어디서든 같은 리소스로 접근이 가능하다. 한 URI는 리소스 하나 뿐만 아니라 다수의 리소스로도 정의가 가능하다. 예를 들어 URI가 `user/1234`라면 ID가 1234인 사용자의 리소스 정보. `/users`라면 다수는 모든 사용자의 리소스 정보를 말한다.
>URL은 클라이언트에 공개하는 리소스의 대략적인 위치를 알려준다. 최종 목적지는 URI이고 중관 과정에 URL를 거쳐가기 때문에 URI는 URL을 포함하는 큰 개념이다. 예를 들어 https://www.google.co.kr/search?id=123가 있다고 하자. 여기서 URL은 https://www.google.co.kr/search 까지이고 실제 리소스 식별 주소 URI는 https://www.google.co.kr/search?id=123 

# OperationSystem

> **32비트 컴퓨터와 64비트 컴퓨터의 차이**
> 32bit 컴퓨터는 CPU 안에 Register의 처리 단위가 32bit임을 말한다. 이 레지스터가 표현할 수 있는 값의 범위에 따라 인식 주소값의 범위가 된다. 그래서 32bit 컴퓨터는 메모리 주소를 4GB까지밖에 인식하지 못한다. ($2^{32} =4 * 2^{10} *2^{10} *2^{10}$)
> 따라서 64bit 컴퓨터는 인식가능한 메모리 공간이 훨씬 크고 단위시간당 처리할 수 있는 데이터가 많다.
  
> **프로세스와 쓰레드의 차이**
>프로세스는 메모리에 올라와 실행되고 있는 프로그램을 말한다.  기본적으로 프로세스는 최소 하나 이상의 쓰레드를 가진다.
>쓰레드는 프로세스 내에서 실제 작업을 수행하는 주체이다. 

> **데드락이란 무엇이고 발생하는 조건은?** 
> 특정 프로세스가 작업에 필요한 자원을 얻지 못해 멈춰있는 상태이다.  다수의 프로세스가 동작하는 멀티 프로그래밍 환경에서 발생할 수 있다.
> 데드락은 운영체제에서 Mutual Exclusion, Hold&Wait, NoPreemption, Circular Wait 조건이 모두 만족하면 발생한다.

>**데드락 처리는?**
>예방(Prevention), 회피(Avoidance) 그리고 탐지 및 회복(Detection & Recover) 방법이 있다.
>예방(Prevention)은 교착 상태 발생 조건 중 하나를 제거함으로써 해결하는 방법으로 자원의 낭비가 심하다. 
>회피(Avoidance)는 은행원 알고리즘 (Banker’s Algorithm)으로 데드락이 발생하지 않을 상황에서만 자원을 할당해줌으로써 데드락을 피한다. 
>탐지 및 회복(Detection & Recovery)는 자원 할당 그래프를 통해 교착 상태를 탐지(Detection)하고 교착 상태를 일으킨 프로세스를 종료하거나, 할당된 자원을 해제함으로써 회복(Recovery)을 한다.

# Server

>**WAS와 Web Server의 차이는?**
>WAS(WebApplication Server)는 동적인 웹 서비스를 제공하기 위해 사용한다. 일반적인 WAS는 WebServer와 WebContainer로 구성된다. 
Web Server는 정적인 웹 서비스를 제공하며, WAS에 비해 기능이 가볍다. 아파치 서버, Nginx가 대표적이다. 

>**WebContainer(Servelet Container)의 역할은?**
>WebContainer는 사용하려는 웹 애플리케이션에서 필요로 하는 Servelet을 생성하고 관리한다. 그 밖에도 WebServer와 통신을 위한 API를 제공하며 요청마다 쓰레드를 생성하여, 하나의 서블릿이 멀티 쓰레드를 처리하도록 한다. 

>**Servelet?** 
>클라이언트의 요청이 들어오면 그 요청에 맞는 응답을 주는 서버측 컴포넌트다. 프론트 컨트롤러 패턴의 프론트 컨트롤러에 해당하고 요청-응답 과정에서 제어 흐름의 사령탑이다. 

# ETC

>**Scaling out과 Scaling up의 차이?**
>Scaling up은 한 컴포넌트를 크고 빠르게 만들어 부하를 줄인다.
>Scaling out은 병렬적으로 컴포넌트를 추가해서 부하를 줄인다. 

>**동기 처리와 비동기 처리의 차이** 
>동기는 순차적으로 필요한 처리를 수행한다. 순차적이기 때문에 흐름이 이해하기 쉽지만, 속도 측면에서 비효율적일 수 있다.
>비동기 처리는 작업 흐름이 순차적이지 않기 때문에 흐름이 이해하기 어렵지만, 속도 측면에서 효율적일 수 있다. 

>**WAR 파일이란 무엇인가?**
>

# Architecture

>**MSA(Micro Service Architecture)?**
>마이크로 서비스는 **독립적이고, 자기 완비적(self-contained)이며 느슨하게 결합된 비즈니스 기능을 모아 전체 시스템을 만드는 아키텍처 스타일**이다. 


> **Docker는?**
> 도커는 **컨테이너 기반의 오픈소스 가상화 플랫폼**이다. 가벼운 컨테이너를 만들고, 탑재하고 실행할 수 있는 플랫폼이다. 컨테이너는 도커 이미지(프로그램)의 구현체로 메모리나 CPU와 같은 자원을 할당받는다. 기존의 가상화 방식은 주로  **OS를 가상화** 했는데 OS이기 때문에 성능이 느렸고 도커는 프로세스를 가상화하기 때문에 빠르게 동작한다. 

# TOOL

> MAVEN?


# Maven(메이븐)

사용하기 좋은 애플리케이션을 만드는 데는 자바 소스 코드를 잘 작성하는 것 이외에도 많은 라이브러리, 플러그인이 필요하다. 한 애플리케이션을 동작하는데 필요한 모든 자원 관리하고 빌드하는 도구의 필요성은 꾸준히 있었다. 

>Maven이란?

**한 프로젝트 전체를 관리하는 빌드 도구**로, 자바 프로젝트를 컴파일, 테스트, 배포하는데 사용된다. 애플리케이션이 구동할때 필요한 자원들을 가져와 여러 빌드 작업을 수행한다. 

>Maven 의존성?

복잡합 애플리케이션은 JUnit, 아파치 commons, Guava등 여러 라이브러리에 의존성을 가지고 있다. 메이븐은 의존성을 정의할 수 있고, 인터넷 상의 www.maven.org 같은 저장소나 Artifactory, Nexus 등을 이용해서 회사 내부에 설치한 저장소에서 필요한 JAR 파일을 직접 다운로드할 수 있다.

>Maven Plugin?

메이븐의 플러그인 시스템은 빌드시 특별한 연산 추가를 가능하게 한다. 의존성 설정과 비슷하게 이 플러그인들은 원격으로 제공되며, 메이븐은 빌드할 때 이들을 찾을 수 있다. 또한 플러그인이 제공하는 연산이 무엇이든 수행할 수 있다. 

## 메이븐 빌드의 기본 디렉터리 구조

![enter image description here](https://p7.hiclipart.com/preview/980/890/407/apache-maven-convention-over-configuration-apache-ant-directory-structure-coc.jpg)

모든 제품 패키지 구조는 src/main/java 디렉터리 하위에 있다. 이 구조의 모든 클래스는 실행할때 클래스 패스 상에 있을 것이다. 

클래스 패스에 다른 파일들도 넣을 수 있다. 이 파일들은 src/main/resources 디렉터리 안에 있다. 여기에는 일반적으로 설정 파일들이 들어간다. 스프링 애플리케이션 컨텍스트의 설정 뿐만 아니라 모든 properties 파일이 포함될 수 있다. 

test 패키지 하위는 테스트용 설정 같은 테스트 코드로 추가할 수 있으며, 이러한 코드는 test/main/java와 test/main/resources에 두면 된다. 메이븐이 빌드의 일부로 어떤 테스트를 실행할때, 이경로에 있는 클래스와 파일들도 클래스 패스에 추가된다. 단 분리된 상태로 유지되며 생산 배포용으로 빌드된 결과물에는 포함되지 않는다. 

## 메이븐 빌드

메이븐 빌드의 정의는 pom.xml에 설정되는데, 이 파일은 프로젝트의 루트 디렉터리에 있다. 메이븐은 이 프로젝트를 빌드하는데 필요한 정보를 pom.xml을 보고 얻는다.
```
<project>
	<modelVersion>4.0.0</modelVersion>
	<groupId>come.wiley</groupdId>
	<artifactId>come.wiley</artifactId>
	<version>come.wiley</version>
	<packaging>jar</packaging>

	<dependencies>
		<dependency>
			<groupId>commons-io<groupId>
			<artifactId>commons-io<artifactId>
			<version>1.4</version>
		</dependency>
		<dependency>
			<groupId>org.springframework<groupId>
			<artifactId>spring-core<artifactId>
			<version>3.0.0.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>jnit<groupId>
			<artifactId>jnit<artifactId>
			<version>4.8.2</version>
			<scope>test</scope>
		</dependency>
	</dependencies>
	<build>
		<plugins>
			<plugin>
				<groupId>org.apached.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>1.7</source>
					<target>1.7</target>
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>
```
POM은 크게 **프로젝트 정의, 빌드용 의존성,  빌드 전용 플러그인** 세가지 영역으로 구분할 수 있다. 

프로젝트 정의는 프로젝트 태그 바로 밑에 정의하는 modelVersion, groupId, version, packaging 등이 된다. 
groupId는 대게 회사 이름으로 패키지 명명 규칙을 따라간다. artifactId는 애플리케이션 이름을 말한다. 이를 코디네이트라고 하거나 임시 참조라고 한다. 

빌드용 의존성에는 dependencies 태그 안에 정의 된다. 빌드를 위해 필요로 하는 의존 라이브러리를 모두 정의할 수 있다. 여기에는 인트라넷 저장소에 이용할 수 있는 회사가 만든 다른 라이브러리일 수도 있고 서드파티 라이브러리가 될 수 도 있다. 

특정 빌드 전용 플러그인은 build 태그로 관여하는데 package 생명 주기 단계에서 어떤 형태의 결과물을 빌드해야 하는지 알려준다. 

## 메이븐 빌드의 생명주기 

>Maven Goal?

애플리케이션 실행을 위한 특정 단위를 골(Goal)이라고 한다. 골에는 compile, test, install 등이 있다.  각 골은 이전 상태에 의존하며 어떤 이유에서든 특정 골을 통한 작업에 문제가 발생하면 전체 빌드가 실패할수 밖에 없다. 

clean
: target 디렉터리를 지워서 이전에 빌드된 파일들을 없앤다. 이는 mvn clean 같은 빌드 상태를 명시해서 초기화하거나 항상 실행되도록 POM을 정의하지 않으면 다음 상태가 되기 전에는 실행하지 않는다.
  
validate
: pom.xml 파일이 메이븐의 빌드 파일용 XML을 따르는지 확인한다. pom.xml 파일이 올바른 검증에 실패한다면 빌드는 메이븐 애플리케이션 초기화를 하기 전에 실패한다.

compile
: 필요한 의존성 전체를 가져온 후, 코드를 컴파일하고, src/main/java의 모든 클래스 파일을 target/classes 디렉터리에 빌드한다. 

test
: src/main/test 디렉터리에 있는 테스트용 클래스들을 컴파일하고 모든 단위테스트 또는 통합 테스트용 코드를 실행한다. 실패하는 테스트가 있으면 기본적으로 전체 빌드가 실패한다.

package
: test 상태가 성공적으로 실행된 다음에 실행되며 WAR나 JAR파일 같은 결과물을 생성한다. 파일은 target 디렉터리 루트에 저장되는데, 독립적으로 실행가능한 파일이 아니다. 왜냐하면 기본적으로 모든 의존 라이브러리를 내장하지 않고 있고 의존성은 여전히 클래스 패스에 포함되어 있어야 하기 때문이다. 참고로 pom.xml의 packaging 태그로 결과물 package 종류를 정할 수 있다. 
```
<packaging>war</packaging>
``` 
install
: 빌드된 결과물들을 내부 Maven 저장소로 보낸다. 저장소는 대게 `HOME/.m2/repository` 디렉터리에 위치한다. 내부적으로 실행되는 메이븐 빌드가 하나 이상 있다면 새로운 결과물은 해당 빌드를 이용할 수 있다. 

deploy
: 메이븐 빌드의 마지막 단계이다. 대게 마무리된 결과물을 배포하는 장소를 정의하거나 배포 장소에 배포를 한다. 배포 장소는 일반적으로 Artifactory나 Nexus 같은 결과물 저장소다. 이러한 저장소들은 빌드를 위한 의존 결과물을 다운로드할 수 있는 전형적인 공간으로 때때로 임시 공간의 역할도 한다.


> GIT 형상관리 툴?




> Written with [StackEdit](https://stackedit.io/).

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTAzMTgzNjQ3NSwtMTY3NjkwNTA0OCwtMj
IzNTY1NjU4LDQ2NjA1NzI5NCwtNjk1NDgyMTMzLC0xNTc1MzMz
MDM0LDMwODU0Mjg0OCw4MjU0MjM4MTcsLTQxMzA5MDA5MiwtMT
g3NzUyOTc5NiwxOTE0NzkwMTA2LDEwODQ2MDc1NjEsOTM0MjI2
NjY5LDE3MDEwOTI3MDUsMTU5NDIyNzcxMSwtNjMyOTIzMjExLC
05Njc0MTYyMTYsMTgyNjg3MjQ0OSw3NDYzMDIyODEsLTM5MDIy
NzkwM119
-->