- computer network는 두 end system 간의 데이터 전송 thorughput을 제한함
- packet을 전송하는 과정에서 packet loss가 있을 수 있음

## 1.4.1 Overview of Delay in Packet-Switched Networks
- 패킷이 한 노드(host or router)에서 다른 노드로 옮겨가는 과정에서, 여러 종류의 지연이 발생될 수 있음
	(예) nodal processing delay, queuing delay, transmission delay, propagation delay ...
- total nodal delay = 발생한 여러 유형의 delay를 모두 합쳤을 때의 delay

### Types of Delay
![](https://i.imgur.com/qCsP4aH.png)

#### Processing Delay
- packet의 헤더를 읽고 packet을 어디로 전달할지 결정하는데 소요되는 delay ( = Nodal Processing)
- upstream을 통해 전달된 packet의 bit-level errors를 체크하는데 소요되는 시간도 포함됨
- processing delay 후 (after nodal processing delay) router B를 향해 가는 링크와 연결된 queue로 패킷을 전달함

#### Queuing Delay
- link를 통해 전달되기 전 packet은 queuing delay를 가짐
- queuing delay는 이미 queue에 들어와 있는 패킷들의 수의 영향을 받음
- queue가 비어있고, link를 통해 현재 전달되고 있는 패킷이 없다면 → queueing delay = 0
- 반대로 queue에 여러 패킷이 대기중이며 link를 통해 패킷이 전달되고 있다면 → long queuing delay

>[!question] packet switching에서는 모든 패킷이 수신된 후에야 전송이 시작되는데, 첫번째로 Queue에 도착한 패킷이라도 queueing delay != 0일 수 있는거 아닌가?
>No! '모든 패킷이 수신된 후에야 전송이 시작 (Store-and-Forward)' 된다는 것은 데이터를 구성하는 여러개의 패킷 모두를 라우터가 수신한 후에 전송한다는 의미가 아니라 **패킷 1개를 구성하는 프레임이 모두 전송되었을 때, 즉 패킷 1개가 온전히 수신되었을 때 다음 링크로 전송**한다는 의미! 따라서 패킷의 관점에서는 빈 queue에 모든 패킷이 수신되었을 때, 바로 다음 링크로 transmit할 수 있는 것

#### Transmission Delay
- 패킷의 모든 비트를 다음 링크로 전송하는 데에 걸리는 시간
	- 💡 비트 내보내기 자체에 걸리는 시간, 다음 destination 까지 전파를 타고 전송하는데 소요되는 시간은 포함하지 않음
- packet 1개가 L bits로 구성되어 있고, routerA에서 routerB로 보내는 transmissoin rate가 R bits/sec라면, **transmission delay = L / R sec**

#### Propagation Delay
- link를 향해 패킷이 내보내기되고, destination 까지 패킷이 전송되는데 소요되는 시간
- link의 매질에 따라 propagation delay가 달라질 수 있음
- propagation delay = distance between 2 routers / propagation speed (거=속x시)

>[!info] Transmission delay vs Propagation delay
>**Transmission delay**
>router가 packet을 밀어내는데에 소요되는 시간. 패킷의 길이, ==**링크의 transmission rate**==에 의존함. 두 라우터 사이의 거리에는 영향을 받지 않음
>
>**Propagation delay**
>출발 라우터에서 도착 라우터로 도착하기까지의 시간 (after transmission delay). 두 라우터 사이의 거리에 영향을 받으나, 패킷의 길이, 링크의 transmission rate에는 영향을 받지 않음

#### Total Nodal Delay
$$
d_{nodal} = d_{proc} + d_{queue} + d_{trans} + d_{prop}
$$
- $d_{prop}$ 은 두 라우터 사이의 거리가 가까운 경우 무시해도 될 정도로 작은 수준
- 그러나 두 라우터 사이의 거리가 멀 경우 $d_{prop}$ 이 $d_{nodal}$에서 차지하는 비율이 커질 수 있음
- transmission rate가 큰 경우 $d_{trans}$는 무시해도 될 정도로 작은 수준
- 그러나 low-speed dial-up modem link와 같이 transmission rate가 작은 경우 $d_{trans}$는 커질 수 있음
- $d_{proc}$은 일반적으로 매우 작은 수준이며, router의 maximum thorughput에 영향을 주는 요소
	- ❓ maximum thorughput : 라우터가 패킷을 **==forwarding==** 할 수 있는 최대의 rate


<hr>


## 1.4.2 Queuing Delay and Packet Loss
### Queuing Delay
- queuing delay는 패킷에 따라 달라질 수 있음
	(예) 10개의 패킷이 queue에 동시에 도착했다고 할 때, 첫번째로 queue에 들어간 패킷은 queuing delay가 없지만 마지막으로 도착한 패킷은 첫번째 패킷에 비해 큰 queuing delay를 가짐

- 따라서, queuing delay를 수치적으로 정의하기 위해서는 constant한 단일 수치보다는 통계적 수치를 활용
	- average queuing delay
	- variance of queuing delay
	- probability of queuing delay

- queuing delay를 결정하는 요소들
	- 트래픽이 queue에 도착하는 rate
	- link의 transmission rate
	- 트래픽이 burst하게 도착하는지, periodically하게 규칙적으로 도착하는지의 여부 등

#### Traffic Intensity
- settings
	- queue에 평균적으로 도착하는 초당 패킷의 개수 : a (units of packets/sec)
	- queue에서 내보내지는 패킷의 transmission rate : R bits/sec
	- 패킷을 구성하는 비트의 개수 : L bits

- queue에 평균적으로 도착하는 초당 비트의 개수 : La bits/sec
- **==(queue의 용량이 무한정 커진다고 할 때) traffic intensity : La/R sec==**
	- queueing delay와 비례함

- if La/R > 1
	- queue에 초당 도착하는 비트의 수가 transmission rate를 초과함
	- queue는 무한정 커짐
	- 따라서 traffic intensity가 1보다 작도록 설계되어야 함

- if La/R ≤ 1
	- L/R 초당 1개의 패킷이 periodically 도착한다면
		- 모든 도착하는 패킷은 empty queue로 들어가게 됨. queuing delay = 0
	- 패킷들이 burst but periodically 도착한다면
		- 첫번째 패킷은 queuing delay가 없지만 두번째 패킷은 L/R초의 queuing delay를 가짐
		- n번째 패킷은 (n-1)L/R초의 queuing delay를 가짐
		- queuing delay의 평균 : $\frac{\sum_{k=1}^{n}{(k-1)L/R}}{n} = (n-1)L/(2R)$

- La/R 값만으로는 queuing delay statistics를 모두 표현할 수 없음
![](https://i.imgur.com/tr7B3aB.png)
>[!example] Example 1
>traffic intensity La/R ≒ 0 이라면
>**패킷들은 드물게 queue안에 대기중인 패킷을 발견하므로 average queuing delay는 0과 비슷한 수준**

>[!example] Example 2
>**traffic intensity가 1과 가까워질수록 average queuing delay는 무한히 증가함**
>traffic intensity La/R에 가까워진다면  La > R 인 시점이 발생할 것이고 (burst하게 도착) queue의 길이가 길어지며 queuing delay가 발생함. La < R 인 시점에 queue의 길이가 줄어듦

### Packet Loss
- 현실에서의 queue는 용량이 유한하기 때문에, traffic intensity가 1에 도달했을 때 queue가 무한히 커지지 않고, 초과하는 수의 packet을 drop하며 full queue상태가 됨 (= packet loss)
- lost packet의 비율은 traffic intensity에 비례함
- performance of node는 queuing delay 뿐만 아니라 packet loss의 비율로도 평가될 수 있음
- 목적지에 모든 패킷이 완전하게 전송되는 것을 보장하기 위해 lost packets들은 재전송됨


<hr>


## 1.4.3 End-to-End Delay
- single router가 존재하는 상황에서의 delay를 넘어, source와 desitnation사이에 여러 라우터가 존재하는 상황을 가정해보자.
- setting
	- N-1 routers between source and destination host (라우터 N-1개 = link N개)
	- network is uncongested
	- 모든 host와 라우터의 transmission rate : R bits/sec

- $d_{end-to-end} = N(d_{proc} + d_{trans} + d_{prop})$
	- 이 때, $d_{trans}$ = L / R (L is the packet size)


<hr>


## 1.4.4 Throughput in Computer Networks
- delay, packet loss외에도 네트워크 성능 평가를 위해서는 end-to-end thorughput을 고려해야 함
- **instantaneous throughput** : host B가 순간적으로 수신한 파일의 비트 (in bits/sec)
	(예) Internet telephony는 낮은 delay와 임계치 수준 이상의 instantaneous thorughput을 필요로 함. 처리율이 아주 높기보다는 일정 임계값 수준 이상을 안정적으로 유지하는 것이 중요
	(예) file transfer는 delay는 크게 고려 요소가 아니며, 가능한 한 높은 thorughput을 가지는 것이 필요

- **average thorughput** : 파일이 F bits로 구성되어 있고 host B가 파일을 모두 수신하는데 T초가 소요되었다면, average thorughput = F/T bits/sec

>[!example] Example 1
>![](https://i.imgur.com/2b2k5hW.png)
>**settings**
>- $R_s$ 서버와 라우터 사이의 rate
>- $R_c$ 라우터와 클라이언트 사이의 Rate
>- 전체 네트워크를 지나는 비트는 서버에서 전송되어 클라이언트로 향하는 비트 뿐임
>
>**server to client thorughput?**
>- $R_s < R_c$라면
>	- 서버에서 전송된 비트가 라우터를 향해 정상적으로 flow하고, 라우터에서 클라이언트를 향해 $R_c$ 의 rate로 전송됨
>
>- 반대로 $R_s > R_c$ 라면
>	- 라우터가 비트를 수신하는 속도만큼 빠르게 비트를 송신할 수 없음
>	- bits will only leave the router at race $R_c$ giving an **end2end throughput of $R_C$.**
>	- 라우터에서 전송을 대기하는 비트는 무한정 증가함
>
>**In Conclusion,**
>- in simple two-link situation, the throughput is $min(R_c, R_s)$
>- time taking to transfer large file of F bits from client to server = $F/min(R_c, R_s)$

>[!example] Example 2
>![](https://i.imgur.com/pr1um3z.png)
>**settings**
>- 여러개의 라우터를 연결하는 N개의 transmission links
>- 각 link의 transmission rate는 $R_1$, $R_2$, ..., $R_n$
>
>**Let's jump to conclusion!**
>- simple two-link situation과 동일하게 end2end throughput = $min(R_1, R_2, ..., R_n)$

>[!example] Example 3
>![](https://i.imgur.com/mE2SMoK.png)
>**settings**
>- 종단 server와 client는 computer network를 통해 연결됨
>- $R_s$ 네트워크와 access link로 연결된 서버의 rate
>- $R_c$ 네트워크와 access link로 연결된 클라이언트의 rate
>- core of communication network의 링크들은 모두 매우 높은 rate를 가짐 ($>R_c, R_s$)
>- 전체 네트워크를 지나는 비트는 서버에서 전송되어 클라이언트로 향하는 비트 뿐임
>
>**Let's jump to conclusion!**
>- 네트워크 코어를 이루는 링크의 rate가 매우 크기 때문에, end2end throughput = $min(R_c, R_s)$
>- 즉, 오늘날 인터넷 네트워크에서 throughput에서 문제가 되는 지점은 일반적으로 access network

>[!example] Example 4
>![](https://i.imgur.com/CfZXxTI.png)
>**settings**
>- 10개의 서버와 10개의 클라이언트가 core of computer network를 통해 연결됨
>- 10개의 server-client pair에서 동시에 10개의 download가 발생하고 있는 상황
>- R 네트워크 코어에 위치한 하나의 공통 링크의 rate ($>R_c, R_s$)
>- $R_s$ 네트워크와 access link로 연결된 서버의 rate
>- $R_c$ 네트워크와 access link로 연결된 클라이언트의 rate
>
>**Let's jump to conclusion!**
>- R이 $R_c$, $R_s$에 비해 수백수천배 크므로, end2end throughput = $min(R_c, R_s)$
>
>**How about ...**
>- $R_s=2Mbps$, $R_c=1Mbps$, $R=5Mbps$ 라면?
>- shared core link의 rate는 500kbps 수준으로 낮아짐
>- end2end throughout  = $min(R_c, R_s, 500kbps) = 500kbps$
>- `Throughput depends not only on the transmission rates of the links along the path, but also on the intervening traffic.`













