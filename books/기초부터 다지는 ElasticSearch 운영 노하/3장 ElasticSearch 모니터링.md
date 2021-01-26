# ElasticSearch 모니터링

1장과 2장에서는 ES를 설치하는 방법과 기본 동작인 문서 색인, 조회, 검색, 분석에 대해 알아보았다. ES를 잘 사용하기 위해서는 ES의 상태를 모니터링하는 것이 필수다. 이번 장에서는 오픈 소스인 Head와 프로메테우스 그리고 X-Pack을 이용해서 클러스터를 모니터링 하는 법을 알아보고 각 모니터링 툴의 장단점도 함께 살펴보자.

# Head를 이용한 모니터링

Head는 클러스터의 상태를 한누에 살펴볼 수 있는 유용한 모니터링 도구이다. 클러스터의 여러정보를 웹 UI를 통해 확인할 수 잇다. 특히 Head의 가장 큰 장점 중 하나는 샤드 배치 정보를 시각적으로 확인할 수 있다는 것이다. 이를 통해 샤드 분배가 특정 노드에 치중되었다거나, 배치가 안된 샤드가 있는 등 샤드 분배와 관련된 문제가 발생했을때 유용하게 사용할 수 있다. 

git을 통해 소스 코드를 다운 받은 후 폴더를 열면 package.json 파일이 있다. 이 파일에는 npm(node package manager)이 Head를 설치하기 위해 필요한 패키지들이 나열되어 있다. 

먼저 npm을 설치하는 명령어는 아래와 같다. 
```
yum -y install npm
```

npm 설치 후 package.json 파일을 참조하여 필요한 패키지들을 설치한다.

```
npm install
```

node의 버전에 따라 경고 메세지가 노출되지만 실행에는 큰 영향이 없으니 무시해도 된다. 다음으로 npm을 실행해보자.

```
npm run start
```

Head를 실행하면 프로세스가 9100번을 통해 사용자의 접속을 처리할 수 있도록 대기 중 상태에 있다. 웹 브라우저를 통해 9100번 포트로 Head에 접속이 가능하다. 

웹 브라우저로 접속하면 먼저 overview 탭을 보여준다. 최상단에 실행중인 클러스터의 주소를 입력할 수 있는 영역이 있고, 여기에 클러스터 주소를 입력하면 된다. 

Head를 통해 클러스터의 노드에 접근하려면 클러스터에서 접근 허용 설정 작업을 해줘야 한다. 클러스터 노드에 있는 elasticsearch.yml 파일에 CORS 허용을 해주어야 한다. 

CORS는 Cross-Origin Resource Sharing의 약자로 웹 애플리케이션이 사용 중인 도메인이 아닌 외부 도메인에서의 리소스 호출 허용 여부를 결정하기 위해 사용되는 기술 중 하나이다. 

```
http.cors.enabled: true // CORS 설정 활성
http.cors.allow-origin: "*" // CORS 설정으로 접근할 있는 사이트를 지정. "*"은 모든 사이트에서 접근가능.
```

설정을 마치고 ES를 재시작하면 해당 ES 클러스터에 연결이 가능하다. 

Head에서 제공하는 다양한 기능이 있지만 자세한것은 홈페이지에서 찾아보도록 하자.

# 프로메테우스를 활용한 모니터링

ES 6.3버전부터 X-Pack의 배이직 라이센스를 기본으로 탑재하여 라이선스를 규칙적으로 갱신하지 않아도 모니터링 기능을 사용할 수 있다. 그 이전 버전의 경우 베이직 라이선스가 무료라 하더라도 1년에 한번 갱신해야 했기 때문에 클러스터의 노드 수가 많다면 모니터링 기능을 활용하기 어려운 것이 사실이었다. 게다가 대규모 클러스터라면 색인의 성능에도 영향을 줄 수 있다. 

프로메테우스(Prometheus)는 데이터를 시간의 흐름대로 저장할 수 있는 **시계열 데이터 베이스의 일종**이다. 데이터 베이스의 일종이지만 더 나아가 수집된 데이터를 바탕으로 임계치를 설정하고 경고 메세지를 받을수 있는 오픈소스 모니터링 시스템으로도 활용이 가능하다. 

