
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

전통적인 GC 알고리즘으로 아래 단계를 거쳐 동작한다. 

Mark
: 계속 남아 있을 객체를 식별한다. 즉 gc 대상이 아닌 참조 중인 객체임을 판별한다.

Sweep
: Heap의 앞 부분부터 Mark되지 않은 객체를 제거한다.

Compact
: Sweep 이후, 비어있는 Heap 공간들을 연속되게 쌓이도록 힙의 앞 부분부터 채운다.

## GC 종류

**- Serial (-XX:+UseSerialGC) : Serial GC는 적은 메모리와 CPU 코어 개수가 적을 때 적합한 방식**

**- Parallel  (-XX:+UseParallelGC) : Serial GC와 기본적인 알고리즘은 같지만 여러개의 Thread가 나누어져 처리하는 방식**

**- Parallel Old (-XX:+UseParallelOldGC)**
Parallele GC와 비교하여 단계의 차이가 있다. 기존 알고리즘이 Mark - Sweep - Compaction 단계를 거치는데 반해 Parallel Old GC는 Mark - Summary - Compaction 단계를 거친다. Summary 단계는 앞서 GC를 수행한 영역에 대해서 별도로 살아 있는 객체를 식별한다는 점에서 차이가 있습니다.

**- CMS (-XX:+UseConcMarkSweepGC)**

Initial Mark 단계에서는 클래스 로더에서 가장 가까운 객체 중 살아 있는 객체만 찾아 냅니다. 따라서 초기에 STW가 발생되는 시간이 매우 짧게 형성되어 이점을 가져 올 수 있습니다.

Concurrent Mark 단계에서는 Initial Mark에서 확인된 객체에서 참조하고 있는 객체들을 따라가면서 확인을 하게 됩니다.

Remark 단계에서는 Concurrent Mark 단계에서 새로 추가되거나 참조가 끊어진 객체를 확인합니다.

마지막으로 Concurrent Sweep 단계에서는 GC를 수행하는 작업을 실행합니다.

특징은 Concurrent Mark / Concurrent Sweep을 수행하는 과정에서 다른 쓰레드 들이 실행되고 있는 상황에서 진행된다는 것이 성능상 이점을 가져 옵니다.

CMS는 STW가 짧다는 장점과 더불어 다음과 같은 단점이 존재합니다.

**a. 다른 GC 방식보다 메모리와 CPU를 더 많이 사용한다.**

**b. Compaction 단계가 기본적으로 제공되지 않는다.**

결국 초기 STW를 줄일 수 있지만, Compaction이 없어 조각난 메모리가 많아지만 오히려 STW가 늘어날 수 있다는 단점을 보유하고 있습니다.



### G1(Garbage-First) 
사실 Java 9에서 부터 CMS(Concurrent Mark Sweep)은 deprecated되었고, 오라클은 새로운 Concurrent Collector를 추천했다. 바로 G1(Garbage-First) 컬렉터이다.

**G1**  works on both old and young generation. The biggest advantage of the G1 GC is its  **performance**. It is faster than any other GC types that we have discussed so far. 


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTM4MTk4ODg0MSwtMTM5NTM2MjM2Niw4OD
k1NTYxMTgsMTc0NjQwNTUyMSwtMjA4NzY3OTYwNl19
-->