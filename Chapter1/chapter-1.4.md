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

<img width="334" alt="figure 1 16 the nodal delay at router A" src="https://user-images.githubusercontent.com/57349658/114261301-c6c8bd80-9a14-11eb-8fab-bfa16229bc58.png">



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



## 1.4.2 Queuing Delay and Packet Loss

### Queuing Delay

큐 딜레이는 컴퓨터 네트워크에서 엄청 중요한 부분이라 수많은 논문들이 존재합니다. 따라서 여기서 다 다룰 순 없기에 간략히 설명드리도록 하겠습니다. 한편으로 queue의 길이가 무한하다는 가정하에 설명드리겠습니다.

직관적으로 생각해봅시다.

큐 딜레이는 트래픽이 커질수록 점점 커지겠죠? 처리받으려고 앞에 대기 중인 패킷들이 많아질 것 아니에요? 그렇다면 트래픽의 정도를 어떻게 정의할 수 있을까요? 트래픽을 떠올리면 뭔가 막히는 느낌이 들잖아요. 큐에 기다리게 만드는 요소들을 한 번 떠올려 봅시다.

1. 패킷이 반드시 queue에 도착하진 않을거에요. 확률적으로 특정 큐에 들어오겠죠.  그 평균 비율을 `a`라고 합시다.

2. 패킷의 크기가 클수록 처리하는데 시간이 걸리겠죠? 따라서 패킷의 크기 `L` 을 들 수 있습니다.
3. 처리하는 속도가 느리다고 해봐요; 그러면 또 대기하는 시간도 늘어날 수밖에 없겠죠. 따라서 처리율 `R` 을 들 수 있습니다.

정리하자면 다음과 같습니다.

` Traffic intensity =  L*a/R`

> L*a 는 평균적으로 큐에 들어오는 bit의 비율이라고 볼 수 있겠죠?



만약에 La/R > 1 이면 처리하는 속도가 못따라는 것이니 큐가 길어지겠죠. 

La/R <= 1이면, 큐에 La bit만큼 온다면 대기 시간이 없겠죠.

따라서 traffic engineering에서 황금률은 La/R <= 1 로 디자인하라~ 입니다. ㅎㅎ



평균 대기시간을 고려해볼게요.

N개의 패킷이 (L/R)N seconds 마다 큐에 도착한다고 보면, 마지막 패킷은 (N-1)L/R 만큼 기다려야겠지요? 그래서 평균 대기시간과 La/R의 관계를 수식으로 구해보면 다음과 같은 그래프를 나타낼 수도 있습니다.



<img width="359" alt="figure 1 18 Dependence of average queuing delay on traffic intensity" src="https://user-images.githubusercontent.com/57349658/114261310-d0522580-9a14-11eb-80a8-875129cfaafa.png">



La/R이 1에 가까울수록 평균 대기시간이 확 늘어나는 형태이지요.



### Packet Loss

현실에서는 queue의 길이가 무한하지 않습니다. 비용과 관련되기 때문이고, 물리적으로도 말이 안되죠; 그래서 패킷이 라우터에 도착했는데 큐가 꽉차있다면 어떻게 될까요?

라우터는 패킷을 버립니다. 즉, 패킷이 유실되는 것이죠. 헐랭~ 

그리고 트래픽이 증가할 수록 손실율은 커집니다. 큐에 대기하는 패킷이 많이지기 때문이겠죠. 그래서 노드의 성능을 측정할 때는 딜레이 뿐만 아니라 패킷 손실율도 그 대상이 될 수 있습니다.

그리고 이렇게 패킷이 손실될 수 있으므로 end-to-end간 통신에서는 패킷을 재전송할 수도 있습니다.



## 1.4.3 End-to-End Delay

여태까지 nodal delay에 대해서 얘기했습니다. 즉, 단일 라우터에 대한 딜레이를 얘기했죠. 여기선 source - to - destination간 총 지연시간을 논의해보겠습니다.

이를 논의하기 위해 다음 상황을 가정하겠습니다.

1. S -> D 간 N-1개의 라우터가 존재한다.

2. network는 한산하다(uncongested) 즉, 큐잉 딜레이를 무시할 수 있습니다.

3. 각 라우터의 processing delay는 dproc이다.

4. 각 라우터의 transmission rate는 R bit / sec 이다.

