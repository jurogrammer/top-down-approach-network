# 2.4 DNS - The Internet's Directory Service

directory란 전화번호부라는 의미인데요, 제목은 DNS service이듯, 전화번호부 서비스가 DNS다. 라고 보시면 됩니다.

이게 뭔말이여~ 싶을 수 있는데요 ㅎㅎ; 다음 예시를 볼게요 책과는 좀 다른 예시를 들겠습니다. 

사람 A와 사람 B가 있어요. 이 둘이 어디서 만나재요. 서로 고민하다가 A가 **강변 테크노마트**에서 만나기로 했습니다. B는 당연히 알겠다고 하겠죠? 

다른 상황을 생각해볼게요 이번엔 A가 **서울 광진구 광나루로56길 85**에서 만나자고 합니다. 물론 정확한 위치긴 하지만... B가 한 번에 알아들을 수 있을까요? 서울 광진구 광나루로56길 85를 지도 서비스에서 검색해보고 강변 테크노마트구나... 라고 이해하겠죠? 

그런데 그 친구 둘이 아니라 우체국이라고 해볼게요. 강변 테크노마트라고 하는 것 보단 보다 정확한 주소가 필요하므로 서울 광진구 광나루로56길 85라고 주소를 기입해주는게 좋겠죠?

컴퓨터에서도 이와 비슷합니다.

사람 입장에서는 www.naver.com 이 더 좋은 것이고, 컴퓨터 입장에서는 223.130.195.95 라는 아이피 주소가 더 좋은 겁니당. 전화번호부에서 가게 이름 - 가게 번호로 매칭 되는 것처럼 DNS service도 hostname -  IP address 로 매칭되게 해주는 서비스지요!





## 2.4.1 Services Provided by DNS

host는 2가지 방법으로 구분될 수 있습니다. 방금 말씀드린 host name과 IP address로요. 사람은 host-name이 익숙하지만, 컴퓨터는 IP address가 필요합니다. 따라서 이를 변환시켜주는 서비스를 DNS가 제공해줍니다. (참고로 mail주소 또한 변환시켜준답니다.)



### 특징

1) DNS servers의 계층 구조로 분산 DB로 구현되어 있고

2) application layer protocol입니다.

3) 보통 dns server는 UNIX machine에 UDP, 53번 Port를 사용하는 DNS protocol이라네용





### DNS 과정

www.someschool.edu로 request message를 보낼 때 과정을 살펴보겠습니다. TCP연결을 하려면 ip를 알아야 하니깐..

1. 동일한 user machine에서 client side DNS application을 실행합니다.
2. browser가 hostname을 따와서 client sdie DNS application에 값을 입력합니다.
3. DNS server에게 query를 전송합니다.
4. DNS client가 최종적으로 response로 IP를 받습니다.
5. browser는 IP를 받았으므로 TCP connection을 시도합니다.



### DNS가 제공하는 부수적인 서비스

#### 1. Host aliasing

호스트의 별명, 별칭을 정해줄 수 있습니다. 네이버만봐도 `nslookup www.naver.com` 라고 터미널에 쳐보시면 실제 이름(canonical name)은 www.naver.com.nheos.com 입니다.

#### 2. Mail server aliasing

메일 쪽은 넘어가겠습니다.

#### 3. Load distribution

cnn.com같은 경우 요청량이 많아 서버가 많습니다. 따라서 각각 다른 end system, 다른 IP address를 가지고 있죠. 따라서 하나의 canonical hostname에 여러 IP들을 가지고 있을 수 있습니다. 

client가 매 요청할 때마다 DNS서버는 모든 IP set을 전달해주는데, 각 반응마다 순서를 로테이트를 시키면서 전달해줍니다. 그리고 클라이언트는 보통 첫번째 ip로 tcp connection을 맺으므로 요청 분산이 가능한것이죠.



## 2.4.2 Overview of How DNS Works

DNS가 어떻게 작동하는지 high-level overview로 알아보겠습니다. DNS는 복잡한 시스템이라 이 책에서 자세히 알아보기엔 한계가 있거든요. 이 설명은 host name을 IP로 바꿔주는 서비스에 초점을 두겠습니다.



DNS client로 hostname의 ip를 알려달라고 query를 보내면 어찌고 저찌고해서 DNS server의 response를 받을 수 있습니다. 즉, 내부 과정은 black box로 잘 캡슐화되어 있습니다.  사실 내부는 전세계적으로 분산된 수많은 DNS server로 구성되어 있습니다. 그리고 DNS application layer protocol에서 dns server와 querying host가 어떻게 커뮤니케이션해야 하는지 정의되어 있죠,



### basic-design

아주 간단한 DNS 디자인을 생각해보겠습니다. 지금은 여러개 분산되어 있지만, 심플하게 생각하기 위해 단 하나의 DNS서버가 있다고 볼게요.그러면 모든 사용자들은 host-name의 ip를 알고 싶다면 이 dns 서버에 바로 요청을 보내 응답을 받으면 됩니다. 매우 간단하죠?

하지만 이 간단한 구조는... client 수가 너무 많아서 문제가 됩니다. ㅠㅠ

다음의 문제를 가지고 있죠

##### 1. a single point of failure

단일 서버가 고장나면 모든 인터넷이 crash나죠..

##### 2. Traffic volume

단일 서버가 그 수많은 DNS 쿼리를 수용해야 합니다. 뜨학!

##### 3. Distant centralized database

거리 때문에 가까이 있는 host는 재빨리 응답받고, 멀리있는 호스트는 늦게 응답받겠죠. 혼잡도도 그만큼 증가하겠구요.

##### 4. Maintenance

모든 인터넷 호스트의 records를 가지고 있어야 하므로 database가 엄청 크고 매우 빈번히 정보가 업데이트될것입니다.



