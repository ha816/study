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

Head를 통해 클러스터의 노드에 접근하려면 클러스터에서 접근 허용 설정 작업을 해주

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjk5MDI0OTY5LDEwOTAzNTIwMjAsMTM4Nj
g4OTQ2NSwtMTA3MjgzMjcxOCwtMjA1OTU5MTAzMCwtMTMxMDcw
ODk3Nyw3MzA5OTgxMTZdfQ==
-->