# 2.7 Socket Programming: Creating Network Applications

이번 시간엔 직접 network application을 개발해보도록 하겠습니다.



바로 만들기 앞서서 다음 같은 사항들을 고려해봅시다.



### 고려 사항들

#### 1. 서버-클라이언트 프로그램

네트워크 내 서로 다른 두 호스트 내 프로세스가 socket을 통해 메세지를 주고 받으며 통신합니다. 그리고 한 쪽은 client 한쪽은 server가 된다.

따라서 개발자는 반드시 서버, 클라이언트 프로그램에서 소켓으로 통신할 수 있도록 개발해야 합니다.



#### 2. 공개 여부에 따른 network application 종류

네트워크 어플리케이션은 공개 여부에 따라 2가지로 나뉠 수 있죠.

##### 1) public protocol network application

HTTP처럼 공개된 프로토콜을 의미합니다. RFCs에 작성되어 있기 때문에 작성된 대로만 클라이언트 프로그램을 구현을 한다면 아무리 내가 서버를 개발하지 않았더라도 해당 프로토콜을 준수한 서버와 통신할 수 있게 되죠.

즉, 클라이언트와 서버를 독립적으로 개발할 수 있습니다.



##### 2) private protocol network  application

공개된 프로토콜이 아니기 때문에 한 개발자가 직접 둘 다 개발을 해야만 하죠. 그리고 다른 개발자는 해당 프로토콜로 당연하게도... 개발할 수 없습니다. 스카이프가 그런 예지요

그리고 RFC에 공식적으로 기입되지 않았기 때문에 공식 포트번호는 피해주는게 좋습니다.



#### 3. transport layer protocol 선택

인터넷 네트워크 위에서 통신하는 프로그램을 작성하려면 TCP, UDP 둘 중 하나를 선택해야 합니다. 특징들은 이전에 말씀드렸구요. 이 책에서는 UDP, TCP 둘 다 작성하도록 해보겠습니다.



#### 4. language 선택

C, C++, java등으로 다 개발할 수 있습니다. 이 책에서는 핵심을 잘 보여줄 수 있도록 Python으로 코딩을 할 예정입니다.



## 2.7.1 Socket Programming with UDP

### 한 번 더 상기!

1. 서로 다른 호스트에 동작하는 각 프로세스의 Socket에 message를 밀어넣거나, 받음으로써 통신한다.
2. Transport layer는 개발자가 아주 약간의 통제만을 가진다.

3. IP로 인터넷 상 호스트 주소를 결정할 수 있다.

4. PortNumber로 소켓의 Identifier를 결정할 수 있다.

   설명을 덧붙이자면, 한 호스트 내에서 소켓이 여러 개 동작할 수 있습니다. 심지어 한 프로세스 내에서도요. 따라서, 결국 어디서 데이터를 받는지 나타내는 PortNumber가 Socket의 Identifier가 될 수 있죠. 각 호수별 101호, 102호 우체통같은거라고 보시면 될 것 같습니다.



따라서 client가 server로 message를 전송할 때, server의 IP, PortNumber를 명시해줘야할 뿐만 아니라 server가 메세지를 회신할 수 있도록 client의 IP, PortNumber도 명시해주어야 하지요.

그런데 kernel에서 충분히 source의 IP, portNumber를 알 수 있으므로 application code에서 메세지를 보낼 때 address를 기입하진 않습니다. kernel에서 대신해주죠.





### 프로그램 명세

1. client가 키보드로 작성한 한 줄의 글을 읽고 서버에게 데이터를 보냅니다.
2. 서버는 데이터를 받고 그 글을 대문자로 변환합니다.
3. 서버는 변환한 데이터를 클라이언트에게 보냅니다.
4. 클라이언트는 변환된 데이터를 읽고 화면에 그 글을 출력합니다.



### 과정

### <img src="/Users/ju/Documents/top-down-approach-network/Chapter2/resources/Figure 2.27 The client-server application using UDP.png" alt="Figure 2.27 The client-server application using UDP" style="zoom: 33%;" />



