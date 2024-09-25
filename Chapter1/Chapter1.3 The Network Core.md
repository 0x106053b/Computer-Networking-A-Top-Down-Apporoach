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

### IP Address
- 모든 end system은 ip address를 가지고 있음
- 어떤 end system이 다른 end system으로 packet을 전송하려고 할 때, 






