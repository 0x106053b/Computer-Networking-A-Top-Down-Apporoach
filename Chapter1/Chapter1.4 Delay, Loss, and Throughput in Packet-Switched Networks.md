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

>[!question] Q. packet switching에서는 모든 패킷이 수신된 후에야 전송이 시작되는데, 첫번째로 Queue에 도착한 패킷이라도 queueing delay != 0일 수 있는거 아닌가?
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

#### Total Nodal Dea
