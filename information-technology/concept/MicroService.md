# Micro Service

서비스 지향 아키텍처(SOA, Service Oriented Architecture)에 이어 점점 더 많은 인기를 끌고 있는 아키텍처 패턴이다. 데브옵스(DovOps)와 클라우드 진영에서 많은 각광을 받고 있다. 

DevOps
: 소프트웨어 개발(Development) + 운영(Operations)의 합성어로, 소프트웨어 개발자와 정보기술 전문가 간의 소통, 협업 및 통합을 강조하는 개발 환경이나 문화를 말한다. 

책에서는 마이크로 서비스는 아래와 같이 정의한다.
```
마이크로 서비스는 자율적이고, 자기 완비적(self-contained)이고 느슨하게 결합된 비즈니스 기능을 모다 전체 시스템을 만드는 아키텍처 스타일이다.
```

## Dock Container와 MicroService

마이크로 서비스의 관점에서 서비스를 컨테이너화해서 배포할 수 있다면 이상적이다. 컨테이너화(containerization)을 하면 하부의 인프라스트럭처를 포함하므로 더 자기 완비적(self-contained)하고 자율적이게 된다. 따라서 마이크로 서비스가 특정 클라우드 환경에 종속되지 않고 중립성을 유지할 수 있다. 

## Containerization(운영체제 가상화; operating system virtualization)

Containerization 컨테이너화 기술은 
컨테이너는 운영체제 위에서 폐쇠적인 사적 공간을 제공한다. 중요한 점은 컨테이너를 사용하면 운영체제의 커널이 독립적인 가상 공간을 제공한다는 점이다. 이런 가상 공간을 컨테이너 또는 가상엔진이라 부른다. 


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI1NDY4MDE0Nyw3NDE0MTI0NzNdfQ==
-->