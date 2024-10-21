- IEEE 802.11 wireless LAN = WiFi
- WLANs (Wireless Local Area Network)를 위해 설계됨
- 주로 가정, 사무실 등에서 70m이내로 통신가능하도록 설계됨
- 802.11 n, ac, and ax standards : WiFi 4~6, 4G, 5G 수준
- 802.11 af, ax : IoT, sensor networks, metering apps를 위해 고안됨

## 7.3.1 The 802.11 Wireless LAN Architecture
![](https://i.imgur.com/nafjQNv.png)
### Baisc Service Set (BSS)
- 802.11 아키텍쳐의 기본적인 block 단위
- 여러개의 무선 station와 ==base station(=Access Point)==를 포함하고 있음
	- 💡 ==ad hoc mode의 경우에는 base station없이 host만 존재한다. 따라서 central control이 없고 외부 network로의 연결 또한 없음==
- 위 그림에서의 access point는 switch/router와 같은 interconnecting device와 연결되어 larger Internet으로 통함
- 일반적으로 가정에는 BSS를 larger Internet에 연결해주는 AP로서의 router가 하나씩 있음

### MAC address
- 802.11의 wireless statino은 6-Byte의 MAC address를 가짐
- AP도 무선 인터페이스에 대한 MAC address를 가짐
- 전 세계적으로 unique한 물리적 주소

### Channles and Association
- 관리자가 AP를 설치하면, 1~2 words의 Service Set Identifier(SSID)를 지정함
- 또한 AP에 channel number를 지정함. 즉 admin이 AP의 frequency를 지정함
	- interference possible : 선택된 channel이 이웃한 AP와 동일하다면 간섭이 있을 수 있음

### WiFi Jungle
- wireless station이 2개 이상의 AP로부터 강력한 신호를 수신하는 상황
- `Each of these APs would likely be located in a different IP subnet and would have been independently assigned a channel.`
- wireless station은 하나의 서브넷에 속해야 함 = 하나의 AP와만 연결되어야 함
	- wireless station은 유일하게 연결된 AP를 통해서만 데이터를 송수신할 수 있음
	1. AP는 주기적으로 AP의 SSID와 MAC을 포함한 beacon frames를 전송
	2. wireless device는 channel을 탐색하면서 beacon frames을 수신
	3. 사용가능한 APs의 존재를 확인한 후 적절한 AP를 선택하여 연결
		- 일반적으로 가장 강한 신호 세기를 가진 AP를 선택함
	4. 연결된 후에는 ...
		- authentication
		- run DHCP (AP의 subnet에서 IP addr을 할당받기 위해)

### Passive/Active Scanning
![](https://i.imgur.com/BUHp1bL.png)
- Passive Scanning
	1. APs들은 beacon frames을 전송
	2. host는 beacon frames를 스캔하고 적절한 AP에게 association request frame을 보냄
	3. 선택된 AP는 host에게 association response frame을 보냄

- Active Scanning
	1. probe(조사) Request frame이 host로부터 브로드캐스팅 (나랑 연결하실분~)
	2. host는 가능한 APs들로부터 probe Response frame을 수신 (저요저요)
	3. host는 적절한 AP에게 association request frame을 보냄
	4. 선택된 AP는 host에게 association reponse frame을 보냄

- association request/response frame은 일종의 handshaking이라고 이해하자


---


## 7.3.2 The 802.11 MAC Protocol




