# 2.1 Principles of Network Applications

기가 막힌 idea가 있다고 봅시다. 어떻게 해야 이걸 실세계 network application으로 만들 수 있을까요?



network app의 코어에서는 개발이란 network를 통해 결국 서로 다른 end systems에서 돌아가는 프로세스끼리 통신하는 거랍니다. 헉 ~!~! 그러네요?;; 너무나 단순 명료한 정의에 매료되었습니다.

브라우저와 웹서버를 볼께요. 브라우저는 결국 클라이언트죠? 네이버 웹페이지를 가지고 오고 싶다고 해봐요. 

1. 크롬 브라우저를 킵니다. (client side end-system process)
2. url 검색창에 `https://www.naver.com` 를 입력합니다. 이때, 해당 도메인 호스트에 있는 네이버 웹서버 프로세스로부터 index page를 요청하는 것이죠.
3. 브라우저가 데이터를 받고 (통신) index화면을 그려줍니다.

헐랭~ 결국 인터넷을 통해서 서로 다른 호스트에 있는 프로세스끼리 통신한거네요. 훽!



그래서 새로운 app을 개발한다면, 여러 end system에 동작할 소프트웨어를 개발해야 합니다. 여기서 network core device관련 코드는 짤 필요가 없어요. **application layer에서 동작하지 않으니까요!!** network layer이하의 코드는 건들 필요가 없습니다. 그림으로 나타내면 다음과 같죠

<img src="/Users/ju/Documents/top-down-approach-network/Chapter2/resources/Figure 2.1 Communcation for a network application takes place between end system at the application layer.png" alt="Figure 2.1 Communcation for a network application takes place between end system at the application layer" style="zoom:67%;" />



결국 end-system에 돌아가는 network application끼리 통신이라 함은 Application Layer로 통신을 의미하게 되는 것이죠.

> 여기서 프로세스가 아니라 network application이라고 부르는데, 좀 더 구체적으로 말해줘서 그런 것 같습니다. transport layer도 소프트웨어 계층이라 프로세스끼리 통신하는 것도 매한가지라서... 그런 것 같네요.



## 2.1.1 Network Application Architectures

제목을 잘 보면 네트워크 아키텍쳐가 아니라 어플리케이션 아키텍쳐입니다! 네트워크 아키텍쳐는 5계층 프로토콜을 의미하는 거구요, 어플리케이션 아키텍쳐는 개발자가 어떻게 해야 여러 end systems에 동작하는 프로그램을 만들지 구조를 짜는거라고 보면 됩니다.

그래서 어플리케이션 아키텍쳐를 구성할 때 다음의 통신의 두 패러다임 중 하나를 선택하게 될 것입니다.

하나는 **client-server architecture** 다른 하나는 peer-to-peer(P2P) architecture입니다.



### Client-Server architecture

다들 잘 알고 있을 내용이라... 간략히 요약정리하여 넘어가겠습니다.

#### Server

* 항상 켜져서 클라이언트들의 요청을 받을 준비를 해야 합니다.

* 도메인이 고정되어 있어야 합니다.

* 요청량 때문에 Data Center라는 가상서버를 두기도 합니다.

  하나의 호스트가 모든 클라이언트의 요청을 감당할 수 없으니까요.

#### Client

* 도메인을 통해 서버에 요청을 전송합니다.

* 클라이언트끼리 직접적으로 통신할 수 없습니다.

  반드시 서버를 통해 통신하는 구조니까요. 서버를 통해 간접적으로 통신할 수 밖에 없죵.





### P2P architecture

#### 정의

그 대신, host간에 직접적인 연결하여 통신하는 구조를 의미합니다. 이때의 호스트를 peer라고 부릅니다.



#### 예시

비트 토렌트

토렌트 프로그램 잘 보면 peer라는 용어가 나올거에요.  ㄷ 같은 구조입니다.



#### 특징

* 데이터 센터에 대한 의존성이 떨어집니다. 

* 자기 확장이 가능하죠

  이게 뭐냐면, 토렌트에서 데이터를 업로드해주는 사람이 많으면 그만큼 파일을 더 빨리 받을 수 있는 거에요. ~ 

* traffic-intensive application은 p2p 아키텍쳐로 되어있습니다.

* 단점으로는  너무 분산되어 있어서 보안, 데이터 신뢰성, performance에 대한 문제를 지니고 있습니다.



몇몇 app들은 두 개를 혼용해서 사용하기도 합니다~

