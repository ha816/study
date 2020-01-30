# MySQL Transaction&Lock

이 장에서는 MySQL의 동시성에 영향을 미치는 잠금(Lock)과 트랜잭션, 그리고 트랜잭션의 격리 수준(Isolation Level)을 공부한다.
Lock과 트랜잭션은 서로 비슷해 보이지만 사실 **잠금은 동시성을 제어하기 위한 기능**이고 **트랜잭션은 정합성을 보장하기 위한 기능**이다.

# Transaction

트랜잭션은 작업의 완전성을 보장한다고 말한다. 이 말은 작업의 일부만 적용되는 현상(partial update)가 없다는 말이다. 


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQxNDY1OTA2Niw5Njk3MDU5MTMsMjA0NT
Y0Nzg4OV19
-->