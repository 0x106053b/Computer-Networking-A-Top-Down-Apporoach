- application을 개발할 때 network-core device의 작동에 관해서는 고려하지 않음
- Network protocol stack의 5계층으로 application layer와 그 하위 계층을 분리해두었기 때문에

## 2.1.1 Network Application Architectures
- application 개발자의 관점에서 network architecture는 개발 시의 고려 대상이 아님
- application architecture 차원에서 (1) client-server 구조인지, (2) P2P 구조인지만 고려

### Client-Server Architecture
- always-on host인 server와 그 server에 요청을 보내는 client로 이루어짐
	(예) 웹 server가 client host로부터의 object 요청을 수신하면, 요청받은 object를 client에게 송신함으로써 요청을 처리함

- client-server architecture에서는 client간의 상호 통신이 이루어지지 않음
- server는 고정된 IP address를 가짐
	- 따라서 client는 always-on server의 fixed IP address에 언제나 요청을 보낼 수 있음

- 대표적인 예로 Web, FTP(File Transfer Protocol), Telnet and e-mail

#### Data Center
- 단일 서버로는 여러 clients로부터의 요청을 모두 처리하기 어려울 수 있으므로, 여러개의 강력한 가상 서버를 호스팅하는 data center가 사용되기도 함
- ISP가 data center를 통하여 데이터를 전송한다면 data center에 요금을 지불해야 함

### P2P Architecture
- server or datacenter에 거의 혹은 아예 의존하지 않음
- 대신 applications이 작동하는 hosts ( = peers) 이 상호 통신함
- **==peers는 service provider의 서비스를 제공받지 않음==**
- 오늘날 수많은 traffic-intensive applications은 P2P 구조에 기반함
	- ❓ traffic-intensive : 파일 공유와 같이 트래픽이 많이 발생하는 서비스

- self-scalability를 가짐
	(예) P2P 파일 공유 아키텍쳐에서 파일을 내려받는 peer는 client, 파일을 업로드하는 peer를 server라고 함. client peer가 파일을 다 내려받으면 해당 peer는 server가 되고, 다른 client가 파일을 다운로드 받을 수 있게 함. 즉, 작업 부하를 다른 peer들과 분배함으로써 비용 측면에서 매우 효과적

- server infrastructure가 필요하지 않으므로 경제적


<hr>


## 2.1.2 Processes Communicating
- 엄밀히 말하면 서로 통신하는 것은 program이 아니라 process임
- 프로세스가 동일한 end system 위에서 작동중이라면, 시스템의 운영체제에 기반하여 상호 소통 가능
- 그러나 이 책이 주목하는 것은 서로 다른 host 위에서 작동중인 프로세스의 통신!
- 서로 다른 host위에서 작동하는 process는 computer network를 통해 message를 교환함으로써 통신

### Client and Server Processes
- 한 쌍의 communicating process내부에서, 편의상 한 프로세스를 client, 다른 프로세스를 server라고 부름
	(예) Web browser is a client process and a Web server is a server process.
	(예) **==p2p file sharing에서도 파일을 다운로드 받는 peer는 client, 업로드 하는 peer는 server==**

>[!quote] client and server의 정의
>In the context of a communication session between a pair of processes, the process that initiates the communication (that is, initially contacts the other process at the beginning of the session) is labeled as the client. The process that waits to be contacted to begin the session is the server.

