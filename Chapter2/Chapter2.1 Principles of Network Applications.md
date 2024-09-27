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
