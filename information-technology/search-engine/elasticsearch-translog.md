# Overview

엘라스틱 서치는 분산 시스템이 지원해야 하는 고가용성(High Availability)을 제공하기 위해 내부적으로 Translog라는 특수한 형태의 파이르 유지하고 관리하고 있다. 장애 복구를 위한 백업 데이터 및 데이터 유실 방지를 위한 저장소로써 Translog를 적극 활용하고 있다. 

# Translog의 동작순서

샤드에 어떤 변경 사항이 생길경우 Translog 파일에 먼저 해당 내역을 기록한 후 내부에 존재하는 루씬 인덱스로 데이터를 전달한다. 루씬으로 전달된 데이터는 인메모리 버포로 저장되고 주기적으로 처리되어 결과적으로 세그먼트가 된다. 

엘라스틱서치에서


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTk4NjM3NDgzMCwxNTExNTA4ODc0XX0=
-->