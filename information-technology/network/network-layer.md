# OSI 계층

![enter image description here](https://t1.daumcdn.net/cfile/tistory/99F6363359FDDC9E1F)


## Transport
전송계층은 송신자와 수신자를 연결하는 통신서비스를 제공하는 계층으로, 쉽게 말해 데이터의 전달을 담당합니다. 그리고 데이터를 보내기 위해 사용하는 프로토콜이 있는데, 그 프로콜들이 바로 오늘의 주인공 TCP와 UDP입니다.

### TCP(Transmission Control Protocol)

-   연결형 서비스로 가상 회선 방식을 제공한다.
    
-   3-way handshaking과정을 통해 연결을 설정하고 4-way handshaking을 통해 해제한다.
    
-   흐름 제어 및 혼잡 제어.
    
-   높은 신뢰성을 보장한다.
    
-   UDP보다 속도가 느리다.
    
-   전이중(Full-Duplex), 점대점(Point to Point) 방식.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc2ODE0NDQ2M119
-->