server가 메세지를 일단 받아야 하므로 client가 메세지를 전송하기 전에 server 프로그램을 실행해주시면 됩니다.



### UDPClient.py

```python
from socket import *

serverName = ’hostname’
serverPort = 12000

clientSocket = socket(AF_INET, SOCK_DGRAM)

message = raw_input(’Input lowercase sentence:’)
clientSocket.sendto(message.encode(),(serverName, serverPort))
modifiedMessage, serverAddress = clientSocket.recvfrom(2048)

print(modifiedMessage.decode())

clientSocket.close()
```

주목할 부분만 설명드리겠습니다.



#### 소켓 생성

```python
clientSocket = socket(AF_INET, SOCK_DGRAM)
```

socket을 생성하는 코드인데요. AF_INET은 IPv4를 사용한다는 의미이구, SOCK_DGRAM은 UDP를 사용한다는 뜻입니다.  이 코드를 통해 IPv4, UDP를 사용하는 소켓을 생성하는 것이죠.



#### 소켓에 데이터 밀어넣기

```python
clientSocket.sendto(message.encode(),(serverName, serverPort))
```

우선 바이트로 데이터를 보내주어야 하기 때문에 메세지를 인코딩하는 과정을 거칩니다. 그리고 그 뒤엔 server의 address를 작성하죠. 이를 통해 패킷에 서버의 주소가 명시됩니다.



#### 소켓으로부터 데이터 받기

```python
modifiedMessage, serverAddress = clientSocket.recvfrom(2048)
```

오; 2048byte의 버퍼를 생성하고 이 버퍼로부터 메세지를 받습니다. 변수명에서 보시다시피, 변환된 메세지와 서버의 주소까지 받을 수 있네요. 애당초 우리가 서버 주소를 알고 보냈기 때문에 필요없긴 한데, 파이썬은 주소 값을 보내줍니다.



#### 데이터 출력

```python
print(modifiedMessage.decode())
```

역시나 서버측에서도 메세지를 인코딩하여 메세지를 전달해줬으므로, 메세지를 출력할 때 decoding 해주는 과정을 거칩니다.



#### 소켓 제거

```python
clientSocket.close()
```

이 명령어를 통해서 소켓이 제거 됩니다! 그리고 프로세스는 terminated되죠.



### UDPServer.py

```python
from socket import *

serverPort = 12000
serverSocket = socket(AF_INET, SOCK_DGRAM)

serverSocket.bind((’’, serverPort))
print(”The server is ready to receive”)

while True:
    message, clientAddress = serverSocket.recvfrom(2048)
    modifiedMessage = message.decode().upper()
    serverSocket.sendto(modifiedMessage.encode(), clientAddress)
```



#### socket binding

```python
serverSocket.bind((’’, serverPort))
```

클라이언트 코드와 달리, 눈에 띄죠? client에서는 socket에 커널에서 임의로 배정해준 포트번호가 배정이 되었지만, 여기선 12000번 포트에 딱 연결시켜주는 작업을 하네요. 아무래도 서버는 부동산 느낌이잖아요. 해당 포트넘버로 들어오는 메세지를 UDPServer 프로세스의 socket으로 전달받기 위해서 소켓을 특정 포트와 묶어줍니다.



#### client Address

```python
message, clientAddress = serverSocket.recvfrom(2048)
```

clientAddress는 서버와 달리 메세지를 전달하기 위해선 반드시 필요한 정보입니다.  서버는 어떤 IP에서, 어떤 포트 번호에서 보냈는지 알 길이 없으니까요. 따라서 서버에겐 매우~ 유용한 정보라고 할 수 있습니다.



이게 끝입니다! 되게 간단하죠? 직관적이구요. UDP는 매우~~ 간단한 기능만 구현한 서비스이기 때문이죠. 



다음 TCP 소켓 프로그래밍 구현과 비교해보시면 매우매우 큰 차이점을 체감하실 수 있을 겁니다. 바로 TCP를 알아보겠습니다.

