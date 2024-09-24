
## 1.1.1 A Nuts and Bolts Description

![[Pasted image 20240924130428.png]]
### Internet
- Network of Networks

### Hosts ( = End Systems)
- 인터넷 용어상, 인터넷에 연결된 모든 장비들은 host 혹은 end systems 이라고 불림
- Server, Client ∈ Hosts (네트워크 상에서 어떤 기능을 하느냐에 따라 구분됨)
- Communication Links, packet switches를 통해 외부 네트워크로 연결됨

### Communication Links
- coaxial cable(동축 케이블), copper line(구리선), optical fiber(광케이블), 그리고 radio spectrum등의 매체를 통해 구현될 수 있음
- 각 매체들은 각기 다른 transmission rate를 가지고 있음
	- ❓ transmission rate : 초당 데이터 전송률

### Packets
- packet : end system이 데이터를 전송하려고 할 때, 데이터를 작은 segment로 나누고 각 segment에 데이터 정보를 포함한 header를 부착함. 이때의 segment+header 집합 패키지를 packet이라 함
- 이 packet이 반대편 end system에 도착하면, 다시 original data로 재조합됨

### Router & Link-Layer Switches
- packet이 communication link로 들어오고, 그 패킷을 목적지로 전달하기 위해 packet switch가 이루어짐
- **Router**
	- ==**network core**에서 작동함==
	- ❓ network core : router들의 연결 집합. 네트워크들의 네트워크
- **Switch**
	- ==**access network**에서 작동함==
	- ❓ access network : 유/무선의 communication link를 가지는 네트워크

> [!info] Router와 Switch의 차이점
> **Router**
> - 목적지로 가는 적합한 경로를 찾는 Routing 역할을 함
> - IP 주소를 기반으로 작동하여 **Network** 계층에 속함
> 
> **Switch**
> - 목적지로 가는 적합한 경로를 찾는 Switching 역할을 함
> - MAC 주소를 기반으로 작동하여 **Link** 계층에 속함
> 

>[!info] 네트워크 구성
>네트워크는 크게 **Network Edge, Access Netowrk, Network Core**의 3단계로 구성되어 있음<br>
>![[Pasted image 20240924141114.png]]<br>
>**Netowrk Edge**
>- 네트워크의 가장자리. 수많은 End System이 속해있음
>- 네트워크 말단의 여러 hosts들의 집합
>
>**Access Network**
>- 네트워크에 접근하기 필요한 네트워크
>- **==ISP가 Access Network를 제공함==**
>
>**Network Core**
>- 전체 시스템의 중앙에 위치하여 데이터를 전송하는 핵심적인 역할을 함
>- Mesh of interconnected routers
>- **==Forwarding, Routing==** 기능을 함

### Path & Links
- packets들이 전달되기 위해서 network를 가로질러 이동하는 일련의 communication link 경로
- 즉, packet switch를 하는 것은 갈림길에서의 최적 경로 결정과 유사하다고 볼 수 있음

### Internet Service Providers(ISPs)
- packet switches network와 communication links로 이루어짐
- end system에게 다양한 방식의 network 접근 기능을 제공함
- 일반 user 뿐만 아니라 content providers에게도 인터넷을 제공함
- **==lower-tier ISP들은 national and international upper-tier ISP를 통해 연결되며, upper-tier ISP들은 높은 속도를 가진 fiber-optic links로 상호 연결됨==**
- lower-tier와 upper-tier ISP 각각은 독립적으로 운영됨

### Protocols
- 인터넷에서의 정보 교환을 제어하기 위한 일종의 규칙
- Transmission Control Protocol (TCP) 와 Internet Protocol (IP)은 가장 흔한 형태의 인터넷 프로토콜
	- ❓ **==IP : Router와 End Systems 사이에서 송수신되는 packet의 formats을 기술==한 프로토콜**


## 1.1.2 A Services Description
### Internet, an Infrastructure that Provides Services to Applications
- 인터넷 애플리케이션은 애플리케이션 서로가 정보를 교환하도록 하는 여러 system을 포함하고 있으므로, Distributed Applications 라고 불리기도 함. 
- ==end system에서 작동하는 애플리케이션은 network core에서의 packet switches에 관여하지 않음==
	`Internet applications run on end systems— they do not run in the packet switches in the network core. Although packet switches facilitate the exchange of data among end systems, they are not concerned with the application that is the source or sink of data.`


#### Socket Interface
- 애플리케이션이 end system 상에서 실행되고, Network Core에서의 packet swithces에 관여하지 않는다면, 서로 다른 end system 사이의 데이터 송수신은 어떻게 이루어질 수 있을까? → socket interface
- 인터넷이 destination으로 데이터를 전달할 수 있도록, 데이터 송신 측의 프로그램이 준수해야 하는 규칙
	`The Internet has a socket interface that the program sending data must follow to have the Internet deliver the data to the program that will receive the data.`


## 1.1.3 What Is a Protocol?
![[Pasted image 20240924143810.png]]
1. *Your computer will send a connection request message to the Web server and wait for a reply.*
2. *The Web server will eventually receive your connection request message and return a connection reply message.*
3. *Your computer then sends the name of the Web page it wants to fetch from that Web server in a GET message.*
4. *The Web server returns the Web page (file) to your computer.*

Network protocol의 작동 방식은 사람의 대화 방식과 유사하다. 두 사람이 원활한 대화를 하기 위해서는 동일한 언어와 대화 매너를 가지고 있어야 하는 것처럼, 네트워크의 end systems 역시 통신을 위해서는 동일한 protocol을 가지고 있어야 한다.

### Network Protocol
- 두 개 이상의 장거리 네트워크 개체 간의 통신에는 protocol이 필수적으로 적용된다. 
	예) congestion-control protocol
	예) protocols in routers determine a packet’s path from source to destination
	💡 **protocol은 복수의 통신매체 간의 메세지 포맷, 순서 외에도 메세지의 송수신제어, 다른 이벤트에 따른 행동을 정의함**
		`A protocol defines the format and the order of messages exchanged between two or more communicating entities, as well as the actions taken on the transmission and/or receipt of a message`.
