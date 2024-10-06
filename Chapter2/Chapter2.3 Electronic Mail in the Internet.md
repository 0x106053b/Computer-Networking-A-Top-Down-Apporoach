- e-mail은 통신 상대와 스케줄을 맞추지 않아도 되는 비동기식(asynchronous) 통신
- fast, easy to distribute, and inexpensive

![](https://i.imgur.com/ZFBqK2y.png)

### User Agents
- user가 메일을 읽고, 답장하고, 전달하고, 저장하고, 작성할 수 있게 함
- 메일 작성자가 메일 작성을 완료하면, user agent는 메일을 ==**작성자의**== mail server로 보냄
	- 메세지는 mail server의 outgoing message queue에 저장됨
- 메일 수신자가 수신된 메일을 읽으려고 할 때, user agent는 user의 mail server의 user mailbox로부터 해당 메일을 꺼내와(retrieve) 유저에게 보여줌

### Mail Server
- 각 user는 mail server를 가지고, 그 안에 user mailbox를 두고 있음
	- user mailbox에는 그 유저에게 전송된 메일들이 저장되어 있음
- 일반적으로 메일은 ==**(1) 송신자의 user agent → (2) 수신자의 mail server → (3) 수신자의 mailbox**== 를 거쳐 전송됨
- 사용자가 mailbox에 접속하려고 하면, mail server는 유저네임 & 패스워드로 사용자 인증을 거침
- 만약 송신자 A의 mail server가 수신자 B의 mail server로 메일을 전송할 수 없다면, A의 mail server는 message queue에 해당 메일을 저장해두었다가 이후 전송을 재시도함

### SMTP
- 전자메일을 위한 대표적인 application layer protocol
- 신뢰적인(reliable) 데이터 전송을 위해 TCP protocol을 채택함
- SMTP는 2개의 면을 가지고 있음
	- client side - 전송자의 message server에서 작동
	- server side - 수신자의 message server에서 작동
	(예) mail server가 다른 mail server로 메일을 전송할때. act as an SMTP client


<hr>


## 2.3.1 SMTP
- SMTP는 mail body의 형식을 7-bit ASCII로 제한함
- 따라서, binary multimedia data는 SMTP를 통해 전송되기 전 ASCII로 인코딩되어야 하며, SMTP로 전송된 후 다시 binary로 디코딩되어야 함

### Process of SMTP mailing
![](https://i.imgur.com/8hjRHfv.png)
1. A가 agent를 열어 B의 이메일 주소와 메일 내용을 입력한 후, user agent에게 메일을 전송하라고 명령을 내림
2. A의 user agent는 message queue가 위치한 A의 mail server로 메일을 전달함
3. A의 mail server에서 작동중인 client'side SMTP는 message queue에 메일이 들어온 것을 확인하고 B의 mail server에서 작동중인 SMTP server로 TCP connection을 생성
4. initial SMTP handshaking 후, A의 client side SMTP는 TCP connection을 통해 메일을 전송
5. B의 mail server에서 server side of SMTP가 메일을 수신하면, mail server는 그 메일을 user mailbox에 저장
6. B는 user agent를 열어 수신된 메일을 확인

- SMTP는 두 종단 사용자가 아무리 멀리 떨어져있더라도 중간 server를 사용하지 않고 곧바로 sender와 receiver를 연결하는 TCP connection을 채택
- 만약 수신자(B)의 mail server가 다운된다면, 전송되려는 메세지는 발신자(A)의 mail server에서 재전송 트리거를 대기함

>[!info] Closer look at how SMTP transfers a message
>- client SMTP는 server SMTP의 25번 port로 TCP 연결을 시도하고, 만약 server SMTP가 작동하지 않는 중이라면 client SMTP는 나중에 연결을 다시 시도함
>- TCP connection이 생성되면, server와 client는 application layer handshaking을 함. handshaking을 하면서 SMTP client는 송신자와 수신자의 이메일 주소를 전달함
>- handshaking 절차가 완료된 후에야 client SMTP는 메일을 전송함
>- SMTP는 server로 오류 없이 메세지를 전달하기 위해 TCP 를 통한 신뢰성 있는 데이터 전송에 의존함
>- server로 보내야 하는 메세지가 여러개인 경우, ==**동일한 TCP connection 상에서 위의 절차를 반복하며 메일을 전송(persistent connection)**== 함. 만약 더 이상 보낼 메세지가 없다면 TCP connection을 종료시킴

### Messages Exchanged between Client & Server
![](https://i.imgur.com/vMeu2K9.png)
- Settings
	- client의 hostname : `crepes.fr`
	- server의 hostname : `hamburger.edu`

- client는 server에게 `"Do youlike ketchup? How about pickles?"` 라는 메세지를 전송
- 메세지 마지막에 전송한 `"."`은 전송할 메세지의 내용이 완료됨을 의미함
- server는 client로부터의 command에 대해 reply code와 영문 설명을 덧붙여 응답함
	(예) `250 Message accepted for delivery`

- SMTP가 persistent connection을 사용한다고 했는데, 여기서 메세지를 더 보내려면 handshaking단계의 `C:  MAIL FROM: <alice@crepes.fr>` 부터 다시 반복하면 됨

>[!info] TelNet
>`telnet serverName 25`
>위와 같은 server-client간의 응답을 얻기 위해서는 telnet을 사용할 수 있음. 이 때, serverName은 메일을 송신하고자 하는 local mail server의 이름이어야 함. 이 코드를 입력하면, local host와 mail server사이의 TCP connection이 생성되고, server로부터 220 reply를 수신하게 됨. 이후 HELO, MAIL FROM, RCPT TO, DATA and QUIT과 같은 SMTP command를 사용하여 mail server와 통신할 수 있음. 


<hr>


## 2.3.2 Mail Message Formats
>[!example] typical message header
>`From: alice@crepes.fr`
>`To: bob@hamburger.edu`
>`Subject: Searching for the meaning of life.`

- 메세지 본문 앞의 header는 해당 메일에 대한 주변적인 정보를 포함하고 있음
- header lines와 message 본문은 CRLF 공백 라인으로 구분됨
- RFC 5322에서는 header lines의 작성 포맷과 그 의미를 정의함
- header line은 콜론(:) 으로 연결된 키워드와 값을 선언하고 있으며, 어떤 키워드는 필수적으로 header line에 포함되어야 하는 반면 어떤 키워드는 optional함
	- **==`From`, `To` header line은 필수적으로 작성되어야 함==**
	- `Subject` header line은 optional
- ==**이 때의 header lines들은 SMTP command에서의 `FROM`, `TO`와 다른 것임 !!**==
	- SMTP command에서의 `FROM`, `TO`는 handshaking protocol에서 사용된 명령어이며, header lines에서의 `FROM`, `TO`는 메일 그 자체에 포함되는 내용임


<hr>


## 2.3.3 Mail Access Protocols
- 수신인의 mail server가 local host에 내장되어 있다면 수신인의 local host는 항상 켜진 채로 인터넷에 연결되어 메일이 수신되기를 대기해야함
- 따라서 일반적으로 user agent는 local host에 내장되어 있지만 mailbox에 접속하는 경우에는 always-on shared mail server로 접근해야 함. 이 mail server는 다른 사용자들과 공유됨

![](https://i.imgur.com/qofS01T.png)
1. 발신인의 user agent는 수신인의 mail server와 직접적으로 소통하지 않고, 발신인의 mail server로 SMTP/HTTP를 통해 메일을 전송
2. 발신인의 mail server는 SMTP를 통해 (SMTP client) 수신인의 mail server로 메일을 전달
	❓ 현재 발신인의 user agent는 destination mail server에 메일을 보낼 수 있는 자원 (mail server) 가 없기 때문에 발신인의 mail server을 거쳐 수신인의 mail server로 메일이 보내질 수 있도록 함
3. 발신인의 mail server에 우선 메일을 전달함으로써, 발신인의 mail server는 수신인의 mail server가 작동할 때까지 주기적으로 메일의 전송을 시도할 수 있음

### IMAP
- 그렇다면 수신인의 mail server까지 도착한 메일을 user agent를 통해 어떻게 local host로 읽어올 수 있을까?
- SMTP는 push protocol이지만 현재 local host는 pull operation을 하려는 것이므로 사용할 수 X
- 만약 수신인이 Web-based e-mail 이나 smartphone app을 사용하고 있다면 user agent는 HTTP protocol을 통해 수신인의 mail server로부터 메일을 꺼내옴
	- 즉, 이 뜻은 수신인의 mail server가 SMTP와 HTTP protocol interface를 모두 가지고 있어야 함을 의미 (mail 수신을 위한 SMTP, mail을 local agent로 pull 하기 위한 HTTP)
- HTTP의 대안으로는 Internet Mail Access Protocol (IMAP)이 주로 사용됨

>[!info]
>- **SMTP** : delivery / stroage to receiver's server
>- **Mail Access Protocol** : retrieval from server



