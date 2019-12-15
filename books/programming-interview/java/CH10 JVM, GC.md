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


전통적인 알고리즘은 mark-and-sweep이다. Heap에 Old 제너레이션은 **mark-sweep-compact** 

1.  The first step of this algorithm is to mark the surviving objects in the old generation.
2.  Then, it checks the heap from the front and leaves only the surviving ones behind (sweep).
3.  In the last step, it fills up the heap from the front with the objects so that the objects are piled up consecutively, and divides the heap into two parts: one with objects and one without objects (compact).


자바 6에는 G1(Garbage First)라는 새로운 알고리즘이 투입되었다. 

If you want to understand G1 GC, forget everything you know about the young generation and the old generation. As you can see in the picture, one object is allocated to each grid, and then a GC is executed. Then, once one area is full, the objects are allocated to another area, and then a GC is executed. The steps where the data moves from the three spaces of the young generation to the old generation cannot be found in this GC type. This type was created to replace the CMS GC, which has causes a lot of issues and complaints in the long term.

The biggest advantage of the G1 GC is its  **performance**. It is faster than any other GC types that we have discussed so far. But in JDK 6, this is called an  _early access_  and can be used only for a test. It is officially included in JDK 7. In my personal opinion, we need to go through a long test period (at least 1 year) before NHN can use JDK7 in actual services, so you probably should wait a while. Also, I heard a few times that a JVM crash occurred after applying the G1 in JDK 6. Please wait until it is more stable.

I will talk about the  **GC tuning**  in the next issue, but I would like to ask you one thing in advance. If the size and the type of all objects created in the application are identical, all the GC options for WAS used in our company can be the same. But the size and the lifespan of the objects created by WAS vary depending on the service, and the type of equipment varies as well. In other words, just because a certain service uses the GC option "A," it does not mean that the same option will bring the best results for a different service. It is necessary to find the best values for the WAS threads, WAS instances for each equipment and each GC option by constant tuning and monitoring. This did not come from my personal experience, but from the discussion of the engineers making Oracle JVM for JavaOne 2010.

In this issue, we have only glanced at the GC for Java. Please look forward to our next issue, where I will talk about  **how to monitor the Java GC status and tune GC**.

I would like to note that I referred to a new book released in December 2011 called "_Java Performance_" ([Amazon](http://amzn.com/0137142528), it can also be viewed from safari online, if the company provides an account), as well as “_Memory Management in the Java HotSpotTM Virtual Machine_,” a white paper provided by the Oracle website. (The book is different from "_Java Performance Tuning_.")


결국 가비지 컬렉션은 다른 제너레이션으로의 이동과 가능한 한 많은 여유 공간을 남겨두려는 목적으로 메모리에서 객체들을 옮기고 자주 접근되는 객체들을 묶어두는등 연산을 수행한다. 이러한 연산들을 컴팩션(compaction)이라고 한다. 컴패션은 live로 표시한 객체들을 다른 물리적인 메모리 위치로 옮김으로써 JVM이 stop-the-world인 메모리 공간을 확보한다. 







> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTk4MTEyMzMwOSwtMTcyNDk5NDM5NCwyMD
E1NTA1NDc5LDc5MDk4NDQ2OSwtMTg4NDMwOTE4OSwtMjE4Mjk3
MzU0LC0xMTM2NDc1NjIyLC05NzY2Mzk0NTAsLTc4Njg1MjU1My
wxMzk1OTU1Mjg4LC0xNTU4ODYxMjg1LC0xNjY5Mjk4MDE5LC0x
NDE5NzM5MjIxLDEyNjg2NjIxODhdfQ==
-->