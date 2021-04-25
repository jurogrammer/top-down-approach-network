# 2.2 The Web and HTTP

초기에 인터넷은 일부 기관에 한해서만 사용됬는데요, 시간이 흐르며 대중화가 됬답니다. 최초로 대중화가 된 application은 바로 www입니다.!



이런저런 웹의 특징이 있겠지만 큰 특징은 데이터를 요구할 때만 작동한다는 것입니다.

오디오나 티비를 생각해보세요. 계속 전파를 쏘고 있다가, 우리가 듣고 싶을 때 채널을 맞추어 듣거나 보는 형태이지만 웹은 우리가 요청시에만 데이터를 전달하죠. 허걱~

이러한 특징 때문에 사람들이 적은 비용으로 쉽게 컨텐츠를 제작할 수 있게 되었스빈다. 그리고 하이퍼링크가 검색 엔진은 인터넷 검색하는데 도움을 주죠.

그리고 현재 웹은 여러 컨텐츠의 플랫폼이 되기도 합니다. 유튜브나 이메일, 인스타그램들을 생각해보세영~



## 2.2.1 Overview of HTTP

HTTP: HyperText Transfer Protocol의 약자입니다. 이 프르토콜은 웹의 핵심이죠.

HTTP는 두가지 요소에 의해 구현됩니다.

1. client program
2. server program

그리고 이 서로 다른 시스템들은 HTTP messages를 서로 교환하죠.

HTTP(프로토콜)은 어떻게 두 클라이언트 서버가 주고받아야하는지, 그리고 메세지의 구조가 정의되어 있습니다.



HTTP를 알아보기 전, web용어를 알아보고 가죠

### Web 용어들

#### Web page

document라고도 불리며 objects를 가지고 있습니다.

#### object

단순히 파일을 의미합니다.

HTML file, JPEG image, Java applet, video clip까지 포함하죠. 이것들은 single url을 통해 위치를 특정지을 수 있습니다.(addressable)



대부분, 웹페이지는 base html file과 몇 개의 오브젝트 참조를 지니고 있죠.

예를 들어서 Web page가 HTML text와 5개의 이미지 파일을 가지고 있다면 웹페이지는 6개의 objects를 지니는 형태가 됩니다.



#### URL

url은 2개의 컴포넌트를 지닙니다.

object를 가지고 있는 **서버의 호스트 이름**, 그리고 **object의 path**로 구성되죠.

```
http://www.someSchool.edu/someDepartment/picture.gif
```

서버의 호스트 이름: www.someSchool.edu

object path: /someDepartment/picture.gif

가 됩니다.



#### Web browsers

client side http를 구현한 application이라고 보시면 됩니다. 여기선 broswer, client라고 혼용하여 부르겠습니다.



#### Web server

서버측 HTTP를 구현한 application이라 보시면 됩니다.



### HTTP의 특징

#### 1. transport layer protocol - TCP

http는 tcp를 사용합니다~ 다시 말해서 http는 connection oriented protocol이라고 볼 수 있죠. browser와 server는 socket을 통해 tcp연결이 되어 있는것이죠.

또한 TCP를 사용했기 때문에 데이터의 신뢰성이 생깁니다. HTTP는 이런 걱정을 할 필요가 전혀 없겠죠~



#### 2. stateless protocol

또 다른 특징 중 하나로는 클라이언트의 상태를 가지고 있지 않습니다. 따라서 특정 사용자가 object를 두번 요청해도 동일하게 object를 반환하죠.





## 2.2.2 Non-Persistent and Persistent Connections

생각해보면 당연한 내용이네요. HTTP는 TCP를 사용한다고 했잖아요? TCP는 connection-oriented service이구요.

그렇다면 데이터를 요청할 때 connection을 맺겠죠? 그리고 데이터를 다 주고나서! 이 connection을 어떻게 할 지에 대한 문제입니다. 클라이언트가 요구한 object를 전송한 즉시 connection을 끊어버릴것이냐, 아니면 계속 connection을 유지할 것이냐  라는 것이죠. 

보낸후 즉시 끊는 것을 **non-persistent connections**, 연결 상태를 유지하는 것을 **persistent connections**라고 부릅니다. 비유를 들자면 방에 문을 열고 들어왔는데, 누군가가 안들어온다면 문을 닫아버리고, 누군가가 들어올거라면 일단 열어놓잖아요? 이런 느낌입니다.



어떤 장단점이 있는지 파악하기 위해 HTTP에서 파악해보겠습니다. (참고로 http의 default설정은 persistent connection입니다.)



### HTTP with Non-Persistent Connections

#### 가정

```
http://www.someSchool.edu/someDepartment/home.index
```

이 url엔 1개의 base HTML file, 10개의 JPEG image가 있다고 보겠습니다.

#### 절차

