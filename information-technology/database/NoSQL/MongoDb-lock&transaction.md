# Lock

MongoDB 서버에서도 멀티 쓰레드의 동시 처리 중에 발생할 수 있는 쓰레드 간의 충돌 문제를 막기 위해서 데이터베이스와 컬렉션 그리고 문서들의 잠금을 사용한다. 여러 계층의 데이터베이스 오브젝트들에 다한 동시처리를 위해서 인텍션 락(interntion Lock)과 다중 레벨의 잠금(Multiple granularity locking)을 활용하고 있다.  또한 MongoDB 서버 차원에서 처리되는 잠금과 각 스토리지 엔진에서 처리되는 잠금으로 나우어 볼 수 있다. 

Shared Lock(S-Lock)
: 공유 잠금 또는 읽기 잠금이라고 불립니다. 현재 쓰레드가 참고하고 있는 데이터를 다른 쓰레드가 변경하지 못하도록 방법하는 것이 목적, 

Exclusive Lock

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5NjAwMjE5MTFdfQ==
-->