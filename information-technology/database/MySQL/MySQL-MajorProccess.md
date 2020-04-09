# Overview

MySQL 실행 계획 성능에 큰 영향을 미치는 작업 단위에 대해 더 자세히 알아 보자. 
풀 테이블 스캔을 제외한 나머지는 모두 스토리지 엔진이 아니라 MySQL 엔진에서 처리되는 내용임을 명심하자. 또한 MySQL 엔진에서 부가적으로 처리하는 작업은 대부분 성능에 큰 영향을 미치는데, 안타깝게도 모두 쿼리의 성능을 저하 시킨다. 

스토리지 엔진에서 읽은 레코드를 MySQL엔진이 아무런 가공 없이 반환하면 최상의 성능을 보장하겠지만, 우리가 원하는 결과는 대부분 그렇지 않다. MySQL엔진에서 처리하는 시간이 오래걸리는 작업의 원리를 안다면 쿼리 튜닝에 큰 도움이 될것이다. 

# 풀 테이블 스캔(full table scan)

인덱스를 사용하지 않고 테이블 전체를 읽어 작업을 처리한다. 풀 테이블 스캔은 아래와 같은 조건에서 주로 풀테이블 스캔을 선택한다.

* 테이블의 레코드가 원채 적어서 풀 테이블 스캔이 더 빠른 경우
* WHERE 절이나 ON 절에서 인덱스를 이용할 수 있는 적절한 조건이 없는 경우
* 인덱스 래이진 스캔을 사용할 수 있는 쿼리라 하더라도 옵티마이저가 판단한 조건 일치 레코드 건수가 너무 많은 경우(통계 정보기준)
* max_seeks_for_key 변수를 특정 값(N)으로 설정하면 인덱스의 기수성(Cardinality)나 선택도(selectivity)를 무시하고, 최대 N건만 읽으면 된다고 판단한다. 이 값이 작으면 인덱스를 더 사용하도록 유도할 수 있다.

일반적으로 테이블 전체 크기는 인덱스보다 훨씬 크기 때문에 풀 테이블 스캔은 상당히 많은 디스크 읽기가 필요하다. 

# ORDER BY 처리(Using filesort)

# Distinct 처리

# 임시 테이블(Using temporary)

# 테이블 조인(table join)
> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIzMTcyMjA3NCwyODY2MDc5NTEsLTE4MD
g5NDExNjksMTg0MTk1NzYxMSwxMTA5NDU5MjQwLC02MTQzNzU5
ODgsLTI4MjQxMDM1XX0=
-->