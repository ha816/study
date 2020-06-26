# Consistency

전통적인 DB 시스템에서 일관성이란 휘발성 저장장치와 비휘발성 저장장치간의 데이터의 일관성 유지를 말합니다. Strict Consistency는 가장 기본사항으로 기존 RDBMS에서 사용되어 왔습니다. 하지만 분산화된 상태에서 strict consistency를 만족하는 것은 매우 어려운 일이며, 또한 DB의 속도를 느리게 만듭니다. 이런 문제를 개선하기 위해 나온것이 Causal Consistency입니다.

## Causal Consistency(Sequential Consistency)

Causal consistency는 strict consistency보다 한단계 완화된 consistency입니다. 
Causal consistency를 사용하기 위해선 아래 두 가지 물리적 요구사항을 지켜야 합니다.

-   Ordering 유지
-   Atomic read/write operation

공유 데이터를 접근할 때, single operation queue가 있어 그 큐에는 write operation과 read operation들이 쌓여있다. 그리고 이 operation들은 in-order로 처리되야 한다. 그리고 operation이 수행되고 있으면 single operation queue에 들어있는 다른 operation들은 대기해야 한다.

이는 strict consistency보다는 빠른 성능을 보인다. (Network 속도에 클럭이 병목될 정도는 아니다.)  **하지만 operation들이 sequential하게 수행되어야 하므로 성능이 만족할정도로 나오지 않는다.**  (previous operation이 완료 될 때까지 기다려야 하므로 느리다.)

causal consistency는 4가지 session guarantees로 재정의 될 수 있습니다. 

Read own Writes
: 만약 한 프로세스가 쓰기 작업을 수행 했다면,  나중에 같은 프로세스가 이 쓰기 작업의 결과를 관찰한다. 
If a process performs a write, the same process later observes the result of its write.

Monotonic Reads
: 한 프로세스에 의해서 관찰된 쓰기 작업들은 단조롭게 감소하는 것이 보장됩니다.

Writes Follow Reads
: 몇몇의 프로세스가 쓰기 작업에 의해 읽기 작업을 수행한고 또 다른 프로세스가 그 쓰기 작업의 결과를 관찰한다면, 그 프로세스도 읽기 작업을 관찰 할 수 있다.
if some process performs a read followed by a write, and another process observes the result of the write, then it can also observe the read (unless it has been overwritten).

Monotonic Writes
: 만약 몇몇의 프로세스가 한 작업을 수행한다면, 후속 쓰기 작업이 추가로 있는, 다른 프로세스가 그것들을 같은 순서로 관찰할 것입니다. 

# Causal Consistency

만약 한 연산이 논리적으로 앞전 연산에 의지하고 있다면, 두 연산자 간의 causal한 관계가 있다고 합니다. 예를 들어, 한 쓰기 연산이 모든 문서를 특정 컨디션에 지우고 있다고 하면, 특정 컨디션을 위한 읽기 연산 때문에 둘의 관계는 causal 관계를 이루고 있씁니다.

causally consistent sessions으로, MongoDB는 그들의 causal 관계십에 기반한 순서대로 causal 연산을 수행합니다. 그리고 클라이언트는 casual 관계들에서 일치성이 맞는지 관찰합니다. 

Causal Consistency을 제공하기 위해선, MongoDB 3.6에서 클라이언트 세션에 가능합니다. A causally consistent session은 `majority` 읽기 연산을 가진것과  `majority` 쓰기 연산의 연속들로 구성됩니다.  애플리케이션은 반드시 클라이언트 세션에서 오직 하나의 쓰레드에서 이런 연산이 수행되도록 해야합니다.
 

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTgyNDExMCwyMTEyMTM3Nzc1LDExNDY0NT
cyMjUsNTI2ODA3NzkwLC05OTc4NzU2MTcsMTAyNTEwNTQ0OSwt
ODgyODg1OTA2LDM4MzY5ODQ3MywtMTMxMzg0MzQ4MSwtODg2OT
IwNzM1LC01MjAxNTYyNDksLTI5MjQ5NDQ5NywyMTM5MTY2NjA0
LC00NTE3Nzk5MDQsLTE5NTY4MjY1OTEsMTY5NzYzMjM0NSwtMT
c0MDczODQ0MF19
-->