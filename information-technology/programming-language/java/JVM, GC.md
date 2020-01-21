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

The runtime data areas are the in-memory areas designed to store data. Those data are used by the developer’s program or by the JVM for its inner working.

![enter image description here](http://coding-geek.com/wp-content/uploads/2015/04/jvm_memory_overview.jpg)

### Thread Memory Area

PC Register 
: Each thread has its own pc (program counter) register, created at the same time as the thread. At any point, each Java Virtual Machine thread is executing the code of a single method, namely the  **current method**  for that thread. The pc register contains the address of the Java Virtual Machine instruction (in the method area) currently being executed.

Note: If the method currently being executed by the thread is native, the value of the Java Virtual Machine’s pc register is undefined.The Java Virtual Machine’s pc register is wide enough to hold a returnAddress or a native pointer on the specific platform.

Stack
:  The stack area stores multiples frames so before talking about stacks I’ll present the frames.

### Frames

A frame is a data structure that contains multiples data that represent the state of the thread in the  **current method**  (the method being called):

-   **Operand Stack**: I’ve already presented the operand stack in the chapter about stack based architecture. This stack is used by the bytecode instructions for handling parameters. This stack is also used to pass parameters in a (java) method call and to get the result of the called method at the top of the stack of the calling method.

-   **Local variable array**: This array contains all the local variables in a scope of the current method. This array can hold values of primitive types, reference, or returnAddress. The size of this array is computed at compilation time. The Java Virtual Machine uses local variables to pass parameters on method invocation, the array of the called method is created from the operand stack of the calling method.

-   **Run-time constant pool reference**: reference to the constant pool of the  **current class**  of the  **current method**  being executed. It is used by the JVM to translate symbolic method/variable reference ( ex: myInstance.method()) to the real memory reference.

### Stack

Each Java Virtual Machine thread has a private  _Java Virtual Machine stack_, created at the same time as the thread. A Java Virtual Machine stack stores frames. A new frame is created and put in the stack each time a method is invoked. A frame is destroyed when its method invocation completes, whether that completion is normal or abrupt (it throws an uncaught exception).

Only one frame, the frame for the executing method, is active at any point in a given thread. This frame is referred to as the  **_current frame_**, and its method is known as the  **_current method_**. The class in which the current method is defined is the  **_current class_**. Operations on local variables and the operand stack are typically with reference to the current frame.

Let’s look at the following example which is a simple addition

`public` `int` `add(``int` `a,` `int` `b){`

`return` `a + b;`

`}`

`public` `void` `functionA(){`

`// some code without function call`

`int` `result = add(``2``,``3``);` `//call to function B`

`// some code without function call`

`}`

Here is how it works inside the JVM when the functionA() is running on:

[![example of the state of a jvm method stack during after and before an inner call](http://coding-geek.com/wp-content/uploads/2015/04/state_of_jvm_method_stack.jpg)](http://coding-geek.com/wp-content/uploads/2015/04/state_of_jvm_method_stack.jpg)

Inside functionA() the Frame A is the top of the stack frame and is the current frame. At the beginning of the inner call to add () a new frame (Frame B) is put inside the Stack. Frame B becomes the current frame. The local variable array of frame B is populated from popping the operand stack of frame A. When add() finished, Frame B is destroyed and Frame A becomes again the current frame. The result of add() is put on the operand stack of Frame A so that functionA() can use it by popping its operand stack.

Note: the functioning of this stack makes it dynamically expandable and contractable. There is a maximum size that a stack can’t exceed, which limit the number of recursive calls. If this limit is exceeded the JVM throws a **StackOverflowError**.

With Oracle HotSpot, you can specify this limit with the parameter -Xss.

## Native method stack (Per Thread)

This is a stack for native code written in a language other than Java and called through JNI (Java Native Interface). Since it’s a “native” stack, the behavior of this stack is entirely dependent of the underlying OS.


## Heap

힙은 모든 쓰레드가 공유하는 공간이다. 모든 객체와 배열들이 힙에 생성된다. 가비지 컬렉터는 힙공간에 더 사용되지 않는 객체를 제거한다. 힙은 동적으로 확장되거나 축소 될수 있다. 


## Method area

The Method area is a memory shared among all Java Virtual Machine Threads. It is created on virtual machine start-up and is loaded by  **classloaders**  from bytecode. The data in the Method Area stay in memory as long as the classloader which loaded them is alive.

The method area stores:

-   class information (number of fields/methods, super class name, interfaces names, version, …)
-   the bytecode of methods and constructors.
-   a runtime constant pool per class loaded.

The specifications don’t force to implement the method area in the heap. For example, until JAVA7, Oracle  **HotSpot**  used a zone called PermGen to store the Method Area. This  **PermGen**  was contiguous with the Java heap (and memory managed by the JVM like the heap) and was limited to a default space of 64Mo (modified by the argument -XX:MaxPermSize). Since Java 8, HotSpot now stores the Method Area in a separated native memory space called the  **Metaspace**, the max available space is the total available system memory.

Note: There is a maximum size that the method area can’t exceed. If this limit is exceeded the JVM throws an  **OutOfMemoryError.**



### SharedMemory VS Non-SharedMermory

JVM에서 실행되는 쓰레드의 관점에서 보자면 메모리 영역은 크게 공유 영역과 비공유 영역으로 나눌 수 있다. 모든 쓰레드는 공유 영역의 메소드 영역(Method Area)와 힙(Heap)영역을 공유한다. 비공유 영역은 각 스레드가 가지는 고유 메모리 영역을 말한다. 스레드는 스택 영역(Stack Area), PC Registers, Native Method Area 영역을 가진다.  
![enter image description here](http://brucehenry.github.io/blog/public/2018/02/07/JVM-Memory-Structure/JVM-Memory.png)

### Heap VS Non-Heap

메모리 영역은 Heap 영역과 Non Heap영역으로도 나누어 생각할 수 있다. 
![enter image description here](https://i.stack.imgur.com/4ySVX.png)

Perm(PermGen)은 자바 8이후부터는 MetaSpace로 명칭이 변경되었다. 일반적으로 String이나 상수 같이 불변 값이 저장된다. PermGeneration은 Method Area와 interned strings를 포함한다. 




### 메소드 영역(Method Area, Class Area)

클래스 파일의 바이트 코드가 로드되는 곳이다. 사용하는 클래스와 static 변수(클래스 변수)가 로드 된다. 여기서 클래스 변수 중 참조 변수의 경우, 실제 객체는 Heap 영역에 저장된다. 단지 로드되는 변수는 Heap에 있는 객체의 주소값만을 가진다. 

```
static int i = 1; //the value 1 is stored in the RunTime Constant Pool(PermGen section(Heap))
static Object o = new SomeObject()
```

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

###  스택 영역(Stack Area)([링크](https://yaboong.github.io/java/2018/05/26/java-memory-management/))

- 각 Thread 는 자신만의 stack 을 가진다.
-   지역변수들은 scope 에 따른 visibility 를 가진다.
-   Heap 영역에 생성된 Object 타입의 데이터의 참조값이 할당된다.
-   원시타입의 데이터가 값과 함께 할당된다.

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

The biggest advantage of the G1 GC is its  **performance**. It is faster than any other GC types that we have discussed so far. 

> Written with [StackEdit](https://stackedit.io/).

> native 메서드란 무엇인가?

자바에서는 C나 C++의 잘 정의된 헤더를 사용할 수 있는 native 메서드를 사용할 수 있다. 코드가 JVM에 로드됐을때 고유(native) 코드를 등록해야 한다 그래야 메서드 호출시 코드를 실행하는데 정확히 필요한 것을 알 수 있다. 




<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1OTAyNzUwMjQsNzI3ODkxODA3LDE3Mz
E5MzYxNTMsLTE3OTI0NzQ4MDgsMTMxOTYzODkwNCwtMTcyMjEw
ODM4NSwxMjAzNTA1OTM0XX0=
-->