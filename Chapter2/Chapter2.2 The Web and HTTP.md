- web은 on demand로 작동함. 사용자는 원할 때, 원하는 것을 수신함 (↔ broadcasted TV)
- everyone can become a publisher at extremely low cost


<hr>


## 2.2.1 Overview of HTTP
### HyperText Transfer Protocol (HTTP)
- web의 application-layer protocol
- server-client program은 서로 HTTP messages를 교환. HTTP는 이 message의 형식을 정의함

### Web Page
- HTML file, a JPEG image와 같은 object로 구성됨
- 대부분의 Web Page는 base HTML file + referenced objects의 조합
	(예) HTML text 와 5장의 사진을 포함한 웹페이지 = the Web page has 6 objects
- HTML file은 object의 URL로 object를 참조해온다
	- URL은 (1) hostname of the server that houses of the object, (2) object's path name 으로 구성됨

>[!example] URL
>`http://www.lgsports.team/lgtwins/koreanseries.jpg` 에서
>`www.lgsports.team` 은 hostname, /lgtwins/koreanseries.jpg는 path name

- Web의 관점에서, Web browser는 client side of HTTP에서 작동시킴
- Web servers는 server side of HTTP를 작동시키고 Web objects를 저장함

### How Web Clients Request
![](https://i.imgur.com/oEsglsz.png)

- HTTP는 Web clients가 Web server에 요청을 보내는 방식과 Web server가 웹 페이지를 Web client에게 전송하는 방법을 정의함
	(1) browser가 server에게 웹 페이지 내부 objects에 대한 HTTP request를 전송
	(2) server는 request를 수신하고 object 정보를 포함한 HTTP response를 browser에게 전송

- ==HTTP는 transport protocol로 TCP를 채택==
	- 💡 ==application-layer에서 transport protocol의 종류를 선택해야 한다고 앞서 언급했었음!==
	- HTTP client는 server와 TCP connection을 생성하고, 한 번 connection이 생성되고 나면 client-server는 socket interface를 통해 TCP에 접근하게 됨
	- TCP를 사용하므로 HTTP request는 receiver(server) 쪽으로의 도착이 보장됨
	- 💡 ==TCP가 데이터의 reliable한 전송과 그 순서를 보장하므로, HTTP는 송수신 시 데이터의 손실에 대해 고려하지 않아도 됨. 계층 구조의 장점!==

#### Stateless Protocol
- ==server는 client의 상태에 대한 고려 없이 requested files을 전송==
	(예) 동일한 client가 동일한 요청을 여러번 반복한다고 해도, server는 이미 처리 완료된 요청이라고 응답하는 대신 동일 요청에 대한 적절한 응답을 여러번 보냄.
	- `It has completely forgotten what it did earlier.`
	- ==HTTP server가 client에 대한 정보를 아예 저장하지 않기 때문==
	- 따라서 HTTP는 Stateless Protocol이라고 불림


<hr>


## 2.2.2 Non-Persistent and Persistent Connections
- client-server interaction이 TCP상에서 이루어질 때, app 개발자는 각 request/response 쌍이
	- 분리된 TCP connection 위에서 이루어지게 할 것인지 ( = non-persistent)
	- 동일 TCP connection 위에서 이루어지게 할 것인지 결정해야 함 ( = persistent)
- HTTP는 non-persistent와 persistent 모두 사용할 수 있음
	- 💡 persistent connection이 default

### HTTP with Non-Persistent Connections
- settings
	- 페이지가 1개의 base HTML file과 10개의 JPEG 이미지로 이루어져 있다고 가정
	- 11개의 objects들은 동일 server 내에 저장되어 있음
	- HTML base file의 URL : `http://www.lgsports.team/lgtwins/koreanseries.index`

- step by step
	(1) HTTP client가 `www.lgsports.team` 서버의 ==port number 80==과 TCP connection을 생성. client와 server 각각에 socket이 생김
	(2) HTTP client는 socket을 통해 server에 HTTP request message 전송. request message는 요청하는 오브젝트의 path name(`/lgtwins/koreanseries.index`) 을 포함함
	(3) HTTP server는 socket을 통해 request message를 수신. 저장소에서 `/lgtwins/koreanseries.index` object를 꺼내어 socket을 통해 client에게 전송
	(4) HTTP server는 TCP에게 TCP connection을 종료하라고 말함
		💡 ==그러나 client가 response message를 모두 수신하기 전까지는 연결이 종료되지 않음==
	(5) HTTP client가 message를 수신. TCP connection이 이 시점 종료됨. client는 reponse message로부터 file을 추출하고, HTML file을 조사하여 10개의 참조된 이미지 object를 발견
	(6) 참조된 10개의 이미지 object에 대해 1~4단계를 반복
		❓ ==그럼 각 object 객체에 대해 server-client TCP connection을 생성하여 총 11개의 TCP connection이 만들어지는건가? → (O)==

- 서로 다른 browser은 다른 방식으로 수신된 object를 화면에 띄움. HTTP는 데이터 송수신 형식에 대한 프로토콜이므로, 수신받은 데이터를 해석하고 화면에 띄우는 방식에는 관여하지 않음
	`The HTTP specifications ([RFC 1945] and [RFC 2616]) define only the communication protocol between the client HTTP program and the server HTTP program.`

- Non-persistent에서는 한 TCP connection이 오직 한 쌍의 request-response message만을 전송
- 위 예시에서는 client가 순차적으로 10개의 이미지 object를 전달받았는지, 동시에 병렬적으로 10개의 이미지를 전달받았는지 알 수 없음 → 모던 browser에서는 이 동시성 정도를 조정 가능함

#### Round-Trip Time (RTT)
![](https://i.imgur.com/7lBdqjT.png)

- client가 base HTML file request를 전송하고 ~ entire file (object 포함)을 수신받기까지의 시간
- ==RTT는 packet-propagation delays, packet-queuing delays, packet-processing delays를 포함==

#### Three-Way Handshake
(1) client가 server에 TCP segment를 전송 (우리 연결하자~)
(2) server는 segment를 확인하고 small TCP segment로 응답 (ㅇㅋ)
	💡 여기까지가 **1RTT** (client가 request를 보내고 + response를 수신)
(3) ==client가 server에게 연결 확인 메세지를 보냄 + 동시에 HTTP request 함께 전달==
	💡 (3)의 HTTP request에 대해 server가 다시 응답을 보내고 client가 이를 수신하면, **도합 2RTT**

>[!info] Non-persistent 연결의 단점
>**각각의 request objects에 대한 여러개의 TCP 연결이 생성되어야 함**
>- 각 TCP 연결에 대해 TCP buffer가 할당되어야 하고, client와 server에 TCP variables를 저장해야 하므로 동시에 여러 clients로부터의 요청을 처리해야하는 Web Server의 관점에서는 큰 부담이 될 수 있음
>
>**모든 object 요청은 2-RTTs를 필요로 함**
>- TCP connection을 생성하는 데에 1RTT + file request/response에 1RTT
>

- HTTP1.1 은 persistent-connection을 지원하며 server가 response를 전송하고 난 후에도 TCP connection이 살아있도록 함
- 동일 server-client 사이의 연속적인 request/response가 하나의 TCP connection을 통해 이루어짐
- 여러개의 Web Page가 동일한 server 내에 저장되어 있다면, 역시 하나의 TCP connection을 통해 응답받을 수 있음
- ==object에 대한 request는 현재 처리중인 request/response의 경과를 기다리지 않고 연속적으로 (back-to-back) 생성할 수 있음 ( = Pipelining)==
- 일정 시간 이상 connection이 사용되지 않으면, TCP connection은 종료됨

>[!info] HTTP on persistent-connection
>HTTP는 기본적으로 pipelining + persistent connections를 사용
>HTTP1.1를 기반으로 만들어진 HTTP/2는
>- 동일한 TCP connection 안에서 여러 request/response가 전달될 수 있도록 함
>- HTTP message를 prioritizing 하는 방식 채택


<hr>


## 2.2.3 HTTP Message Format
### HTTP Request Message
>[!example]
`GET / somedir/page.html HTTP/1.1`
`Host: www.someschool.edu`
`Connection: close`
`User-agent: Mozilla/5.0`
`Accept-language: fr`
- ASCII 문자로 작성되어 있음
- 여러 줄이 개행을 반복하며 request를 구성함
- 길이는 가변적임 (예시와 다르게 5줄 이상일 수도 있음)
- 첫 번째 행은 ==request line==, 이후의 행들은 ==header lines== 라고 함

#### Request Line
- 첫번째 행 `GET / somedir/page.html HTTP/1.1`
- (1) method field, (2) URL field, and (3) HTTP version field로 구성됨
	- method field : GET / POST / HEAD / PUT / DELETE 등
	- GET method는 browser가 object를 요청할 때 사용됨
	- URL field에는 object의 path name을 작성

#### Header Lines
- `Host : www.someschool.edu` 는 object가 저장된 host를 가리킴
	- ❓ 이미 TCP connection이 생성되어 있는데 host server 정보가 왜 필요하지?
		→ host header는 Web proxy caches에게 필요한 정보

- `Connection: close` 를 통해 browser는 server에게 해당 연결의 지속/비지속 여부를 전달
	- 이 경우 `Connection : close` 이므로 비지속 연결

- `User-agent : Mozilla/5.0`를 통해 요청을 보내는 browser type을 전달
	- server가 user agents의 종류에 따라 동일 object의 다른 버전을 전송하므로 필요한 정보

- `Accept-language : fr` 를 통해 user가 object의 어떤 버전을 수신받고 싶어하는지를 전달
	- 대응하는 version이 없다면 server는 default version을 전송

#### Entity Body
- header lines 뒤에 entity body가 따라올 수 있음
- GET method를 사용하는 경우 공란, POST method를 사용하는 경우 작성됨
	(예) `For example, when a user provides search words to a search engine. With a POST message, the user is still requesting a Web page from the server, but the specific contents of the Web page depend on what the user entered into the form fields.`

>[!info] form에서 POST가 아닌 GET이 사용될 수 있다?
>HTML form은 종종 GET method를 사용하며, form을 통해 입력된 데이터를 requested URL에 삽입하여 사용한다. 가령, GET method를 사용하는 form에 'bokyung', 'hyunsoo' 필드를 입력하여 제출하였다면, URL은 `www.lgtwins.team/playersearch?bokyung&hyunsoo` 의 구조를 가진다.




`
`