이러한 문제점들 때문에 결국 distributed design을 선택하게 된것이죵.이러한 면 때문에 DNS는 인터넷에서 어떻게 분산 데이터베이스를 구현해야할 지에 대한 완벽한 예제가 되기도 합니다.



### A Distributed, Hierarchical Database

scale문제 때문에 계층적 구조를 지니고 있습니다.  각 층마다 가지고 있는 정보가 제한되어 있죠. 위에서 보았듯, 한 서버가 모든 데이터를 가지고 있으면 여러 문제점들이 발생하니까요. 따라서 총 3개의 계층으로 나누어져 있습니다. root DNS servers, top-level domain(TLD) DNS servers, authoritative DNS servers

<img src="/Users/ju/Documents/top-down-approach-network/Chapter2/resources/Figure 2.17 Portion of the hierarchy of DNS servers.png" alt="Figure 2.17 Portion of the hierarchy of DNS servers" style="zoom:50%;" />

Roots는 top level domain의 TLD servers IP address를 반환해줍니다.

그리고 client는 TLD server에게 host name에 해당하는 ip를 물어보는 형태입니다.



#### 3 class DNS servers

##### 1. Root DNS servers

2016년 기준으로 400개의 root name servers가 존재한다고 합니다. 그리고 그 root dns server들은 13개의 운영 기관에 속해있지요.

##### 2. Top-level domain(TLD) servers

각 top-level domains들은 다음과 같습니다. com, org, net, edu, gov, kr...등이 있죠. TLD를 뒷받쳐주는 인프라는 매우 복잡합니다...

TLD server들은 authoritative DNS severs의 IP를 제공해줍니다.

##### 3. Authoritative DNS servers

공개적으로 접근할 수 있는 호스트 이름들은 모두 authoritative DNS servers에 존재한다고 보시면 됩니다. 얘가 그 담당이거든요. 그리고 각 조직들은 자신들의 authoritative dns server의 구현을 할 수 있죠. 대부분 대학교나 큰 회사들은 자신들의 메인, 백업 authoritative DNS server를 운영한다네요.



#### Local DNS server

그리고 이외에도 1가지 중요한 DNS server가 있습니다. 바로 local DNS server입니다. 비록 엄밀히 말하여 3 class엔 속하지 않으나 매우 중요한 역할을 수행하고 있죠. 보통 ISP에 의해 관리됩니다. local host DNS server는 매우 가까이 위치하고 있으며, 클라이언트의 dns query들은 이곳으로 요청되어진다고 보면 됩니다.

요놈은 프록시 역할도 하죠.



여튼 함 과정을 살펴보겠습니다.

#### hostname-to-ip까지 query과정

client-host: cse.nyu.edu

알고 싶은 호스트: gaia.cs.umass.edu

gaia.cs.umass.edu가 어떻게 ip로 변환되는지 알아보겠습니다.

<img src="/Users/ju/Documents/top-down-approach-network/Chapter2/resources/Figure 2.19 Interaction of the various DNS servers.png" alt="Figure 2.19 Interaction of the various DNS servers" style="zoom:50%;" />



1. client host에서 dns.nyu.edu라는 local DNS server로 gaia.cs.umass.edu의 ip가 뭔지 message를 보냅니다. (또는 DNS request)
2. local DNS server는 Root DNS server에게 해당 query meesage를 전송합니다.
3. Root DNS server는 **edu** suffix를 보고 TLD servers의 IP list를 응답으로 전달해줍니다.
4. local DNS server는 응답받은 TLD servers 리스트를 보고, 다시 요청을 보냅니다.
5. TLD servers는 **umass.edu** suffix를 보고 authoritative DNS server를 전달해줍니다.
6. 마지막으로 Local DNS server는 authoritative DNS server에게 query message를 전송한 뒤 IP를 얻습니다.
7. 그리고 그 IP address를 전달해줍니다.



이 과정에서 총 8번의 DNS message를 주고 받았습니다... query 4번, response 4번이요! 그런데... TLD서버가 모든 authoritative DNS server를 알지 못할 때도 있어서 인접한 DNS server에게 물어보기도 합니다. 

예를 들면 dns.umass.edu가 아니라, 각 부서 별로 또 DNS server를 가진다고 합시다. 그리고 그 host의 끝은 cs.umass.edu로 되죠. 그렇다면 local DNS host였던 dns.nyu.edu가 dns.cs.umass.edu를 받고 다시 한 번 쿼리를 날리겠죠... 계층이 결국 하나 더 생긴다고 보면 됩니다. 그러면 message를 10번 주고 받게되는 것이죠



한편으로 그림에서 보이듯, query의 종류는 2개로 구분되어 질 수 있습니다.

#### 1. recursive query

local DNS server가 client DNS를 대신하여 query한다는 점에서 recursive query라 부릅니다.

#### 2. iterative query

모든 query message의 response가 local DNS server에게 전달되므로 iterative query라고 부르죠

> 왜 recursive, iterative 라고 지었는지는 자세히 안나옵니다. 위 설명들도 정의라기 보단 상황에 따른 구분이라고 보는게 맞을 것 같네요...  30분정도 찾아봤는데 안나오네용 . DNS 구조에 대해 더 자세히 알아야만 나올 답이라 패스하겠습니다.



### DNS Caching

위 과정은 모두~ caching을 무시한 과정입니다. 요청을 하고 응답을 받을 때마다 그 메세지들을 local memory에 저장합니다. 그리고 영구적이진 않으므로 보통 2일을 주기로 caching된 값은 버려집니다.

요청을 받았을 때 local DNS server에 root dns의 message가 캐싱되어 있다면 root dns에 쿼리를 안보낼 수도 있습니다. 그래서 보통 root DNS server로 트래픽이 적은 편이지요.

