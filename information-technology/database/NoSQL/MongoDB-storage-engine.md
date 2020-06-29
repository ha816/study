# Overview

스토리지 엔진은 사용자의 데이터를 디스크와 메모리에 저장하고 읽어오는 역할을 합니다. 아래 그림은 MongoDB에 있는 스토리지 엔진의 특징을 정리해 둔 그림입니다.

![Building Applications with MongoDB's Pluggable Storage Engines ...](https://webassets.mongodb.com/_com_assets/cms/StorageEngineArchIMG2-ju0tb22fup.png)

MySQL과 비슷하게 MongoDB도 다양한 스토리지 엔진을 사용할 수 있도록 스토리지 엔진이 플러그인 형태로 제공됩니다. MongoDB 스토리지 엔진은 하나의 인스턴스에서 하나의 스토리지 엔진만 사용이 가능합니다.

> MMAPv1 스토리지 삭제
> MongoDB가 처음 출시되었을때 부터 사용되던 스토리지 엔진. 
> 대부분 낮은 버전의 MongoDB의 문제점은 MMAPv1 스토리지 자체가 가지고 있던 단점 때문입니다.

> 4.2 버전부터는 모든 관련 내용이 삭제되었습니다. 
# MMAPv1

MongoDB가 처음 출시되었을때 부터 사용되던 스토리지 엔진.

컬렉션 수준의 잠금을 지원합니다.
자체적으로 내장된 캐시 기능이 없어 운영체제의 캐시를 사용합니다. 이는 커널이 제공하는 시스템 콜을 거치게 되기 때문에 오버헤드가 상대적으로 큰 편입니다.


>MMAPv1 스토리지 삭제 
>MongoDB 4.0 버전부터는 MMAPv1 storage engine은 Deprecated 되었습니다.

Deprecated in MongoDB 4.0, the MMAPv1 storage engine and all its related flags and options are removed in MongoDB 4.2. If you are still using MMAPv1
Deprecated in MongoDB 4.0, the MMAPv1 storage engine and all its related flags and options are removed in MongoDB 4.2. If you are still using MMAPv1



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwODI4MjM0MjgsLTExMzc3MTgwMjAsMT
M3MzM1ODk3MiwtMTM3NDUxNjk4N119
-->