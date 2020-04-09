# Overview

MySQL 실행 계획 성능에 큰 영향을 미치는 작업 단위에 대해 더 자세히 알아 보자. 
풀 테이블 스캔을 제외한 나머지는 모두 스토리지 엔진이 아니라 MySQL 엔진에서 처리되는 내용임을 명심하자. 또한 MySQL 엔진에서 부가적으로 처리하는 작업은 대부분 성능에 큰 영향을 미치는데, 안타깝게도 모두 쿼리의 성능을 저하 시킨다. 

스토리지 엔진에서 읽은 레코드를 MySQL엔진이 아무런 가공 없이 반환하면 최상의 성능을 보장하겠지만, 우리가 원하는 결과는 대부분 그렇지 않다. MySQL엔진에서 처리하는 시간이 오래걸리는 작업의 원리를 안다면 쿼리 튜닝에 큰 도움이 될것이다. 

# 풀 테이블 스캔(full table scan)

인덱스를 사용하지 않고 테이블 전체를 읽어 작업을 처리한다. 풀 테이블 스캔은  



* 테이블 레코드

# ORDER BY 처리(Using filesort)

# Distinct 처리

# 임시 테이블(Using temporary)

# 테이블 조인(table join)
> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4MDg5NDExNjksMTg0MTk1NzYxMSwxMT
A5NDU5MjQwLC02MTQzNzU5ODgsLTI4MjQxMDM1XX0=
-->