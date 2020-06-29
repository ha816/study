# Overview

스토리지 엔진은 사용자의 데이터를 디스크와 메모리에 저장하고 읽어오는 역할을 합니다. 아래 그림은 MongoDB에 있는 스토리지 엔진의 특징을 정리해 둔 그림입니다.

![Building Applications with MongoDB's Pluggable Storage Engines ...](https://webassets.mongodb.com/_com_assets/cms/StorageEngineArchIMG2-ju0tb22fup.png)

MySQL과 비슷하게 MongoDB도 다양한 스토리지 엔진을 사용할 수 있도록 스토리지 엔진이 플러그인 형태로 제공됩니다. MongoDB 스토리지 엔진은 하나의 인스턴스에서 하나의 스토리지 엔진만 사용이 가능합니다.

> MMAPv1 스토리지
> MongoDB가 처음 출시되었을때 부터 사용되던 스토리지 엔진. 
> 대부분 낮은 버전의 MongoDB의 문제점은 MMAPv1 엔진 자체가 가지고 있던 문제점
> MongoDB 4.2 버전부터 MMAPv1 엔진은 삭제되었습니다.

# WiredTiger

MongoDB의 디폴트 스토리지 엔진. 

WiredTiget 스토리지 엔진은 내부적인 잠금 경합 최소화(Lock-free Algorithm)을 위해서 하자드 포인터(Hazard-Pointer)나 스킵 리스트(Skip-List)와 같은 많은 신기술을 채택하였습니다. 

추가적으로 잠금 없는 데이터 읽기(Non-blocking consistent read)를 위한 MVCC 기술과 데이터 파일 압축 그리고 암호화 기능을 모두 제공합니다. 

## WiredTiger 내부 작동 방식

![enter image description here](https://image.slidesharecdn.com/mongodb-wiredtiger-webinar-150709200625-lva1-app6892/95/a-technical-introduction-to-wiredtiger-11-638.jpg?cb=1436472726)


### Cache(공유캐시)

WT 스토리지 엔진에서 사용자의 쿼리는 공유 캐시를 커치지 않고 처리할 수 없습니다. 가끔은 하나의 쿼리를 위해 수천에서 수만번의 캐시 데이터 페이지를 참조해야할 수도 있기 때문에 공유 캐시의 

WiredTiget 스토리지 엔진은 다른 DBMS와 동일하게 B-Tree 구조의 데이터 파일과 서버 장애 발생시 데이터를 복구하기 위한 저널 로그(WAL, Write Ahead Log, Logging)를 가지고 있습니다. 

기본적으로 MongoDB는 단일 문서 단위에 Transactions을 보장합니다. 
만약 사용자가 특정 문서를 변경하면, WT가 트랜잭션을 시작하고 커서를 이용해서 원하는 다큐먼트의 내용을 변경합니다. 변경 내용은 먼저 캐시에 적용되는데, 디스크에 기록되기 전에 변경 내용을 저널 로그에 기록한 다음 사용자에게 작업 처리 결과를 리턴합니다. 

이런 식으로 공유 캐시가 어느 정도 쌓이면 WT는 체크포인트를 발생시켜서 공유 캐시의 더티 페이지들을 모아 디스크에 기록합니다. 이때 메모리 상의 더티 페이지는 디스크에 기롭하기 전 원본 데이터와 변경된 정보의 병합)을 거쳐야하는데, 이를 WT의 Reconciliation 모듈이 처리합니다. 


사용자가 쿼리를 실행하면 블록 매니저(Block Manager)를 통해서 필요한 데이터 블록을 디스크에서 읽어온 다음 공유 캐시에 적재 하고 처리합니다. 

사용자 요청 쿼리가 실행되면 블럭 매니저는 계속해서 새로운 데이터 페이지들을 공유 캐시로 읽어 들여야 하는데, 더 이상 데이터 페이지를 읽어 들일 공간이 없으면 사용자 쿼리를 수행할 수 없게 된다. 이런 상황을 피하기 위해서 WT는 Eviction 모듈을 사용하며, 이 모듈은 공유 캐시가 적절한 메모리 사용량을 유지하도록 공유 캐시에서 자주 사용되지 않는 데이터 페이지들을 제거하는 작업을 수행한다. 

만약 제거해야 하는 데이터 페이지가 더티 페이지라면, 리컨실리에이션을 수행하고 공유 캐시에서 제거합니다.

WT 스토리ㅣ지 엔진의 데이터 블록은 모두 가변사이즈입니다. 






공유캐시(Cache)

Page read/write(I/O)

Block Management(Eviction; 퇴거, reconciliation; 친해지기)

트랜잭션이 특정 문서에 적용되면, 문서의 변경점이 logging과정을 걸쳐서 저장됩니다.


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjU2ODgwNDA1LC02MDk3MTIxMjEsLTE5OT
Y0MTA5NDQsODA4NDEyNjQ0LC0xNTUyNTI3OTAwLC04ODIwMDM5
MiwtMTUzMTk5ODk2LDE4NDg0MTQyMjAsLTU5MzQ3MTg0MSwtNz
Y0MTUwOTA2LC0xMTM3NzE4MDIwLDEzNzMzNTg5NzIsLTEzNzQ1
MTY5ODddfQ==
-->