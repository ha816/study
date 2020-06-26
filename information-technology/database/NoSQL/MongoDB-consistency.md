# Consistency

전통적인 DB 시스템에서 일관성이란 휘발성 저장장치와 비휘발성 저장장치간의 데이터의 일관성 유지를 말합니다. Strict Consistency는 가장 기본사항으로 기존 RDBMS에서 사용되어 왔습니다. 하지만 분산화된 상태에서 strict consistency를 만족하는 것은 매우 어려운 일이며, 또한 DB의 속도를 느리게 만듭니다. 이런 문제를 개선하기 위해 나온것이 Causal Consistency입니다.

## Causal Consistency(Sequential Consistency)

Causal consistency는 strict consistency보다 한단계 완화된 consistency입니다. 
Causal consistency를 사용하기 위해선 아래 두 가지 물리적 요구사항을 지켜야 합니다.

-   Ordering 유지
-   Atomic read/write operation

사실 제일 상식적인 consistency이다. 공유 데이터를 접근할 때, single operation queue가 있어 그 큐에는 write operation과 read operation들이 쌓여있다. 그리고 이 operation들은 in-order로 처리되야 한다. 그리고 operation이 수행되고 있으면 single operation queue에 들어있는 다른 operation들은 대기해야 한다.

이는 strict consistency보다는 빠른 성능을 보인다. (Network 속도에 클럭이 병목될 정도는 아니다.)  **하지만 operation들이 sequential하게 수행되어야 하므로 성능이 만족할정도로 나오지 않는다.**  (previous operation이 완료 될 때까지 기다려야 하므로 느리다.)


Strict Consistency
: 가장 엄격한 일관성으로 모든 읽기가 가장 최근에 기록된 결과를 읽어야 한다.

Causal Consistency
: 타임스탬프보다 이벤트 순서에 일관성을 부여하여, 관련성이 있는 모든 쓰기는 순서대로 읽혀야 한다.

 

# Causal Consistency

만약 한 연산이 논리적으로 앞전 연산에 의지하고 있다면, 두 연산자 간의 causal한 관계가 있다고 합니다. 예를 들어, 한 쓰기 연산이 모든 문서를 특정 컨디션에 지우고 있다고 하면, 특정 컨디션을 위한 읽기 연산 때문에 둘의 관계는 causal 관계를 이루고 있씁니다.

causally consistent sessions으로, MongoDB는 그들의 causal 관계십에 기반한 순서대로 causal 연산을 수행합니다. 그리고 클라이언트는 casual 관계들에서 일치성이 맞는지 관찰합니다. 

Causal Consistency을 제공하기 위해선, MongoDB 3.6에서 클라이언트 세션에 가능합니다. A causally consistent session은 `majority` 읽기 연산을 가진것과  `majority` 쓰기 연산의 연속들로 구성됩니다.  애플리케이션은 반드시 클라이언트 세션에서 오직 하나의 쓰레드에서 이런 연산이 수행되도록 해야합니다.
 

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTg4Mjg4NTkwNiwzODM2OTg0NzMsLTEzMT
M4NDM0ODEsLTg4NjkyMDczNSwtNTIwMTU2MjQ5LC0yOTI0OTQ0
OTcsMjEzOTE2NjYwNCwtNDUxNzc5OTA0LC0xOTU2ODI2NTkxLD
E2OTc2MzIzNDUsLTE3NDA3Mzg0NDBdfQ==
-->