- network application을 개발자가 해야하는 첫번째 결정 중 하나는 application이 어떤 transport protocol 위에서 작동하게 하느냐의 문제 : TCP? UDP?
	- TCP : connection oriented, reliable byte-stream channel
	- UDP : connectionless, without any guarantees about delivery

- prioprietary application 개발 시 well-known port number의 사용을 피해야 함


<hr>


## 2.7.1 Socket Programming with UDP
- application 개발자는 socket의 application-layer side에는 통제 권한을 가지지만 transport-layer side에는 적은 통제권만을 가짐
- packet이 송신자의 socket을 통과하면, packet에 destination address를 부착해 패킷이 인터넷을 통과하여 적절한 process에의해 수신되도록 함
- packet이 receving socket에 도착하면 receiving process는 그로부터 데이터를 추출
- 어떤 destionation address가 packet에 부착되어야 할까?
	- destination host의 IP address을 사용해 Internet의 router들은 패킷을 destination host로 전달함
	- 한 host내에서 작동하고 있는 여러 process를 구분하여 특정 process의 socket에 packet을 전송하기 위해서는 port number를 사용
	- 💡 packet's destination = destination IP address + destination port number

- **==일반적으로 source IP address와 source port number역시 패킷에 추가적으로 부착되는 정보이긴 하나, UDP application code를 작성하는 경우 위 두 정보는 작성되지 않고 운영체제에 의해 자동적으로 삽입==**되는 정보임 
	- ❓ TCP connection은 connection-oriented 연결이므로 source address에 따라 다른 socket을 사용할 수 있으나, **==UDP는 connectionless이므로 source address에 대한 정보가 필요없음==**

