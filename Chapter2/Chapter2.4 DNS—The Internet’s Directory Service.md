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
- user의 host에서 작동중인 application이 hostname을 IP address로 번역하는 상황을 가정
	1. application은 client side DNS를 키고, IP address로 번역하고자 하는 hostname을 확정
	2. client side DNS는 주어진 hostname을 가지고 DNS server에 질의를 보냄
		💡 모든 DNS 질의와 응답은 port 53번의 UDP datagram을 통해 이루어짐
	3. delay후, user의 host는 보냈던 요청에 대응하는 IP address 매핑값을 수신함
	4. 이 매핑값(IP address)은 요청한 application으로 전달됨
		💡 application의 관점에서는, DNS는 간단한 translation service를 제공하는 black box

- DNS를 단순하게 설계한다면, 모든 매핑값을 저장하고 있는 단일한 DNS server를 두는 방식을 고안할 수 있음 (centralized 방식). client는 간단하게 모든 질의를 단일 DNS server로 보내고, DNS server는 곧바로 질의에 대해 응답
- 그러나 단일 DNS server architecture은 아래와 같은 문제를 가짐
	- A single point of failure : 단일 DNS 서버가 고장나면, 모든 인터넷 host는 DNS 서비스를 이용할 수 없게 됨
	- Traffic volume : 단일 DNS 서버는 수백만 호스트로부터의 요청을 처리할 수 없음
	- Distant centralized database : DNS 서버가 한개라면 필연적으로 어떤 host로부터는 매우 멀어질 수 밖에 없음. 이는 매우 긴 delay를 초래함
	- Maintenance : 단일 DNS 서버는 모든 Internet host에 대한 정보를 저장할 수 없음. 데이터가 매우 많을뿐더러, 새로운 모든 host에 대한 업데이트를 계속 해주어야 함

### A Distributed, Hierarchical Database
- DNS는 계층적으로 짜여진 distributed servers로 구성되어 있음
- hostname - IP address 매핑 정보는 여러 데이터베이스에 분산되어 저장됨
- DNS server는 3개의 클래스로 구분됨
	- (1) root DNS servers,
	- (2) top-level domain (TLD) DNS servers
	- (3) authoritative DNS servers

#### Root DNS Servers
- 전 세계적으로 1000개 이상의 root server가 흩어져 위치하고 있음
- TLD server의 IP address를 제공하는 기능

#### TLD DNS Servers
- `.com`, `.org`, `.net`, `.edu` 와 같은 top-level domain과 `uk`, `fr`, `ko`와 같은 국가 단위 도메인은 TLD DNS server를 가짐
- authoritive DNS server의 IP address를 제공하는 기능

#### Authoritive DNS Server
- 인터넷을 통해 접속가능한 모든 organization은 host와 IP address를 매핑하는 접근가능한 DNS records를 가짐
- organization은 authoritive DNS server의 구현 방식을 선택할 수 있음
- organizatino 관리자는 DNS records가 authoritive DNS server에 저장될 수 있도록 service provider에게 일정 금액을 지불해야 함

