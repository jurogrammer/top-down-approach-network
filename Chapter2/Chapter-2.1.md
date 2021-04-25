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





## 2.1.2 Processes Communicating

이번 소단원에서는 network application을 개발하기 전에, 어떻게 서로 다른 두 프로세스가 통신하는지 알아보겠습니다.

우선, 호스트 내 프로세스끼리 통신은 운영체제에 의해 컨트롤됩니다. 이건 IPC 내용을 참고하시면 될 것 같구요... 여기서는 서로 다른 두 호스트간에 통신을 얘기해보겠습니다.

프로세스는 network를 통해서 messages로 통신하죠.

1. sender process는 메세지를 생성하고 네트워크로 전송합니다.
2. receiver process 그 메시지를 받고 더 나아가 메세지를 돌려줌으로써 응답할 수도 있습니다.



### Client and Server Processes

client, server가 뭐 대단한 용어같은데, 데이터를 받으면 client, 보내면 server에요. 여기 내용은 이게 전부입니다.

P2P보면 데이터를 다운로드하는 동시에 업로드하잖아요? 그러면 그 end-system은 클라이언트일까요? 서버일까요?

정답은 둘 다입니다. ㅎㅎ; 그냥 역할 놀이하는 거에요.



### The Interface Between Process and the Computer Network

서로 다른 두 프로세스는 메세지를 전송함으로써 통신한다고 했잖아요? 그럼 그 메세지는 아래 계층으로 전달되야 하는거겠죠?

결론적으로, 어떤 인터페이스를 통해 아래 계층으로 메세지가 전송될까요?? 자바에서 terminal에 문자를 출력하려면 System.out.println를 사용해야하는 것 처럼요.

바로바로~~ Socket이라는 놈입니다. 한 번은 꼭 들어봤으리라 생각듭니다. ㅎㅎ

#### Socket

Socket은 호스트내 Application Layer와 Transport Layer 사이를 연결시켜주는 인터페이스입니다. 이것은 host의 컴퓨터에 내장되어 있죠.

그래서 소켓으로 모든 Application Layer쪽을 컨트롤 할 수 있습니다. 

한편으로 socket으로 약간 transport layer를 컨트롤할 수 있죠.  

1)어떤 프로토콜을 선택할 지, 

2) transport-layer의 parameter 수정 ex:)  buffer size

총 두 가지를 컨트롤할 수 있습니다.

그래서 application developer가 transport protocol을 정하면 그 프로토콜로 고정되어 해당 서비스를 받을 수 있죠.



### Addressing Processes

한편으로, 메세지 투입구까지 알았잖아요? 그렇다면, 저기 어딘가에 있는 호스트의 프로세스를 어떻게 특정하여 메세지를 전송할 수 있을까요?

아래 그림을 보고 다시 한 번 생각해보세요.



<img src="/Users/ju/Documents/top-down-approach-network/Chapter2/resources/Figure 2.3 Application processes.png" alt="Figure 2.3 Application processes" style="zoom:50%;" />

### 

특정 짓기 위해서 다음을 고려해야 한다고 볼 수 있습니다.

#### 1) 호스트의 주소

인터넷상 호스트의 주소는 뭐라고 했죵? 바로 IP입니다~ IP를 알면 호스트를 특정지을 수 있어요.

#### 2) 프로세스 번호

프로세스간 통신이라고 했죠? 호스트의 프로세스를 특정지어야 하잖아요~ 프로세스번호도 번호지만, 소켓으로 통신한다고 했죠? 나중에 나올 내용이지만, 소켓이 연결된 포트번호로 프로세스를 지정할 수 있습니다.

그래서 보통 유명한 어플리케이션은 Webserver -> port 80, SSL -> port 443 등으로 미리 지정되어 있습니다.



### 2.1.3 Transport Services Available to Applications

이번에는 transport layer에 대해 간단히 알아보겠습니다. 왜냐하면, 어플리케이션을 작성할 때 어떤 transport service를 사용할 지 정해주어야 하거든요.  소켓이 application layer 와 Transport layer를 이어주는 부분이니까요.



그렇다면 어떤 기준으로 서비스를 선택해야 할까요? 그 기준 또는 성질 4가지를 알아보겠습니다. 

#### 1. reliable data transfer

chapter1에서 언급했듯이 생각보다 패킷이 100% 잘 전달되리라 보장할 수 없습니다. 패킷이 손실, 변조될 수 있거든요. 따라서 문서들의 데이터가 바뀐다는 것은 매우 큰 손실 일 수 있습니다.

따라서 application의 특징에 따라 데이터를 올바르게, 그리고 모두 전달하기 위한 전송 방법을 고려해야할 수도 있죠. 이런 측면을 reliable data transfer이라고 부릅니다.

