# OSI 계층

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
eyJoaXN0b3J5IjpbLTY1Mjk1NDk1MiwxMzk3ODAwMjIzLDE3Nj
gxNDQ0NjNdfQ==
-->