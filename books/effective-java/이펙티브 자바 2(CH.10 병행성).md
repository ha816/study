## 병행성

스레드를 이용하면 다양한 작업을 병렬적으로 할 수 있다. 
물론 병령(concurrent) 프로그래밍은 단일 스레드(single-threaded) 프로그래밍 보다 어렵다. 

우리가 하는일 가운데 상상수는 본질적으로 병렬적이며, 멀티코어 프로세서에서 좋은 성능을 내야하는 요구사항도 보편적이 되어가고 있기 때문에 병행성을 포기할 수는 없다.

이번 장에서는 병렬 프로그램을 만드는데 도움이 되는 지치믕 다룬다. 

### 66. 변경 가능 공유 데이터에 대한 접근은 동기화 하라

mutex(상호 배제, mutual exclusion) : 뮤텍스는 상호배제의 줄임말.
제어되는 섹션에 하나의 쓰레드만을 허용하기 때문에 해당 섹션에 접근하려는 다른 쓰레드들을 강제적으로 막음으로써 첫 번째 스레드가 해당 섹션을 빠져나올 떄 까지 기다린다.

Semaphore(세마포어) : 공유 리소스에 접근할 수 있는 최대 허용치만큼 동시에 사용자 접근을 할 수 있게 한다. 쓰레드들은 리소스 접근 요청을 할 수 있고 세마포어에서는 카운트가 하나씩 줄어들게 되며 리소가 모두 사용 중 인경우(카운트 0) 다음 작업은 대기를 하게 된다. 리소스 사용을 마쳤다는 신호를 보내면 카운트가 하나 늘어나게 되고 다음 작업이 사용 할 수 있다.

http://sqlmvp.kr/140188335482

synchronized : 특정 메서드나 코드 블록을 한번에 한 스레드만 사용하도록 보장하는 키워드

대다수 유저는 동기화를 상호배재적(mutually exclusive) 관점, 다른 스레드가 변경 중인 객체의 상태를 관측할 수 없어야 한다는 관점, 으로 본다. 

객체는 캡슐화 되어 있기 때문에 바로 필드에 접근할 수 없다. 
그러한 객체라면, 해당 객체를 접근하는 메서드는, 해당 객체에 락을 건다. 
락을 건 메서드는 객체의 상태를 관측할 수 있고, 선택적으로 객체 상태를 변경 할 수 도 있다. 

동기화를 쓰면, 모든 메서드가 항상 객체의 일관된 상태만 보도록 만들 수 있다. 

동기화가 없다면, 한 스레드가 만든 변화를 다른 쓰레드가 확인 할 수 없다. 
동기화는 스레드가 일관성이 깨진 객체를 관측 할수 없도록 하고, 동기화 메서드나 동기화 블록에 진입한 스레드가 동일한 락의 보호아래 이루어진 모든 변경의 영향을 관측하도록 보장한다. 
-> 동기화를 하면, 스레드가 일관성이 있는 객체만 관측 가능하고 락의 보호아래에서 이루진 모든 변경을 본다. 

**자바 언어 명세에서는 long이나 double이 아닌 모든 변수는
long, double이 아닌 모든 변수는 원자적으로 읽고 쓸수 있다!** 

원자성(atomicity) : 어떤 작업이 한번에 이루어져야 한다는 것을 말한다. java 에서는 32비트 변수를 읽고 쓰는 작업은 모두 원자성 작업이다. 이러한 atomic 변수를 사용하면 synchronized 키워드 없이 사용해도 lock 도 없고 스레드에 안전하다.

