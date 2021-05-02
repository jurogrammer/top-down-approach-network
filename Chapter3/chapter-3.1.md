# 3.1 Introduction and Transport-Layer Services

trnasport layer에 대해 이전 챕터에서 배웠던 것을 리뷰해보겠습니다.



### logical communication

transport-layer protocol은 서로 다른 호스트에서 돌아가는 프로세스간 논리적으로 통신(**logical communication**)하는 것을 제공해줍니다. 다시 말해서 마치 서로 다른 호스트의 프로세스가 곧바로 연결되어 있는 것 처럼 느껴지는 것이죠. 두 호스트는 물리적으로 엄청 떨어져 있을 수도 있는데 말이죵.



### 구현 위치

그리고 transport layer는 라우터가 아닌 호스트에 구현되어 있습니다.



### 보내는 쪽 작업(sending side)

1. application message를 받습니다.
2. 그 메세지를 쪼개고(chunk), transport-layer header를 추가합니다. (이 패킷이 바로 segment가 됩니다.)

3. network-layer로 segment를 전송합니다.

   router는 network-layer field만을 보고 작업 진행(계층화, 캡슐화)



### 받는 쪽 작업(receiving side)

1. network-layer는 datagram으로부터 segment를 extract합니다.
2. transport-layer로 segment전송.
3. transport-layer에서 app이 데이터를 사용할 수 있도록 작업하여 전달.







## 3.1.1 Relationship Between Transport and Network Layers

### 1. protocol stack에서 transport layer는 network layer에 존재합니다.

이 뜻은 tranport layer의 서비스가 network layer에 영향을 받는다는 뜻이 됩니다.

예를 들어서 라우팅하는 과정에서 패킷을 잃어버릴 수 있다고 말씀드렸죠? 그래서 transport layer는 reliable data service를 제공합니다. 또한, timing관련하여 말씀드리면 network layer에서 일정 시간 내 패킷을 전송해줄 수 있다.라고 보장 못해요. 그러니까 transport layer에서 또한 타이밍을 100% 보장할 수 없겠죠. ㅠㅠ



### 2. Network layer는 호스트 간의 논리적 연결을 도맡고, transport layer는 서로 다른 호스트간 프로세스의 논리적 연결을 도맡습니다.

오... 명확하네요. IP가 인터넷 상에 호스트를 구분짓는 역할이였고, portNumber가 프로세스가 가진 소켓의 idenifier였으니까요. 한편으로, 패킷을 받는 쪽 transport layer를 생각해볼게요. network layer로 부터 패킷을 받았는데, 이 패킷을 어떤 소켓으로 전달해줘야 할까요?

호스트 내 프로세스는 여러 개 있을 수 있고, 프로세스 내에서도 소켓을 여러 개 생성할 수 있습니다. 패킷을 보내줘야할 소켓을 식별하여 전송해주는 것. 그것을 transport layer가 해줘야한다는 뜻이 됩니다.



따라서, application layer를 공부할 때 tranport layer를 언급했던 것처럼, transport layer를 공부할 때 어느정도 network layer에 대해 언급드리도록 하겠습니다.

한편으로 위 두가지의 관계 때문에 다음처럼 표현하더라구요.



"Extending host-to-host delivery to process-to-process delivery"



## 3.1.2 Overview of the Transport Layer in the Internet

### 종류

이전에 간단히 언급했듯, 인터넷에서 transport layer protocol은 UDP와 TCP가 있습니다.

UDP는 unreliable, connectionless service이구요, TCP는 reliable, conneciton-riented service입니다.

이전 챕터에서 해보셨듯, 개발자는 소켓 프로그래밍 시 (어플리케이션 개발 시) 둘 중 하나를 선택해야 합니다.



### 용어

#### segment

transport-layer packet입니다.

UDP(user datagram protocol)는 또 datagram이라고 부르는데 network layer의 packet과 혼용되는 말이라, segment로 통일합니다.



### Network layer에 대해 

transport layer를 설명하면서 어느정도 언급해야 하므로 간단히 network layer에 대해 정리하고 가겠습니다.

#### IP

인터넷에서 network layer에서 사용되는 프로토콜을 지칭합니다. **Internet Protocol**의 약자에요. (켁; 충격이네요. 그냥 IP 주소를 관용어처럼 얘기하다가 이렇게 정확한 의미를 알아보니... )

IP가 host간 logical communication을 제공하죠.



#### IPservice

1) best-effort delivery service

best-effort... 데이터를 잃어버릴 수 있다는 뜻입니다. 따라서 unreliable service라고 부르죠.

2) IP Address

모든 호스트는 적어도 하나의 IP Address를 가지게 됩니다.





### UDP/TCP 서비스 간단 정리

#### 공통

* host-to-host에서 메세지를 전송해주는 기능을 process-to-process로 메세지를 전송하는 기능을 transport-layer multiplexing and demultiplexing이라고 부릅니다.
* error checking 기능도 있습니다.



#### UDP

* 얘도 unreliable data transfer입니다.
* congestion  control기능이 없습니다. 데이터 다 때려박을 수 있죠.



#### TCP

* reliable data transfer

* congestion  control 기능도 제공합니다.

  이 기능이 없다면,,, traffic이 증가함에 따라 패킷 송수신 시간이 급격히 증가하는 인터넷 특성상 혼돈의 상태가 되겠죵



## 정리 및 한탄

이번 챕터에서는 개요다보니 용어 및 컨셉에 대해 설명드렸습니다. 책보면 쉽게 이해할 수 있는 내용들이라 이걸 정리해야하나 싶을 정도로 재미없었네요. ㅜㅜ 의무감으로 적는 느낌이 들어서 대충 적은 것 같네요... 방법을 좀 바꿔봐야겠습니다. 

