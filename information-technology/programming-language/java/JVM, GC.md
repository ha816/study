# JVM(Java Virtual Machine)

JVM은 Java 프로그램이 실행되는 장소다. 운영체제와 애플리케이션 사이에 위치하면서 애플리케이션이 특정 플랫폼에 상관없이 독립적으로 실행되도록 한다. 추가적으로 JVM은 메모리 관리를 위해 GC를 수행한다. 

## Overview

![enter image description here](http://coding-geek.com/wp-content/uploads/2015/04/jvm_overview.jpg)

JVM의 핵심적 역할은 컴파일(compile)과 클래스 로딩(class loading)이다. 컴파일을 하면 .class확장자를 가진 **바이트 코드**가 생성된다. 이러한 바이트코드를 실행 중인 JVM의 메모리로 가져오는 걸 **class loading(클래스 로딩)**이라고 한다. 이것을 클래스 로더가 한다. 클래스 로더는 클래스 파일을 추상하여 JAR 같은 파일을 읽어 JVM의 메모리에 올릴 수 있다. 

This diagram gives on overview of the JVM:

-   The JVM  **interprets**  bytecode which is  **produced**  by the compilation of the source code of a class. Though the term JVM stands for “Java Virtual Machine”, it runs other languages like scala or groovy, as long as they can be compiled into java bytecode.
-   In order to avoid disk I/O, the bytecode is loaded into the JVM by  **classloaders** in one of the the runtime data areas. This code stays in memory until the JVM is stopped or the classloader (that loaded it) is destroyed.
-   The loaded code is then  **interpreted**  and executed by an  **execution engine**.
-   The execution engine needs to store data like a pointer to the ligne of code being executed. It also needs to store the data handled in the developer’s code.
-   The execution engine also takes care of dealing with the underlying OS.

## 메모리 영역

![실행 중인 데이터 영역](http://coding-geek.com/wp-content/uploads/2015/04/jvm_memory_overview.jpg)

### PC Register(Per Thread, Non-Shared)
각 스레드는 고유의 프로그램 카운터 레지스터(program counter register)를 가진다.  프로그램 카운터 레지스터는 현재 실행 중인 (메서드 영역안) JVM 명령어 주소를 기억한다.

### Native Method Stack(Per Thread, Non-shared)
다른 언어로 쓰인 네이티브 코드를 위한 스택이다. 그리고 네이티브 코드는 JNI(Java Native Interafce)를 통해 호출된다. 말 그대로 네이티브 스택이기 때문에, 이 스택의 모든 행동은 완전히 OS에 종속적된다. 

### Stack

각 스레드는 자신만의 고유한 스택을 가진다. 스택은 다수의 프레임(Frame)으로 구성된다. 또 프레임은 쓰레드의 상태를 나타내는 데이터 구조들로 이루어져 있다. (Operand Stack, Local variable array, Run-time constant pool reference)
메소드 호출시 필요로 하는 변수들을 스택에 저장하고, 메소드 실행이 끝나면 스택을 반환한다. 스택 변수의 생애주기는 스코프에 영향을 받는데 특정 스코프가 종료되면 스코프 안에 선언된 변수들은 스택에서 제거된다.

Operand Stack
: 이 스택은 파라미터를 다루기 위한 바이트코드 명령어에 의해서 사용된다. 또는 메서드 콜에 파라미터를 전달하거나 스택 최상단의 호출된 메서드의 결과를 얻기위해 사용된다. 

Local Variable Array
: 이 배열은 현재 메서드 스코프의 지역 변수 모두를 포함한다. 이 배열은 원시 타입, 참조 또는 반환 주소등을 가질 수 있다. JVM은 메서드 호출시 파라미터 전달을 위해 로컬 변수들을 사용한다. 호출된 메서드의 지역 변수 배열은 호출하는 메서드의 operand stack으로 부터 생성된다.

Run-time constant pool reference
: 현재 실행 중인 메서드의 클래스의 상수 풀에 대한 참조를 말한다. 이 참조는 실제 메모리 참조에 대한 심블릭 메서드/변수 참조를 번역하는데 JVM이 사용한다. 

![example of the state of a jvm method stack during after and before an inner call](http://coding-geek.com/wp-content/uploads/2015/04/state_of_jvm_method_stack.jpg)

Inside functionA() the Frame A is the top of the stack frame and is the current frame. At the beginning of the inner call to add () a new frame (Frame B) is put inside the Stack. Frame B becomes the current frame. The local variable array of frame B is populated from popping the operand stack of frame A. When add() finished, Frame B is destroyed and Frame A becomes again the current frame. The result of add() is put on the operand stack of Frame A so that functionA() can use it by popping its operand stack.

### Non-Shared Memory


#### Heap

힙은 모든 쓰레드가 공유하는 공간이다. 모든 객체와 배열들이 힙에 생성된다. 가비지 컬렉터는 힙공간에 더 사용되지 않는 객체를 제거한다. 힙은 동적으로 확장되거나 축소 될수 있다. 


## 메소드 영역(Method Area, , Class Area)

클래스 파일의 바이트 코드가 로드되는 곳이다. 사용하는 클래스와 static 변수(클래스 변수)가 로드 된다. 여기서 클래스 변수 중 참조 변수의 경우, 실제 객체는 Heap 영역에 저장된다. 단지 로드되는 변수는 Heap에 있는 객체의 주소값만을 가진다. 

```
static int i = 1; //the value 1 is stored in the RunTime Constant Pool(PermGen section(Heap))
static Object o = new SomeObject()
```



The Method area is a memory shared among all Java Virtual Machine Threads. It is created on virtual machine start-up and is loaded by  **classloaders**  from bytecode. The data in the Method Area stay in memory as long as the classloader which loaded them is alive.

The method area stores:

-   class information (number of fields/methods, super class name, interfaces names, version, …)
-   the bytecode of methods and constructors.
-   a runtime constant pool per class loaded.

The specifications don’t force to implement the method area in the heap. For example, until JAVA7, Oracle  **HotSpot**  used a zone called PermGen to store the Method Area. This  **PermGen**  was contiguous with the Java heap (and memory managed by the JVM like the heap) and was limited to a default space of 64Mo (modified by the argument -XX:MaxPermSize). Since Java 8, HotSpot now stores the Method Area in a separated native memory space called the  **Metaspace**, the max available space is the total available system memory.

Note: There is a maximum size that the method area can’t exceed. If this limit is exceeded the JVM throws an  **OutOfMemoryError.**



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


 

## 마치며 

![enter image description here](https://i.stack.imgur.com/4ySVX.png)

Perm(PermGen)은 자바 8이후부터는 MetaSpace로 명칭이 변경되었다. 일반적으로 String이나 상수 같이 불변 값이 저장된다. PermGeneration은 Method Area와 interned strings를 포함한다. 




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

The biggest advantage of the G1 GC is its  **performance**. It is faster than any other GC types that we have discussed so far. 

> Written with [StackEdit](https://stackedit.io/).

> native 메서드란 무엇인가?

자바에서는 C나 C++의 잘 정의된 헤더를 사용할 수 있는 native 메서드를 사용할 수 있다. 코드가 JVM에 로드됐을때 고유(native) 코드를 등록해야 한다 그래야 메서드 호출시 코드를 실행하는데 정확히 필요한 것을 알 수 있다. 




<!--stackedit_data:
eyJoaXN0b3J5IjpbLTkyMDgyMDI5OCwtMTU3NTIzMzQxNiw0Nz
Y5NjM5MTAsNjMxMTAzODM2LC05ODAyNTU5Myw3Mjc4OTE4MDcs
MTczMTkzNjE1MywtMTc5MjQ3NDgwOCwxMzE5NjM4OTA0LC0xNz
IyMTA4Mzg1LDEyMDM1MDU5MzRdfQ==
-->