![enter image description here](https://img1.daumcdn.net/thumb/R800x0/?scode=mtistory2&fname=https://t1.daumcdn.net/cfile/tistory/997CAA3F5A815A361C)

프로메테우스는 각종 메트릭을 저장하는 TSDB(Time Series Data Base)의 역할을 하는 Prometheus Server가 중추적 역할을 한다. 각종 지표들을 Exporters를 통해 가져올수도 있고 Pushgateway를 통해 입력할 수도 있다. 각 항목에 대해 임계치를 설정하여 AlertManager를 통해 경고 메세지를 받을 수도 있다.

이번 장에서는 Prometheus Server와 Exporter를 설정해보고 데이터를 Grafana로 시각화하는 과정을 다룰것이다.

사실 프로메테우스는 ES말고도 많은 시스템을 모니터링 할 수 있는 Exporter를 제공한다. 

## 프로메테우스 서버 설치

자 이제 Prometheus Server부터 설치해보자. 공식 홈페이지에서 본인의 환경에 맞는 바이너리를 선택해서 받는다. 다운로드한 후 /user/local/ 디렉터리로 압축 파일을 복사해서 압축을 풀면 아래와 같은 디렉터리가 생성된다. 

```
bin games java lib libexec 
share etc include jdk-11.0.1 lib64
sbin
prometheus-2.6.0.linux-amd64
```
prometheus-2.6.0.linux-amd64에 들어가면 아래와 같은 파일들을 추가로 볼 수 있다.

```
LICENSE, NOTICE, console_libraries, consoles, data, prometheus, prometheus.yml, promtool
```

이 중에 prometheus.yml 파일이 환경설정 파일이며, 다양한 항목을 설정할 수 있다. 

```
global:
... 

scrape_configs: // 1: 가져올 메트릭 항목
	- job_name: 'elasticsearch' // 2: jon 이름

	# metrics_path defaults to '/metrics'
	# scheme defaults to 'http'

	static_configs:
	- targets: ['prometheus-test002.domain.com:9108'] // 데이터를 가져올 장소(exporter)
```

scrape_configs는 Prometheus Server가 메트릭을 가져올 항목을 정의한다. 여러 개의 풀링 항목을 정의 할 수 있으며, 여기서는 job_name이 elasticsearch이고 prometheus-test002.domain.com에서 데이터를 가져오도록 정의했다. 자 이제 Prometheus Server를 실행해보자.

```
./prometheus --config.file=prometheus.yml
```

## Exporter 설치 및 실행

다음으로 Exporter를 실행해보자. ES를 위한 Exporter는 여러가지가 있는데 그 중 가장 많이 사용하는 elasticsearch_exporter를 사용해보자.

github에 elasticsearch_exporter를 검색하여 바이너리 파일을 다운 받는다. 이제 모니터링 하고자 하는 클러스터의 URL을 넣고 -es.all, -es.indices 옵션을 추가해서 실행하면 된다. 

```
./elasticsearch_exporter -es.uri http://elasticsearch.domain.com:9200 -es.all -es.indices level=info ts="" caller=main.go:95 msg="starting elasticsearch_exporter" addr=:9108
``` 

Exporter를 실행한 후에 메트릭을 잘 가져오는지 확인하려면 curl을 사용하면 된다. 
```
curl -s http://localhost:9108/metrics | more // 9108 Export에 질의하기 
```

여기까지 잘 동작이 되었다면 Prometheus Server는 자동으로 Exporter를 통해 메트릭을 수집하고 있을것이다. 

ES -> Exporter -> Prometheus Server

## Grafana 설치 및 실행

Grafana의 설치와 실행도 매우 간단하다. 

RPM 파일을 다운로드하고 rpm -ivh 명령을 통해서 설치하면 /etc/grafana 디렉터리가 생성된다.

grafana.ini 파일로 환경을 설정한다. 

```
[server]
#Protocoal (http, https, socket)
;protocol = http

# The ip address to bind to, empty will bind to all interfaces 
http_addr = 0.0.0.0

# The http port to use
http_port = 3000

```

Grafana가 외부 요청을 받기 위해 사용할 IP주소는 http_addr이다. 0.0.0.0으로 설정하면 내부에서의 호출과 외부에서의 호출을 모두 받을 수 있다. 기본값은 127.0.0.1이지만 127.0.0.1로 설정할 경우 서버의 외부에서는 호출이 불가능해지기 때문에 0.0.0.0으로 설정하는 것이 좋다.

설정이 끝나면 Grafana를 실행 하자. Grafana를 실행한 후 가장 먼저 데이터 소스를 설정해야 한다. 데이터 소스는 Grafana가 시각화할 데이터를 저장해 놓은 곳을 의미한다.

추가 가능한 여러 데이터 소스 중에서 Prometheus를 선택한다. 마지막으로 Exporter에서 제공하는 Grafana 대시보드를 추가해보자. import를 선택한다. 

import 화면에서 JSON 파일을 업로드해도 되고 Exporter에서 제공해주는 JSON 파일을 복사해서 붙여 넣어도 된다.

gitHub에서도 대시보드 JSON 파일이 위치한 곳을 찾을 수 있다.
// github.com/justwatchcom/

대시보드 설정이 완료되면 프로메테우스를 통해서 데이터를 볼 수 있다. 대시보드를 통해 전체 문서의 수, 인덱스들의 데이터 크기, 평균 힙 메모리 사용량등 클러스터의 상태를 알 수 있는 다양한 정보를 확인할 수 있다. 보다 자세한 내용은 7장에서 다루겠다. 

# X-Pack을 이용한 모니터링

X-Pack의 모니터링 기능은 베이직 라이선스로 활용할 수 있으며, 베이직 라이선스는 무료로 사용할 수 있다.

6.3 이전 버전은 1년에 한번 베이직 라이센스를 재활성화하기 위해 클러스터 노드를 전부 재시작해야 하지만, 관리하고 있는 클러스터와 노드의 수가 적다면 X-Pack 모니터링도 좋은 방안이다. 

X-Pack 모니터링을 사용하기 위해선 먼저 Kibana 설치가 필요하다. Kibana는 ElasticSearch에 저장된 로그를 검색하거나 그래프 등으로 시각화할때 활용하는 도구이다. 사실상 ElasticSearch의 웹 UI와 같은 역활을 하는 도구로 생각하면 된다. ES에 저장된 문서 조회, 간단한 검색 요청, 데이터 분석을 통한 시각화 작업 모두 Kibana에서 한다. Kibana를 통한 문서조회와 시각 작업등은 8장에서 보다 자세히 알아보겠다. 

이번 절에서는 설치와 모니터링 활용법을 살펴보자.

Kibana는 공식 홈페이지에서 다운로드할 수 있다. 

```
rpm -ivh ./kibana-6.4.2-x86_64.rpm
```

설치하면 /etc/kibana 디렉터리가 자동으로 생성되며 디렉터리 안에 kibana.yml이라는 환경 설정 파일이 들어 있다. 환경 설정 파일의 항목들 중 대부분은 그대로 사용해도 되지만 아래 두 항목은 반드시 환경에 맞춰서 바꾸어 주어야 한다. 

|항목  | 내용|
|--|--|
| server.host  | Kibana가 외부의 접근을 처리하기 위한 IP 주소를 지정한다. 외부에서 접근할 수 있도록 하려면 기본값인 127.0.0.1이 아닌 0.0.0.0으로 설정한다. |
|elasticsearch.url| Kibana가 접근해서 문서를 조회하거나 시각화하는 등의 작업을 하게될 ElasticSearch 클러스터의 주소를 입력해준다.|

사실 Kibana 7점대부터 elasticsearch.url 대신 elasticsearch.hosts 설정이 사용된다. 

kibana.yml 파일 변경하기
```
server.host: "0.0.0.0"
elasticsearch.url: "http://elasticsearchserver:9200"
```

netstat - nlp | grep 5601

이제 5601 포트로 웹을 열어보면 Kibana화면이 실행된다. 
Kibana를 설치하고 접속한 다음 왼쪽의 Monitoring 탭을 클릭하면 현제 모니터링 상태에 대한 안내 메세지가 나타난다. 

Turn on monitoring 버튼을 클릭하면 모니터링을 설정하고 있다는 메시지가 나타난 뒤 모니터링 화면이 나타난다. 

Monitoring에서는 클러스터의 Health 상태, 노드의 ㄱ







> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwMjk0MzA1OTUsLTE1ODQwNDE1MTcsLT
EzNzE3Mzc1NDQsLTI2MDgxNDkwMiwtMjAzOTc3NjA5MCwtMTc2
MzM5ODI3MCwxODExMTEzODgwLC0xMTkzMjU2ODU4LC04NzIxOD
M3NTksLTE3MDU2MTk4MTYsNDQxMDA3Njk4LC0xNjQ1MzMxOTgz
LDczNDI1MjE4NywxMDQxMzc3MDUsLTgzMzM3MjA2MiwtNzk2NT
EyNTA4LC05MTU1NTg3MTMsMTA5MDM1MjAyMCwxMzg2ODg5NDY1
LC0xMDcyODMyNzE4XX0=
-->