### Network Security
- network security
	- 나쁜 사람들이 어떻게 우리 컴퓨터 네트워크를 공격하는지
	- 그리고 이러한 공격으로부터 어떻게 네트워크를 보호하는지
	- 혹은 이러한 공격을 사전에 차단하려면 어떻게 네트워크를 설계해야 하는지

### The Bad Guys Can Put Malware into Your Host Via the Internet
#### Malware
- 디바이스를 손상시킬 수 있는 데이터
	- virus : user's device를 감염시키기 위해서는 user와의 interaction이 필요함
	- worms : explicit user interaction 없이도 감염될 수 있음
- Once malware infects our device ...
	- 컴퓨터에 저장된 파일들을 무단으로 삭제하거나
	- spyware를 설치하여 개인정보를 수집하고 유출함

#### Botnet
- 해커의 명령을 수행하는 감염된 디바이스들의 집합
- 해커들은 botnet에 등록된 호스트를 대상으로 스팸 이메일을 전송하거나 DDoS 공격을 함

#### Self-replicating
- malware에 감염된 호스트는 인터넷에서 감염 대상이 될 다른 호스트를 탐색
- self-replicating malware는 매우 빠른 속도로 전염됨

### The Bad Guys Can Attack Servers and Network Infrastructure
### Denial-of-Service (DoS) Attacks
- network, host와 같은 network infrastructure을 정상적인 사용자로 하여금 사용할 수 없게 함
- **Vulnerability attack** : vulnerable application에 적절한 순서로 조작된 일련의 패킷을 전송하면, application의 작동이 중지되거나 나쁜 경우 호스트가 고장날 수 있음
- **Bandwidth flooding** : 타겟 호스트에 매우 많은 양의 패킷을 한번에 전송함으로써 해당 호스트의 링크를 매우 붐비게 만들어 정당한 패킷이 도달하지 못하게 방해함
- **Connection flooding** : 타겟 호스트와 수많은 TCP 연결을 생성하여 호스트를 혼잡 상태로 만들고, 정당한 connection이 처리되지 못하게 방해함

>[!info] Bandwidth flooding
>![](https://i.imgur.com/sWahQqA.png)
>▲ Distributed Dos (DDos) Attack
>
>만약 targeted host가 R bps의 access rate를 가지고 있다면, 공격자는 R에 수렴하는 수준의 공격을 보내야 DoS 공격이 이루어질 수 있음. 그러나 R이 매우 크다면? 단일 attack source만으로는 충분한 공격을 보내지 못할 수 있음. 더불어 단일 attack source로만 공격 패킷을 보낸다면 upstream router가 이상을 감지하여 해당 source로부터의 트래픽을 모두 차단하여 DoS 공격을 막을 수 있음. 
>
>**Distributed Dos (DDos) Attack**
>공격자가 여러개의 분산된 호스트를 통해 targeted host에 공격 패킷을 전송하여 위의 한계를 극복할 수 있음. 주로 botnet의 여러 호스트들을 사용하여 bandwidth flooding 공격을 함.

### The Bad Guys Can Sniff Packets
#### Packet Sniffer
- 무선 데이터 송수신을 하는 경우, 수신 호스트 근처에 passive receiver를 설치하여 수신 호스트로 송신되는 패킷들을 복사할 수 있음
- 유선 환경에서도 packet sniffer가 설치될 수 있음. LAN에서 송수신되는 packet의 복사본을 취할 수 있음
- packet sniffer은 passive receiver이므로 packet 내부에 직접 침투하는 형태의 공격을 하지는 않음. 따라서 packet sniffer의 존재를 탐지하기 어려움

### The Bad Guys Can Masquerade as Someone You Trust
- 임의의 source/destination 주소와 content를 담은 패킷을 생성하여 인터넷으로 송수신하는 것은 쉬움
- 만약 receiver가 해당 패킷을 신뢰하여 패킷의 명령을 따른다면?
- 인터넷은 초기 'a group of mutually trusting users'를 전제로 설계되었으므로 이런 공격이 가능

#### IP Spoofing
- 거짓 source address를 가진 악성 패킷을 인터넷으로 전송하는 것
- 주로 신뢰할 수 있는 호스트인 양 수신 호스트를 속이는 방식
- IP spoofing을 방지하기 위해서는 end-point authentication 필요
	- 패킷이 우리가 신뢰하고 있는 source에서 송신된 것인지 destination에서 검사