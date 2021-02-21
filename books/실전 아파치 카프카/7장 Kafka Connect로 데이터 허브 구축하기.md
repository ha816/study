# Overview

카프카의 전형적인 사례 중이 하나인 데이터 허브 아키텍처를 응요한 예를
알아보는 장이다. 

# Kafka Connect

Kafka Connect는 아파치 카프카에 포함된 프레임워크로 카프카와 다른 시스템과의 데이터 연계에 사용한다. 카프카에 데이터를 넣거나, 카프카에서 데이터를 추출하는 과정을 간단히 하기 위해 만들어졌다. 

카프카는 특성상 다양한 시스템과 연계 하기 때문에 Connect에서는 다른 시스템과 연결하는 부분을 커넥터라는 플로그앤 형태로 제공한다. 

Kafka Connect에서는 카프카에 데이터를 넣는 프로듀서 쪽 커넥터를 **Source**라고 부르고, 카프카에서 데이터를 출력하는 컨슈머 쪽 커넥터를 Sink(싱크)라고 한다. 

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTYxNjI3NDA3OSwtNzQ4NjExMTNdfQ==
-->