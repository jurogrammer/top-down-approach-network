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



