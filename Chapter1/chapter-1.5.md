# 1.5 Protocol Layers and Their Service Models

앞서 내용을 배웠듯이... 엄청 복잡하죠? 이걸 이해할 수 있는 아키텍쳐가 있냐는 거에요.. 이 책에선 Yes라고 하네요. 한 번 알아봅시다~





## 1.5.1 Layered Architecture

어떻게~ 거대한 시스템을 이해할 수 있는가? 에 대해서 layered architecture를 얘기합니다. 비행기에 탑승하고 내리는 과정을 들어 설명했는데... 제가 비행기를 잘 안타봐서 ㅎㅎ; 지하철로 설명드려보겠습니다.



지하철을 타기 위해선

1.  지하철 입구에 들어간 다음
2. 카드를 찍고
3. 지하철에 탑승합니다.
4.  그리고 지하철이 원하는 목적지에 도착했다면
5.  하차한 후에 
6. 다시 카드를 찍고
7. 밖으로 나옵니다.



이러한 일련의 과정을 다음처럼 그려보겠습니다.

<img src="/Users/ju/Documents/top-down-approach-network/Chapter1/resource/subway-trip.png" alt="subway-trip" style="zoom:67%;" />

오.. 층같은게 보이지 않나요? 다음 처럼요~

![subway-trip-layer](/Users/ju/Documents/top-down-approach-network/Chapter1/resource/subway-trip-layer.png)

지하철타는 일련의 과정을 다음처럼 층을 지어 설명할 수 있습니다.

1. 지하철 입구

   역할: 지하철이라는 공간에 들어가기 위한 기능을 맡는 부분

   서비스 방식: 에스컬레이터나 계단으로 들어가거나 나갈 수 있게 해준다.

2. 결제 창구

   역할: 지하철 서비스 이용에 대한 댓가를 치룰 수 있는 부분

   서비스 방식: 카드를 대주거나 코인을 넣으면 비용을 지불할 수 있다. 또한, 지하철을 이미 이용한 사람에 대하여 추가 결제 및 환승을 돕는다.

3. 지하철 문 

   역할: 지하철에 들어갈 수 있도록 하는 부분

   서비스 방식: 문이 열리거나 닫힌다.

4. 지하철

   역할: 실제 사람을 이동시켜주는 부분

   서비스 방식: 안으로 들어가면 이동합니다.



계층의 특징은 다음과 같이 정리할 수 있습니다.

각 계층은 (1) 자신의 서비스를 제공해주거나,  (2) 바로 하위 계층의 서비스를 이용하기도 하죠

결제창구 layer를 예시로 볼까요?

결제 창구는 지하철을 이용하려는 사람에게 돈을 받아냅니다. 이건 자신 본연의 서비스라 볼 수 있죠. 

또한, 지하철 서비스를 이미 받고 나가려는 사람(하위계층)에게도 어느정도 이동 했는지 측정하여 추가 요금도 받아냅니다.

> 이 부분이 확 와닿진 않네요 ㅎㅎ;



헐~ 그러면 이렇게 계층을 구분했더니 뭐가 좋을까요?

* 다른 곳의 책임과 역할은 고민하지 않아도 됩니다. 결제 창구에서는 어떻게 들어왔든지 관심없고 카드나 코인을 제대로 사용하는지만 집중하면 되죠.  다시 말해서, 자신에게 형식에 맞는 서비스가 들어오는지 나가는지 input output만 고려하면 됩니다. functional 하게요~ 추상화처럼, 구체는 바뀌더라도 추상은 변하지 않으면 다양하게 변경이 가능합니다.



이러한 장점 때문에, 만약에 새로운 기능이 필요하다면 그 사이에 레이어를 추가시켜도 돼요! 인풋 아웃풋만을 잘 고려해서요. 스크린 도어를 예로 들을 수 있을 것 같네요.

결제를 한 사람과 지하철의 문 사이에 **안전이라는 기능**을 추가했잖아요?