참고로 비디오나 오디오는 손실이 좀 되도 상관없기도 합니다.



#### 2. throughput

보장된 처리량을 제공해주는지로 보면 됩니다.  음악을 생각해볼게요. 전송량이 많지 않아도, 끊김없이 들으려면 일정 처리량을 보장해주어야겠지요?

한편으로 처리량에 민감한 application을  bandwidth-sensitive applications라고 부릅니다. 그 반대는 elastic applications라고 부르죠(mail, file 전송같은 것들)



#### 3. timing

타이밍 관련된 것도 보장할 수 있어요. 여기선. 엄청나죠? 게임보면 핑 있잖아요. 몇 ms이내에는 반드시 데이터를 받아야 한다. 이런걸 보장해주는 요소라고 보면 됩니다.



#### 4. security

보안과 관련된 서비스도 제공해줄 수 있어요. 데이터를 암호화하여 전송하는 것이죠.



### 2.1.4 Transport Services Provided by the Internet

앞서 언급했던 4가지 요소는 일반적인 네트워크에서 transport layer가 제공하는 서비스를 4가지 측면에서 살펴본거구용. **인터넷** 네트워크에서는 TCP,UDP 2가지 종류의 transport layer protocol이 존재합니다.

#### 

#### TCP services

tcp service model은 connection-oriented service, reliable data transfer service 두 가지를 포함하죠.

##### 1. connection-oriented service

application message를 전송하기 전에 transport-layer control information을 서로 교환합니다. 이러한 정보 교환 과정을 3-way handshake라고 부르죠.

이러한 handshake 과정이 성공적으로 끝난다면, 두 프로세스의 소켓 간에 **tcp connection**이 존재한다.라고 부릅니다!

> 헉, 소켓 간에 TCP-connection이 존재한다고 말하네용.

이 tcp connection은 full-duplex connection이라 부르는데요, 용어를 찾아보니half-duplex connection과 비교하면 와닿으실 겁니다.

half-duplex connection은 한쪽이 보내고 있으면 한 쪽은 받기만 해야하는데, full-duplex는 한쪽이 보낼 때 다른 한 쪽도 보낼 수 있습니다.

> 흠... 그렇다는 것은 sender, receiver buffer를 둘 다 지녀야 한다는 뜻이 되겠네요...? 아닐 수도 있구용 ㅎㅎ;



자세한 내용은 어차피 3장에서 배우니 이렇구나... 하고 넘어가시면 됩니다.



##### 2. reliable data transfer service

앞서 말했듯이 에러없이, 정확한 순서대로, 누락없이 데이터를 보내는 것을 보내죠.





#### UDP Services

아주 기본이 되는 서비스라고 보시면 됩니다. 통신하기 위한 최소만 설정되어 있어요. 그래서 connection을 가질 필요가 없어서 handshake과정도 필요없고 신뢰성있는 데이터도 보장하지 않아요.





### 2.1.5 Application-Layer Protocols

프로세스들이 메세지를 소켓으로 집어넣는다고 말했는데요, 메세지의 구조는 어떻게 되어 있을까요? 양식같은거 말이에요. 

비유를 들어볼게요.

편지를 쓸 땐 상단에 보낸이, 중간엔 내용 하단엔 받는 이정도 보내잖아요? 뭐 비문학 글쓰기를 한다면 서론 본론 결론정도로 글을 쓰구요. 이렇게 글의 구조가 바뀌죠? application meesage도 그렇다는거에요.

이러한 구조를 강제하면 protocol이라고 말할 수 있죠. 그 메세지가 바로 application-layer의 protocol이라고 말할 수 있습니다. application-layer protocol은 다음 아래 사항을 규정하죠

* 교환되는 message의 타입을 정합니다. ex:) request message, response message
* 다양한 message type의 문법을 정합니다. ex:) 어떤 구분자를 사용하여 field의 구분할 지
* field의 의미를 정합니다. ex:) information field의 의미
* 프로세스가 언제 어떻게 메세지를 보내고 받아야할 지에 대한 규칙을 정합니다.



application 프로토콜은 RFCs에 공식적으로 규정되어 있기도 하구요, skype처럼 비공개로 지니고 있기도 합니다.



### 2.1.6 Network Applications Covered in This Book

application-layer인 만큼... 수많은 개발자들이 지금도 여러 protocol을 개발하고 있습니다. 이 모든 내용을 다 알아야할 필요가 있을까요... 따라서 유명하고 중요한 것들만 알아보겠습니다.

이 책에선 메일 서비스 프로토콜, P2P등도 다루지만, 지금은 웹 개발자이므로 HTTP, DNS에 대해서만 알아보겠습니다~ 나중에 혹여나 업종이 바뀌면 그때 배우면 되죵 ㅎㅎ