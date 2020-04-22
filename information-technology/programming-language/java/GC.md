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

Mark 작업은 계속 남아 있을 객체를 식별한다. 즉 Major GC 제거 대상이 아닌 사용 중인 객체를 식별한다. Sweep 작업은 Old 영역의 Mark되지 않은 객체를 제거한다. Compact 작업은 Sweep 작업 이후, 살아남은 객체를 메모리 공간 앞쪽부터 연속되게 쌓이도록 한발생한다고 말한다. Old 영역은 기본적으로 데이터가 가득 차면 GC를 실행한다. GC Policy에 따라 어떠한 방식으로 gc를 수행할 것이지가 결정되어 이는 성능에 커다른 영향을 끼치게 합니다. 

### CMS(ConcurrentMark&Sweep, -XX:+UseConcMarkSweepGC)

아래 그림은 전통적인 싱글 스레드만 사용한 GC와 CMS간 차이를 보여주는 그림이다. 
CMS는 InitalMark, ConcurrentMark, Remark, Concurrent Sweep 4가지 단계가 있다. Initial Mark 단계에서는 Old영역 루트로부터 직접 닿을 수 있는 초기 객체 집합을 식별한다. Concurrent Mark 단계에서는 Initial Mark에서 식별하고 있던 객체들을 따라가면서 모든 살아있는 객체를 식별한다. 하지만 다른 쓰레드들이 동작 중이라 참조가 계속 변화하고 있기 때문에 Remark 단계에서 실질적으로 새로 추가되거나 참조가 끊어진 객체를 최종 식별한다. 마지막으로 Concurrent Sweep 단계에서는 마킹되지 않은 객체를 제거한다.

![enter image description here](https://miro.medium.com/max/4356/1*cXlP_rU-UjQR5uE1Tw8dqA.png)

CMS는 STW가 짧다는 장점과 다른 쓰레드들이 실행되고 있는 수행된다는 점 때문에 성능상 이점이 있다. 하지만 다른 GC 방식보다 메모리와 CPU를 더 많이 사용하며 Compact 과정이 없기 때문에 초기 STW를 줄일 수 있지만, 애먜한 크기의 조각난 메모리가 많아지만 오히려 STW가 많아질 수 있다는 단점이 있다. 

### G1(Garbage-First) 

사실 앞서 설명한 CMS는 Java 9에 도입된 G1 GC 덕분에 사실상 deprecated 되었고 오라클에서 공식적으로 추천하는 GC는 바로 G1GC이다.
G1GC는 메모리공간을 마치 바둑판처럼 영역 구분을하고 각 영역에 객체를 할당한다. 그러다가 특정 영역이 꽉 차면 다른 영역에 객체를 할당하고 그 특정영역에서만 GC를 수행한다. G1GC는 장기적으로 성능이슈가 발생할 수 있는 CMS GC의 대체 방안으로 고안되었으며, 성능은 지금까지 다룬 어떤 GC 보다 뛰어나다.

## GC Types By Thread

Thread 사용법에 따른 GC 종류는 크게 3가지 타입이 있다.

serial collector
: uses a single thread to perform all garbage collection work, which makes it relatively efficient because there is no communication overhead between threads. It is best-suited to single processor machines -XX:+UseSerialGC.

parallel collector(also known as the throughput collector)
: performs minor collections in parallel, which can significantly reduce garbage collection overhead. It is intended for applications with medium-sized to large-sized data sets that are run on multiprocessor or multithreaded hardware.

concurrent collector
: performs most of its work concurrently (for example, while the application is still running) to keep garbage collection pauses short. It is designed for applications with medium-sized to large-sized data sets in which response time is more important than overall throughput because the techniques used to minimize pauses can reduce application performance.

![enter image description here](https://codeahoy.com/img/blogs/gc-compared.png)

## GC Usages

Young영역과 Old 영역에 따라서 사용할 수 있는 GC 알고리즘이 다르기 때문에 반드시 적합한 알고리즘의 조합을 사용해야 한다. 아래 그림은 GC 별로 사용 가능한 알고리즘을 나타낸다.

![enter image description here](https://codeahoy.com/img/blogs/gc-collectors-pairing.jpg)

1.  “Serial” is a stop-the-world, copying collector which uses a single GC thread.
2. “Serial Old”(MSC) is a stop-the-world, mark-sweep-compact(MSC) collector that uses a single GC thread.
3.  **“Parallel Scavenge”**  is a stop-the-world, copying collector which uses multiple GC threads.
4.  **“ParNew”**  is a stop-the-world, copying collector which uses multiple GC threads. It differs from “Parallel Scavenge” in that it has enhancements that make it usable with Concurrent Mark Sweep(CMS). For example, “ParNew” does the synchronization needed so that it can run during the concurrent phases of CMS.
5.  **“CMS”**  (Concurrent Mark Sweep) is a mostly concurrent, low-pause collector.
6.  **“Parallel Old”**  is a compacting collector that uses multiple GC threads.

CMS와 ParNew은 굉장이 잘 동작한다. 또 Parallel Scavenge, Parallel Old 조합도 좋다.

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
.
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDc4Mjc0OTMsMzA1MTc4NTcxLC0zODUzOD
AxODcsOTQ3ODg5NTM4LDE1MDQxMjc3NTksLTEyMjcyNDAzMDcs
LTE0MTAxNjE2ODYsMTExMTQxMzI4NCwtOTYyMjA2MDYwLDEyND
gyNTQ5OTksNjE0MzIzNDEwLC0xMjYyNzIyNDM5LDE4Mzk5NTY2
MjksLTEzMjY4NzQ2MjMsLTIxNDE3NjM2OTgsLTE4NzM0MDU5ND
AsMTE4ODcyOTYwNSw0NDYyMTU0MzIsMTE1NzIyOTc3NCwtMTM5
NTM2MjM2Nl19
-->