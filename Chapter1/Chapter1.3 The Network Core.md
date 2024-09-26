### Network Core
![](https://i.imgur.com/ckeys55.png)

- network core : mesh of packet switches + interconnects the Internet's end systems

## 1.3.1 Packet Switching
### Packets
- data source는 packet이라는 smaller chunks로 분할되어 송수신됨
- source-dest 경로에서 packet은 ==**router와 link-layer switches**==를 통해 경로를 설정함 (packet switch)
- packet은 각 링크의 full transmission rate로 전송됨

>[!info] Transmission Rate
>source end system이 L bits 짜리 packet을 R bits/sec 링크를 통해 전송한다면,
>해당 패킷 1개를 전송하는 데에 소요되는 시간은 $\frac{L}{R}$ 초

### Store-and-Forward Transmission
![](https://i.imgur.com/JE5WMTy.png)

- packet switch를 하는 장비는 전체 패킷을 수신한 후에야 패킷의 첫 비트를 다음 링크로 전송할 수 있음
	`Packet switch must receive the entire packet before it can begin to transmit the first bit of the packet onto the outbound link.`

- 예를 들어, 한 패킷이 L bits로 구성된 3개의 패킷을 source에서 destination으로 전송한다고 할 때 : 
	- (1) 그림과 같이 1st packet의 일부분이 Router에 도착
	- (2) 그러나 router는 store-and-forward 방식을 채택하기에 1st packet의 일부를 구성하는 비트를 destination으로 전송할 수 없음 → **==해당 비트들을 buffer 함==**
	-  (3) router가 1st packet의 전체 비트를 수신하면, 그제서야 1st packet에 소속된 비트들을 destination으로 전송할 수 있음

- packet이 tramit되는 시간을 계산해보면
	- L/R초 
		- 1st packet을 router가 모두 수신함
		- router에서 dest로 송신 시작
	- 2L/R초
		- dest에 1st packet이 모두 도착
		- 2nd packet을 router가 모두 수신함
		- 2nd packet을 dest로 송신 시작
	- 3L/R초
		- dest에 2nd packet이 모두 도착
		- 3rd packet을 router가 모두 수신함
		- 3rd packet을 dest로 송신 시작
	- 4L/R초
		- dest에 3rd packet이 모두 도착
		- Lbits의 packet 3장이 R bits/sec 링크를 통해 모두 전송 완료

>[!info] packet 1개를 전송하는 데에 소요되는 시간 공식
>L bits로 이루어진 1개의 packet이 Rbits/sec의 링크 N개를 통과하는데 걸리는 시간은 : 
>$$
>D_{end-to-end} = N\frac{L}{R}
>$$
>**(+) L bits로 이루어진 패킷 p개가 Rbits/sec의 링크 N개를 통과하는데 걸리는 시간은 :**
>$$
>D_{end-to-end} = [(p-1)+N]\frac{L}{R}
>$$
>*p번째 패킷이 첫번째 라우터에 도착하는 데 걸리는 시간이 $p\frac{L}{R}$초,*
>*p번째 패킷이 첫번째 라우터에서 나머지 N-1 링크를 건너는 데 걸리는 시간이 $(N-1)\frac{L}{R}초$*

### Queuing Delay and Packet Loss
#### output buffer ( = output queue)
- 라우터에서 보내려고 하는 패킷들을 저장하는 공간
- 라우터에 도착한 패킷이 링크를 통해 전송하려고 하나 해당 링크가 사용중일 경우, output buffer에 저장됨
- store-and-forward의 특징으로, 패킷들은 output buffer queuing delay를 가짐
- queuing delay는 해당 네트워크의 혼잡도의 영향을 받음

#### packet loss
- 만약 유한한 버퍼의 저장공간이 모두 찬 상태에서 새로운 패킷이 버퍼에 저장되려고 한다면 → packet loss
- 새롭게 도착한 패킷 혹은 이미 버퍼에 저장된 패킷 중 하나가 drop됨

### Forwarding Tables and Routing Protocols
- 패킷을 communication link로 전송하는 과정에서, 그 경로를 어떻게 설정할 수 있을까?

#### IP Address
- 모든 end system은 ip address를 가지고 있음
- 어떤 end system이 다른 end system으로 packet을 전송하려고 할 때, 
	- (1) source end system은 packet의 header에 목적지의 IP address 정보를 추가함
		- 💡 이 때의 IP address는 **==hierarchical structure==** 임
	- (2) 패킷이 라우터에 도착하면, 라우터는 목적지의 IP address를 확인
		- 💡 ==**각 라우터의 outbound link와 destination의 IP address를 매핑한 forwarding table을 이용하여 다음 이동 경로를 설정함**==
	- (3) 적절한 인접 라우터로 패킷을 전송

#### How to make routing table?
- 인터넷은 어떤 특별한 routing protocol을 이용하여 forwarding table을 자동으로 구성함


<hr>


- link와 switches를 가로질러 데이터를 전송하는 방식은 circuit switching과 packet switching두가지가 있음

## 1.3.2 Circuit Switching
### Circuit Switching
- ==**두 장비 사이의 자원들(buffers, link transmission rate)은 communication session이 열려있는 동안 reserved됨 (↔ packet-switched network)**==
	- 💡 transmission rate가 sender-to-receiver connection에 의해 점유되므로, sender은 보장된 constant rate로 receiver에게 데이터를 전송할 수 있음
- **==session의 메세지들은 자원이 필요할 때마다 자원의 사용을 요청하고, 자원에의 접근 권한을 획득할 때까지 대기해야 함==**
	- 💡 session이 열리면 해당 session이 닫힐때까지 자원을 점유하므로, 다른 session이 자원을 사용중이라면 해당 자원이 자유로워 질 때까지 대기해야 함

- 전통적인 전화 네트워크는 이 circuit switching networks 를 사용함

![](https://i.imgur.com/kljhlSe.png)
- 두 host가 통신하고 싶은 경우, 네트워크는 두 host사이 dedicated한 end-to-end 연결을 생성
- Thus, in order for Host A to communicate with Host B, the network must first reserve one circuit on each of two links
- 한 link에 4개의  circuit이 존재하므로, 각 연결은 total transmission capacity의 1/4을 사용함

>[!info] In the case of packet switching ...
>packet switching은 link의 자원을 점유하지 않기 때문에 link가 다른 패킷의 송수신으로 인해 혼잡한 상태라면, **패킷은 buffer에서 link를 사용할 수 있을 때까지 대기**해야 하며 이는 delay를 초래함. 
>즉, **packet switch는 전송 시간을 보장(guarantee)할 수 없음**


### Multiplexing in Circuit-Switched Networks
![](https://i.imgur.com/IGDDQh0.png)

- circuit-switched networks는 FDM 방식 혹은 TDM 방식으로 구현될 수 있음
#### Frequency-Division Multiplexing (FDM)
- 주파수 분할 다중화
- 링크의 주파수 대역을 분할하여 각 connection에 할당함으로서 여러개의 connections을 구현함
- (예) FM 라디오는 88MHz ~ 108MHz 사이의 주파수 대역을 분할하여 여러개의 방송채널을 구현함
#### Time-Division Multiplexing (TDM)
- 시분할 다중화
- 신호가 수신되는 시간을 일정 크기의 frame으로 분할하고, frame을 고정된 수의 time slots으로 분할
- 네트워크가 새로운 connection을 생성할 때, 모든 frame의 time slot 한 칸을 각 connection에 할당
- Transmission rate of circuit = frame rate x #(of bits in a slot)
	- (예) 한 link가 1초에 8k frames을 전송하고, 한 time slot이 8 bits로 이루어져 있다면, 각 connection circuit은 64kbps transmission rate를 가짐
	- ❓ 한 frame 내의 time slot 개수가 고려되지 않는 이유는, 한 frame 내의 여러 time slot 각각은 다른 connection circuit을 생성하므로, 특정 connection의 시점에서는 1개의 frame당 time slot 1개 만큼을 전송할 수 있음

>[!info] circuit switching 예시
>- Host A로부터 Host B로 640k bits를 전송하려고 함
>- 네트워크는 프레임당 24개의 time slots를 가진 TDM 방식을 채택
>- bit rate = 1.536 Mbps
>- Host A가 파일을 전송하기 위한 회선을 배정하는 데에 500msec 소요
>
>**풀이**
>1.536 Mbps / 24 = 64 kbps (각 connection은 64kbps의 bit rate를 가짐)
>640k / 64 kpbs = 10 sec
>10 sec + 500msec = 10.5 sec
>
>**핵심**
>transmission time은 link의 개수와 무관함 (link를 reserve하여 사용하므로?)

### Circuit Switching vs Packet Switching
- packet switching 찬성론자들 ( = circuit switching 반대)
	- circuit switching의 dedicated circuits은 silent period동안 낭비됨
		- 💡 그러나 packet switching은 better sharing of transmission capacity를 가짐
	- circuit switching을 위해 end-to-end circuit을 배정하는 과정이 복잡함
		- 💡 그러나 packet switching은 구현이 쉽고 효율적이며, 저렴하기까지 함


- circuit switching 찬성론자들 ( = packet switching 반대)
	- packet switching은 end-to-end delay를 보장할 수 없으므로 real-time service에 부적합

- 현대 사회에서 더 지배적으로 사용되는 방식은 packet switching

>[!question] Example 1
>**Q. 사용자가 1 Mbps 링크를 공유한다고 가정하고, 각 사용자들은 활동 시간과 비활동 시간을 반복한다고 하자. 사용자는 전체 시간에서 10%만 활동하며 나머지 90% 시간에는 활동하지 않는다.**
>
>**Setting**
>- 활동 시간 : 100 kbps의 일정 속도로 데이터를 생산
>- 비활동 시간 : 데이터를 생산하지 않을 때
> 
>**Circuit Switching**
>- 100kbps가 모든 사용자에게 할당되어야 함.
>- TDM의 경우 1초짜리 frame이 10명의 사용자를 위해 0.1sec으로 분할된다면 각 사용자에게는 프레임당 1개의 time slot이 할당됨. 즉 Circuit switching은 동시에 10명까지만 사용가능
>
>**Packet Switching**
>- 사용자가 데이터를 송수신 하고 있을 확률은 10% 이므로,
>- 10명 이하의 사용자가 동시 접속중일 확률 : $\sum_{n=0}^{10} (0.4)^n(1-0.4)^{(10-n)} = 0.9996$ 
>- 10명 이하의 사용자가 있다면 100kps x 100 = 1 Mbps 이하의 데이터를 생산하므로 링크의 transmission rate를 초과하지 않아 성능 지연이 발생하지 않음
>
>💡 10명 이상의 동시 사용자가 있을 확률은 매우 희박하므로, circuit switching과 packet switching은 거의 동일한 성능을 가짐 (packet switch는 여기서 동일한 성능으로 3배 이상의 사용자 수를 수용할 수 있음)

>[!question] Example2
> **Q. 10명의 사용자가 있다고 가정하자. 1번과 동일하게, 사용자는 1 Mbps 링크를 공유한다. 한 사용자가 한번에 1,000비트 패킷을 1,000개 생성하고 다른 사용자는 패킷을 생성하지 않는다.**
> 
> **Circuit Switching**
> - TDM을 예시로 들면, 사용자가 10명이므로 한 frame이 100kps의 time slots 10개로 분할될 수 있음
> - 따라서 한 사용자가 1k 비트 패킷을 1k개 생성한다면, $(1k * 1k)/100kps = 10sec$이 소요됨
> 
> **Packet Switching**
> - 다른 패킷을 생성하는 사용자가 없으므로, 한 사용자가 1Mbps의 링크를 모두 사용할 수 있음
> - 1k bits 패킷 1k를 모두 전송하는 데 $1M / (1k * 1k) = 1sec$이 소요됨


<hr>


## 1.3.3 A Network of Networks

### A Network of Networks
- access ISP는 wired/wireless connectivity를 제공함
	(예) DSL, cable, FTTH, WiFi and cellular
- end users들을 access ISP에 연결하는 것을 넘어, access ISP간의 interconnection도 필요

### ISP interconnection
- 방법1. 각 access ISP를 서로 직접 연결 (mesh design)
	- 비용이 많이 듬
	- 세상에 존재하는 다른 access ISP와의 1:1 link를 생성해야 한다는 어려움\

#### Network Structure 1
- ==**Network Sturcture 1 : 단일 global ISP를 모든 access ISP와 연결하여 interconnection 구현**==
	- global ISP : 전 지구를 아우르는 라우터와 link의 집합이자, 수많은 access ISP의 근처에 라우터를 설치
	- 광범위한 지역을 커버하므로 비용 소모가 큼
		- 연결을 제공하는 access ISP에 traffic에 비례하는 과금을 시키는 방식으로 이익 실현 가능
		- global transit ISP는 provider, access ISP는 customer인 구조

#### Network Structure 2
- **==Network Structure 2 : 여러개의 global ISP를 모든 access ISP와 연결하여 interconnectoin 구현==**
	- **==2-tier hierarchy==**
	- Network structure 1에서처럼 global ISP가 수익성을 가진다면, 시장 원리에 따라 여러개의 global ISP가 생길 것!
	- access ISP는 여러 competing global transit ISP 중에서 가격과 제공 서비스를 기준으로 선택할 수 있게 되므로 Network structure 1보다 2를 더 선호함
	- 여러개의 global ISP들은 상호 연결되어야 함
		- 그렇지 않으면 서로 다른 global ISP에 연결된 access ISP들은 서로 통신할 수 없음 (exclusive)

#### Network Strucrue 3
- ==**Network Structure 3== : $\sum{access ISP}$ ⊂  $\sum{regional ISP}$ ⊂ $\sum{global ISP}$==**
	- **==multi-tier hierarchy==**
	- Network structure 2가 $\sum{access ISP}$ ⊂ $\sum{global ISP}$ 로 이루어진 2-tier였다면, Network structure 3은 regional ISP등을 추가적인 계층으로 삽입한 multi-tier 구조임

>[!info] multi-tier hierarchy ISPs
>현실적으로 모든 지역을 커버하는 ISP는 존재할 수 없으므로, 지역 단위의 regional ISP (lower-tier ISP) 가 존재하고, 각 regional ISP는 tier-1 ISP에 연결됨 (access ISP - regional ISP - tier 1 ISP)
>(물론 access ISP가 tier 1 ISP에 직접 연결하는 방법도 있음)
>
>**각 ISP는 자신의 상위 계층의 ISP의 client로서 그들에게 비용을 지불함**
>따라서 top-tier ISP는 다른 provider에게 돈을 지불하지 않음 (상위 계층이 존재 X)
>

>[!question] Example 1
>For example, in China, there are **access ISPs in each city**, which connect to **provincial ISPs**, which in turn connect to **national ISPs**, which finally connect to **tier-1 ISPs**.

#### Network Strucrue 4
- **==Network Structure 4 : Network Structure 3 + PoPs + multi-homing + peering + IXPs==**
- **PoPs (Points of Presence)**
	- provider 네트워크 안의 한개 이상의 라우터 집합. customer 네트워크가 provider 네트워크와 연결될 수 있도록 함
	- 가장 하위의 access network를 제외한 모든 계층에 존재함 (access network는 provider X 이므로)
	- customer는 제3자 telecommunication provider로부터 고속 접속 링크를 대여하여 PoP의 라우터에 접속할 수 있음

- **Multi-home**
	- 2개 이상의 provider network에 연결하는 것
		(예) An access ISP may multi-home with two regional ISPs and also with a tier-1 ISP.
	- 한 provider로의 접속이 실패하면 다른 provider로 접속할 수 있기 때문에, failure에 robust함

- **Peer**
	- customer ISP가 provider ISP에게 traffic에 비례하여 지불하는 비용을 감소시키기 위해, 동일 level의 인근한 ISP들은 higher-tier ISP와의 연결 없이 서로 상호 연결될 수 있음 (can peer)
	- Network Structure 2의 global transmit ISP들이 연결되는 것 역시 peer

- **IXPs (Internet Exchange Point)**
	- multiple ISPs들이 peer할 수 있는 meeting point

#### Network Structure 5
![](https://i.imgur.com/BUYwxdp.png)

- ==**Network Structure 5 : Network Structure4 + content-provider networks**==
- 대표적인 예시로, Google datacenter는 content-provider network의 선두자로, 50-100개의 data center들과 함께 Google의 private TCP/IP network를 연결함
- upper-tier ISP와의 연결을 우회하기 위해 lower-tier ISP와 직접 peering
- 1-tier ISP를 통해 도달할 수 있는 access network를 위해 google network 역시 1-tier ISP와 연결됨
	- 연결된 1-tier ISP에게 트래픽에 비례하는 요금을 지불
	- Users and content providers are customers of lower-tier ISPs

- By Creating its own network,
	- content provider은 upper-tier ISP로 지출되는 비용을 감소시킴
	- 그들의 서비스가 유저에게 도달하는 과정에 통제권을 가짐