1. HTTP client가 host로부터 TCP connection을 initiate합니다.
2. 그리고 연결됬다면 HTTP client는 /someDepartment/home.index 파일을 요구하죠.
3. http server는 Request message를 받고 처리하여 해당 경로에 있는 자신의 파일을 HTTP message로 전송합니다.
4. HTTP server의 process는 TCP에게 연결을 끊어달라 요청합니다. (이때 바로 끊어지진 않고 일련의 과정이 있습니다.)
5. HTTP client는 object를 받고 TCP를 종료합니다. 그런데 이 object에는 10개의 JPEG파일이 있는 reference를 가지고 있음을 알아내죠.
6. 위 과정을 각 JPEG image를 얻기 위해 총 10번 시행합니다.

한편으로, 웹페이지를 보여주는 과정은 안적혀있는데, 그건 브라우저의 일입니다. HTTP는 클라이언트 서버 프로세스간 메세지를 주고 받기 위한 통신 규약만 적혀있는 셈이지요.



#### 모호성

각 TCP연결 당 정확히 1개의 object만을 전달받고, 바로 연결을 끊습니다. 따라서 총 11번의 TCP connection이 필요한 셈이지요!

하지만 위 과정만으로는 판단하기엔 모호한 요소가 있죠

1) 데이터를 일련의 TCP connection으로 받았는지, 병렬로 받았는지

2) 병렬의 크기를 몇으로 설정했는지( 보통 브라우저는 5 ~ 10개의 커넥션을 생성할 수 있다고 합니다. )



#### RTT (round-trip time)

시간을 측정하기 위해선 RTT라는 단위를 정의해야 합니다.

이게 뭐냐면 작은 패킷이 클라이언트 -> 서버 -> 다시 클라이언트로 돌아올 때까지 걸리는 시간을 의미하죠. 따라서 RTT엔 packet-propagation delay, packet-queuing delay, packet-processing delay가 포함됩니다.



#### 시간 측정

![이미지 2021. 4. 25. 오후 1.36](/Users/ju/Documents/top-down-approach-network/Chapter2/resources/이미지 2021. 4. 25. 오후 1.36.jpg)

타임라인

1RTT: 3-way handshake의 최초 2파트 (connection 설립)

2RTT: 3-way handshake의 마지막 파트인 데이터 요청과 응답 (object 요청 및 받기)

time to transmit file: 파일 전송 시간



따라서 결국 간략히 말하자면 2RTT + transmission time(=received time)이 소요된다고 볼 수 있습니다. (파일 전송할때 처리 속도, 받을 때 처리속도가 동일하다 봐야겠죠.)



#### 단점

##### 1. 매 요청마다 새로운 TCP connection을 생성해야 합니다.

각 요청마다 TCP buffer 및 변수를 client, server측 각각 할당받아야 합니다. 여러 클라이언트를 받는 서버는 이것은 매우 부담이 되겠지요.

##### 2. 각 object별 2RTTs의 딜레이가 생깁니다.



### HTTP with Persistent Connections

하나의 object를 보낸 뒤 TCP연결을 즉시 끊지 않고 그 연결된 TCP로 연속적으로 request를 받고, response해주는 형태를 지닙니다.

이때 connection이 종료되는 시점은 요청이 일정시간 들어오지 않았을 때 connection이 끊기도록 되어있죠.



## 2.2.3 HTTP Message Format

메세지의 규약이 있겠죠? 메세지를 http에서 정한 형태로 보내주고, http에서 정한 방식대로 파싱을 해줘야 그 의미를 알 수 있겠죠. 결국, 매우 매우~ 핵심적인 단원이라 보시면 됩니다.

```
GET /somedir/page.html HTTP/1.1
Host: www.someschool.edu
Connection: close
User-agent: Mozilla/5.0
Accept-language: fr
```

1. message는 ASCII text로 구성되어 있습니다.

2. 5개의 라인으로 되어있는데, 각 라인 끝에는 carriage return, line feed가 있죠 (\r\n) 이보다 더 많은 라인을 가질 수 있습니다.

3. 첫번째 라인을 request line이라 부릅니다.

4. 두번째 이후의 라인을 header lines라고 부릅니다.

   각각의 의미를 살펴보면 다음과 같습니다.

   1) Host: ... => object가 존재하는 host 명

   2) Connection: close => non-persistent connection 설정

   3) User-agent: ~~ => broser type

   4) Accept-language: fr => 프랑스어 버전으로 주세요~ 요청하는 것. 서버에서 없다면 default version을 건네줌

   이 외에도 수 많은 헤더를 가질 수 있습니다.

5. method에 따라 entity body도 가지죠. 

그래서 이 양식을 보면 좀 뒤통수 맞으실 수 있습니다.

