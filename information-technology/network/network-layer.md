# OSI 계층

**7계층 – 응용 계층(Application)**: 디핑 소스 비유를 확장하면 응용 계층은 가장 위에 있다. 사용자에게 보이는 부분이다. OSI 모형에서는 “최종 사용자에게 가장 가까운” 계층이다. 7층에서 작동하는 응용프로그램은 사용자와 직접적으로 상호작용한다. 구글 크롬(Google Chrome), 파이어폭스(Firefox), 사파리(Safari) 등 웹 브라우저와 스카이프(Skype), 아웃룩(Outlook), 오피스(Office) 등의 응용 프로그램이 대표적이다.  
  
**6계층 – 표현 계층(Presentation)**: 표현 계층은 응용 계층의 데이터 표현에서 독립적인 부분을 나타낸다. 일반적으로 응용프로그램 형식을 준비 또는 네트워크 형식으로 변환하거나 네트워크 형식을 응용프로그램 형식으로 변환하는 것을 나타낸다. 다시 말해 이 계층은 응용프로그램이나 네트워크를 위해 데이터를 “표현”하는 것이다. 대표적인 예로는 데이터를 안전하게 전송하기 위해 암호화, 복호화하는 것인데, 이 작업이 바로 6계층에서 처리된다.  
  
**5계층 – 세션 계층(Session)**: 2대의 기기, 컴퓨터 또는 서버 간에 “대화”가 필요하면 세션(session)을 만들어야 하는데 이 작업이 여기서 처리된다. 이 계층에는 설정, 조율(예: 시스템의 응답 대기 기간), 세션 마지막에 응용프로그램 간의 종료 등의 기능이 필요하다.  
  
**4계층 – 전송 계층(Transport)**: 전송 계층은 최종 시스템 및 호스트 간의 데이터 전송 조율을 담당한다. 보낼 데이터의 용량과 속도, 목적지 등을 처리한다. 전송 계층의 예 중에서 가장 잘 알려진 것이 전송 제어 프로토콜(TCP)이다. TCP는 인터넷 프로토콜(IP) 위에 구축되는데 흔히 TCP/IP로 알려져 있다. 기기의 IP 주소가 여기서 작동한다.  
  
**3계층 – 네트워크 계층(Network)**: 네트워킹 전문가 대부분이 관심을 두고 좋아하는 라우터 기능 대부분이 여기 네트워크 계층에 자리잡는다. 가장 기본적으로 볼 때 이 계층은 다른 여러 라우터를 통한 라우팅을 비롯한 패킷 전달을 담당한다. 보스턴에 있는 컴퓨터가 캘리포니아에 있는 서버에 연결하려고 할 때 그 경로는 수백 만 가지다. 이 계층의 라우터가 이 작업을 효율적으로 처리한다.  
  
**2계층 – 데이터 링크 계층(Data Link)**: 데이터 링크 계층은 (두 개의 직접 연결된 노드 사이의) 노드 간 데이터 전송을 제공하며 물리 계층의 오류 수정도 처리한다. 여기에는 2개의 부계층도 존재한다. 하나는 매체 접근 제어(MAC; Media Access Control) 계층이고 다른 하나는 논리적 연결 제어(LLC) 계층이다. 네트워킹 세계에서 대부분 스위치는 2계층에서 작동한다.  

참고문헌 : [https://tyeolrik.github.io/network/2017/02/14/Networking-4-Data-Link-Layer.html](https://tyeolrik.github.io/network/2017/02/14/Networking-4-Data-Link-Layer.html)
  
**1계층 – 물리 계층(Physical)**: OSI 디핑 소스의 밑바닥에는 물리 계층이 있다. 시스템의 전기적, 물리적 표현을 나타낸다. 케이블 종류, (802.11 무선 시스템에서와 같은) 무선 주파수 링크는 물론 핀 배치, 전압, 물리 요건 등이 포함된다. 네트워킹 문제가 발생하면 많은 네트워크 전문가가 물리 계층으로 바로 가서 모든 케이블이 제대로 연결돼 있는지, 라우터나 스위치 또는 컴퓨터에서 전원 플러그가 빠지지 않았는지 확인한다.  
  
원문보기:  
[http://www.ciokorea.com/news/36536#csidxe025ca0e7da4691bd4a5fd1f9bd8838](http://www.ciokorea.com/news/36536#csidxe025ca0e7da4691bd4a5fd1f9bd8838) ![](http://linkback.ciokorea.com/images/onebyone.gif?action_id=e025ca0e7da4691bd4a5fd1f9bd8838)


![enter image description here](https://t1.daumcdn.net/cfile/tistory/99F6363359FDDC9E1F)


## Transport
참고 문헌:  [[TCP/UDP]](https://mangkyu.tistory.com/15)

전송계층은 송신자와 수신자를 연결하는 통신서비스를 제공하는 계층으로, 쉽게 말해 데이터의 전달을 담당합니다. 그리고 데이터를 보내기 위해 사용하는 프로토콜이 TCP와 UDP이다. 

![enter image description here](https://t1.daumcdn.net/cfile/tistory/990C0F3359FDD3F80C)

### TCP(Transmission Control Protocol)

TCP는 발신지와 수신지를 연결하여 패킷을 전송하기 위한 논리적 경로를 배정한다. 3-way handshaking은 목적지와 수신지를간 정확한 전송을 보장을 위해 세션을 수립하는 과정이다.  

![enter image description here](https://t1.daumcdn.net/cfile/tistory/991BEB3359FEB5712F)

### UDP(User Datagram Protocol)

데이터를 데이터그램 단위로 처리하는 프로토콜, 데이터그램이란 독립적인 관계를 지니는 패킷이라는 뜻이다.

UDP는 비연결형 프로토콜이다. 즉, 연결을 위해 할당되는 논리적인 경로가 없는데, 그렇기 때문에 각각의 패킷은 다른 경로로 전송되고, 각각의 패킷은 독립적인 관계를 지니게 되는데 이렇게 데이터를 서로 다른 경로로 독립적으로 처리하게 된다.

![enter image description here](https://t1.daumcdn.net/cfile/tistory/9969973359FEB59309)

연결을 설정하고 해제하는 과정이 존재하지 않습니다. 서로 다른 경로로 독립적으로 처리함에도 패킷에 순서를 부여하여 재조립을 하거나 흐름 제어 또는 혼잡 제어와 같은 기능도 처리하지 않기에 TCP보다 속도가 빠르며 네트워크 부하가 적다는 장점이 있지만 신뢰성있는 데이터의 전송을 보장하지는 못합니다. 그렇기 때문에 신뢰성보다는 연속성이 중요한 서비스  예를 들면 실시간 서비스(streaming)에 자주 사용됩니다.



<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI3NzYzNjc5MywxMDA3ODc4NjMxLDUxNj
A0NzA2MCwtNjUyOTU0OTUyLDEzOTc4MDAyMjMsMTc2ODE0NDQ2
M119
-->