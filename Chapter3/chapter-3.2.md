# 3.2 Multiplexing and Demultiplexing

표현이 너무 마음에 들어서 책에 있는 그대로 적어보겠습니다.

we discuss transport-layer multiplexing and demultiplexing, that is, extending the host-to-host develivery service provided by the network layer to a process-to-process delivery service for applications running on the hosts.

넷트워크 레이어에 의해 전송된 패킷을 프로세스까지 배송을 연장시키는 방법이라고 합니다. 

<img src="/Users/ju/Documents/top-down-approach-network/Chapter3/resources/3.2 extend~.jpeg" alt="3.2 extend~" style="zoom:50%;" />

위와 같은 이미지가 아닐까요? (추후 더 설명을 참고하시면 프로세스에 아래에 소켓이 그려져 있긴 해야 합니다.)



## 필요한 이유

이 서비스가 왜 필요한지 예시로 알아보겠습니다.

hostB의 application layer 서비스로 web page, FTP, Telnet이 있다고 생각해보세요. hostA에서 보낸 packet이 hostB에 도착했어요. 그렇다면 hostB에서 실행중인 3가지 서비스 중 어디로 보내줘야할 지 결정해야겠지요? 

필요성이 확 느껴지나용.



## 전달 방법

전달 방법은 segment의 field를 참고하여 소켓으로 전송하게 됩니다. 이전 장에서 배웠듯, application layer와 transport layer의 메시지 송수신 인터페이스가 소켓이니까요.



이렇게, transport layer에서 segment의 필드를 보고 특정 소켓에 전송하는 서비스를 **demultiplexing**이라 부릅니다. 

반대로 segments를 network layer로 전송하는 일을 **multiplexing**이라고 부르죠.



## 구체적으로...

multiplexing, demultiplexing을 위해선 2가지 요건이 필요합니다.

### 요건

#### 1) socket이 unique identifier를 가지고 있어야 하며,

#### 2) 각 segment는 socket을 식별할 수 있는 field를 가지고 있어야 합니다.

2번에 대해 좀 더 자세히 설명해보겠습니다.

이 field는 source port number field, destination port number field가 되죠. 각각은 16bit로, 0~65535의 수를 가집니다. 

0 ~ 1023 번호까지는 well-known port numbers라 불리며,  RFC에 적힌 유명한 application service가 사용하죠. 따라서 어플 개발하실 땐 이 포트 번호는 피하시는 게 좋습니다. 전에 봤던 80번 -> 웹서버, 53번 -> DNS 서버 등 처럼요.

<img src="/Users/ju/Documents/top-down-approach-network/Chapter3/resources/Figure 3.3 source and destination port-number fields in a transport-layer segment.png" alt="Figure 3.3 source and destination port-number fields in a transport-layer segment" style="zoom:50%;" />



이처럼 segment를 applicaiton에 전달과정을 다음처럼 좀 더 구체적으로 말할 수 있습니다.

 인터페이스인 소켓이 생성될 때 portNumber를 할당 받아야하며, 받는 쪽 transport layer는 destination 포트 넘버를 보고 message를 소켓에 전송하여 application에 메세지가 도달한다. 라구요. (사실 이건 UDP에 딱 해당하는 말이고 TCP는 좀 다릅니다 ㅎㅎ; 충격적이죠.)



## 소켓 프로그래밍에서...

이전에 살펴본 UDP 소켓 프로그래밍을 다시 한 번 살펴볼게요.

```python
clientSocket = socket(AF_INET, SOCK_DGRAM)
```

이때, UDP socket이 생성되고 자동으로 port number가 할당되죠. well-known port numebr를 피하기 위해 1024 ~ 65535 중, 현재 사용하지 않는 **UDP 포트 번호**로 할당된답니다.





```python
clientSocket.bind(('', 19157))
```

이 명령어로는 생성된 소켓을 특정 포트 번호와 연관시킬 수 있죠.



그래서 웹서버를 개발한다면, 80번 포트가 well-known port number이므로 해당 포트 번호를 바인딩하도록 개발해야 합니다. 반면에 클라이언트 소켓은 자동으로(transparently) 포트 번호가 할당되죠.



### UDP에서 socket 구분

UDP에서는 단순히 (destination IP Address, destination port number)로 소켓 구분이 가능합니다. 따라서 데이터 보낼 때 이 두 가지만 명시해줘도 되죠. 



한편으로, 충분히 받는이의 소켓을 구분할 수 있는데, 왜 source IP Address, source port number를 작성해줄까요? 그 이유는... 답장하기 위해서 입니다.ㅎㅎ; 어찌보면 당연하죠.







## Connection-Oriented Multiplexing and Demultiplexing

이 파트를 배우면 꽤나 충격먹을 겁니다.