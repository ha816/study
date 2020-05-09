# Overview

엘라스틱 서치는 분산 시스템이 지원해야 하는 고가용성(High Availability)을 제공하기 위해 내부적으로 Translog라는 특수한 형태의 파이르 유지하고 관리하고 있다. 장애 복구를 위한 백업 데이터 및 데이터 유실 방지를 위한 저장소로써 Translog를 적극 활용하고 있다. 

# Translog의 동작순서

샤드에 어떤 변경 사항이 생길경우 Translog 파일에 먼저 해당 내역을 기록한 후 내부에 존재하는 루씬 인덱스로 데이터를 전달한다. 루씬으로 전달된 데이터는 인메모리 버포로 저장되고 주기적으로 처리되어 결과적으로 세그먼트가 된다. 

엘라스틱서치에서 기본적으로 1초에 한번씩 Refresh 작업이 수행되는데, 이를 통해 추가된 세그먼트의 내용을 읽을 수 있게 되고 검색에 사용된다. 하지만 Refresh 작업이 일어나더라도 Translog 파일에 기록된 내용은 삭제되지 않고 계속 유지된다. 이처럼 **Translog는 일어나는 모든 변경사항을 담고 있는 특수한 형태의 로그이다.** 이러한 특성을 이용해서 엘라스틱 서치는 Translog의 내역을 바탕으로 장애 발생 시 복구 작업을 수행할 수 있다. 

하지만 Translog 파일에 계쏙 로그가 누적될 수 는 없다. 특정 시점이 되면 불필요한 과거 로그는 삭제도


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTk5NTIwNjk1MCwxNTExNTA4ODc0XX0=
-->