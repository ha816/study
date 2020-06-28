# Lock

MongoDB 서버에서도 멀티 쓰레드의 동시 처리 중에 발생할 수 있는 쓰레드 간의 충돌 문제를 막기 위해서 데이터베이스와 컬렉션 그리고 문서들의 잠금을 사용한다. 여러 계층의 데이터베이스 오브젝트들에 다한 동시처리를 위해서 인텍션 락(interntion Lock)과 다중 레벨의 잠금(Multiple granularity locking)을 활용하고 있다.  또한 MongoDB 서버 차원에서 처리되는 잠금과 각 스토리지 엔진에서 처리되는 잠금으로 나우어 볼 수 있다. 

WirtedTiger 스토리지 엔진을 도입하면서 데이터베이스와 컬렉션에 대한 공유 잠금과 배타적 잠금만으로는 동시성 처리를 

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTUxMTQxMjY5XX0=
-->