5. link의 propagation delay는 dprop이다.

6. nodal delays는 축적되며, end-to-end delay를 줄 수 있다.

   ```
   dend-end = N(dproc + dtrans + dprop)
   ```



### Traceroute

end-to-end 딜레이를 확인하기 위해 traceroute프로그램을 사용할 수 있습니다.

작동원리는 다음과 같죠.

특별한 패킷이 있습니다. (아마 이에 대한 규약이 있나봅니다.)

1~N으로 넘버링을 하고 S에서 D로 패킷을 보내요. 1번째 라우터는 outbound link에 연결된 라우터에 패킷을 전송하지 않고 바로 S로 보냅니다. 그러면 시간 측정할 수 있겠죠? 2~N개 시간도 무시할 수 있구요.

그리고 2번째 라우터에서도 동일하게 2번째 패킷을 다음 라우터에 보내지않고 S로 보내죠. 최종적으로 D는 S에게 전송합니다.

이렇게 해서 시간을 측정할 수 있어요. 책에서는 3회 반복하여 총 3*N개의 패킷을 S에서 D로 전송했네요.

여기서 주목할만한 점은 queuing delay가 시간에 따라 다르기 때문에 6번째 라우터에 보낸 패킷이 7번째 라우터에 보낸 패킷보다 늦게 도착할 수 있다는 점이죠. ㅎㄸ



### End System, Application, and Other Delays

앞서 언급한 delay말고도 end system에서는 다른 종류의 delay가 존재할 수 있습니다.  예를 들어

wifi나 모뎀같은 곳으로 패킷을 보내는데 기다릴 수 있구요(요놈들의 프로토콜이 그렇게 정해져서...)

다음으로는 미디어를 패킷으로 만드는 과정의 딜레이가 존재할 수 있어요. VoIP서비스가 그렇죠. 인코딩되고 디지털화된 목소리를 패킷으로 만들어야하죠. 이 처리시간은 VoIP서비스에 큰 영향을 끼칠수 있죠.







### 1.4.4 Throughput in Computer Networks

End - to - End 간 데이터 처리율 관점에서 살펴볼게요. 처리량이 뭐냐구요? 파일을 전송합니다. 보통 초당 얼마나 보내지는지 표시가 되잖아요? 이 처리량에 대해 알아보자는 겁니다.평균 처리량은 파일의 크기 F, 호스트 A에서 B로 전송하는데 걸리는 시간 T를 나누 값 F/T가 되겠습니다.

```
average throughput = F/T bits/sec
```

이 평균 처리량에 영향을 주는 요소가 무엇이 있을까요?

<img width="338" alt="figure 1 19 Throughput for a file transfer from server to client" src="https://user-images.githubusercontent.com/57349658/114261318-d6e09d00-9a14-11eb-93af-bde7f0f78e8e.png">

이 그림을 볼게요.

Rs는 server -> router link의 전송률, Rc는 router -> client link의 전송률을 의미합니다.

아무리 Rs가 높더라도 Rc가 낮다면 결국 Rc를 따르게 되겠죠?  그리고 Rc가 아무리 높더라도 Rs가 낮으면 Rs를 따르겠죠. 따라서 결국 처리율은 min{Rs,Rc}가 되겠죠.

그래서 예상 시간 T = F/min{Rs,Rc}라 할수도 있겠습니다.



Figure b의 평균 처리량은 min{R1,R2....RN}이 되겠죠.



현대의 네트워크

<img width="278" alt="Figure 1 20 End-to-end throught" src="https://user-images.githubusercontent.com/57349658/114261331-ecee5d80-9a14-11eb-9c81-2e7ea5381e4d.png">



a는 뭐 동일한데요, b도 살펴봐야 합니다.

common link라는게 존재하나봐요. 그래서 이녀석의 전송률도 고려해야 합니다. 

Rs = 2Mbps, Rc =1 Mbps 이고 common link의 R = 5 Mbps라고 할게요. 10개의 서버가 10개의 클라이언트에게 데이터를 보낸다고 하면 어떻게 될까요?

균등하게 전송률을 나눈다는 가정하에 평균 처리량은 500kbps가 되게 됩니다. 5 Mbps를 10개로 나눠가지니까요.

그래서 아무리 전송률이 높은 link가 있다하더라도, 이게 Bottleneck link가 될 수 있습니다.