### Socket Programming
![](https://i.imgur.com/skymwjU.png)
- socket programming을 위한 간단한 client-server application
	1. client는 키보드로부터 문자열 한 줄을 읽어오고, 그 데이터를 sever로 보냄
	2. server는 그 데이터를 수신하여 문자열을 모두 대문자로 변환함
	3. server는 변환된 데이터를 client에게 전송함
	4. client는 전송된 데이터를 수신하여 화면에 디스플레이

>[!example] UDPClient.py
>`from socket import *` 
>- 파이썬 socket module을 불러옴
>
>`serverName = ’hostname’`
>- hostname을 작성할 수도 있고 `123.123.123.123.` 이렇게 IP addr을 작성할 수도 있음
>- hostname을 사용하는 경우 DNS lookup이 자동으로 IP addr을 찾아줌
>
>`serverPort = 12000`
>
>`clientSocket = socket(AF_INET, SOCK_DGRAM)`
>- client socket을 생성
>- 첫번째 파라미터 : address family를 의미
>- 두번째 파라미터 : socket의 종류를 의미 (`SOCK_DGRAM`은 UDP socket을 의미)
>- socket을 생성할 때에 source port number를 지정하지 않았음에 주목! OS가 알아서 배정할거임
>
>`message = input(’Input lowercase sentence:’)`
>
>`clientSocket.sendto(message.encode(),(serverName, serverPort))`
>- 첫번째 파라미터 : message를 byte type으로 인코딩한 값
>- 두번째 파라미터 : destination addr
>- 💡 앞서 언급한 바와 같이 source addr은 OS가 알아서 message에 부착함
>
>`modifiedMessage, serverAddress = clientSocket.recvfrom(2048)`
>- packet의 soruce addr(IP addr + port number)은 serverAddress 변수에 저장됨
>- .recvfrom의 2048은 입력 buffer의 사이즈
>
>`print(modifiedMessage.decode())`
>
>`clientSocket.close()`
>- socket을 닫음

>[!example] UDPServer.py
>`from socket import *` 
>
>`serverPort = 12000` 
>
>`serverSocket = socket(AF_INET, SOCK_DGRAM)`
> 
>`serverSocket.bind((’’, serverPort))` 
>- socket에 명시적으로 port number(12000)을 지정
>- 즉, server의 IP address의 port number 12000으로 보내지는 패킷은 다 이 socket으로 들어옴
>
>`print(”The server is ready to receive”)` 
>
>`while True:` 
>- socket에 packet이 수신되기를 대기함
>
>	`message, clientAddress = serverSocket.recvfrom(2048)`
>	- packet의 data는 message 변수에 저장됨
>	- source addr은 clientAddress에 저장됨
>	- 💡 UDP server는 이 source(client) addr을 회신에 활용함
>	
>	`modifiedMessage = message.decode().upper()`
>	- 수신된 인코딩된 데이터를 디코딩한 후 가공하여 modifiedMessage에 저장
>	
>	`serverSocket.sendto(modifiedMessage.encode(), clientAddress)`
>	- source addr로 가공된 데이터를 회신
>	- 회신을 보낸 이후에도 client로부터의 packet수신을 기다리며 while loop를 돔


<hr>


## 2.7.2 Socket Programming with TCP
- UDP와 다르게 TCP는 connection-oriented protocol
- TCP connection을 생성하기 전에 client-server handshaking 과정이 필요
	- source addr(IP addr + port number) + dest addr(IP addr + port number) 모두 필요

- TCP socket 생성 시, server가 client의 initial contact에 응답하려면 준비상태여야 함
	- UDP와 같이 TCP server가 client의 initial contact 전부터 실행되고 있거나
	- **client로부터의 initial contact를 환영하는 special socket을 가지고 있거나**

- client가 TCP socket을 생성할 때, server의 welcoming socket으로부터 온 addr (IP addr + port number) 정보를 명시
- socket이 생성되면, client는 server와 3-way handshaking
	- 💡 handshaking은 transport layer상에서 이루어짐

- handshaking 후 client는 server process의 welcoming socket에 접촉하고, 해당 client에 할당된 새로운 socket을 생성함
	- 즉, server process의 welcoming socket과 handshaking 이후 만들어지는 client에 할당된 socket 총 2가지 종류의 socket이 있다고 볼 수 있음

![](https://i.imgur.com/hkqb2CM.png)
- application의 관점에서 client의 socket과 server의 socket은 pipe에 의해 직접적으로 연결됨
- 따라서 TCP connection은 server가 보내진 순서대로 bytes를 수신할 것을 보장함
- ( + reliable service)
- server process는 socket을 통해 데이터를 수신할 뿐만 아니라 송신할 수도 있음

![](https://i.imgur.com/skymwjU.png)
- UDP의 예시와 동일한 application을 가정하여 TCP socket programming을 해보자

>[!example] TCPClient.py
>`from socket import *`
>
>`serverName = ’servername’`
>
>`serverPort = 12000`
>
>`clientSocket = socket(AF_INET, SOCK_STREAM)`
>- 첫번째 파라미터 : underlying network의 종류
>- 두번째 파라미터 : socket의 종류를 의미 (`SOCK_STREAM은 TCP socket을 의미)
>- socket을 생성할 때에 source port number를 지정하지 않았음에 주목! OS가 알아서 배정할거임
> 
>`clientSocket.connect((serverName,serverPort))`
>- TCP socket으로 데이터를 전송하기 전에, TCP connection 생성이 선행되어야 함
>- 파라미터 : 연결하고자 하는 server의 addr
>- 위 코드라인이 실행된 후 handshaking을 거쳐 TCP connection이 생성됨
>
>`sentence = input(’Input lowercase sentence:’)`
>
>`clientSocket.send(sentence.encode())`
>- packet을 send할 때 dest addr을 파라미터로 넣지 않음. TCP connection 이미 有
>
>`modifiedSentence = clientSocket.recv(1024)`
>
>`print(’From Server: ’, modifiedSentence.decode())` 
>
>`clientSocket.close()`

>[!example] TCPServer.py
>`from socket import *` 
>
>`serverPort = 12000` 
>
>`serverSocket = socket(AF_INET, ,SOCK_STREAM)`
> 
>`serverSocket.bind((’’, serverPort))`
>- UDP 의 경우와 같이 server의 port number를 가지고 server socket을 생성
>- 이 때 serverSocket은 welcoming socket의 기능을 함
> 
`serverSocket.listen(1)`
>- client가 server에 접촉할 때까지 대기
>
>`print(”The server is ready to receive”)` 
>
>`while True:` 
>- socket에 packet이 수신되기를 대기함
>
>	`message, clientAddress = serverSocket.recvfrom(2048)`
>	
>	`connectionSocket, addr = serverSocket.accept()`
>	- client가 welcome socket에 대해 TCP connection 생성을 요청
>	- accept()를 사용하여 client-server 연결을 위한 server TCP socket을 생성
>	- 이후 handshaking을 거쳐 TCP connection 생성
>	
>	`sentence = connectionSocket.recv(1024).decode()`
>	`capitalizedSentence = sentence.upper()`
>	`connectionSocket.send(capitalizedSentence.encode())`
>	`connectionSocket.close()`
>	- serversocket은 다른 client로부터의 접촉을 대기하며 남아있음




