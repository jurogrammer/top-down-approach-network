# 1.4 Delay, Loss, and Throughput in Packet-Switched Networks

인터넷을 서비스를 제공해주는 infrastructure로 보자 했잖아요? 그러면 end system간 서로 데이터를 주고 받는거구요. 아쉽게도... 데이터를 생각하는 것만큼 문제없이 뿅! 전달해주지 못합니다.

물리적인 제약이 존재하거든요... 자연의 법칙에 종속되어 있잖아요 ㅠ

현실에서는 패킷 전송에 딜레이가 존재하고, 심지어 패킷을 잃어버릴 수 있습니다. 또한 처리속도에도 제한이 있구요.

이번 장에서는 이러한 제한사항들을 알아보고 계산하는 방법을 알아보겠습니다.





## 1.4.1 Overview of Delay in Packet-Switched Networks

호스트 -> 라우터 -> 호스트로 도착하는 여정에서 여러 종류의 딜레이가 발생합니다.

총 4가지로 분류할 수 있죠.

1. nodal processing delay
2. queuing delay
3. transmission delay
4. propagation delay

이것의 총합이 total delay가 됩니다~

packet switching 및 computer networks를 정확히 이해하기 위해서는 이러한 딜레이를 잘 알고 있어야 합니다!



### Types of Delay

<img src="/Users/ju/Documents/top-down-approach-network/Chapter1/resource/figure 1.16 the nodal delay at router A.png" alt="figure 1.16 the nodal delay at router A" style="zoom:67%;" />



노드로부터 router A에서 route B로 패킷이 전달되는 상황을 보겠습니다.



end-system으로부터 routerA에 패킷이 도착했다 봅시다. 이때 A는 패킷을 뜯어서 적절한 outbound link router로 패킷을 전송해줘야겠지요? 이때, outbound link queue의 대기열이 없어야만 곧바로 전송될 것입니다.



뭐 이런 큰 흐름을 가지고 딜레이를 하나하나씩 알아봅시다!



#### Processing Delay

패킷의 해더를 검사하고 어디로 라우팅해줘야할 지 결정하는 시간을 의미합니다.

bit-level error을 확인하는 시간도 포함됩니다~ 말 그대로, 라우팅에서 패킷을 전달하기 위해 처리하는 시간이라고 보시면 됩니다!

보통 실제에서 소요되는 시간은  micro second 이하입니다! (ㅈㄴ빠르네요)



#### Queuing Delay

우리가 보낸 패킷보다 먼저 도착한 패킷들이 모두 전송될 때까지 대기해야겠지요? 전송될 때까지 기다리는 시간을 Queuing Delay라 보시면 되겠습니다.

실제에서 소요되는 시간은  micro seconds ~  milli second가 되겠습니다. 이것도 빠르네요;



#### Transmission Delay

FIFO queue에서 대기 중인 패킷이 모두 전송되고 나서야 우리가 보낸 패킷이 전송되겠죠?  이때 패킷이 전송되는데 걸리는 시간을 의미합니다. 

전에 설명했던 것이지만, 패킷이 L bit이고, router A -> router B로의 link의 전송률이 R bit/sec 라고 한다면 전송시간은 L/R이 되겠습니다.

다시 말하자면, 길이가 L bit인 패킷을 link로 보투 전송하는데 걸린 시간을 의미하죠.

실제에서 소요되는 시간은 microseconds ~ millisecond가 되겠습니다.



### Propagation Delay

bit가 link로 밀어 넣어지면, router B까지 전파되는데 시간이 좀 걸리겠죠? B에 도착할 때까지 걸린 시간을 propagation delay라고 합니다. 이 속도는 link의 물리적인 미디어에 달려있지요. 구리선이냐, 광랜선이냐... 같은거요.

> propagation이라고 용어를 쓴거보니... 전파. 파동과 관련된 물리학의 표현을 떠올린 것 같네요... 소리가 전파된다. 더덜.



전파되는 속도는 빛의 속도입니다. 따라서 router A -> router B의 거리가 d라면 propagation delay는 d/c가 되겠죠?

보통 millisecond가 소요됩니다.



total nodal delay = dproc + dqueue + dtrans + dprop



상황에 따라서 각 요소들은 무시될 수 있겠죠? 두 라우터간 거리가 가까우면 dprop는 거의 0에 수렴하는 것처럼요. 위성사이 통신이라면 무시할 수 없는 수준이 되겠구요..



