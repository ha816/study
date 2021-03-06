# 대상 독자

ElasticSearch 클러스터를 구축하고 운영하고자 하는 개발자, 데브옵스 엔지니어, 시스템 엔지니어를 대상으로 한다. 

# 책의 구성

크게 4개의 파트로 구성되어 있다. 

1. ElasticSearch를 시작하기 위한 과정을 다룬다.
	2. Rest
2. ElasticSearch 운영과 관련된 내용
3. ElasticSearch의 활용방안과 성능 수치를 확인하는 과정
4. ElasticSearch의 성능과 관련된 내용

# 1장 

## ElasticSearch란?

ElasticSearch는 루씬 기반의 오프 소스 검색 엔진이다. JSON 기반의 문서를 저장하고 검색할 수 있으며, 문서들의 데이터를 기반으로 분석작업도 가능하다.

| 항목 |특징|
|--|--|
| 준실시간 검색엔진 | 실시간이라고 할만큼 색인된 데이터가 빠르게 검색됨 |
| 클러스터구성| 한 대 이상의 노드를 클러스터로 구성하여 높은 수준의 안정성을 이루고 부하를 분산할 수 있음|
| 스키마리스 | 입력될 데이터에 대해 미리 스키마를 정의하지 않아도 동적으로 스키마 생성이 가능|
| Rest API| Rest API 기반의 쉬운 인터페이스로 비교적 진입장벽이 낮음|

ElasticSearch는 준실시간성 검색 엔진이다. 준실시간성 검색 엔진이란 실시간에 준하는 검색을 제공한다는 의미이다. JSON 문서가 입력되면 우선 ElasticSearch의 메모리에 저장되고 후에 샤드라는 데이터 저장공간에 저장되고 문서를 검색할 수 있게 된다.

준실시간성은 refresh_interval이라는 파라미터의 영향을 받는다. 해당 파라미터를 조절하면 특정 시간 이후에 검색할 수 있게 변경할 수 있다. 

클러스터란 여러 대의 컴퓨터 혹은 구성 요소들을 논리적으로 결합하여 전체리르 하나의 커뮤터 혹은 하나의 구성요소처럼 사용할 수 있게 해주는 기술이다. 

클러스터로 구성하면 높은 수준의 안정성을 얻고 부하를 분산 시킬 수 있다. 임의의 Node1d에서 문제가 생겨도 해당 노드만 제외하면 나머지 노드들로 클러스터의 구성을 유지할 수 있기 때문이다. 따라서 전체 클러스터가 멈춰 버리는 일은 없다. 

클러스터를 구성하는 모든 노드는 메시 형태로 요청을 주고 받는다. 이 때문에 어떤 노드에서도 색인/검색 작업을 처리할 수 있다. 

메시 형태(mesh) - 모든 구성요소가 서로 논리적으로 연결되어 있어서 다른 노드들과 직접적으로 통신할 수 있는 네트워크 형태를 말한다. ElasticSearch도 클러스터를 구성 하는 노드들 역시 Mesh형태 이기 때문에 각 노드가 직접 통신가능하고, 이를 통해 노드들 간의 부하분산를 수행할 수 있다.

Node2로 요청을 보내고 부하가 크면 Node3로 요청을 보낼 수도 있다. 

스키마리스는 문서를 입력하기도 전에 문서에 어떤 필드를 저장할 것인지 사전에 정의하지 않아도 된다는 의미이다. RDBMS와는 다르게 ElasticSearch를 미리 문서를 정의하지 않아도 된다. 또한 이미 입력된 정보와 최근에 입력한 정보사이에 필드가 추가되는 것과 같은 변경점에도 미리 스키마를 정의할 필요가 없다. ElasticSearch가 새로운 필드에 대한 스키마를 자동적으로 만든다.

ElasticSearch는 검색 엔진으로서 기능뿐만 아니라 입력된 문서들을 다양한 방식으로 분석할 수 있는 분석엔진으로도 사용이가능하다. ElasticStack이라는 로그 수집/분석 시스템이 전세계적으로 활용되고 있다. 

## 설치하기

ElasticSearch는 다양한 방법으로 설치할 수 있다. RPM(Redhat Package Manager)은 리눅스에서 제공하는 레드햇 기반의 패키지 설치매니저이다. 간단하게 설치가 가능하고 ElasticSearch 실행과 중지를 위한 스크립트도 만들어 주기 때문에 편리하다. 

설치 환경은 CeonOS7 이다. CentOS 7이상은 systemd를 사용하기 때문에 ElasticSearch 프로세스를 띄우기 위한 스크립트 역시 systemd 스크립트로 제공된다.  
RPM으로 설치한다면 rpm 명령 한줄로 ElasticSearch 설치 작업이 끝난다. 자동으로 설치되는 디렉터리들은 아래와 같다. 

|항목| 내용|
|--|--|
|/etc/elasticsearch  | ElasticSearch의 환결설정과 관련된 파일들이 모여 있는 디렉터리. elasticsearch.yml과 jvm.options 파일등이 있다.|
|/usr/share/elasticsearch/bin  | ElasticSearch실행을 위한 바이너리 파일이 모여 있는 디렉터리이다. systemd에서 실행시키는 bin/elasticsearch와 플러그인 설치 시 사용하는 bin/elasticsearch-plugin등이 있다.|
|/var/log/elasticsearch  | 디폴트로 설정되는 로그파일 저장 디렉터리. elasticsearch.yml 파일에 다른 디렉터리를 정한다면 이 디렉터리는 빈 디렉터리가 된다.|
|/var/lib/elasticsearch  |디폴트로 설정되는 힙 덤프 생성 디렉터리이다. ElasticSearch 프로세스가 비정상적으로 종료되거나 힙 덤프 생성을 요청했을때 이 디렉터리에 생성된다.|
|/var/run/elasticsearch  | 디폴트로 설정되는 프로세스 ID 저장 디렉터리이다. ElasticSearch 프로세스가 실행된 이후에 해당 프로세스의 ID가 elasticsearch.pid라는 파일로 저장된다.|

환경에 대한 자세한 내용은 4장에서 다룬다. 자 이제 ElasticSearch 프로세스를 실행시켜보자. 
```
sudo systemctl start elasticsearch //실행
sudo systemctl status // 상태확인
sudo systemctl start elasticsearch //중단
```

systemctl status 명령으로 프로세스 실행 여부를 확인했다면 curl 명령어로 잘 동작하는지 확인할 수 있다. 기본적으로 ElasticSearch는 9200번 포트로 실행된다. 

(https://eglowc.tistory.com/16)


그 밖에 RPM이 아닌 DEB, tar 확장자의 elasticsearch 파일로 어떻게 설치하는지 알 수 있다. 다음 장에서는 ElasticSearch의 기본동작을 알아보자.

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNjU0MTE2MjMsLTkyMTk0Nzc0OSwtMTY3MT
AyNDQzMCwtMTc2OTMxNjU4MCwtNzUzNDM1MTQ0LDEwMzgyNDU5
MjUsNTc3MjU0MDcsLTI3Mjg5ODA4NywtMTY5NjQ0MDQ4LDI2ND
MxMjI1MiwyMDU3Njg3NDAyXX0=
-->