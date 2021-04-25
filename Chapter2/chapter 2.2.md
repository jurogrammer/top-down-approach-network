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