출처: [http://mygumi.tistory.com/111](http://mygumi.tistory.com/111) [마이구미의 HelloWorld]

자바 언어 명세인 메모리 모델 : 한 스레드가 만든 변화를 다른 스레드가 관측할수 있는 시점과, 그 절차를 규정한다. 

예를 들어, 한 스레드에서 다른 스레드를 중지해야 한다고 할때,  Thread.stop이 메서드가 있다. 그런데 **이건 절대로 쓰지말자** 
그것보다 boolean 필드를 이용해서 한 스레드는 필드의 값이 true로 바뀌는지 확인하여 true로 바뀌면 실행을 스스로 중단하면 된다. 
몇몇 프로그래머는 boolean 필드는 원자적으로 읽고 씀으로 동기화가 필요 없는것 아니냐 라고 생각한다.

예제에는 main 스레드가 변경한 stopRequest의 새로운 값을 후면 스레드가 언제 볼수 있을지 알수가 없다.
```
public class StopThread {

	private static boolean stopRequestd;
	private static synchronized void requestStop(){ stopRequested = true; }

	private static synchronized void stopRequested() {
	return stopRequested;
	}
}
```

이런 식으로 동기화를 해줘야 원하는 방향으로 프로그래밍이 동작.
**읽기 연산과 쓰기 연산 모두에 동기화를 적용해 주자**

사실 위에서 동기화 메서드는 동기화를 꼭 하지 않아도 원자적이다. 
다시 말해, 메서드를 상호배제를 위해서 사용하는 것이 아니라, 순전히 스레드간의 통신 문제를 해결하기 위함

멀티 스레드 환경에서 문제는 아래와 같이 두가지로 생각해 볼수 있다. 
* 경쟁 상태(race condition)
- 여러 스레드 같은 시점 변수를 읽는 상태.
* 변수의 가시성(visibility)
- 변수들이 사용될 수 있는 영역의 범위


먼저 변수의 가시성의 경우 ...
변수의 가시성 : 변수의 값은 CPU와 메모리에 저장된다. 변수 값을 계산할 때 CPU에 있는 변수 값을 사용할지 메모리에 있는 값을 사용할지 알수가 없는 것이 변수의 가시성 문제다. 
이 문제는 volatile을 사용하면 해결이 가능하다. 

volatile을 사용한다면 선언된 변수를 읽고 쓰는 모든 과정을 메인 메모리에서만 처리한다. 이로써 어느 저장공간에 있는 변수를 사용할지 변수의 가시성 문제를 해결할 수 있다. 

하지만 가시성 문제는 해결했지만, 여전히 경쟁 상태 문제가 발생한다.
메인 메모리만을 이용한다하더라도 동시에 변수를 읽어들이는 상황은 발생하기 때문이다.

volatile의 경우는 하나의 스레드가 쓰기 연산을 하고, 다른 스레드에서는 읽기 연산을 통해 최신 값을 가져올 경우. 즉 다른 스레드에서는 업데이트를 행하지 않을 경우 이용할 수 있다.  
그래서 업데이트의 정확성이 꼭 필요한게 아니고 missing update가 상관 없는 상태라면 volatile을 써도 된다 라고 합니다. 

각각의 스레드는 새로운 최신값을 항상 가져온다. 
모든 스레드가 0이라는 값을 가져왔다고 치자. 
그 중 한 스레드에서 계산을 통해 +1 을 적용했다. 그러면 메모리에 새로운 값 1이 적용된다. 그리고 다른 스레드는 바뀐 최신 값을 새로 불러와서 마찬가지로 +1을 해야하는거 아냐??? 
그런데 아니래 ㅋㅋㅋㅋㅋㅋ
왜그런고 하니 ordering을 안해줘서 그렇다네?? 

he reasons for that are very well explained at  [Jenkov.com](http://tutorials.jenkov.com/java-concurrency/volatile.html)  so I will discuss them briefly.

When running multi threaded application on multi CPU hardware it’s possible that 2 Java threads can execute in 2 different CPU threads. The CPU threads have their own CPU Thread Cache (a.k.a Java Stack Cache) that is used by the JVM to copy over variables from the Main Memory, obviously for performance reasons since access to Main Memory is very costly. And here lies the issue! **There is no guarantee when the updated variable is going to get flushed back to Main Memory for other threads to access.**

접근하는 다른 스레드를 위해서 수정된 변수가 메인 메모리로 새로 들어갈 보증이 없다. 

https://jorosjavajams.wordpress.com/volatile-vs-synchronized/

#### Use Volatile when you variables are going to get read by  **multiple**  threads, but written to by only  **one**  thread

The below diagram shows two threads: Thread 1 and Thread 2. If Thread 1 will read and write to a common “counter” variable it will:

1.  read it from the Main Memory into CPU cache;
2.  write to it in CPU Cache;
3.  flush the new value back to Main Memory;

Since only one thread is operating on that variable – all is fine.

However, if Thread 2 needs to also read that variable from the Main Memory it may end up with the old, non-incremented value, because there is no guarantee when the Thread 1 will flush it’s update. This represents memory visibility issue – the updates of one thread are not visible to other threads.

The situation can be quickly resolved by using Volatile modifier on the “counter” variable. **Volatile will force all changes made by this thread to the “counter” variable in the CPU Cache to be immediately flushed back to the Main Memory so other threads can always access fresh data, effectively keeping the volatile variable out of CPU caches.**

Volatile should not be used when multiple threads can read and write to shared variables, because though any changes made by Thread 1 will be flushed back to Main Memory, the flushing will still take time and that creates racing condition which may at some point ruin “counter” ‘s value.

a `volatile` variable is no longer enough to guarantee correct visibility. The short time gap in between the reading of the `volatile` variable and the writing of its new value, creates an [race condition] where multiple threads might read the same value of the `volatile`variable, generate a new value for the variable, and when writing the value back to main memory - overwrite each other's values.

Imagine if Thread 1 reads a shared `counter` variable with the value 0 into its CPU cache, increment it to 1 and not write the changed value back into main memory. Thread 2 could then read the same `counter`variable from main memory where the value of the variable is still 0, into its own CPU cache. Thread 2 could then also increment the counter to 1, and also not write it back to main memory. This situation is illustrated in the diagram below:

즉 Thread 1에서 count = 0(메모리에 있던 0값) + 1; 을 실행하면 메모리에서 0을 가져오고 캐시에 올린 다음 +1을 계산하고 다시 메인메모리에 refresh하지 않을수 있다. 그렇기 때문에 Thread2에서는 마찬가지로 메모리에 있던 0값을 가져와 캐시에 올리고 +1을 계산한다. 그러면 결국 메인메모리에 refresh되는 것은 +2가 아닌 +1이 겹쳐져 두번 업데이트가 된다. 

http://tutorials.jenkov.com/java-concurrency/volatile.html#volatile-is-not-always-enough
    
출처: [http://mygumi.tistory.com/112](http://mygumi.tistory.com/112) [마이구미의 HelloWorld]


  
  
출처: [http://mygumi.tistory.com/112](http://mygumi.tistory.com/112) [마이구미의 HelloWorld]

boolean 필드 stopRequested를 volatile로 선언하면, 락을 사용하는 동기화를 쓸 필요가 없다. 
**volatile은 상호배제성을 실현 하진 않지만 어떤 스레드건 가장 최근에 기록된 값을 읽도록 보장한다.**

휘발성(volatile) : 멀티스레드에서 사용되는 변수의 변화(값의 변화)에 대한 **가시성(visibility)**을 보장한다.
각 스레드들은 volatile 변수를 읽어 들일때 CPU 캐시가 아닌 메모리로 부터 읽어들인다. 즉 변수값 변경에 있어서는 멀티스레드에 안전하지 않지만 각 멀티스레드에서 읽은 volatile 변수는 가장 최근에 쓰여진 값을 보는것을 보장할 수 있다.
변수의 가시성 : 변수의 값은 CPU와 메모리에 저장된다. 변수 값을 계산할 때 CPU에 있는 변수 값을 사용할지 메모리에 있는 값을 사용할지 알수가 없는 것이 변수의 가시성 문제다. 

추가 내용 : https://www.javamex.com/tutorials/synchronization_volatile.shtml

Essentially, volatile is used to indicate that a variable's value will be modified by different threads.

Declaring a  volatile  Java variable means:
-   The value of this variable will  **never be cached thread-locally**: all reads and writes will go straight to "main memory";
-   Access to the variable acts as though it is enclosed in a  synchronized  block, synchronized on itself.
		- 변수에 접근은 마치 synchronized block에서 금지구역인것처럼 동작한다. 스스로에게 동기화를 건다. 

We say "acts as though" in the second point, because to the programmer at least (and probably in most JVM implementations) there is no actual lock object involved. Here is how synchronized and volatile compare:
-> 마치 동기화 블록인것 처럼 행동을 한다는게 포인트

enclosed : 금지구역의(형용사)

Difference between synchronized and volatile
| 특징 | Synchronized  | Volatile |
|--|--|--|
| Type of variable | Object | Object or primitive|
| Null  allowed? | No | Yes|
| Can block? | Yes | No|
| All cached variables synchronized on access? | Yes | From Java 5 onwards|
| When synchronization happens | When you explicitly enter/exit a  synchronized  block | Whenever a volatile variable is accessed.|
| an be used to combined several operations into an atomic operation? | Yes | Pre-Java 5, no.Atomic get-set of volatiles possible in Java 5|

### Java 5 definition of  volatile

As of Java 5, accessing a volatile variable creates a  **memory barrier**: it effectively  **synchronizes  _all_  cached copies of variables with main memory**, just as entering or exiting a  synchronized  block that synchronizes on a given object. Generally, this doesn't have a big impact on the programmer, although it does occasionally make  volatile  a good option for safe  _object publication_. The infamous  [double-checked locking](https://www.javamex.com/tutorials/double_checked_locking.shtml)  antipattern actually becomes valid in Java 5 if the reference is declared  volatile. (See the page on  [how to fix double-checked locking](https://www.javamex.com/tutorials/double_checked_locking_fixing.shtml)  for an example.) And  volatiles memory synchronization behaviour has occasionally allowed more efficient design in some of the library classes via a technique dubbed  [synchronization 'piggybacking'](https://www.javamex.com/tutorials/synchronization_piggyback.shtml).


문제를 피하는 가장 좋은 방법은 변경 가능 데이터를 최대한 공유 하지 말자! 굳이 공유를 한다면 변경 불가능한 데이터를 공유 하거나 그럴필요가 없으면 공유하지 마라. **변경 가능한 데이터의 경유라면 한 스레드만 이용하도록 해라** 

변경 가능한 데이터를 한 스레드로만 처리할땐 사실을 문서로 남기자. 

요약하자면, 변경 가능한 데이터를 공유 할땐 데이터를 읽거나 쓰는 모든 스레드는 동기화를 수행해야 한다. 동기화를 하지 않으면, 다른 스레드가 만든 변경사항을 관측한다는 보장이 없다. 

##67. 과도한 동기화는 피하라.

상황에 따라 동기화를 너무 과도하게 적용하면 성능저하, 교착상태, 비결정 동작등의 문제가 발생한다. 

**생존 오류나 안전 오류를 피하고 싶으면, 동기화 메서드나 블록 안에서 클라이언트에게 프로그램 제어 흐름을 넘기지 마라**

- 동기화가 적용된 영역안에서는 재정의 가능 메서드나 클라이언트가 제공한 함수 객체 메서드를 호출하지 말라는 것. 
- 동기화 영역이 존재하는 클래스 관점에서, 이러한 메서드는 불가해(alein) 메서드다. 해당 메서드가 무슨일을 하는지, 제어 할수도 없다. 
- 동기화 영역안에서 이러한 메서드를 호출하면 예외나 교착 상태, 데이터 훼손등이 발생한다. 

생존 오류(liveness failure) : 일정 조건이 되면 스레드가 종료 되어야 하는데 적절한 동기화 실패로 계속해서 스레드가 살아있는 상태.
안전 오류(safety failure) : 적절한 동기화 실패로, 다수의 스레드가 연산에 개입하여 잘못된 값을 계산하는 것.
생존 오류나 안전 오류는 정말로 찾기 힘든 오류다. 

1번 예제의 문제는 예외가 발생한다. observers의 리스트를 순회하고 있는데 리스트에서 원소를 삭제하려고 한것이다. 물론 클래스 메서드에서 순회시에 리스트 observers가 병렬적으로 수정되는 일을 막기 위해서 동기화를 해두었지만 ... 
동기화 블록 안에서 외부(불가해, alein)의 객체의 메서드를 호출해서 리스트 observers를 변경하는것까지 막을 순 없었다. 

2번 예제의 경우 예외는 발생하지 않지만 교착 상태가 발생한다. 
이미 메인 스레드에서 observer 추가 메서드를 실행하면서 리스트 observers에게 락을 걸어둔 상태인데 후면 스레드가 removeObserver를 호출하려고 할때 observers에 락을 걸려 한다. 
메인 스레드는 후자 스레드가 작업을 끝내기를 기다리면서 락을 걸고 있는데  -> 데드락

다행이도 이런 문제는 불가해(alien) 메서드를 동기화 영역 밖으로 옮겨 해결 가능하다. 
-> 순회시 락을 거는게 문제였기 때문에 ... 
-> 복사본을 만들어 쓰자!

동기화 영역 바깥에서 불가해 메서드를 호출하는걸 open call이라고 한다. 

명심할것은, 동기화 영역안에서 수행되는 작업은 최대한 작업 양을 줄여야 한다. 시간이 오래 걸리는 작업을 한다면, 동기화 영역 밖에서 하려고 노력해보자. 

요약하자면, 데드락과 데이터 훼손 문제를 해결하려면, 동기화 영역 안에서 불가해 메서드는 호출하지 말라는 것. 그리고 동기화 영역 안에서는 작업의 양을 제한하자. 

### 68. 스레드보다는 실행자와 태스크를 이용하라.

자바 릴리스 1.5부터 자바 플랫폼에 유틸 concurrent가 추가 되었다. 이 패키지에는 실행자 프레임워크(Executor Framework)가 추가 되었는데, 유연성이 높은 인터페이스 기반 태스크(task) 실행 프레임워크다. 

이 프레임워크를 사용하면 모든 면에서 나는 작업 큐를 한줄의 코드로 생성이 가능하다. 

```
ExecutorService executor = Excutors.newSingThreadExecutor();
executor.execute(runnable); // 실행자에 Runnable을 넘겨 실행
executor.shutdown(); // 실행자가 종료되도록 한다, 반드시 넣자
```

큐의 작업을 처리하는 스레드를 여러개 만들고 싶을때는 스레드 풀이라는 실행자 서비스를 생성하는 정적 팩터리 메서드를 이용하면 된다. 
Executors에는 필요한 실행자 대부분을 만드는 정적 팩터리 메서드가 있다. 

상황에 따라 다른 실행자 서비스를 만들어 활용한다. 
보통 부하가 크지 않을때 : newCachedTreadPool이 좋다. 
캐시 기반 스레드풀의 경우, 작업이 큐로 들어가는 것이 아니라, 실행을 담당하는 스레드로 바로 넘겨진다. 가용한 스레드가 없는 경우, 새 스레드가 만들어 짐으로, 부하가 많은 서버의 경우 부적합

부하가 큰 환경 : newFiexedThreadPool 
고정된 스레드 개수를 가지는 풀을 만들거나 ...  직접 threadPoolExcecutor를 상속받아 만들자.

Thread는 작업의 단위 였을 뿐만 아니라 작업 실행 매커니즘이였지만... 이제는 작업과 실행 매커니즘이 분리되었다. 
중요한것은 작업의 단위이며, 테스트라고 한다. 
태스크에는 두 종류가 존재 : Runnable, Callable;

테스크를 실행하는 메커니즘은 executor이고 실행 정책을 정한다. 

### 69. wait이나 notify 대신 병행성 유틸리티를 사용하자. 

wait과 notify를 정확하게 쓰는건 어려우니 그냥 유틸 쓰자! :)

실행자 프레임워크는 68에서 공부했으니 이번 장에서는 병행 컬렉션과 동기자에대해 공부하자

병행 컬렉션
---- 
List, Queue,Map등의 표준 컬렉션 인터페이스에 대한 고성능 병행 컬렉션 규현을 제공. 
병행성 높이기 위해 동기화를 내부적으로 처리.
컬렉션 외부에서 병행성 처리하는 것은 불가하고 설사 하더라도 효과가 없음.

확실한 이유가 없으면, Collections.synchronizedMap이나 HashTable 대신에 ConcurrentMap을 쓰자
Hashtable, HashMap, ConcurrentHashMap 비교: https://jdm.kr/blog/197

컬렉션 인터페이스 가운데 몇몇은 봉쇄 연산(blocking operation)이 가능.
성공적으로 수행될때까지 대가 하도록 대기(wait)가 가능하다. 
EX) BlockingQueue는 queue를 확장해서 take와 같은 연산을 추가하였다. 
take : 큐의 맨앞 원소제거 후 반환하는데, 큐가 비어있는 경우 대기한다.

봉쇄연산을 통해서 생산자 - 소비자 큐(producer-consumer queue)라고 하는 작업큐 구현에 blocking queue를 사용할 수 있다. 
생산자 스레드(producer thead)는 큐에 작업을 넣는다.
소비자 스레드(consumer thread)는 큐에 작업이 들어오면 처리한다.
대다수의 ExcutorService의 구현은 BlockingQueue를 사용한다. 

동기자
----
동기자들은 스레드들이 서로를 기다릴수 있도록 하여, 상호 협력이 가능하게 한다. 
가장 널리 쓰이는 동기자: CountDownLatch와 Semaphore
카운트 다운 래치(CountDownLatch) : 일회성 배리어, 하나 이상의 스레드가 작업을 마칠때까지 다른 여러 스레드가 대기하도록 한다. 
카운트 다운 래치에 정의된 유일한 생성자는 래치의 카운트다운 메서드가 호출될 수 있는 수를 나타내는 int값을 인자로 받는다.  대기 중인 스레드가 진행하려면 그 횟수가 다 소진 될때까지 카운트다운 메서드를 호출해야 한다. 

논외로 만약 어떤 작업을 병렬적으로 처리하는데 드는 시간을 재는 간단한 프레임워크를 만든다고 하면...?
작업을 실행할 수행자와 병행성 수준 int값, Runnable 객체를 인자로 받을 메서드가 필요하다! 

시간을 재는 타이머 스레드가 실행되기 전에, 모든 작업 스레드는 작업 실행 준비를 해둬야하고 타이머 스레드가 출발 신호를 내리면 작업을 시작한다. 작업 스레드가 일을 끝내면 타이머 스레드는 시계를 멈추고 시간을 잰다. 
이러한 로직은 wait와 notify로 구현하자면 ...
-> 번잡해...
-> 하지만 카운트다운 래치를 쓰면 정말 직관적이다. 

코드 -> 

몇가지 주의 사항 
1. 스레드 고갈 교착상태 : time 메서드에 전달된 실행자는 병행성 수준 만큼의 스레드를 생성할 수 있어야 한다. 
2. System.currentTimeMills 대신에 System.nanoTime을 사용하자.  특정 구간의 실행시간을 잴때는 System.nanoTime을 사용하는게 좋다.

wait나 notify를 쓰는것 보다 병행성 유틸리티를 사용하는 것이 맞긴 하지만, 기존 코드도 유지할 필요가 있다. 
wait 메서드 : 스레드가 어떤 조건이 만족되길 기다리도록 하고 싶을때 사용
동기화 영역 내에서 호출해야 하며, 호출 대상 객체에는 락이 걸린다.
호출하는 스레드가 반드시 고유 락을 갖고 있어야 한다. 다시 말해, `synchronized` 블록 내에서 호출되어야 한다. 
객체의 모니터링 락에 대한 권한을 가지고 있다면 모니터링 락의 권한을 놓고 대기한다.


// wait메서드를 사용하는 표준적 숙어
synchronized (obj) {
	while(조건이 만족되지 않을 경우 순환문 실행){
			obj.wait(); // 락 해제. 깨어나면 다시 락 획득, 갖고 있던 고유 락을 해제하고, 스레드를 잠들게 한다. 
	}
		// 조건이 만족되면 그에 맞는 작업 실행
}

**wait 메서드를 호출할때는 반드시 대기 순환문 숙어대로 하자**
순환문 밖에서 호출하면 안된다. 

항상 스레드가 락이 걸린 객체는 다르다는 것을 기억하자.
스레드는 특정 객체를 다루는데 락이 걸린 객체가 있으면 작업이 불가하다. 

동기화 블록에 들어가면 이 객체는 다른 스레드에서 볼수가 없다. 
while문에서 조건이 만족되지 않으니 obj.wait 실행 // 다른 스레드에서 볼수가 없으니 몇 번 건너 뛰다가 볼수 있는 스레드에서 obj.wait()을 한다. 
// 그러면 obj에 대한 락이 해제되서 다른 스데르가 볼수 있다. 그리고 해당 스레드는 대기 상태로 들어간다. 

notify메서드 : 잠들어 있던(대기중) 스레드 중 임의로 하나를 골라 깨운다.
notifyAll 메서드 : 호출로 잠들어 있던(대기중) 스레드 모두 깨운다.

많은 사람들은 항상 notifyAll을 쓰라고 한다. 보수적인 관점에서 보면 맞는 말이다. 
깨어날 필요가 있는 모든 스레드를 깨우므로, 항상 정확한 결과가 나온다. 

만약 모든 스레드가 동일한 조건이 만족되길 기다리고 있고, 그 중 하나 스래드만 재개하려면 notify를 써서 최적화 할수도 있다. 
그렇다고 하더라고 notifyAll을 쓰자. 
-> 다른 스레드가 우연히, 또는 악의적으로 wait를 호출하는 상황을 피할수 있다. notify를 특정 스레드가 삼켜버리면 정말 실행되어야 하는ㄷ 다른 스레드들을 무한히 대기하게 될수도 있다. 

요약하자면, wait 나 notify를 써서 코딩하는 것은 "병행성 어셈블리 언어"를 사용하는것과 같다. 새로 만드는 프로그램에서 wait나 notify를 사용한 이유는 거의 없다. 
만약 유지보수를 위해 쓴다면, while 문 안에서 wait를 호출하는 표준숙어를 따르자. 그리고 일반적으로 notifyAll을 쓰도록 하자.

### 70. 스레드 안정성에 대해 문서로 남겨라

어떤 메서드가 다중 스레드에 안전한지 문서에 synchronized 키워드가 있는지를 보면 알수 있는데, 이러한 가정에는 문제가 있다. 
synchronized가 스레드 안정성을 나타내기에 충분하다는 주장은 스레드 안정성이 전부 아니면 전무(all or nothing )이라는 오해에서 비롯된 것이다. 
**병렬적으로 사용해도 안전한 클래스가 되려면 어떤 수준의 스레드 안정성을 제공하는 클래스인지 문서에 명확하게 남기자** 

스레드의 안정성
|이름| 설명 | 샘플 |
|--|--|--|
|변경 불가능(immutable)  | 이 클래스로 만든 객체들은 상수다. 따라서 동기화 매커니즘이 없어도 병렬적으로 이용이 가능하다. | String, Long, BigInteger |
|무조건적 스레드 안정성(unconditionally thread-safe)  | 이 클래스의 객체들은 변경이 가능하지만, 적절한 내부 동기화 매커니즘을 갖추고 있어서 외부적으로 동기화 매커니즘을 적용하지 않아도 병렬적으로 사용할수 있다. |Random, ConcurrentHashMap|
|조건부 스레드 안정성(conditionally thread-safe)| 무조건적 스레드 안정성과 거의 비슷한 수준이지만 몇몇 스레드는 외부적 동기화가 없이는 병렬적으로 사용 할수 없다. | Collections.synchronized 계열 메서드가 반환하는 포장객체. 이러한 객체는 iterator의 외부적 동기화 없이 병렬적으로 사용 불가|
|스레드 안정성 없음| 이런 클래스의 객체들은 변경 가능하다. 해당 객체들을 병렬적으로 사용하려면 클라이언트는 메서드를 호출하는 부분을 클아이언트가 선택한 외부적 동기화 수단으로 감싸야 한다. | ArrayList, HashMap|
|다중 스레드에 적대적(thread-hostile)|이런 클래스의 객체는 설사 메서드를 호출하는 모든 부분을 외부적 동기화 수단으로 감싸더라도 불안전하다. 이런 클래스가 되는것은 보통 정적 데이터를 변경하기 때문이다. 보통은 이런 클래스를 고의로 만들지는 않는다. 그저 병행성을 생각하지 않고 구현했기 때문이다. | System.runFinalizersOnExit: depercated| 

대략적으로 보면 조건부/ 무조건부 스레드 안정성 범주는 전부 ThreadSafe 어노테이션에 해당한다. 

조건부 스레드 안정성의 경우 문서를 만들때 신중해야 한다.  어떤 순서로 메서드를 호출할때 외부 동기화 매커니즘을 동원해야 하는지, 그리고 그 순서로 메서드를 실행하려면 어떤 락을 사용해야하는지 명시. 
보통은 객체 자체에 락을 걸면 되는데 예외도 있다. 

요약하자면, 산문조로 기렉 풀어 쓰건 아니건 스레드 안정성 어노테이션을 이요하건 간에, 모든 클래스는 자신의 스레드 안전성 수준을 문서로 분명히 밝혀야 한다. 
synchronized만으로 스레드 안정성을 판단하지 말자. 

조건부 스레드 안정성을 제공하는 클래스는 어떤 순서로 메서드를 홀출할 때 외부적 동기화가 필요한지 문서에 밝혀야 하고, 어떤 락을 획득하게 되는지도 밝혀야 한다. 
무조건부 스레드 안정성을 제공하는 클래스의 경우, synchrnoized로 선언하는 대신 private 락 객체를 이용하면 어떨지 ...? 

자바 스레드 모니터링 : http://kiwi99.tistory.com/19?category=375710

### 71. 초기화 지연은 신중하게 하라 

초기화 지연(lazy initalization)은 필드 초기화를 실제로 그 값이 쓰일때 까지 초기화를 미루는 것이다. 초기화 지연 기법은 기본적으로 최적화 기법이지만, 클래스나 객체 초기화 과정에서 발생하는 해로운 순환성을 해소하기 위해서도 쓰인다. 

초기화 지연을 적용할때 최고의 지침은 ... '정말로 필요하지 않으면 하지 마라'
초기화 지연은 양남의 검이다. 
초기화 지연이 적용되 ㄴ필드 가운데 실제 초기화 되어야 하는 필드 비율, 초기화 비용, 그리고 필드의 실제 사용 빈도에 따라 실제 성능은 달라 질수 있다. 
즉 필드의 사용빈도가 낮고 초기화 비용이 높다면 쓸만한것이다. 

다중 스레드 환경에서 초기화 지연 기법을 구현하는건 더욱이 어렵다. 
이번 절에서는 다루는 모든 초기화 기법은 스레드 안정성을 보장한다. 
**대부분의 경우라면, 지연된 초기화를 쓰느니 일반 초기화를 하는 편이 낫다.** 

```
// 객체 필드를 초기화하는 일반적인 방법
private final FieldType filed = computeFieldValue();
```
초기화 순환성 문제 : Class A in its constructor creates instance of class B, class B creates instance of class C, and class C creates instance of class A.
초기화 순환성 문제를 해결을 위해서 초기화를 지연시키는 경우에는 동기화된 접근자를 사용하자! 

```
private FieldType field; 
// 동기화된 접근자를 통한 객체 필드 초기화 지연방법
synchronized FielType getField() {
	if(field == null){
		filed = computeFieldValue();
	}
	return field;
}
```

성능 문제 때문에 정적 필드의 초기화를 지연시키고 싶을때는 초기화 지연 담당 클래스(lazy initalization holder class)숙어를 적용하자. 요청 기반 초기화 담당 클래스(initalizae-on-demand holder class) 라고도 한다. 클래스는 실제 사용되는 순간 최기화 된다. 

```
/// 정적필드 초기화 지연 담당 클래스 숙어
private static class FieldHolder {
	static final FieldType field = computeFieldValue();
}
static FieldType getField() { return FieldHolder.field(); }
```

이 숙어가 장점은 getField()를 앞에서 처럼 동기화 메서드로 선어하지 않아도 된다는것이다. 따라서 초기화 지연시켜도 메서드 이용 비용은 전혀 증가하지 않는다. 

성능 문제 때문에 정적이 아닌 일반 객체 필드 초기화를 지연 시키고 싶다면 이중 검사(double- check) 숙어를 사용하자! 
초기화가 끝난 필드를 이용하기 위해 락을 걸어야 하는 비용을 없앨수 있다. 
숨은 아이디어는 필드의 값을 두번 검사하자! 먼저 락 없이 검사하고, 초기화가 되지 않은것 같으면, 락을 걸어서 검사한다.  두번째 검사에서 초기화가 되지 않았다고 판정되면 필드를 초기화 한다. 초기화 된 필드에는 락을 걸지 않으므로 반드시 volatile로 선언해야 한다. 

```
//이중 검사 패턴을 통해 객체 필드 초기화 지연 코드
prviate volatile FieldType field;
FieldType getField(){
	FieldType result = field; 
	if(result == null){ //첫번째 검사 without 락
		synchronized(this){
			result = field;
			if(result == null) { //두번째 검사 with 락
				field = result = computeFieldValue();
			}
		}
	}
	return result;
}
```
일단 코드가 난해해보인다. 지역변수 result를 쓴 이유가 명확하지 않다. 
엄밀하게 이야기 하면 반드시 필요한것은 아니지만 이미 초기화된 필드는 딱 한번만 읽도록 하는것이다. 
-> volatile로 선언됬기 때문에 메모리 상의 최근 값을 다시 한번 읽는다. 

자바 1.5이전에는 이중 검사 숙어를 안정적으로 사용할 수 없었다. volatile이 가진 의미가 이중 검사 패턴을 지원할 정도로 강력하지 않았기 때문에.... 
메모리 모델이 도입된 이후에는 문제가 해결 되었다. 

이중 검사 숙어에는 변종 가운데는 주의할게 두 가지 있다. 

여러번 초기화가 되어도 상관없는 객체 필드 초기화를 지연시키고 싶을때 ... 
두번째 검사를 없애버려도 된다. 
-> 동기화에서 검사를 할 필요가 없다. 여러번 새로 초기화하니까? 

이번 절에서 설명한 모든 초기화 기법은 객체 참조 필드 뿐만 아니라 기본 자료형에도 사용이 가능하다. 수치 기본 필드는 필드값을 null이 아니라 0으로 비교를 해야한다. (0은 수치 기본 필드의 기본값이다)

모든 스레드가 필드 값을 재계산하더라도 상관 없고 필드 자료형이 long이나 double 아닌 기본 자료형인 경우, 단일 검사 숙어에서 volatile 키워드는 빼도 된다. 
즉 int, boolean, float 등은 volatile 키워드를 빼도 된다. 

오약하자면, 대부분의 필드 초기화는 지연시키지 말자. 더 좋은 성능을 내거나, 초기화 순환성을 제거할 목적으로 필드 초기화를 지연시키고자 하는경우, 적절한 초기화 지연 기술을 쓰자. 
객체 필드에는 이중검사 숙어을 적용하고  정적 객체 필드에는 초기화 지연 담당 클래스 숙어를 적용하자. 
여러번 초기화해도 문제가 없는 필드라면, 단일 검사 숙어도 고려하자.

### 72. 스레드 스케줄러에 의존하지 마라

실행할 스레드가 많을때, 어떤 스레드를 얼마나 오랫동안 실행할지 결정하는 것은 스레드 스케줄러이다. 정확성을 보장하거나 성능을 높이기 위해 스레드 스케줄러에 의존하는 프로그램은 이식성이 떨어진다. 

안정적이고, 즉각 반응하고 이식성이 좋은 프로그램을 만드는 방법은, 실행 가능 스레드의 평균 수가 프로세서 수보다 너무 많아지지 않도록 하는 것이다. 이렇게 하면, 스레드 스케줄러가 하는 것은 그저 실행 가능 스레드들을 계속해서 실행하는 것 뿐이다. 따라서 스레드를 스케줄링하는 정책이 크게 달라지더라도 동작은 별반 다르지 않게 된다. 실행 가능한 스레드의 수는 모든 스레드의 수와는 다르다. 

실행 가능 스레드의 수를 일정 수준으로 낮추는 기법의 핵심은 각 스레드가 필요한 일을 하고 나서 다음에 할일을 기다리게 만드는 것이다. **스레드는 필요한 일을 하지 않을때는 실행중 이어서는 안된다.**

실행자 프레임 워크 관점에서 보자면,... 
스레드 풀의 크기는 적절히하고, 태스크의 크기는 적당히 작게, 그리고 서로 독립적으로 만들어라. 

그리고 스레드는 바쁘게 대기(busy-wait)하면 안된다. 즉, 무엇인가 발생하기 기다리면서 공유 객체를 계속 검사하면 곤란하다는 것이다.
프로세스에 부담을 주고, 다른 스레드가 할 일의 양을 줄인다. 

```
private int count; 
public void await(){
	while(true) {
		synchronized(this) {
			if(count == 0) return;
		}
	}
}
```
다른 스레드에 비해 충분한 CPU시간을 받지 못하는 스레드가 있는 탓에 겨우겨우 동작하는 프로그램을 만나더라도 **Thread.yield를 호출해서 문제를 해결할려고는 하지 말자!
이식성이 없다.**
어떤 JVM에서는 성능이 좋아지고 나빠지고 효과가 없을 수 있다. 
비슷하게 스레드 우선순위를 변경하는 것이 있는데, **스레드 우선순위는 자바 플랫폼에서 가장 이식성이 낮은 부분 가운데 하나다**. 

요약하자면, 프로그램의 정확성을 스레드 스케줄러에 의존하지 말자. 그런 프로그램은 안정적이지도 않고 이식성도 보장되지 않는다. Thread.yield나 스레드 우선순위에도 의존하지 말자. 

### 73. 스레드 그룹은 피하라

스레드 시스템이 기본적으로 제공하는 추상화 단위(abstraction) 가운데는 스레드, 락, 모니터(monitor) 이외에도 스레드 그룹(thread group)이라는것이 있다.  
스레드 그룹은 원래 애플릿을 격리시켜 보안 문제를 피하고자 고안도니것이지만, 그 목적에는 실패 했고 그 중용성도 희미해졌다. 

그럼 스데르 그룹이 제공하는 기능은 ?
특정한 Thread 기본 연산을 여러 스레드에 동싱 적용 -> 대다수는 폐기되었고 거의 쓰이지 않는다. 

역설적이게, ThreadGroup API의 스레드 안정성은 떨어진다. 
.... 
**스레드 그룹은 이제 폐기된 추상화 단위다!**

자바 1.5버전 전에는 ThreadGroup.uncaughtException 메서드가 있었는데 어떤 스레드가 무점검 예외(uncaught exeception)을 던지면 제어권을 가져오는 유일한 수단이 있었다. 
스택 추적 정보를 응용프로그램 로그에 보내는데 유용하다. 
하지만 1.5이후에는  Thread의 setUncaughtExceptionHandler가 같은 기능을 제공한다. 

요약하자면, 스레드 그룹에는 쓸만한 기능이 별로 없고, 쓸만하면 결함이 있다. 스레드 그룹은 무시하자. 스레드를 논리적인 그룹으로 나누는 클래스를 만들어야 한다면, 스레드 풀 실행자를 이용하는 것이 바람직 할 것이다. 





































































> Written with [StackEdit](https://stackedit.io/).
