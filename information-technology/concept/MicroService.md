# Micro Service

마이크로 서비스는 **독립적이고, 자기 완비적(self-contained)이며 느슨하게 결합된 비즈니스 기능을 모아 전체 시스템을 만드는 아키텍처 스타일**이다. 
서비스 지향 아키텍처(SOA, Service Oriented Architecture)에 이어 점점 더 많은 인기를 끌고 있는 아키텍처 패턴이다. 데브옵스(DovOps)와 클라우드 진영에서 많은 각광을 받고 있다. 

> DevOps
> 소프트웨어 개발(Development) + 운영(Operations)의 합성어로, 소프트웨어 개발자와 정보기술 전문가 간의 소통, 협업 및 통합을 강조하는 개발 환경이나 문화를 말한다. 
> 
## Containerization(컨테이너화 가상화; operating system virtualization)

컨테이너화(Containerization) 기술은 운영체제의 입장에서 본다면 사적인 가상공간을 제공하는 것이다. 그리고 이런 가상 공간을 컨테이너 또는 가상엔진이라 부른다. 
컨테이너는 뚜렷히 구분되는 소프트웨어 컴포넌트를 빌드하고, 탑재하고 실행할 수 있는 편리한 매커니즘이다. 일반적으로 컨테이너는 하나의 애플리케이션을 실행하는데 필수적인 모든 실행 바이너리 파일과 라이브러리를 함께 가진다. 

**컨테이너의 특성**
* 자기 완비적
	* 컨테이너는 실행에 필요한 바이너리와 의존하는 모든 라이브러리를 가진다.
* 경량성
	* 컨테이너는 크기가 작다. (가상 머신 이미지 크기에 비해서)
* 이식성
	* 컨테이너는 장비간 및 클라우드 서비스간 이식이 가능하다.
* 버전관리
	* 컨테이너는 버전관리를 지원한다. 아카이브 파일을 관리하듯 컨테이너도 버전 관리되는 산출물을 만들 수 있다.
* 재사용성
	* 컨테이너 이미지는 재사용이 가능하다. 
* 불변 컨테이너
	* 컨테이너는 생성되고 폐기 될뿐 수정되지 않는다는 것이 불변 컨테이너다. 

## 가상 머신과 컨테이너의 차이 

언뜻 보면 가상화와 컨테이너화는 정확하게 동일한 특징을 갖고 있는 것으로 보인다. 하지만 컨테이너와 가상화는 같지 않다. **가상머신과 컨테이너는 가상화 분야에서 서로 다른 문제를 해결하는 다른 기법이다.**

![enter image description here](https://mapr.com/blog/top-technology-trends-machine-learning-event-driven-microservices-dataops-and-cloud-to-edge/assets/containers.png)

가상 머신은 컨테이너에 비해 훨씬 더 로우 레벨에서 동작한다. 가상머신은 CPU, 메인보드, 메모리 등과 같은 하드웨어를 가상화한다. 일반적으로 게스트 OS라 불리는 별도의 운영체제를 가진 고립된 유닛이다. 가상 머신은 운영체제 전체를 복제해서 가상 머신위에서 실행하는데, 이때 호스트 운영체제 환경에 전혀 의존하지 않는다. 가상 머신이 운영체제 전체를 내장하기 때문에 본질적으로 무겁다. 

컨테이너는 전체 하드웨어나 운영체제를 가상화 하지 않는다. 컨테이너는 호스트 커널 및 운영체에 일부를 공유한다. 컨테이너에는 게스트 OS 라는 개념이 있다. 컨테이너는 호스트 운영체제 위에서 고립된 실행 환경을 제공한다. 그래서 가볍고 빠르다. 컨테이너 내부에서 실행되는 프로세스들은 동일한 장비상의 다른 컨테이너에서 실행되는 프로세스들과 완전히 독립적이다. 하지만 동일한 호스트에 있는 컨테이너들이 운영체제 를 공유함으로 제약 사항도 있다. 예를 들어 컨테이너 내부에서는 IP 테이블 방화벽 규칙 같은것을 설정 할 수 없다. 

## MicroService & Container

마이크로 서비스와 컨테이너는 직접적으로 아무런 관련이 없다. 마이크로 서비스는 컨테이너 없이도 수행될 수 있으며, 컨테이너 역시 일체형 애플리케이션을 실행할 수 있다.  하지만 둘은 매우 궁합이 잘 맞는다.

![Image result for docker container](https://i0.wp.com/www.docker.com/blog/wp-content/uploads/011f3ef6-d824-4d43-8b2c-36dab8eaaa72-1.jpg?fit=650%2C530&ssl=1)

위의 그림은 동일한 장비 위에서 실행되는 세 개의 마이크로 서비스를 보여준다. 컨테이너는 같은 운영체제를 공유하지만 실행환경은 독립적이다. 

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTExODYzODE1MDgsLTkyNDY3MjUsMTM4NT
c3NDM3NiwtMTc4NDU0NTQwNCwxMjUwMzQwMjQxLC04ODkwMTE1
NzAsLTE0ODE0NjYwNDMsMTgxNjI4NzI3LC0xMTg2Nzg5MzEzLD
IzMDU4ODUzNiw1NjMwNTY1ODEsOTEwNzU4MTUyLDEwMDk5NDU0
MDgsMTk1MDY1ODU3MCwzMjE1MzA0ODIsNzQxNDEyNDczXX0=
-->