# JVM

JVM은 Java Virtual Machine의 약자로 운영체제와 애플리케이션 사이에 위치하면서 애플리케이션이 특정 플랫폼에 상관없이 독립적으로 실행되도록 한다. 그리고 JVM은 메모리 관리를 위해 GC를 수행한다. 


![enter image description here](http://coding-geek.com/wp-content/uploads/2015/04/jvm_overview.jpg)

Java 컴파일러(javac)가 소스 코드를 컴파일 하면 .class확장자를 가진 **바이트 코드**가 생성된다. 이 바이트코드를 JVM의 메모리 공간으로 가져오는 걸 **class loading(클래스 로딩)**이라 하며 이것을 클래스 로더(class loader)가 수행한다. 가져온 바이트 코드는 JVM이 멈추거나 클래스 로더가 종료되기 전까지 머무른다. 

## JVM 메모리 영역

JVM 메모리 영역은 Shared Memory영역과 Non-Shared Memory 영역으로 나뉜다.  JVM의 모든 스레드들은 Shared Memory의 메소드 영역(Method Area)과 힙 영역(Heap Area)을 공유한다. Non-Shared Memory 영역은 각 스레드가 가지는 고유하고 독립적인 메모리 영역이고 대표적으로 Stack 영역이 있다.

![enter image description here](http://brucehenry.github.io/blog/public/2018/02/07/JVM-Memory-Structure/JVM-Memory.png)

### PC Register(Per Thread, Non-Shared)
각 스레드는 프로그램 카운터 레지스터(program counter register)를 가진다.  프로그램 카운터 레지스터는 현재 실행 중인 명령어 주소를 저장한다.

### Native Method Stack(Per Thread, Non-Shared)
다른 언어로 쓰인 네이티브 코드를 위한 스택이다. 네이티브 코드는 JNI(Java Native Interafce)를 통해 호출되는데 말 그대로 네이티브 스택이기 때문에, 이 스택의 모든 작업은 OS에 완전히 종속적이다. 

### [Stack](https://coding-geek.com/wp-content/uploadsyaboong.github.io/java/20158/04/jvm_memory_overview.jpg)(Per Thread, Non-Shared)

각 스레드는 자신만의 고유한 스택 영역을 가진다. 스택은 다수의 프레임(Frame)으로 구성되는데 프레임은 쓰레드의 현 상태를 나타내는 데이터 구조들로 이루어져 있다. (데이터 구조: Operand Stack, Local variable array, Run-time constant pool reference) 

>**Operand Stack**
파라미터를 다루기 위한 바이트코드 명령어가 사용하는 스택이다. 호출한 메서드에 파라미터를 전달하거나 스택 최상단의 호출된 메서드의 결과를 얻기위해 사용된다. 
>**Local Variable Array**
현재 메서드 스코프안의 모든 지역 변수 저장하는 배열이다. 원시 타입, 참조, 반환 주소 등을 가진다. JVM은 메서드 호출시 파라미터 전달을 위해 로컬 변수들을 사용한다. 
>**Run-Time Constant Pool Reference**
현재 실행 중인 메서드를 가진 클래스의 상수 풀에 대한 참조를 말한다. JVM이 심블릭 참조를 번역하는데 사용한다. 

스택 변수의 생애주기는 스코프에 영향을 받는 데 특정 스코프가 종료되면 스코프 안에 선언된 변수들은 스택에서 제거된다. 클래스 변수(static)와 원시 타입의 변수를 제외한 변수가 스택에 속한다. 여기서 주의할점은 변수가 가리키는 실제 객체는 힙에 들어가 있다는 것을 주의해야 한다. 

### 메소드 영역(Shared; Method Area, Class Area)

클래스 로더가 컴파일된 바이트코드를 읽어 적재하는 공간이다. 클래스 별로 정보가 저장되기 때문에 Class Area라고도 불린다. 클래스 별 적재 정보는 아래와 같다.

-   class information (number of fields/methods, super class name, interfaces names, version, …)
-  static variables(class variables)
-   a runtime constant pool

클래스 파일은 고유의 constant pool을 가지고 JVM에 의해 실제 메모리에 올라가면 **runtime constant pool**이)가 로드 된다. 여기서 클래스 파일 constant pool의 구현체가 runtime constant pool이다.
 한 클래스 파일의 모든 심블릭 참조(**symbolic reference**)는 상수풀에 저장된다. 심블릭 참조란 문자열인데 실제 사용하려는 객체를 찾기위해 사용한다.

```
if (obj.getClass() == String.class) { // do something }
```
위의 코드를 컴파일하면 아래와 같은 바이트 코드가 생성된다. 
```
aload_1
invokevirtual   #21; //Method java/lang/Object.getClass:()Ljava/lang/Class;
ldc     #25; //class java/lang/String
if_acmpne       20
```
`idc` 명령은 심블릭하게 저장되어 있는 클래스를 참조한다(#25 == String클래스). JVM이 바이트 코드를 실행할때, 사용할 클래스를 식별하는데 심블릭 참조를 사용하여 실제 객체의 주소값을 반환한다. 여기서 객체의 주소값을 반환한다는 점에 주의하자. 실제 모든변수 중 참조 변수의 경우, 실제 객체는 Heap 영역에 저장된다. 변수에는 단지단지 로드되는 변수는 Heap에 있는 객체의 주소값만을 가진다. 

```
static int i = 1; //the value 1 is stored in the RunTime Constant Pool(PermGen section(Heap))
static Object o = new SomeObject()
```

**명세서에서는 Heap에 메서드 영역을 구현하도록 강제하지 않았다.** 자바 7 이전에는 PermGen이라는 영역에 메서드 영역이 있었다. PermGen은 힙처럼 JVM에 의해서 관리되는 공간으로 사용 공간의 제한이 있었다. 그러나 자바 8에서 메서드 영역은 MetaSpace라 불리는, 사용 공간을 동적으로동적으로(실행시간에) 할당하여 쓰는 메모리 공간에 있다. 

### Heap(Shared)

힙은 모든 쓰레드가 공유하는 공간이다. 모든 객체와 배열들이 힙에 생성된다. 힙은 동적으로 확장되거나 축소 될수 있### Heap영역

객체를 저장하기 위해 영역, new 연산자를 통하여 개체를 동적으로 생성한다.  객체를 할당할때 이용할 수 있는 메모리가 충분치 않으면 JVM은 가비지 컬렉션을 이용해 힙에서 메모리를 재사용하려고 한다. 그래도 충분한 메모리가 없으면 OutOfMemoryError가 발생하며 JVM이 종료된다. 
  
#### Heap's Generation

[힙 메모리 영역의 흐름](https://dzone.com/articles/understanding-the-java-memory-model-and-the-garbag)

![enter image description here](https://cdn.journaldev.com/wp-content/uploads/2014/05/Java-Memory-Model.png)

| generation name | explanation |
|--|--|
| Eden |객체가 처음 생겼을때 할당되는 메모리 영역 |
| Survivor | Minor GC의 수집대상에서 제외된 객체가 있는 영역으로 똑같이 분할된 S0과 S1으로 나누어져 있다. |
| Tenured(Old) | Survivor 공간에서 최대 나이 임계값에 다다른 객체가 이주하는 공간으로 낮은 빈도로 gc의 대상이 된다.|
| Perm(PermGen) | 일반적으로 String이나 상수 같이 불변 값이 저장된다. 자바 8에서 PermGen 대신 MetaSpace라는 Heap영역 외부에 메모리 공간을 사용한다.|

## 마치며 

![enter image description here](https://i.stack.imgur.com/4ySVX.png)

JVM이 동작하면, 각 클래스에 대한 정보를은 물리 메모리에 위치할 MetasSpace에 있는 Method Area에 로드한다. Method Area는 각 클래스에 대한 메타 정보와 런타임 컨스탄트 풀을 가진다. 쓰레드는 고유의 메모리 영역인 ProgramCounter, Stack, Native Stack으로 구성된다. Stack은 다시 Frame들로 구성된다. 라는 새로운 영역으로 변경되었다.|

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNzk2NzYzMTQsLTk3NjYwNzg4Nyw1Mjg5OT
AwMDQsLTEyNDYwMDg3MzMsLTM0NjkwMzQ0OCwxMDc4Njc0NzMw
LC02NTgyNDAxODcsNzY2MjI1NDQ4LDE5NjM1MjczMDYsLTE0Mj
Q4NzI1ODQsLTE3NDg2NDc0NjZdfQ==
-->