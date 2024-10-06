### Hostname
- Internet host를 식별할 수 있는 식별자 중 하나
	(예) `www.facebook.com`

- mnemonic(연상적인) 특징이 있기 때문에 사용자들이 선호함
- 그러나 hostname은 internet내부에서 host가 어디에 위치하는지에 대한 정보를 적게 나타냄
- 가변적인 alpha-numeric 문자열로 구성되어 있어 router가 처리하는 데에 어려움이 있음

### IP Address
- 위의 hostname이 가진 한계로 인해 host를 식별하기 위해 IP address가 사용되기도
- 4bytes로 구성되어 있으며 hierarchical structure를 가짐
- . 을 기준으로 왼쪽에서 오른쪽 방향으로 계층 구조를 가지기 때문에, host location에 대한 정보를 확인할 수 있음 (어떤 네트워크 내부에 위치해 있는지?)


<hr>


## 2.4.1 Services Provided by DNS
- 사람들은 hostname을, router는 IP address를 선호함
- Domain Name System(DNS)는 이 상반된 선호에 대응하기 위해 hostname을 IP address로 변환하는 directory service를 제공
- **==DNS는 계층의 DNS server에서 작동되는 분산 데이터베이스이며, host가 분산 데이터베이스로 질의하도록 하는 application-layer protocol==**
- **==DNS protocol은 UDP transport-layer protocol을 채택하며 port번호 53을 사용==**

>[!info] How DNS works
>어떤 user의 host가 `www.someschool.edu/index.html` 을 요청했을 때 `www.someschool.edu` 로 이 HTTP request를 전달하려면, user의 host는 먼저 `www.someschool.edu`의 IP address를 알아야 함
>
>1. 사용자 host는 DNS application의 client side를 실행
>2. browser는 URL에서 hostname `www.someschool.edu` 를 추출하여 이 hostname을 DNS의 client side로 전달
>3. DNS client side는 hostname을 포함한 질의를 DNS server로 전달
>4. DNS는 hostname에 대응하는 IP address를 포함한 응답을 DNS server로부터 수신
>5. browser는 DNS로부터 IP address를 전달받아 해당 IP address의 80번 port와 TCP connection을 생성

- DNS의 사용은 internet application으로 하여금 추가적인 delay를 갖게 할 수 있음. 그러나 IP address를 인근한 DNS server에 캐싱함으로써 DNS delay와 DNS network traffic을 졸일 수 있음

### Additional Services DNS provides
#### Host Aliasing
- 복잡한 hostname을 가진 host는 하나 이상의 alias name을 가지기도 함
- 이 때 원래의 복잡한 hostname을 canonical hostname이라고 함
- alias hostname은 canonical hostname에 비해 mnemonic함
- **==DNS는 host의 IP address 뿐만 아니라 alias hostname에 대응하는 canonical hostname을 얻기 위해 사용됨==**

#### Mail Server Aliasing
- 이메일 주소는 mnemonic한 것이 사용하기에 간편함
- DNS는 mail application에 의해 실행되어 alias hostname 혹은 ip address를 통해 canonical hostname을 얻는 데에 사용될 수 있음
- 💡 MX record는 기업의 mail server와 Web server가 동일한 aliased hostname을 가질 수 있도록 함

#### Load Distribution
- DNS는 복제된(replicated) 서버들의 부하를 분산시키기 위해서도 사용됨
- 붐비는 웹 사이트들은 repliacted multiple server를 두고 있는데, 각 서버는 다른 시스템 위에서 작동하며 따라서 다른 IP address를 가짐
- 이 replicated web server들의 ip address들은 하나의 alias hostname으로 묶임
	- 각각의 replicated web server들은 각자의 canonical hostname을 가짐
- client가 DNS server에 DNS 질의를 보내면 DNS server는 set of IP address를 기반으로 응답을 보내게 되는데, **==이 때의 set of IP address는 각 요청마다 순환식으로 전달됨==**
	- client가 여러개의 IP주소 묶음을 응답으로 받았을 때 client는 그 묶음 중 첫번째 IP address로 HTTP request를 보내기 떄문에, 서버 부하를 분산시키는 효과를 내기 위해서는 DNS server가 IP address 묶음의 첫번째 IP address를 돌려돌려 돌림판으로 보내주어야 함
- DNS rotation은 e-mail에서도 사용될 수 있어서, 여러개의 메일 서버가 하나의 alias name을 공유할 수 있음


<hr>


## 2.4.2 Overview of How DNS Works




