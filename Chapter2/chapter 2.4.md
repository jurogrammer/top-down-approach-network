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



