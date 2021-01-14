# ElasticSearch 모니터링

1장과 2장에서는 ES를 설치하는 방법과 기본 동작인 문서 색인, 조회, 검색, 분석에 대해 알아보았다. ES를 잘 사용하기 위해서는 ES의 상태를 모니터링하는 것이 필수다. 이번 장에서는 오픈 소스인 Head와 프로메테우스 그리고 X-Pack을 이용해서 클러스터를 모니터링 하는 법을 알아보고 각 모니터링 툴의 장단점도 함께 살펴보자.

# Head를 이용한 모니터링

Head는 클러스터의 상태를 한누에 살펴볼 수 있는 유용한 모니터링 도구이다. 클러스터의 여러정보를 웹 UI를 통해 확인할 수 잇다. 특히 Head의 가장 큰 장점 중 하나는 샤드 배치 정보를 시각적으로 확인할 수 있다는 것이다. 이를 통해 샤드 분배가 특정 노드에 치중되었다거나, 배치가 안된 샤드가 있는 등 샤드 분배와 관련된 문제가 발생했을때 유용하게 사용할 수 있다. 




> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMzczNjUzMDk0LC0yMDU5NTkxMDMwLC0xMz
EwNzA4OTc3LDczMDk5ODExNl19
-->