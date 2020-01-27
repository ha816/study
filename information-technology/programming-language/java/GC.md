
# GC(Garbage Collection)
[GC types](https://www.cubrid.org/blog/understanding-java-garbage-collection)
[Java Garbage Collection](https://d2.naver.com/helloworld/1329)
[GC 잘하는 법
](https://waspro.tistory.com/380)

> GC(가비지 컬렉션)이란 무엇인가?

가비지 컬렉션은 기존에 할당된 메모리 중 사용되지 않는 메모리를 재사용하는 메커니즘이다. 가비지 컬렉션을 쓰면 메모리를 직접 해제할 필요가 없다. 

가비지 컬렉션 알고리즘은 더 이상 참조하지 않는 메모리를 찾아 메모리를 할당할 때 이용하는 공통점이 있다. 

GC에 대해서 알아보기 전에 알아야 할 용어가 있다. 바로 'stop-the-world'이다. stop-the-world란, GC을 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것이다. stop-the-world가 발생하면 GC를 실행하는 쓰레드를 제외한 나머지 쓰레드는 모두 작업을 멈춘다. GC 작업을 완료한 이후에야 중단했던 작업을 다시 시작한다. 어떤 GC 알고리즘을 사용하더라도 stop-the-world는 발생한다. **대개의 경우 GC 튜닝이란 이 stop-the-world 시간을 줄이는 것이다.**

Java는 프로그램 코드에서 메모리를 명시적으로 지정하여 해제하지 않는다. 가끔 명시적으로 해제하려고 해당 객체를 null로 지정하거나 System.gc() 메서드를 호출하는 개발자가 있다. null로 지정하는 것은 큰 문제가 안 되지만, System.gc() 메서드를 호출하는 것은 시스템의 성능에 매우 큰 영향을 끼치므로 **System.gc() 메서드는 절대로 사용하면 안 된다.**

## GC Heap Strucuture

효율적인 가비지 컬렉션을 위한 정책은 두 가지 가설 하에 만들어졌다.(사실 가설이라기보다는 가정 또는 전제 조건이라 표현하는 것이 맞다).

>**weak generational hypothesis**
>대부분의 객체는 금방 접근 불가능 상태(unreachable)가 된다.
>오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다.

이 가설의 장점을 최대한 살리기 위해서 HotSpot VM에서는 크게 2개로 물리적 공간을 나누었다. 둘로 나눈 공간이 Young 영역과 Old 영역이다.

![enter image description here](https://i.stack.imgur.com/8ZtFA.png)

### Young Generation(Eden, From Survivor, To Survivor)

새롭게 생성한 객체의 대부분이 위치한다. Young 영역은 Eden, Survivor 영역으로 구성되어 있다. 다시 Survivor는 From과 To로 이루어져 있으며, Eden에서 살아남은 Object는 From으로 이동한다. 이후 From에서 살아남은 객체는 To로 이동하고 여기서 또 살아남은 객체는 다시 To로 이동한다. 이를 반복적 수행하다가 Hit(GC에서 살아남은 횟수)가 Tenuring Threshold 만큼 수행된 객체들은 Old 영역으로 이동된다.

대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 매우 많은 객체가 Young 영역에 생성되었다가 사라진다. 이 영역에서 객체가 사라질때 Minor GC가 발생한다고 말한다.

### Old 영역(Old Generation, Tenured Generation)

접근 불가능 상태로 되지 않아 Young 영역에서 살아남은 객체가 여기로 복사된다. 대부분 Young 영역보다 크게 할당하며, 크기가 큰 만큼 Young 영역보다 GC는 적게 발생한다. 이 영역에서 객체가 사라질 때 Major GC(혹은 Full GC)가 발생한다고 말한다. Old 영역은 기본적으로 데이터가 가득 차면 GC를 실행한다. GC Policy에 따라 어떠한 방식으로 gc를 수행할 것이지가 결정되어 이는 성능에 커다른 영향을 끼치게 합니다.

## GC Algorithms

[GC 기초](https://codeahoy.com/2017/08/06/basics-of-java-garbage-collection/)

일반적으로 3개의 GC Algorithms 타입이 있다. 

serial collector
: uses a single thread to perform all garbage collection work, which makes it relatively efficient because there is no communication overhead between threads. It is best-suited to single processor machines -XX:+UseSerialGC.

parallel collector(also known as the throughput collector)
: performs minor collections in parallel, which can significantly reduce garbage collection overhead. It is intended for applications with medium-sized to large-sized data sets that are run on multiprocessor or multithreaded hardware.

concurrent collector
: performs most of its work concurrently (for example, while the application is still running) to keep garbage collection pauses short. It is designed for applications with medium-sized to large-sized data sets in which response time is more important than overall throughput because the techniques used to minimize pauses can reduce application performance.

![enter image description here](https://codeahoy.com/img/blogs/gc-compared.png)

Young 영역과 Old 영역에서 마다 사용할 수 있는 GC 알고리즘이 다르기 때문에 반드시 Compatiable한 알고리즘을 사용해야 한다. 아래 그림은 GC 별로 사용 가능한 알고리즘을 그림으로 나타냈다. 각 요소들에 대해서도 알아보도록 하자.

![enter image description here](https://codeahoy.com/img/blogs/gc-collectors-pairing.jpg)

1.  “Serial” is a stop-the-world, copying collector which uses a single GC thread.
2. “Serial Old”(MSC) is a stop-the-world, mark-sweep-compact(MSC) collector that uses a single GC thread.
3.  **“Parallel Scavenge”**  is a stop-the-world, copying collector which uses multiple GC threads.
4.  **“ParNew”**  is a stop-the-world, copying collector which uses multiple GC threads. It differs from “Parallel Scavenge” in that it has enhancements that make it usable with Concurrent Mark Sweep(CMS). For example, “ParNew” does the synchronization needed so that it can run during the concurrent phases of CMS.
5.  **“CMS”**  (Concurrent Mark Sweep) is a mostly concurrent, low-pause collector.
6.  **“Parallel Old”**  is a compacting collector that uses multiple GC threads.

CMS와 ParNew은 굉장이 잘 동작한다. 또 Parallel Scavenge, Parallel Old 조합도 좋다.

### Mark-Sweep-Compact

앞에서도 보았지만, 

전통적인 GC는 Old 제너레이션에서  **mark-sweep-compact** 방법을 사용한다. 

1.  The first step of this algorithm is to mark the surviving objects in the old generation.
2.  Then, it checks the heap from the front and leaves only the surviving ones behind (sweep).
3.  In the last step, it fills up the heap from the front with the objects so that the objects are piled up consecutively, and divides the heap into two parts: one with objects and one without objects (compact).


One important thing to know about the CMS is that there have been  **[calls to deprecate](https://openjdk.java.net/jeps/291)**  it and it will probably happen in Java 9 :’( Oracle recommends that the new concurrent collector, the  [Garbage-First](https://docs.oracle.com/javase/7/docs/technotes/guides/vm/G1.html)  or the  **G1**, introduced first with Java, be used instead:

> The G1 collector is a server-style garbage collector, targeted for multi-processor machines with large memories. It meets garbage collection (GC) pause time goals with high probability, while achieving high throughput.

**G1**  works on both old and young generation. It is optimized for larger heap sizes (>10 GB). I’ve not experienced G1 collector first-hand and developers in my team are still using CMS, so I can’t yet compare the two. A quick online search reveals benchmarks showing  [CMS outperforming](http://blog.novatec-gmbh.de/g1-action-better-cms/)  [G1](https://dzone.com/articles/g1-vs-cms-vs-parallel-gc). I’d tread carefully, but G1 should be fine. It can be enabled with:


자바 1.6에는 G1([Garbage First](https://www.oracle.com/technetwork/tutorials/tutorials-1876574.html))라는 새로운 방법을 사용하게 되었다. 

The biggest advantage of the G1 GC is its  **performance**. It is faster than any other GC types that we have discussed so far. 


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbODUzMDQ3MTA3LC0xMzk1MzYyMzY2LDg4OT
U1NjExOCwxNzQ2NDA1NTIxLC0yMDg3Njc5NjA2XX0=
-->