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

WiredTiget 스토리지 엔진은 다른 DBMS와 동일하게 B-Tree 구조의 데이터 파일과 서버 클래시로 부터 데이터를 복구하기 위한 저널 로그(WAL, Write Ahead Log)를 가지고 있습니다. 








> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTg0ODQxNDIyMCwtNTkzNDcxODQxLC03Nj
QxNTA5MDYsLTExMzc3MTgwMjAsMTM3MzM1ODk3MiwtMTM3NDUx
Njk4N119
-->