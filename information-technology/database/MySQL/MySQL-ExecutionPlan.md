# Overview

DBMS에서는 요청이 들어온 쿼리를 항상 같은 방법으로 똑같은 결과를 내는 것이 아니다. 아주 많은 방법이 있지만 그중에서 어떤 방법이 최적이고 최소의 비용으로 결과를 낼지 결정해야 한다. 이를 위해 DBMS는 테이블의 데이터가 어떤 분포로 저장되어 있는지 통계 정보를 참조하고, 그러한 기본 데이터를 비교해 최적의 실행 계획을 수립한다. 이 작업을 DBMS에서는 옵티마이저가 담당한다. 

MySQL에서는 EXPLAIN 명령을 통해 실행 계획을 확인이 가능하다. 이 장에서는 실행 계획에 표시되는 내용이 무엇인지, MySQL 서버가 내부적을


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMzgxOTYxNzE0XX0=
-->