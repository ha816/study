# GC(Garbage Collection)

가비지 컬렉션은 할당된 메모리 중 실제론 사용하지 않는 메모리를 재사용하는 메커니즘이다. 가비지 컬렉션을 쓰면 개발자가 직접 메모리를 해제할 필요가 없다. 

GC에선 매우 중요한 용어가 있는데 바로 stop-the-world'(STW)이다. stop-the-world란, GC 작업을 위해 JVM이 애플리케이션을 멈추는 것이다. stop-the-world가 발생하면 GC를 실행하는 쓰레드를 제외한 나머지 쓰레드는 모든 작업을 멈춘다. 그리고 GC 작업을 완료한 이후에야 중단했던 작업을 다시 시작한다. 

어떤 GC 알고리즘을 사용하더라도 stop-the-world는 발생한다. 일반적으로 GC 튜닝의 목적은 stop-the-world 시간을 줄이는 것이다.

Java는 프로그램 코드에서 메모리를 명시적으로 해제하지 않는다. 가끔 명시적으로 해제하려고 변수를 null로 지정하거나 System.gc() 메서드를 호출하는 개발자가 있다. null로 지정하는 것은 큰 문제가 안 되지만, System.gc() 메서드를 호출하는 것은 시스템 성능에 지대한 악영향을 끼치므로 **System.gc() 메서드는 절대로 사용하면 안 된다.**

## GC Heap Strucuture

가비지 컬렉션을 효율적으로 하기 위한 전략은 weak generational hypothesis이라는 두 가지 가설 하에 만들어졌다. (사실 가설이라기보다 가정 또는 전제 조건이라 표현하는 것이 맞다.) 이 가설에 기반하여 HotSpot VM에서는 메모리 공간을 Young 영역과 Old 영역으로 나누었다. 

>**weak generational hypothesis**
>대부분 객체는 금방 접근 불가능 상태(unreachable)가 된다.
>오래된 객체에서 젊은 객체로 참조는 아주 적게 존재한다.