![](https://i.imgur.com/BRTqN4N.png)

- DNS client가 hostname에 대응하는 IP address를 찾는 과정 (`www.amazon.com`)
	- (1) client는 root DNS server중 하나에 접촉함. 이 때 root DNS server는 top-level domain `.com` 에 대응하는 TLD DNS server의 IP address를 반환함
	- (2) client는 (1)에서 받은 TLD DNS server의 IP address에 접촉함. 이 때 TLD(.com) DNS server는 authoritive server `amazon.com`에 대응하는 DNS server의 IP address를 반환함
	- (3) client는 (2)에서 받은 authoritive server의 IP address에 접촉하여, `www.amazon.com` hostname에 매핑된 IP address를 반환받음

#### Local DNS Server
- DNS hierarchy에는 속하지 않지만 DNS architecture의 핵심적인 기능을 함
- ISP는 local DNS server를 가지며, host가 ISP에 연결되면, ISP는 한개 이상의 local DNS server의 IP address를 host에게 제공함
- host의 Local DNS는 host와 가까이 위치해 있어 delay ↓ 
	(예) institutional ISP의 경우 local DNS server는 host와 동일한 LAN으로 연결됨
	(예) residential ISP의 경우local DNS server는 적은 수의 router hop 이내로 host와 연결됨

- host가 DNS 질의를 생성하면, 해당 질의는 local DNS server로 보내져 proxy의 기능을 함

#### How Local DNS Server Works
![](https://i.imgur.com/to34b6B.png)
- Settings
	- host `cse.nyu.edu` 가 `gaia.cs.umass.edu`의 IP address를 얻으려고 함
	- host `cse.nyu.edu`의 local DNS server는 `dns.nyu.edu`
	- host `gaia.cs.umass.edu`의 authoritive DNS server는 `dns.umass.edu`

1. `cse.nyu.edu`가 local DNS server인 `dns.nyu.edu`에 DNS 질의를 보냄. 이 때, 질의는 번역하고자 하는 hostname `gaia.cs.umass.edu`를 포함하고 있음
2. local DNS server는 root DNS server에 질의를 포워딩함
3. root DNS server는 `.edu` suffix를 확인하고, local DNS server에게 `.edu` 도메인을 관리하는 TLD DNS server의 IP address 묶음을 전달
4. local DNS server는 수신한 IP address 묶음을 사용하여 `.edu` TLD DNS server에 질의를 전송
5. `.edu` TLD DNS server는 `umass.edu` suffix를 확인하고 ` dns.umass.edu` authoritive DNS server의 IP address 를 local DNS server에 전달함
6. Local DNS server는 동일한 DNS 질의를 `dns.umass.edu` 에 전송함
7. `dns.umass.edu` authoritive server는 `gaia.cs.umass.edu`에 대응하는 IP address를 local DNS server에 전송함
8. Local DNS server는 `gaia.cs.umass.edu`로의 IP address를 host에게 반환

- 위 Local DNS server 예시에 DNS caching이 추가되면 query traffic을 줄일 수 있음
- 또한, 위 예시의 TLD server는 Authoritive DNS server의 hostname을 알고 있음을 전제함. 그러나 만약, TLD server가 Authoritive DNS server를 모르는 대신 그 intermediate server의 IP address만을 알고 있다면, 2번의 질의+응답 과정이 추가되어 총 10번의 질의+응답을 거치게 됨
- recursive queries : `cse.nyu.edu`에서 `dns.nyu.edu`로 보내진 질의로 인해 `dns.nyu.edu` host가 다른 DNS server들과 질의를 교환하면서 mapping값을 탐색하므로
- iterative queries : `dns.nyu.edu`로부터의 질의가 다른 DNS server를 거쳐 response로 돌아오는 흐름이 여러번 반복되므로

### DNS Caching
- query chain에서 DNS server가 다른 server로부터 DNS reply를 수신하면, 매핑값을 서버의 local memory에 저장
- DNS는 delay performance 성능을 향상시키고, 인터넷 상을 떠도는 DNS message의 수를 줄이기 위해 DNS caching 을 사용
- **==이후 caching된 mapping에 대한 질의가 도착한다면, 해당 server가 authoritive server가 아님에도 매핑값의 IP address를 반환할 수 있음==**
- hostname - IP address 매핑이 영구적인 것은 아니므로, 일정 기간이 흐르면 DNS server는 캐싱 값을 만료시킴
- **==TLD server의 IP address로 캐싱 가능. local DNS server가 query chain 상에서 root DNS server에 질의하는 과정을 단축시킴==**


<hr>


## 2.4.3 DNS Records and Messages
- DNS distributed database는 resource records(RR)을 저장함
- DNS server로부터의 응답은 하나 이상의 RR값을 포함함

#### Resource Record
- `(Name, Value, Type, TTL)`의 4개 필드로 구성된 튜플 형태
- TTL은 resource record가 살아있을 수 있는 기간, 즉 caching된 RR이 삭제되기까지의 시간

#### Type == A
- **==`Name`은 hostname을, `Value`는 hostname에 대응하는 IP address를 의미함==**
- 표준의 hostname-to-IP address 매핑을 제공
(예) `(relay1.bar.foo.com, 145.37.93.126, A)`

#### Type == NS
- **==`Name`은 domain을, `Value`는 해당 도메인 내부의 host의 IP address를 얻을 수 있는 authoritive DNS server의 hostname을 의미함==**
- query chain에서 DNS 서버로 route해야하는 경우 사용
- (foo.com, dns.foo.com, NS)

#### Type == CNAME
- **==`Name`은 alias hostname을, `Value`는 alias hostname의 canonical hostname을 의미함==**
- ==**canonical hostname을 얻기 위해 DNS client는 CNAME record를 질의함**==
- `(foo.com, relay1.bar.foo.com, CNAME)`

#### Type == MX
- **==`Value`는 alias hostname `Name`의 canonical name of mail server를 의미함==**
- mail server의 hostname이 단순한 alias name을 가질 수 있도록 함
- MX record를 사용함으로써 기업은 기업의 서버(Web server 등)와 mail server에 동일한 aliased name을 사용할 수 있음
- **==mail server의 canonical name을 얻기 위해 DNS client는 MX record를 질의함==**
- `(foo.com, mail.bar.foo.com, MX)`

>[!info]
>DNS server가 **특정 hostname에 대한 책임 서버라면, hostname에 대한 Type A RR을 포함**하고 있을 것이고, (DNS server가 authoritive가 아니더라도 caching 되어있을 수 있음) **책임 서버가 아니라면 해당 hostname에 대한 질의를 이어나가기 위한 다른 DNS server의 DNS hostname을 저장한 Type NS RR와 NS의 value값에 대응하는 IP address를 위한 Type A RR을 모두 포함하고 있을 것!**

>[!example]
>Suppose an edu TLD server is not authoritative for the host `gaia.cs.umass.edu`. Then this server will contain a record for a domain that includes the host `gaia.cs.umass .edu`, for example, `(umass.edu, dns.umass.edu, NS)`. The edu TLD server would also contain a Type A record, which maps the DNS server dns.umass.edu to an IP address, for example, `(dns.umass.edu, 128.119.40.111, A)`.
>

### DNS Messages
- DNS message는 DNS 질의(query)와 응답(reply)만으로 이루어짐
- 질의와 응답은 동일한 format을 가짐

![](https://i.imgur.com/qsSfFkk.png)
- 첫번째 12Bytes : header section
	- 첫번째 필드는 질의를 식별하는 번호를 포함함. 이 query의 identifier는 reply의 identifier 필드에 동일하게 복사되어, 이후 client가 응답을 수신했을 때 어떤 query에 대한 응답인지 식별할 수 있도록 함
	- flag 필드에는 여러개의 flag가 있음
		- 1bit의 query(0)/reply(1) flag, 
		- DNS server가 queried host name에 대해 authoritive인지를 나타내는 1bit flag
		- client가 DNS server로 하여금 RR를 가지고 있지 않은 경우 recursion을 하길 원하는지를 나타내는 1bit flag
		- DNS server가 recursion을 지원하는지를 나타내는 1bit flag
	- 나머지 4개의 필드는 header section 뒤에 따라오는 data section의 number of occurences를 나타냄

- ==**question section은 query에 대한 정보를 포함함**==
	- 질의하고자 하는 name
	- name에 대해 질의하고자 하는 질문의 type
		(예) hostname과 매핑된 IP address를 질의하는 경우 Type A

- ==**answer section은 질의된 name에 대응하는 DNS server로부터의 RR 응답을 포함함**==
	- RR은 Type, Value, TTL을 포함하고 있는 튜플
	- DNS server은 여러개의 RR을 응답할 수 있음
		❓ 하나의 hostname이 여러개의 IP address를 가질 수 있으므로

- authority section은 다른 authoritive server의 정보를 포함함
- additional section은 다른 유용한 정보를 포함함
	- answer field가 MX 질의에 대한 응답으로 mail server의 canonical hostname을 포함한 RR을 반환하고 있다면, additional section은 mails server의 canonical hostname에 대한 Type A record -  canonical hostname의 IP address를 포함

### Inserting Records into the DNS Database
#### DNS registrar
- domain name의 유일성을 인증해주는 유료 기관
- domain name을 DNS database에 저장하고, 서비스 제공 비용을 받음
- 이전에는 단일 DNS registrar이 독점하는 형태였지만, 현재는 많은 DNS registrar이 경쟁하며 운영중이고, Internet Coporation for Assigned Names and Numbers (ICANN)이 DNS registrar 의 기관 등록을 승인함
- domain name을 DNS registrar에 등록할 때, primary and secondary authoritive DNS server의 hostname과 IP address를 제공해야 함
	- **==registrar은 위 정보를 받아 아래 두 레코드를 TLD DNS server 에 저장**==
		- ==**`(networkutopia.com, dns1.networkutopia.com, NS)`**==
		- ==**`(dns1.networkutopia.com, 212.212.212.1, A)`==**

- Web server에 대한 Type A record와 Mail server에 대한 Type MX record가 저장되게 하여 서버로의 이메일 전송이 가능하게 할 수 있음

>[!Example]
>Suppose Alice in Australia wants to view the Web page www.networkutopia.com. As discussed earlier, her host will first send a DNS query to her local DNS server. The local DNS server will then contact a TLD com server. (The local DNS server will also have to contact a root DNS server if the address of a TLD com server is not cached.) This TLD server contains the Type NS and Type A resource records listed above, because the registrar had these resource records inserted into all of the TLD com servers. The TLD com server sends a reply to Alice’s local DNS server, with the reply containing the two resource records. The local DNS server then sends a DNS query to 212.212.212.1, asking for the Type A record corresponding to www.networkutopia.com. This record provides the IP address of the desired Web server, say, 212.212.71.4, which the local DNS server passes back to Alice’s host. Alice’s browser can now initiate a TCP connection to the host 212.212.71.4 and send an HTTP request over the connection. Whew! There’s a lot more going on than what meets the eye when one surfs the Web!


<hr>


## 2.4.4 DNS Security
### DDos Attacks
- root DNS server에 traffic을 동시에 많이 발생시켜 서버 과부화 만들기
- traffic filtering이 발생하는 경우 DDos 공격 무력화
- local DNS server가 TLD server를 캐싱한다면, Root DNS server로의 접근을 우회하므로 DDos 공격 무력화

### Redirect Attacks
- DNS query를 질의 중간에 가로채어 잘못된 사이트로 redirect되도록 거짓 reply 전송
- DNS server로 위조된 reply를 보내어 잘못된 RR이 DNS server에 캐싱되도록 함

### Exploit DNS for DDos
- 출발된 IP 주소를 조작하여 DNS 질의에 대한 응답이 조작된 IP주소로 전송되도록 함
- DNS 패킷에는 인증 절차가 필요 없으므로 가능










