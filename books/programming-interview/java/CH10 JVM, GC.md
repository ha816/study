# JVM 

> JVM?

JVM은 Java Virtual Machine의 약자로 Java 프로그램이 실행되는 장소다. 운영체제와 애플리케이션 사이에 위치하면서 애플리케이션이 특정 플랫폼에 상관없이 독립적으로 실행되도록 한다. 그리고 JVM은 메모리 관리를 위해 GC를 수행한다. 

> JVM의 역할

JVM의 핵심적 작업은 compile이다. 컴파일을 하면 .class확장자를 가진 바이트 코드가 생성된다. 이러한 바이트코드를 실행 중인 JVM의 메모리로 가져오는 걸 class loading(클래스 로딩)이라고 한다. 이것을 클래스 로더가 한다. 이 클래스 로더는 클래스 파일을 추상화해 디스크, JAR 같은 것들을 메모리로 불러 올 수 있다. 

> native 메서드란 무엇인가?

자바에서는 C나 C++의 잘 정의된 헤더를 사용할 수 있는 native 메서드를 사용할 수 있다. 코드가 JVM에 로드됐을때 고유(native) 코드를 등록해야 한다 그래야 메서드 호출시 코드를 실행하는데 정확히 필요한 것을 알 수 있다. 

## JVM 메모리 영역

JVM 메모리 영역은 크게 Shared Memory영역과 Non-shared Memory 영역으로 나뉜다.  JVM에서 실행되는 모든 프로그램들(스레드)들은 Shared Memory의 메소드 영역과 힙 영역을 공유하게 된다. Non-shared memory는 스레드가 별로 할당되는 고유 메모리 영역을 말한다. 각 스레드는 Stack 영역을 가진다. 

![enter image description here](http://brucehenry.github.io/blog/public/2018/02/07/JVM-Memory-Structure/JVM-Memory.png)


### Heap영역

객체를 저장하기 위해 동적으로(실행시간에) 할당하여 쓰는 메모리 영역, new 연산자를 통하여 개체를 동적으로 생성한다. 객체를 할당할때 이용할 수 있는 메모리가 충분치 않으면 JVM은 가비지 컬렉션을 이용해 힙에서 메모리를 재사용하려고 한다. 그래도 충분한 메모리가 없으면 OutOfMemoryError가 발생하며 JVM이 종료된다. 
  
#### Heap's Generation

[힙 메모리 영역의 흐름](https://dzone.com/articles/understanding-the-java-memory-model-and-the-garbag)

![enter image description here](https://cdn.journaldev.com/wp-content/uploads/2014/05/Java-Memory-Model.png)

| generation name | explanation |
|--|--|
| Eden |객체가 처음 생겼을때 할당되는 메모리 영역 |
| Survivor | Minor GC의 수집대상에서 제외된 객체가 있는 영역으로 똑같이 분할된 S0과 S1으로 나누어져 있다. |
| Tenured(Old) | Survivor 공간에서 최대 나이 임계값에 다다른 객체가 이주하는 공간으로 낮은 빈도로 gc의 대상이 된다.|
| Perm(PermGen) | 일반적으로 String이나 상수 같이 불변 값이 저장된다. 자바 8에서 PermGen은 물리 메모리에 위치할 MetaSpace라는 새로운 영역으로 변경되었다.|

### 메소드 영역(Method Area)

정적 메소드와 정석 클래스 변수를 저장하기 위한 공간이다.  

### 스택영역(Stack Area)  

스택 영역은 각 스레드들이 가지는 고유 영역이다. 지역변수, 메서드 정보가 저장되는 곳으로 스레드가 메소드 호출시 필요로 되는 변수들을 스택에 저장하고, 메소드 실행이 끝나면 스택을 반환한다. 스택 변수의 생애주기는 스코프에 영향을 받는데 특정 스코프가 종료되면 스코프 안에 선언된 변수들은 스택에서 제거된다. 

# GC(Garbage Collection)

> GC(가비지 컬렉션)이란 무엇인가?

가비지 컬렉션은 기존에 할당된 메모리 중 사용되지 않는 메모리를 재사용하는 메커니즘이다. 가비지 컬렉션을 쓰면 메모리를 직접 해제할 필요가 없다. 

가비지 컬렉션 알고리즘은 몇 가지가 있다. 모두 작동 중인 코드에서 더 이상 참조하지 않는 메모리를 찾은 후 메모리를 할당할 때 이용한다는 공통점이 있다. 

[GC types
](https://www.cubrid.org/blog/understanding-java-garbage-collection)


전통적인 GC는 Old 제너레이션에서  **mark-sweep-compact** 방법을 사용한다. 

1.  The first step of this algorithm is to mark the surviving objects in the old generation.
2.  Then, it checks the heap from the front and leaves only the surviving ones behind (sweep).
3.  In the last step, it fills up the heap from the front with the objects so that the objects are piled up consecutively, and divides the heap into two parts: one with objects and one without objects (compact).


자바 1.6에는 G1([Garbage First](https://www.oracle.com/technetwork/tutorials/tutorials-1876574.html))라는 새로운 방법을 사용하게 되었다. 

The biggest advantage of the G1 GC is its  **performance**. It is faster than any other GC types that we have discussed so far. But in JDK 6, this is called an  _early access_  and can be used only for a test. It is officially included in JDK 7. In my personal opinion, we need to go through a long test period (at least 1 year) before NHN can use JDK7 in actual services, so you probably should wait a while. Also, I heard a few times that a JVM crash occurred after applying the G1 in JDK 6. Please wait until it is more stable.

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyMDk5MjY0NzAsLTE1MDk4NzY3MjAsLT
E4Mzg0NjMwNDUsLTE3MjQ5OTQzOTQsMjAxNTUwNTQ3OSw3OTA5
ODQ0NjksLTE4ODQzMDkxODksLTIxODI5NzM1NCwtMTEzNjQ3NT
YyMiwtOTc2NjM5NDUwLC03ODY4NTI1NTMsMTM5NTk1NTI4OCwt
MTU1ODg2MTI4NSwtMTY2OTI5ODAxOSwtMTQxOTczOTIyMSwxMj
Y4NjYyMTg4XX0=
-->