![enter image description here](https://i.stack.imgur.com/8ZtFA.png)

### Young Generation(Eden, From Survivor, To Survivor)

새로운 객체가 생성되는 공간이다. 하지만 대부분의 객체가 곧 접근 불가 상태가 되기 때문에 Minor GC에 의해서 사라져버린다.

Young 영역은 Eden, Survivor 영역으로 구성되어 있다. Eden에서 살아남은 객체는 Survivor 영역으로 이동한다. 

Survivor From에서 살아남은 객체는 Survivor To로 이동하고, Survivor To에서 살아남은 객체는 다시 Survivor From으로 이동한다. 이를 반복적 수행하다가 Hit(Minor GC로 부터 살아남은 횟수)가 임계값(Tenuring Threshold) 만큼 커진 객체들은 Old영역으로 이동한다.

### Old 영역(Old Generation, Tenured Generation)

Young 영역에서 살아남은 객체가 여기로 복사된다. 대부분 메모리 공간을 Young 영역에서 보다 크게 할당하며, 크기가 큰 만큼 Young 영역보다 GC는 적게 발생한다. 이 영역에서 GC는 Major GC(혹은 Full GC)가 수행한다. 사용하는 Major GC의 알고리즘에 따라 성능이 크게 좌지우지 된다. 일반적인 GC 튜닝은 Old 영역의 메모리 공간을 대상으로 한다.

## GC Algorithms

전통적인 GC 알고리즘으로는 MSC(Mark-Sweep-Compact)이 있다. STW(stop-the-world)를 줄이기 위해 GC 알고리즘은 계속해서 발전해왔다. 동시성 쓰레드를 이용한 CMS(ConcurrentMark&ConcurrentSweep)도 최근까지 사용되었다. 자바 9부터는 오라클의 G1(Garbage-First) 각광받고 있다. 


### MSC(Mark-Sweep-Compact)

Mark 작업은 계속 남아 있을 객체를 식별한다. 즉 Major GC 제거 대상이 아닌 사용 중인 객체를 식별한다. Sweep 작업은 Old 영역의 Mark되지 않은 객체를 제거한다. Compact 작업은 Sweep 작업 이후, 살아남은 객체를 메모리 공간 앞쪽부터 연속되게 쌓이도록 한다. 

### CMS(ConcurrentMark&Sweep, -XX:+UseConcMarkSweepGC)

 Initial Mark 단계에서는 클래스 로더에서 가장 가까운 객체 중 살아 있는 객체만 찾아 냅니다. 따라서 초기에 STW가 발생되는 시간이 매우 짧게 형성되어 이점을 가져 올 수 있습니다. 
Concurrent Mark 단계에서는 Initial Mark에서 확인된 객체에서 참조하고 있는 객체들을 따라가면서 확인을 하게 됩니다.
Remark 단계에서는 Concurrent Mark 단계에서 새로 추가되거나 참조가 끊어진 객체를 확인합니다.
마지막으로 Concurrent Sweep 단계에서는 실제 GC(가비지 컬렉션)을 수행합니다. 

![enter image description here](data:image/png;base64,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)

CMS은 STW가 짧다는 장점과 Concurrent Mark / Concurrent Sweep을 수행하는 과정에서 다른 쓰레드 들이 실행되고 있는 상황에서 진행된다는 것이 성능상 이점을 가져온다.
단점으로는 다른 GC 방식보다 메모리와 CPU를 더 많이 사용하고 Compaction이 기본적으로 제공되지 않는다. 결국 초기 STW를 줄일 수 있지만, Compaction이 없어 조각난 메모리가 많아지만 오히려 STW가 늘어날 수 있다는 단점을 보유하고 있습니다.

### G1(Garbage-First) 
사실 Java 9에서 부터 CMS(Concurrent Mark Sweep)은 deprecated되었고, 오라클은 새로운 Concurrent Collector를 추천했다. 바로 G1(Garbage-First) 컬렉터이다.

**G1**  works on both old and young generation. The biggest advantage of the G1 GC is its  **performance**. It is faster than any other GC types that we have discussed so far. 

G1GC는 메모리를 바둑판처럼 각각의 영역으로 구분하고 각 영역에 객체를 할당하여 GC를 실행합니다. 그러다가, 해당 영역이 꽉 차면 다른 영역에서 객체를 할당하고 GC를 실행합니다. 즉 기존의 Young, Old 영역에서 진행하는 메모리 처리 방식이 한 영역에서 모두 담당한다고 이해하면 됩니다.

G1GC는 장기적으로 문제가 야기될 가능성이 있는 CMS GC의 대체 방안으로 고안되었으며, 성능상 뛰어나다는 장점이 있습니다.



## GC Thread Types

Thread 사용법에 따른 GC 종류는 크게 세 개의 타입이 있다.

serial collector
: uses a single thread to perform all garbage collection work, which makes it relatively efficient because there is no communication overhead between threads. It is best-suited to single processor machines -XX:+UseSerialGC.

parallel collector(also known as the throughput collector)
: performs minor collections in parallel, which can significantly reduce garbage collection overhead. It is intended for applications with medium-sized to large-sized data sets that are run on multiprocessor or multithreaded hardware.

concurrent collector
: performs most of its work concurrently (for example, while the application is still running) to keep garbage collection pauses short. It is designed for applications with medium-sized to large-sized data sets in which response time is more important than overall throughput because the techniques used to minimize pauses can reduce application performance.

![enter image description here](https://codeahoy.com/img/blogs/gc-compared.png)

## GC Usages
Young 영역과 Old 영역에서 마다 사용할 수 있는 GC 알고리즘이 다르기 때문에 반드시 Compatiable한 알고리즘을 사용해야 한다. 아래 그림은 GC 별로 사용 가능한 알고리즘을 그림으로 나타냈다. 각 요소들에 대해서도 알아보도록 하자.

![enter image description here](https://codeahoy.com/img/blogs/gc-collectors-pairing.jpg)

1.  “Serial” is a stop-the-world, copying collector which uses a single GC thread.
2. “Serial Old”(MSC) is a stop-the-world, mark-sweep-compact(MSC) collector that uses a single GC thread.
3.  **“Parallel Scavenge”**  is a stop-the-world, copying collector which uses multiple GC threads.
4.  **“ParNew”**  is a stop-the-world, copying collector which uses multiple GC threads. It differs from “Parallel Scavenge” in that it has enhancements that make it usable with Concurrent Mark Sweep(CMS). For example, “ParNew” does the synchronization needed so that it can run during the concurrent phases of CMS.
5.  **“CMS”**  (Concurrent Mark Sweep) is a mostly concurrent, low-pause collector.
6.  **“Parallel Old”**  is a compacting collector that uses multiple GC threads.

CMS와 ParNew은 굉장이 잘 동작한다. 또 Parallel Scavenge, Parallel Old 조합도 좋다.



## Major GC

Serial (-XX:+UseSerialGC)
: Serial GC는 적은 메모리와 CPU 코어 개수가 적을 때 적합한 방식이다. 전통적인 MSC 방식을 따른다. 

Parallel  (-XX:+UseParallelGC)
: Serial GC와 기본적인 알고리즘은 같지만 여러 개의 Thread가 나누어져 처리하는 방식

Parallel Old (-XX:+UseParallelOldGC)
: Parallel GC와 비교하여 **단계의 차이가 있다.** 기존 알고리즘이 Mark - Sweep - Compaction 단계를 거치는데 반해 Parallel Old GC는 MSC(Mark - Summary - Compaction) 단계를 거친다. Summary 단계는 앞서 GC를 수행한 영역에 대해서만 제거를 하여 성능향상을 꾀한다. 



## 참고 문헌
[GC types](https://www.cubrid.org/blog/understanding-java-garbage-collection)
[Java Garbage Collection](https://d2.naver.com/helloworld/1329)
[GC 잘하는 법
](https://waspro.tistory.com/380)[GC 기초](https://codeahoy.com/2017/08/06/basics-of-java-garbage-collection/)


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5NTQ0MjEyOTYsLTEyNjI3MjI0MzksMT
gzOTk1NjYyOSwtMTMyNjg3NDYyMywxNDMzNzAzNTkyLC0yMTQx
NzYzNjk4LC0xODczNDA1OTQwLDExODg3Mjk2MDUsNDQ2MjE1ND
MyLDExNTcyMjk3NzQsLTEzOTUzNjIzNjYsODg5NTU2MTE4LDE3
NDY0MDU1MjEsLTIwODc2Nzk2MDZdfQ==
-->