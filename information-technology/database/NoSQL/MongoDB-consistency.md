# Causal Consistency

만약 한 연산이 논리적으로 앞전 연산에 의지하고 있다면, 두 연산자 간의 causal한 관계가 있다고 합니다. 예를 들어, 한 쓰기 연산이 모든 문서를 특정 컨디션에 지우고 있다고 하면, 특정 컨디션을 위한 읽기 연산 때문에 둘의 관계는 causal 관계를 이루고 있씁니다.

causally consistent sessionsd으로 


If an operation logically depends on a preceding operation, there is a causal relationship between the operations. For example, a write operation that deletes all documents based on a specified condition and a subsequent read operation that verifies the delete operation have a causal relationship.

With causally consistent sessions, MongoDB executes causal operations in an order that respect their causal relationships, and clients observe results that are consistent with the causal relationships.

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3NDA3Mzg0NDBdfQ==
-->