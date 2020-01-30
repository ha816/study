# Overview

이 장에서는 MySQL의 동시성에 영향을 미치는 잠금(Lock)과 트랜잭션, 그리고 트랜잭션의 격리 수준(Isolation Level)을 공부한다.

Lock과 트랜잭션은 서로 비슷해 보이지만 사실 **잠금은 동시성을 제어하기 위한 기능**이고 **트랜잭션은 정합성을 보장하기 위한 기능**이다. 잠금은 여러 커넥션에서 동시에 동일한 자원(레코드나 테이블)을 요청할 경우 순서대로 한 시점에는 하나의 커넥션에서만 변경할 수 있도록 한다. 
격리수준은 하나의 트랜잭션 이나 여러 트랜잭션 간의 작업 내용을 어떻게 공유하고 차단할 것인지를 결정한다.

## MySQL Transaction

트랜잭션은 작업의 완전성을 보장한다고 말한다. 이 말은 작업의 일부만 적용되는 현상(partial update)가 없다는 말이다. 

MySQL에서 트랜잭션을 지원하는 스토리지 엔진은 대표적으로 InnoDB다. MyISAM은 지원하지 않는다.

### 주의사항 



<!--stackedit_data:
eyJoaXN0b3J5IjpbMTQzNjAyOTM0NCw5Njk3MDU5MTMsMjA0NT
Y0Nzg4OV19
-->