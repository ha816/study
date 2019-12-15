# JVM 

> JVM?

JVM은 Java Virtual Machine의 약자로 Java 프로그램이 실행되는 장소다. 운영체제와 애플리케이션 사이에 위치하면서 애플리케이션이 특정 플랫폼에 상관없이 독립적으로 실행되도록 한다. 그리고 JVM은 메모리 관리를 위해 GC를 수행한다. 

> JVM의 역할

JVM의 핵심적 작업은 compile이다. 컴파일을 하면 .class확장자를 가진 바이트 코드가 생성된다. 이러한 바이트코드를 실행 중인 JVM의 메모리로 가져오는 걸 class loading(클래스 로딩)이라고 한다. 이것을 클래스 로더가 한다. 이 클래스 로더는 클래스 파일을 추상화해 디스크, JAR 같은 것들을 메모리로 불러 올 수 있다. 

> native 메서드란 무엇인가?

자바에서는 C나 C++의 잘 정의된 헤더를 사용할 수 있는 native 메서드를 사용할 수 있다. 코드가 JVM에 로드됐을때 고유(native) 코드를 등록해야 한다 그래야 메서드 호출시 코드를 실행하는데 정확히 필요한 것을 알 수 있다. 

## JVM 메모리 영역

JVM 메모리 영역은 크게 Shared Memory영역과 Non-shared Memory 영역으로 나뉜다.  JVM에서 실행되는 모든 프로그램들(스레드)들은 Shared Memory의 메소드 영역과 힙 영역을 공유하게 된다. Non-shared memory는 스레드가 별로 할당되는 고유 메모리 영역을 말한다. 각 스레드는 Stack 영역을 가진다. 

### Heap영역

힙 영역(Heap)
* 프로그램 상에서 데이터를 저장하기 위해 동적으로(실행시간에)할당하여 쓸 수 있는 메모리 영역, new 연산자를 통하여 개체를 동적으로 생성한다. 

new 키워드는 자바 heap영역에 메모리를 할당한다. 객체를 할당할때 이용할 수 있는 메모리가 충분치 않으면 JVM은 가비지 컬렉션을 이용해 힙에서 메모리를 재사용하려고 한다. 그래도 충분한 메모리가 없으면 OutOfMemoryError가 발생하며 JVM이 종료된다. 

![enter image description here](https://cdn.journaldev.com/wp-content/uploads/2014/05/Java-Memory-Model.png)
  
#### Heap's Generation

| generation name | explanation |
|--|--|
| Eden |객체가 처음 생겼을때는 Eden이라는 메모리영역에 할당 |
| Survivor | 가비지 컬렉션의 수집 대상에서 제외되면 Survivor이라고 하는 메모리 영역으로 |
| Tenured(Old) | 이 공간에서도 수집 대상에서 제외되는 객체는 Tenured(Old)에 옮겨진다. 이 제네레이션은 더 낮은 빈도로 가비지 컬렉터의 대상이 된다. |


가비지 컬렉션에서 객체가 수집 대상에서 제외된다면 다른 제너레이션으로 옮겨진다. 예를 들어 older 제너레이션은 가비지 컬렉션이 자주 수집하지 않는 객체가 저장되어 있다. 이미 오랫동안 제거 되지 않았음이 증명된 것으로 가비지 컬렉터가 수집할 가능성이 낮다. 

마지막으로 PermGen이라고 하는 제네레이션도 있다. 여기 속한 객체는 가비지 컬렉션에서 선택되지 않고, 일반적으로 String이나 상수 같이 JVM에서 실행되는데 필요한 불변상태가 포함된다. 자바 8에서 PermGen은 물리 메모리에 위치할 MetaSpace라는 새로운 영역으로 변경되었다. 

![enter image description here](http://brucehenry.github.io/blog/public/2018/02/07/JVM-Memory-Structure/JVM-Memory.png)


메소드 영역(Method Area)
* 자바 프로그램을 구성하고 있는 **메소드와 클래스 변수(static으로 선언된 변수)를 저장**하기 위한 공간이다.  
* JVM은 복수개의 스레드가 메소드를 정상적으로 사용하기 위한 동기화 기법을 제공



> 스택과 힙의 차이는?

스택영역은 각 스레드들이 가지는 영역이다. 기본값, 객체의 참조, 메서드가 저장되는 위치이다. 따라서 스택은 변수의 생애주기는 코드의 스코프에 영향을 받는다. 특정 스코프의 실행이 종료되면 스코프 안에 선언된 변수들은 스택에서 제거된다. 

스택영역(Stack area)  
* LIFO(Last-In-First-Out)  
* **메소드가 호출되어 실행될 때**  메소드의 매개변수, 메소드 내에 선언된 메소드 지역변수, 번호나 값, 임시 변수 등을 저장하기 위한 공간
* 메소드 호출시 필요로 되는 변수들을 스택에 저장하고, 메소드 실행이 끝나면 스택을 반환


# GC(Garbage Collection)

> GC(가비지 컬렉션)이란 무엇인가?

가비지 컬렉션은 기존에 할당된 메모리를 재사용하는 메커니즘으로, 나중에 메모리를 할당할때 재사용할 수 있다. 대부분의 프로그래밍 언어는 가비지 컬렉션을 자동으로 실행한다. 가비지 컬렉션을 쓰면 메모리를 직접 해제할 필요가 없다. 

가비지 컬렉션 알고리즘은 몇 가지가 있다. 모두 작동 중인 코드에서 더 이상 참조하지 않는 메모리를 찾은 후 메모리를 할당할 때 이용할 수 있도록 반환한다는 공통점이 있다. 전통적인 방법은 mark-and-sweep방식이다. 실행 중인 코드에서 참조하는 객체는 live로 표시되고 참조하는 것도 live로 한다. 

이 과정이 끝나면 live로 표시되지 않는 메모리르 할당할 수 있게 만든다. 이때 메모리를 재배치하려고 JVM의 모든 쓰레드가 정지되는데 이를 stop-the-world라고 한다. 

자바 6에는 G1(Garbage First)라는 새로운 알고리즘이 투입되었다. 

결국 가비지 컬렉션은 다른 제너레이션으로의 이동과 가능한 한 많은 여유 공간을 남겨두려는 목적으로 메모리에서 객체들을 옮기고 자주 접근되는 객체들을 묶어두는등 연산을 수행한다. 이러한 연산들을 컴팩션(compaction)이라고 한다. 컴패션은 live로 표시한 객체들을 다른 물리적인 메모리 위치로 옮김으로써 JVM이 stop-the-world인 메모리 공간을 확보한다. 





> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTg3MzkwMjI0NiwxMzk1OTU1Mjg4LC0xNT
U4ODYxMjg1LC0xNjY5Mjk4MDE5LC0xNDE5NzM5MjIxLDEyNjg2
NjIxODhdfQ==
-->