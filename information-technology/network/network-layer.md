# OSI 계층

![enter image description here](https://t1.daumcdn.net/cfile/tistory/99F6363359FDDC9E1F)


## Transport
참고 문헌:  [[TCP/UDP]](https://mangkyu.tistory.com/15)

전송계층은 송신자와 수신자를 연결하는 통신서비스를 제공하는 계층으로, 쉽게 말해 데이터의 전달을 담당합니다. 그리고 데이터를 보내기 위해 사용하는 프로토콜이 TCP와 UDP이다. 

![enter image description here](https://t1.daumcdn.net/cfile/tistory/990C0F3359FDD3F80C)

### TCP(Transmission Control Protocol)

TCP는 발신지와 수신지를 연결하여 패킷을 전송하기 위한 논리적 경로를 배정한다. 그리고 3-way handshaking과정은 목적지와 수신지를 확실히하고 정확한 전송을 보장하기 위해서 세션을 수립하는 과정을 의미합니다. 이러한 과정은 TCP 서비스의 신뢰성을 보장한다. 

### UDP(User Datagram Protocol)

데이터를 데이터그램 단위로 처리하는 프로토콜, 데이터그램이란 독립적인 관계를 지니는 패킷이라는 뜻으로, UDP의 동작방식을 설명하자면 다음과 같습니다.

위에서 대충 눈치채셨듯이 TCP와 달리 UDP는 비연결형 프로토콜입니다. 즉, 연결을 위해 할당되는 논리적인 경로가 없는데,

그렇기 때문에 각각의 패킷은 다른 경로로 전송되고, 각각의 패킷은 독립적인 관계를 지니게 되는데 이렇게 데이터를 서로

다른 경로로 독립적으로 처리하게 되고, 이러한 프로토콜을 UDP라고 합니다.

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI4ODQ5NjA0LDE3NjgxNDQ0NjNdfQ==
-->