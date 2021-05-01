# Chapter3 Transport Layer

application layer와 network layer 사이에 있는 Transport Layer는 layered network architecture에서 서로 다른 호스트에 있는 프로세스간 통신에서 핵심적인 역할을 맡습니다.



이번 챕터에서는 transport-layer의 원칙에 대해 논의한 다음, 현존하는 프로토콜이 그 원칙에 따라 실제로 어떻게 구현됬는지를 알아볼 예정입니다. Internet의 transport protocol인 TCP, UDP를 알아볼 것이지요.



첫 내용은 transport layer와 network layer간 관계입니다. transport layer의 핵심적인 기능을 설명할 예정이지요. 

network layer의 전달 서비스를 연장(extends)하는 역할말이죠.



다음으로는 컴퓨터 네트워킹에서 가장 근본적인 문제를 다룰 것입니다. 바로~ 데이터의 신뢰성에 대해서 말이죠. TCP가 어떻게 이를 구현했는지 살펴볼 것입니다.



다음으로는 컴퓨터네트워크에서 두번째 근본적인 중요한 문제에 대해 다룰 것입니다. transmission rate를 어떻게 컨트롤할 것 이냐 말이죠. congestion의 원인과 결과를 배우고 컨트롤 기술을 배울 것 입니다. 이를 배운 뒤에는 TCP의 congestion control을 배울 예정입니다.



그럼, 다음 시간엔 3.1장을 시작해보겠습니다.