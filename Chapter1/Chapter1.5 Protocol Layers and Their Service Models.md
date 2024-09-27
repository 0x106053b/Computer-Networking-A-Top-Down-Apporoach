## 1.5.1 Layered Architecture
### A Layered Architecture
- 계층화는 복잡한 시스템의 특정 기능에 대한 논의를 더욱 간편하게 함
- 모듈화를 제공함으로써 단순화의 이점 → 각 레이어에서 제공되는 서비스의 수정이(update) 간편함
- 어떤 레이어에서 제공하는 시스템/서비스가 변경되더라도, 다른 레이어에는 변동이 없음

### Protocol Layering
- 프로토콜을 구성하는 하드웨어 & 소프트웨어와 그 프로토콜을 계층의 형태로 구조화
- 각 프로토콜은 각각의 레이어에 속해있음 (Each protocols belongs to one of the layers)
- 각 레이어는 레이어 고유의 서비스를 제공함
	- (1) 레이어 안의 특정 서비스를 수행하거나
	- (2) 바로 아래 계층의 서비스를 이용함 (?)

>[!example] Example
>**n번째 레이어가 네트워크 종단에서 또 다른 종단으로 신뢰성있는(reliable) 메세지 전송을 제공한다면,**
>이는 n-1번째 레이어에서 unreliable한 메세지 송수신을 한 후, n번째 레이어에서 메세지의 전송 상태를 확인하고 시정하는 형태로 제공될 수 있음

- 프로토콜 레이어는 소프트웨어, 하드웨어, 혹은 그 둘의 결합 내에서 구현될 수 있음
	(예) Application-layer protocols : 대부분 end system의 소프트웨어에서 구현됨

### Protocol Stack
![](https://i.imgur.com/AFBoWJQ.png)

#### Application Layer
- network applications과 application layer protocol이 위치한 계층
- HTTP, SMTP, FTP protocol 등을 포함함 ( + DNS)
- 다른 end system의 application과 packet을 교환하기 위한 protocol을 필요로 하는 end system에 위치
- **==application layer에서 송수신하는 packet을 message라고 함==**

#### Transport Layer
- transport layer는 application layer의 message를 endpoints간 송수신하는 레이어
- TCP, UDP protocol이 대표적
- **==transport layer에서 송수신하는 packet을 segment라고 함==**

>[!info] TCP and UDP
>**TCP**
>- application에 connection-oriented 서비스를 제공
>	- ❓ connection-oriented : 상대 endpoint와 고정된 connection을 확립한 후 통신 (통신하는 동안은 네트워크 자원을 예약함). 통신을 하고자 하는 대상 간의 접속이 이루어져 있을 때만 통신이 가능
>- application layer의 message를 destination으로 전송하는 것을 보장(guarantee)
>- congestion-control을 위해 긴 messages를 여러개의 segments로 분할
>
>**UDP**
>- application에 connectionless 서비스를 제공
>- reliability, flow control, congestion control 모두 제공하지 않음

#### Network Layer
- **==network layer에서 송수신하는 packet을 datagram이라고 함==**
- datagram을 다른 host로 전달하는 프로토콜이 위치한 계층
- **==source host의 transport layer의 프로토콜(TCP / UDP)이 segment와 목적지의 주소를 network layer로 전달. network layer는 destination의 transport layer로 segment를 전송==**
- **==IP Protocol==**
	- datagram의 필드값을 정의
	- end systems와 라우터가 이 필드에 대해 어떻게 기능해야되는지를 정의
	- 이 때문에 network layer가 IP layer라고 불리기도 함

- **==Routing Protocol==**
	- datagram이 soruce에서 destination으로 이동할 경로를 결정하는 프로토콜

#### Link Layer
- **==link layer에서 송수신하는 packet을 frame이라고 함==**
- network layer가 datagram의 이동경로를 결정했으니, 하나의 노드에서 다음 노드로 packet을 전송하려면 link layer가 기능해야 함
- process
	- (1) source host의 network layer이 datagram을 link layer로 전달
	- (2) link layer는 datagram을 경로상의 다음 노드로 전달
	- (3) 다음 노드의 link layer는 datagram을 상위 network layer로 전달

- link layer에서 제공하는 서비스는 link가 채택한 특정 link layer protocol에 기반하여 기능함
	(예) `Note that this reliable delivery service is different from the reliable delivery service of TCP, which provides reliable delivery from one end system to another.`

#### Physical Layer
- link layer의 기능이 인접한 네트워크 요소로 전체 frame을 전송하는 것이었다면, physical layer는 frame내부의 각 bits들을 다음 노드로 전송하는 데에 초점을 둠
- physical layer의 프로토콜은 link와 link의 transmission rate에 의존함
	(예) `Ethernet has many physical-layer protocols: one for twisted-pair copper wire, another for coaxial cable.`


<hr>


## 1.5.2 Encapsulation
