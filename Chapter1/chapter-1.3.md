# 1. NetworkCore

end-system끼리 연결시켜주는 networkCore에 대해 알아봅시다~

개념도로는 다음과 같습니다.



<img src="/Users/ju/Documents/top-down-approach-network/Chapter1/resource/IMG_F07B16A6D0E8-1.jpeg" alt="IMG_F07B16A6D0E8-1" style="zoom:40%;" />



## 1.3.1 Packet Switching

* packet switch와 packet switching은 다른 의미네요.
* packet switch는 router, switch처럼 패킷을 전달해주는 device를 의미합니다. packet switching은 그 작업을 의미하구요.



네트워크의 application들은 messages를 서로 주고 받습니다. messages는 어떤 것이든 될 수 있구요.

source -> destination으로 보내기 위해선 packet이라는 단위로 쪼개서 보내야 합니다! 이 packet은 communication link(광섬유, 구리선...) ~ packet switches를 돌아다니죠.



데이터 보내는 속도는 **link의 최대 전송률(transmission rate)**을 따릅니다~ 따라서, 보낼 packet의 bit크기가 L bits 이고, 전송률이 초당 R bits면 packet을 보내는 시간은  L/R sec가 되겠습니다~

> 헉, 패킷 스위치에 달린게 아니라 link에 달려 있구낭.. 그런데 한편으로는 라우터에서 1gbps가 아니라 100mbps 랜선지원해주는게 있던뎅. 라우터가 지원해줘야할 듯!

### Store-and-Forward Transmission

대부분의 packet switch는 links에 packet을 집어넣을 때, buffer가 존재합니다. 한 패킷을 모두 받아야만 그때서야 전송하죠!



Source ---- packet switch ----- Destination



#### 속도 계산 문제

##### Q. S -> D까지 L bits 패킷 1개를 처음으로 전달할 때 얼마나 걸릴까요?

**가정**

* 전송 딜레이 없음
* link에서 즉시 전송

위처럼 구성되어 있고, source에서 link를 통해 패킷 스위치에 보내는 전송률이 R bits/sec 라면,

1. 첫 패킷이 패킷 스위치에 모두 전송될 때까지 L/R sec 소요
2. 모두 전송되었으므로 이 직후에서야 데이터를 받음



모두 전송될 때까지는 2L/R sec 소요



##### Q. 3개의 패킷을 모두 보내는데는?

4L/R sec

첫번 째꺼 보낼 때 두번째꺼 전송받고 있음.



### Queuing Delays and Packet Loss

#### output buffer

각각 패킷 스위치에 연결된 link마다 ouput buffer(output queue)를 지니고 있습니다. 이 버퍼의 역할은 전송할 패킷을 보관하는 역할을 하죠. 이것이 바로 패킷 스위치에서 중요한 역할을 맡습니다!

패킷 스위치에서 한 링크의 전송률이 15Mbps고, 이 link로 A,B Source에 연결된 link에서 비트를 100mbps로 받는다고 생각해보세요.

output buffer를 소비하는 속도가 못따라가니 output buffer가 점점 차겠죠?

##### output buffer에 패킷이 존재할 때 나올 수 있는 상황

* 도착하는 패킷은 버퍼에서 **기다릴 수 밖에** 없죠. 

  앞 패킷이 모두 전송되어야 하니, 자기보다 앞선 패킷의 데이터 크기 / R sec가 **queuing delay**가 될 수 있습니다!

* 버퍼가 **꽉 차** 있을 수도 있습니다!

  방금 도착한 **패킷이 손실**되거나 큐 안의 패킷이 손실될 수 있습니다. ㄷ ㄷ ㄷ



> congestion: 패킷이 큐에 대기해야하는 상태. 교통 혼잡 생각하면 될 듯! 혼잡하다 -> 기다려야 한다잖어~



### Forwarding Tables and Routing Protocols

와나~ 패킷 스위치가 패킷을 받으면 어떻게 어떤 output link로 전달할지 알까?! How does the router determine which link it should forward the packet onto?

결론: 컴퓨터 네트워크 타입마다 다르게 포워딩함. ㄷㄷ

#### Internet

모든 end system이 IP address가지고 있음. 따라서 S(source)가 D(destination)의 IP주소를 패킷 header에 작성함.

우편 주소처럼 계층적 구조를 지닙니다. (홀링?)

패킷이 network에 있는 라우터에 도착하면 이 라우터는 packet에서 D를 보고는 인근 라우터로 넘겨줍니다! 다시 말해서, **라우터마다 forwarding table을 지니게 되죠!**

forwarding table의 값은 D의 주소에 맞는 router의 outbound link로 맵핑시켜주도록 구성되어 있습니다. 즉, D의 ip 주소가 indexing역할을 합니다.



아뉘~ 어떻게 table 구성하냐고~~ 는 감칠맛나게 Chapter5에서 설명해준답니다.ㅜ 더욱 자세한 내용은 Chapter5에서!



## 1.3.2 Circuit Switching

중요도가 떨어지는 것 같아 대략 넘어가겠습니다.

패킷을 쪼개서 인터넷에 보내서 패킷이 부분적으로 처리되기를 바라는 방식과 달리, Circuit Switching은 S에서 D까지 link를 예약하고 사용하는 방식입니다. 전통적인 전화가 이러한 방식으로 구성되어 있다고 합니다.



### Multiplexing in Circuit-Switched Networks

