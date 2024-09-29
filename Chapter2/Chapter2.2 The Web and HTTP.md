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
- 페이지가 1개의 base HTML file과 10개의 JPEG 이미지로 이루어져 있다고 가정하면