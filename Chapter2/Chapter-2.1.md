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



