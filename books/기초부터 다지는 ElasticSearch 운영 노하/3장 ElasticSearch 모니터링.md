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

프로메테우스는 각종 메트릭을 저장하는 TSDB(Time Series Data Base)의 역할을 하는 Prometheus Server가 중추적 역할을 한다. 

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA0MTM3NzA1LC04MzMzNzIwNjIsLTc5Nj
UxMjUwOCwtOTE1NTU4NzEzLDEwOTAzNTIwMjAsMTM4Njg4OTQ2
NSwtMTA3MjgzMjcxOCwtMjA1OTU5MTAzMCwtMTMxMDcwODk3Ny
w3MzA5OTgxMTZdfQ==
-->