### The Interface Between the Process and the Computer Network
### Socket
![](https://i.imgur.com/qPMTgRW.png)

- 프로세스는 socket을 통해 메세지를 전송하고, socket으로부터 메세지를 수신
- 위 그림을 참고하였을 때, ==socket은 application layer와 transport layer 사이의 인터페이스==
	- 💡 따라서 Application Programming Interface = API 라고 함
- application 개발자는 application-layer side의 socket을 통제할 수 있지만, transport-layer side의 socket은 통제할 수 없음
- ==application 개발자가 transport-layer에서 통제권을 가지는 것은==
	- ==transport protocol의 선택==
	- ==maximum buffer, maximum segment size와 같은 일부 transport-layer의 파라미터 조정==

>[!question] 질문
>그렇다면 application 개발자는 application-layer의 통제 권한만을 가진다는 틀린 표현인가?

### Addressing Processes
- 한 host위의 프로세스에서 다른 host위의 프로세스로 packet을 전달하려면, receiving process의 주소가 필요
- receiving process를 특정하기 위해서는
	- destination host의 주소
	- destination host내의 process 식별자 (여러개의 프로세스가 동시에 실행 중일 수 있으므로)

#### IP address
- 32-bit의 주소
- uniquely identifying the host

#### Port Number
- host 내부의 process 식별자 (more specifically, the receiving socket)
- 널리 사용되는 application은 특정 port number를 할당받기도 함
	(예) Web Server의 port number : 80
	(예) mail server process (using the SMTP protocol)의 port number : 25


<hr>


## 2.1.3 Transport Services Available to Applications
- 앞 절에서 말한 것처럼, socket은 application layer와 transport layer 사이의 인터페이스
- application이 socket을 통해 메세지를 전송하면, transport layer protocol은 socket으로부터 전달받은 메세지를 receiving process의 socket으로 전송할 책임을 가짐
- ==application을 개발할 때에는, available transport-layer protocol을 선택해야 함==
	- ❓ 그렇다면 어떤 기준에 의해 transport-layer protocol을 선택할 수 있을까?

### Reliable Data Transfer
- reliable data delivery를 제공하는 protocol
- computer network 상에서 packet 분실이 이루어질 수 있음
	- 라우터의 buffer가 용량을 넘어 drop됨 ( = packet loss)
	- 전송 과정에서 패킷의 비트가 손상되어 router 혹은 host에 의해 버려짐
- 여러 application에서 데이터의 손실은 심각한 결과를 초래할 수 있으므로, 신뢰성 있는 데이터 전송은 필수

>[!info] loss-tolerant application
> **packet loss가 일정 수준까지는 용인되는 application. reliable data transfer가 필요하지 않음**
> (예) conversational audio/video chat 과 같은 multimedia application은 lost data가 소량의 glitch를 유발할 뿐 심각한 장애를 초래하지 않음

#### Throughput
- sending process가 receiving process로 보낼 수 있는 bits rate
- network path상의 대역폭을 여러 프로세스가 공유하고, 그 상태는 계속 변하므로 available throughput은 변동함
- 어떤 transport-layer protocol은 이 available throughput rate를 일정 수준 이상으로 보장함
	- `With such a service, the application could request a guaranteed throughput of r bits/sec, and the transport protocol would then ensure that the available throughput is always at least r bits/sec.`
-  ==Bandwidth-sensitive applications== : throughput requirements를 가진 application
	(예) multimedia applications
	→ 그러나 어떤 multimedia applications은 매 시점 available throughput 환경에 따라 적절한 수준의 rate를 가지도록 데이터를 다르게 인코딩하는 기법을 채택함 (인터넷 상태 안좋으면 화질 구려짐)
- ==Elastic applications== : 그때그때 available한 수준의 throughput을 유동적으로 사용하는 application
	(예) electronic mail, file transfer, and Web transfers

#### Timing
- transport-layer protocol은 통신 시간을 보장할 수도 있음
- interactive real-time applications (Internet telephony), 가상 환경, 온라인 미팅, 멀티플레이어 게임 등 사용자의 액션과 서버의 응답 사이에 delay가 작아야 자연스러운 applications들에 주로 필요함

#### Security
- transport protocol이 전송 프로세스로부터의 데이터를 암호화하여 destination에 전송할 수 있음
- 반대로 receiving host의 transport layer는 암호화된 데이터를 전달받은 후 복호화함
- 이 외에도 data integrity, end-point authentification 등


<hr>


## 2.1.4 Transport Services Provided by the Internet






