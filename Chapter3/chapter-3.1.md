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



