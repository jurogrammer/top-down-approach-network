# 클라이언트와 연결된 모든 소켓의 포트번호는 서버 소켓과 동일하다!



## 오해

오늘 말씀드릴 내용은 저에겐 가히~ 충격적이라 할 수 있겠습니다. 포트 번호에 따라 소켓이 결정된다. 라고 생각했거든요? 다시말해서 포트 번호 80번이라고 하면 거기에 해당하는 소켓 하나만이 있는것이죠.

따라서, TCP 서버에서 클라이언트와 connection을 맺을 때마다 클라이언트 소켓을 새로 생성하잖아요? 그 클라이언트들의 포트 번호가 모두 다를 겁니다. 그리고 이론적으로 resource만 충분하다면, 소켓 번호에 4바이트가 할당되므로 최대 2^16개의 소켓만을 생성할 수 있다고 생각했습니다.

하지만... 이번에 전공도서 파기를 하면서 이 생각은 완전히! 잘못됬다는 것을 깨달았네요.



### 책 내용

제가 본 책의 내용은 다음과 같습니다. 주제는 multiplexing/demultiplexing 입니다. - [Computer Networking - A top down approach, 7th]



It should now be clear how the transport layer could implement the demultiplexing service: Each socket in the host could be assigned a port number, and when a segment arrivees at the host, **the transport layer examines the destination port number in the segment and directs the segment to the corresponding socket.** The segment's data then passes through the socket into the attached process. As we'll see, **this is basically how UDP does it.** However, we'll also see that <u>**multiplexing/demultiplexing in TCP is  yer more subtle.**</u>



그래요... transport layer에서 dst port 확인 후 그에 상응하는 socket에 segment를 전달해준다. 여기까지는 알고 있는 내용 그대로입니다. 그런데!! UDP에서만 그렇고 TCP는 다르다니??? 다 똑같은거 아니였나 싶었죠... 그래서 다음 TCP 내용을 봤습니다. 정리하자면 다음과 같죠.



1. TCP의 소켓은 UDP와 달리 4가지의 identifier로 구분된다. (src IP Address, Src Port, dst IP Address, dst Port)
2. 클라이언트 A, B의 프로세스의 소켓이 80번 포트의 웹서버와 연결을 맺는다면, **HTTP request는 모두 80번 포트로 향한다.**



엥? 이 부분이 받아들이기 어렵고 충격이였습니다. 1년간 서버 호스트에서 포트번호가 다른 소켓이 생성되어 각자 연결되는 것이라 봤거든요. 따라서 테스트를 통해 실제로도 그런지 확인하겠습니다.



## 테스트 방법

### 검증하고자 하는 명제

여러 TCP 클라이언트와 연결 맺은 TCP 서버 측 클라이언트 소켓의 포트 번호가 모두 동일한 포트번호를 가르킨다.



### 절차

1. 집에 있는 컴퓨터에서 TCP서버를 가동한다.
2. 집 이외 subnet에서 TCP 클라이언트 3개를 실행한다. 
3. 각각 집에 있는 컴퓨터의 서버로 connection 요청을 한다.

4. 다음을 확인한다.

   1) 클라이언트에서 보낸 메세지가 80번 포트를 향하는지

   2) 서버의 클라이언트 소켓들이 80번 포트에 할당되어 있는지



1), 2)에서 모두 80번 포트를 향했다면, 명제는 참이된다.



확인하는 도구로는 3가지를 선택할 것입니다.

4-1)은 클라이언트에서 wire shark로 확인할 것입니다.

​	패킷 분석하는데 아주~ 유명한 툴이지요?

4-2)는 서버에서 netstat, lsof로 확인해볼 것 입니다.

​	netstat은 리눅스에서 tcp connection을 보여주는 명령어이고,

​	lsof는 list open file 의 약자로, 리눅스에서 열려있는 파일 목록들을 보여주는 명령어입니다. 리눅스에서는 소켓 = 파일로 취급하니, lsof로도 확인가능하겠지요?



## 테스트 실시

### 환경 구성

간단히 테스트하기  위해 파이썬을 이용하여 소켓 프로그래밍을 하겠습니다.



#### TCP 서버

```python
import socket
import os
from _thread import *

ServerSocket = socket.socket()
port = 12000
ThreadCount = 0
try:
    ServerSocket.bind(('', port))
except socket.error as e:
    print(str(e))

print('Waitiing for a Connection..')
ServerSocket.listen(5)


def threaded_client(connection):
    connection.send(str.encode('Welcome to the Servern'))
    while True:
        data = connection.recv(2048)
        reply = 'Server Says: ' + data.decode('utf-8')
        if not data:
            break
        connection.sendall(str.encode(reply))
    connection.close()

while True:
    Client, address = ServerSocket.accept()
    print('Connected to: ' + address[0] + ':' + str(address[1]))
    start_new_thread(threaded_client, (Client, ))
    ThreadCount += 1
    print('Thread Number: ' + str(ThreadCount))
ServerSocket.close()
```

간략히 설명을 드리자면, iterative server이구요, connection을 맺어 client socket을 반환하면, 쓰레드를 새로 생성하여 client socket을 처리하도록 하는 코드입니다. 12000포트에 바인딩을 해주었습니다.

그리고 클라이언트로 받은 메세지를 그대로 전송해주는 echo server이지요.

