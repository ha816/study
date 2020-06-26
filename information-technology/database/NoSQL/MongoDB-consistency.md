# Consistency

전통적인 DB 시스템에서 일관성이란 휘발성 저장장치와 비휘발성 저장장치간의 데이터의 일관성 유지를 말한다. 크게 Strict Consistency와 Causal Consistency로 나뉘는데, Strict Consistency는 가장 기본사항으로 기존 RDBMS에서 사용되어 왔다. 하지만 분산화된 상태에서 strict consistency를 만족하는 것은 매우 어려운 일이며, 또한 DB의 속도를 느리게 만든다. 이런 문제를 해결하기 위해 나온 것인 Causal Consistency이며, 분산 및 병렬처리 환경에서 발생하는 일관성을 인과적 관계로  구현한 기술이다. 


# Causal Consistency

만약 한 연산이 논리적으로 앞전 연산에 의지하고 있다면, 두 연산자 간의 causal한 관계가 있다고 합니다. 예를 들어, 한 쓰기 연산이 모든 문서를 특정 컨디션에 지우고 있다고 하면, 특정 컨디션을 위한 읽기 연산 때문에 둘의 관계는 causal 관계를 이루고 있씁니다.

causally consistent sessions으로, MongoDB는 그들의 causal 관계십에 기반한 순서대로 causal 연산을 수행합니다. 그리고 클라이언트는 casual 관계들에서 일치성이 맞는지 관찰합니다. 

Causal Consistency을 제공하기 위해선, MongoDB 3.6에서 클라이언트 세션에 가능합니다. A causally consistent session은 `majority` 읽기 연산을 가진것과  `majority` 쓰기 연산의 연속들로 구성됩니다.  애플리케이션은 반드시 클라이언트 세션에서 오직 하나의 쓰레드에서 이런 연산이 수행되도록 해야합니다.
 

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTE3NjY2MzEyOSwtMTMxMzg0MzQ4MSwtOD
g2OTIwNzM1LC01MjAxNTYyNDksLTI5MjQ5NDQ5NywyMTM5MTY2
NjA0LC00NTE3Nzk5MDQsLTE5NTY4MjY1OTEsMTY5NzYzMjM0NS
wtMTc0MDczODQ0MF19
-->