좀 신기해서 적어봅니다. 어떻게 한 번에 한 회로에 여러 end에서 보낸 데이터를 전송할 것이냐 같습니다. 두 가지 방식이 있습니다.

1. frequency-division multiplexing (FDM)

   주파수 대역을 분할하여 전송 전화로 치자면 4kHz로 나눈다네요. 

   와~ 라디오 생각해보면 ~~메가헤르즈!방송 이러잖아요 소리가 잘 들리는 곳 사이에 채널을 두면 소리가 지지직, 곂쳐서 나오구요! 홀리씻! 잡음이라함은 결국 내가 원하는 데이터의 주파수만을 받을 수 있는 주파수에(아마 범위로 받겠죠?) 두지 않아서 다른 주파수 대역의 데이터(bit)도 받아서 1101010101010을 소리로 변환하는 과정에서 잡음이 들리는게 아닌가 추론할 수 있겠습니다. 개꿀잼~

   

2. time-division multiplexing(TDM)

   좀; 괴랄한 방식같습니다. 시간으로 분할해서 데이터를 받는대요; 너무 타임 크리티컬하네용; 다시 말해서 4개의 end에서 보낸다고 한다면, 시간을 일정 간격으로 1,2,3,4 나눕니다. 1초로 나눈다고보겠습니다.

   정확히 첫 데이터 도착부터 4초동안 데이터를 수신한다면 0~1초는 A꺼 1~2초는 B꺼, 2~3초는 C꺼, 3~4초는 D꺼가 되겠습니다.

### 단점

데이터 안보내는 중에 회로가 유휴(idle)상태를 가집니다. 아까웡~ 계속 써야 되는디



### Packet Switching VS Circuit Switching





* packet switching은 실시간 전송에 적합하지 않다. ex:)전화
* packet switching은 좀 더 나은 전송 용량을 제공한다, 좀 더 쉽고 효율적이고 비용이 적게 나간다!



어느 면에서 효율적이라는 거여?

link: 1Mbps

users: 100kbps 보내고 싶음



circuit switching에서 TDM을 쓴다면, 반드시 10명의 유저가 전송해야 함.(잘라서 보내야 하니깐.) 따라서 동시에 10명한테만 서비스 제공가능. 

packet switching에서 특정 시간에 1명의 유저가 패킷 보낼 확률이 0.1(1/10)이라고 볼게요. 35명의 users가 있고, 11명, 또는 그 이상이 패킷을 보낼 확률은 어떻게 될까요? 대략 0.0004입니다!

> 35명의 유저 중 x명이 동시에 사용할 확률 => 35Cx(0.1)^x(0.9)^(35-x)<img src="/Users/ju/Documents/top-down-approach-network/Chapter1/resource/스크린샷 2021-04-06 오후 9.54.26.png" alt="스크린샷 2021-04-06 오후 9.54.26" style="zoom: 67%;" />

그래서 35명일 때 동시에 10명이하가 쉽게 사용할 수 있는 확률이 0.9996이라는 계산이 나오죠! 35명이 쓸수 있다는 얘기네용.

 

## 1.3.3 A Network of Networks

ISP를 통해서 end system이 연결된다고 배웠죠? 이번에는 ISP가 어떻게 구성되어 있는지 알아보자는 겁니다.

ISP를 KT,SKT 등으로 생각할 수도 있지만, 작게는 학교, 회사로도 볼 수 있어요! 거기에 연결된 컴퓨터가 인터넷을 쓸 수 있잖아요?

### 대빵(tier-1) ISP

그런데 그런 ISP들이 서로 다 연결하려고 해봐요;  ISP가 한 둘이 아닐텐데 거기에 다 연결해야 한다니... 엄청난 비용이 아닐 수가 없죠?

그래서 생각할 수 있는 구조가 facade패턴마냥 모두 연결되어 있는 대빵 ISP가 있다고 보는 거에요.

대빵 ISP가 모두 연결하려고 비용을 얼마나 투자했겠어요. 당연히 돈 받아내겠죠. 여기서 provider, customer관계가 생깁니다.



### regional ISP

현실적으로 대빵 ISP가 어떻게 모두 연결되어 있겠어요; 각 나라마다 기지국을 다 가지고 있을까요. 아닙니당. 그래서 보통 regional ISP가 존재하고, 그 regional ISP가 대빵 ISP랑 연결되어 있는 형태이지요.

계층적으로 구성되어 있어요.



더 나아가서, 대빵 ISP가 하나만 있진 않습니다. 그리고 얘네들끼리 연결되어 있는 구조이지요.



### IXP

대빵보다 아래에 있는 ISP_a, ISP_b가 있다고 봅시다. 얘네들이 대빵들 통해서 연결되면 돈 받으니깐 서로 직접적으로 연결되길 바랄 수 있겠죠? 그래서 IXP라는 것이 등장합니다. Internet Exchange Point라고 해서 서로 연결될 수 있도록 도와주는 것이지요.



### Content-provider network

마지막으로는 content-provider network라는 것이 등장합니다. 데이터를 제공해주는 업체?인가봐요. 자세한 설명은 안되어 있네요. 현재는 이 content-provider network와 모두 연결되어 있다고 합니다.

그래서 구조는 다음과 같습니다.

<img src="/Users/ju/Documents/top-down-approach-network/Chapter1/resource/스크린샷 2021-04-10 오후 12.25.53.png" alt="스크린샷 2021-04-10 오후 12.25.53" style="zoom:50%;" />