부수적으로 몇 개의 쓰레드가 생성되었는지도 출력합니다.





#### TCP 클라이언트

##### 코드

```python
from socket import *

serverName = 'secret'
serverPort = 12000
clientSocket = socket(AF_INET, SOCK_STREAM)
clientSocket.connect((serverName, serverPort))

while True:
    sentence = input("input message\n")
    if sentence == 'exit':
        clientSocket.close()

    clientSocket.send(sentence.encode())
    serverMessage = clientSocket.recv(1024).decode()
    print("fromServer: ", serverMessage)
```

입력받은 메세지를 그대로 서버로 전송해주는 코드입니다. 

serverName에는 host IP나 domain이 들어가야 하지만, 제 서버 컴퓨터 보안을 위해 비공개하겠습니다 ㅎㅎ;





#### 서버

![서버-연결](/Users/ju/Documents/top-down-approach-network/Chapter3/resources/서버-연결.png)

다음처럼 클라이언트 3개와 연결되어 있다고 나옵니다.

##### 클라이언트

![클라이언트 연결](/Users/ju/Documents/top-down-approach-network/Chapter3/resources/클라이언트 연결.png)

3개의 iterm에서 각각 서버와 연결을 했습니다. echo service도 정상 작동하네요.

그렇다면 hello message를 보냈을 때 실제로 12000포트번호로 hello message를 보내는지 확인해보겠습니다.





### 1. wire shark

##### 프로세스1

<img src="/Users/ju/Documents/top-down-approach-network/Chapter3/resources/클라이언트1-wireshark.png" alt="클라이언트1-wireshark" style="zoom:50%;" />



##### 프로세스2

<img src="/Users/ju/Documents/top-down-approach-network/Chapter3/resources/client2-wireshark.png" alt="client2-wireshark" style="zoom:50%;" />



##### 프로세스3

<img src="/Users/ju/Documents/top-down-approach-network/Chapter3/resources/client3-wireshark.png" alt="client3-wireshark" style="zoom:50%;" />

헉; 모두 hello message가 12000포트 번호로 전송하고 있네요...!! (보안을 위해 호스트를 특정지을 수 있는 정보는 삭제했습니다.)

wireshark로는 확인되었습니다... 그렇다면... 서버에서도 확인해보겠습니다.



### 2. netstat

header: 

| Proto | Recv-Q | Send-Q | Local Address | Foreign Address | State |



![netstat](/Users/ju/Documents/top-down-approach-network/Chapter3/resources/netstat.png)

### 3. lsof

header:

| COMMAND | PID | USER | FD | TYPE | DEVICE | SIZE/OFF | NODE | NAME |



![lsof](/Users/ju/Documents/top-down-approach-network/Chapter3/resources/lsof.png)



!!! 정말로 4개의 identifer로 소켓을 보여주고 있습니다. 첫번째는 server socket이고, 다음 3개는 client socket이라고 보시면 됩니다. src PortNumber에 해당하는 50426, 50427, 50428를 가지고 있네요.



### 결론

TCP socket은 동일한 PortNumber를 할당받을 수 있다!

4개의 identifer를 가지기 때문에 서로 다른 src IP Address, srcP ortNumber로 서버측 transport Layer에서 서로 다른 소켓으로 demultiplexing이 가능한것이지요.



### 그러면 PortNumber는 뭐지?

여태까지 이러한 사실을 모르고도 통신을 잘하고 있었잖아요? 그러면 포트 번호가 정말 무엇인지 생각해봅시다. 기존에 어떻게 생각했는지 떠올려보죠.

우리가 웹서비스를 사용하겠다! 하면 80번 포트와 connection을 맺었고, ssh로 서버와 통신하고 싶다! 하면 22번 포트와 connection을 맺었죠?

다시 말해서 **서비스**와 **포트번호**를 매칭시키고 있던 것이죠. web service가 아니라 80번 포트라고요. 즉, 포트는 서비스를 사용하기 위한 논리적인 입구의 번호라고 보시면 됩니다. wiki에서 어떻게 정의되어있는지 살펴보시죠.

In computer networking, a **port** is a communication endpoint. At the software level, within an operating system, a port is a logical construct that identifies a specific process or a type of network service. 



번역하자면, 포트는 커뮤니테이션 엔드포인트를 의미합니다. 소프트웨어 레벨에선 특정 프로세스나 네트워크 서비스를 구별하는 논리적인 구성을 의미하죠.  켁~ 그렇기 때문에 평소에 웹서비스는 80번~ 이라고 생각했군요. 

설계한 사람들 천재 아닙니까?ㅜㅜ 기가막히게 추상화시켜서 오늘 설명드린 사실을 모르고도 우리는 소켓프로그래밍을 잘하고 있었네요...



## 허나, 의심되는 것 - connection?

그런데 netstat을 쳐보면 첫 라인에 다음과 같은 글이 적혀있습니다.

Active Internet connections (w/o servers)

이 문장을 보고 그냥 지나칠 수 없었습니다.

"왜 소켓을 보여준다고 말 안하고 **connection**이라고 할까? 4개의 identifier가 있으니 **socket**이라고 불러도 될 것 같은데..."

전문성이 높아질 수록 단어 하나하나 허투루말하는 법이 없거든요.

이 질문은 다음 주제로 다루도록 하겠습니다.