머리가 뿅하지 않나요 ㅎㅎ;  나중에 나올 https도 이런 생각으로 접근하면 쉽게 받아들여지더라구요.



#### 단점

그런데 네트워크를 계층화로 프로토콜을 보려는 관점이 문제가 있기도 합니다. 이걸 언급하려는 이유는 이상적인 계층화로 네트워크를 보려고 애쓰진 말아 달라는 의미에요. 네트워크를 바라보는 하나의 도구라고만 여겨주십쇼. 단점을 말씀드리겠습니다.

* 네트워크는 계층 별로 비슷한 기능을 지닐 수도 있습니다.
* 계층에 의존적인 성향이 있기도 하지요. 최대한 서로 독립적이어야 하는데용. 

이러한 부분에 유념하시어 네트워크를 알아봅시다.



### Protocol Layering

각 계층을 service provider라고 보시면 됩니다. 그리고 프로토콜에 따라 계층을 나누었어요. 그게 5계층 레이어입니다.

정확히는 Five-Layer - Internet - protocol stack라고 부르죠.

각각 physical layer, link layer, network layer, transport layer, application layer가 되겠습니다.

> 지겹도록 들어본 osi 7 layer가 있긴한데, 이것은 인터넷 환경에서 개발에는 실용성이 떨어지는 정의라고 하네요



이 책의 제목처럼 Top-Down 방식으로 설명해보겠습니다.



### Application Layer

network application과 application-layer protocol이 존재하는(reside) 계층입니다.

network application이라 함은 크롬이나 슬랙같은 어플리케이션을 의미하는거구요, application-layer protocol은 http, smtp, ftp같은 프로토콜을 의미합니다.

dns도 그 중 하나라네요.

그렇다보니 application endpoint 사이에 protocol이 분산되어 있다.(distirubted)라고 표현하네요



application layer의 packet을 message라고 칭합니다.





### Transport Layer

application endpoint간의  application-layer message를 전송해주는 역할을 합니다. 

> 이것만 들었을 땐 전송만 해주는데 뭐;; 따로 계층을 지어야하나... 싶죠? ㅎㅎ;;

인터넷에서는 2개의 transport protocol이 존재하죠. TCP와 UDP입니다. 자세한 내용은 뒤에서 설명하죠. 간단히 설명하자면 TCP는 신뢰성있는 데이터를 보장해주는 기능을 맡고, UDP는 신뢰성있는 데이터는 보장못해주는 대신 전송이 빠릅니다. 이러한 기능을 구현한 계층이라고 보시면 돼요.



transport layer의 packet을 segment라고 부릅니다.



### Network Layer

다른 호스트로 패킷을 전송해주는 책임을 맡고 있습니다. 다시 말해서 segment를 다른 호스트로 전달해주는 책임이죠.

IP protocol이 존재합니다. 그리고 인터넷의 네트워크레이어에선 routing protocol도 포함되어 있죠. 라우팅 알고리즘은 개발자가 정할 수 있구요.



여기서의 패킷은 datagram이라 불립니다.



### Link Layer

network layer가 호스트로의 전송을 책임지고 있다면, link layer는 route에서 **한 노드에서 다른 노드**로 전송해주는 책임을 지고 있습니다. 좀 더 구체적이라할 수 있죠??

> packet switches들을 link layer에선 노드라고 칭하는 것 같습니다.



link layer의 프로토콜이 또 있겠죠? 여기서도 신뢰성있는 데이터를 주고받는 프로토콜이 있습니다. 기능이 비슷해보여도 TCP와 완전 다른거죠; 계층 자체가 다르니. 헷갈리지 마시기 발바니다.

여기선 그 예시로 ethernet, wifi, protocol같은 것을 의미하네요.



이때 패킷은 frame이라 불립니다.



### Physical Layer

frame에 있는 **각 bit**를 다른 노드로 넘겨주는 역할을 합니다. 여기의 프로토콜이라 하면 구리선, 광랜선 등등이라 할 수 있죠.