<img src="/Users/ju/Documents/top-down-approach-network/Chapter2/resources/general format of an HTTP request message.png" alt="general format of an HTTP request message" style="zoom:50%;" />

sp는 space bar를 의미하고, cr lf는 carriage return, line feed를 의미합니다. 헐랭~



METHOD를 살펴보겠습니다.

**GET**

get에선 entity body는 없습니다. 만약 작성한다해도 quey parameter로 넘어가죠. 만약에 form에 input field가 monkeys, bananas를 작성했다면 

/animalsearch?monkeys&bananas 형태로 url이 변경되어 요청됩니다.



**POST**

보통 form을 작성했을 때 post 메서드로 요청이 갑니다. 보통 검색을 한다면 form fields의 값에 따라 결과가 달라지죠.



**HEAD**

보통 디버깅할 때 사용됩니다. GET method와 유사하나, 실제 object를 받진 않습니다.



**PUT**

object를 특정 경로에 업로드하는 것을 허용합니다.



**DELETE**

web server의 특정 경로에 있는 object를 삭제하도록 해주죠.



### HTTP Response Message

request message에 대한 response message를 의미합니다.

```
HTTP/1.1 200 OK
Connection: close
Date: Tue, 18 Aug 2015 15:44:04 GMT
Server: Apache/2.2.3 (CentOS)
Last-Modified: Tue, 18 Aug 2015 15:11:03 GMT
Content-Length: 6821
Content-Type: text/html

(data data data dta data ...)
```

response message또한 3개의 부분으로 나뉘죠

1. status line
2. header line
3. entity body

로 나뉩니당.



#### 1. status line

3개의 필드로 구성되어 있습니다. protocol version, status code, status meesage로 구성되어 있죠.

status code 및 message 몇 개를 살펴보겠습니다.

* 200 OK: request가 성공했고, 그에 맞는 response가 반환되었다는 뜻입니다.
* 301 Moved Permanently: 요청한 object가 이동되었다는 뜻입니다. 그리고 새 url이 표기 되는데, Location: header에 그 새로운 url이 표시가 됩니다. 보통 클라이언트 소프트웨어에서는 자동으로 새로운 url로 이동하죠
* 400 Bad Request: request가 잘못되었다고 나타내주는 일반적인 오류 코드입니다.

* 404 Not Found: 요청한 Document가 서버엔 없다는 뜻입니다.
* 505 HTTP Version Not Supported: request message에 작성된 http version을 서버가 제공해주지 않는다는 뜻입니다.



#### 2. header line

* Connection: close => message를 보낸 뒤 tcp를 끊을 것이라 말해주는 헤더입니다.
* Date:  => http response가 언제 생성되었고 보내졌는지말해주는 헤더입니다. (object 수정시간 아닙니다.)
* Server: => 누가 이 메세지를 생성했는지 말해주는 헤더입니다.
* Last-Modified: => object가 생성되거나 수정된 날짜를 의미합니다. (Date랑 구분해야겠지요?)
* Content-Length: => object의 byte 수를 의미합니다.
* Content-Type: => object entity body의 타입을 의미합니다. 여기선 text/html 이라고 하네요.  object type은 공식적으로 확장자가 아닌 이 헤더에 의해 결정됩니다.





### 2.2.4 User-Server Interaction: Cookies

앞 헤더 부분을 이해하셨다면 정말 별거 없습니다... HTTP server는 stateless하다 했잖아요? 그런데 상태를 그래도 유지하고 싶다는 것이죠. 방법은~ 간단합니다. 

헤더에 서버가 생성해낸 값을 클라이언트에게 넘겨주고 클라이언트가 그 정보를 헤더에 담아 요청을 하도록 만들면 됩니다. 그렇다면 서버는 누가 그 요청을 보냈는지 정보를 알 수 있으니까요. 이게 쿠키입니다.

쿠키는 거기서 유래된다고 하더라구용.(정확히는 기억이 안나네요...) 쿠키 반으로 쪼개서 딸에게 준 뒤 얼굴 잊어버릴 때 쯤 만나서 서로의 쿠키를 맞대어보면 딸인지 알 수 있는 것. 그런 느낌입니다.

그렇다면 서버는 유저에게 넘겨준 값을 가지고 있어야 하니까 값을 데이터 베이스가 필요하겠죠? 더불어 상태에 대한 값도 집어넣어줘야겠구요. 이를 4가지 요소로 정리할 수 있습니다.

1. http response message의 cookie header line
2. http request message의 cookie header line
3. user의 end system에 저장되고 broswer에 의해 관리되는 cookie file
4. web site의 back-end database



상호 작용은 아래와 같습니다.

<img src="/Users/ju/Documents/top-down-approach-network/Chapter2/resources/Figure 2.10 Keeping user state with cookies.png" alt="Figure 2.10 Keeping user state with cookies" style="zoom:50%;" />

