### End Systems ( = Hosts)
end system들은 다양한 애플리케이션들을 '호스팅' 할 수 있기 때문에 'host'라고 불리기도 한다.
- desktop computers (예 : PC, Macs and Linux boxes)
- servers (예 : Web and e-mail servers)
- mobile devices (예 : laptops, smartphones and tablets)

### Client & Server
더 나아가, Host는 'client'와 'server'로 분류할 수 있다. 
- client (예 : desktop, mobile PCs and smartphones)
- server : 웹페이지를 저장하고 분산시키거나, 비디오를 스트리밍하는 등 강력한 기능을 가지고 있음


## 1.2.1 Access Networks
- 접속 네트워크 : end system을 edge router와 연결하는 네트워크
![](Pasted%20image%2020240924152003.png)

### Home Access : Digital subscriber line (DSL)
- 거주자는 wired local phone access를 제공하는 local telephone company(telco) 로부터 DSL internet access를 제공받음
	- 즉, DSL 인터넷의 사용자라면 그 사용자의 전화 회사 역시 인터넷 회사와 동일한 회사겠구나!

#### DSL modem
![](Pasted%20image%2020240924152628.png)
- DSL modem은 기존의 전화 회선을 사용하여 telco의 중앙 사무실(CO)의 DSLAM과 데이터를 송수신함
- DSL modem은 Home PC로부터 디지털 데이터를 수신하고, 이를 high-frequency tones로 변환하여 전화 회선을 통해 CO로 전송함
- 각 가정으로부터 수신된 analog signal은 CO의 DSLAM을 통해 digital format으로 변환됨
- **==각 가정의 전화 회선은 통신 데이터와 전화 신호를 동시에 전달하므로, 이 두 신호는 다른 주파수로 변환됨==**
	- 💡 이후 DSLAM에서 통신 데이터와 전화회선을 분리하여 각각을 Internet과 Telephone Network로 전달하기 위함! **==(=주파수 분할 다중화, FDM)==**
- 반대로, 가정에서는 splitter가 수신된 신호를 통신 데이터와 전화 신호로 분리함. 이후 분리된 통신 데이터는 DSL modem을 지나며 digital format으로 변환됨

>[!info] DSL modem and DSLAM
> phone line이 통신 데이터와 전화 신호를 모두 송수신하기에, 이를 구분하는 작업이 필요하다. 가정에서는 splitter가, CO에서는 DSLAM이 이 신호를 분리한다. 덧붙여, 가정에서 통신 Data로 분리된 신호는 DSL Modem을 거쳐 digital format으로 변환된다.

- DSL표준은 다양한 transmission rate를 정의하고 있는데, downstream rate와 upstream rate가 서로 다름 (asymmetric)
	- 💡 downstream rate > upstream rate
- transmission rate는 표준에 정의된 것보다 낮을 수 있는데, 그 이유는 아래와 같음
	- DSL Provider가 인터넷 서비스 계약의 레벨에 따라 다른 limit을 둔 경우
	- 가정과의 CO의 물리적 거리 (DSL은 단거리 통신을 목적으로 디자인되었음)
	- twisted-pair line의 표준과, 전기 출력의 세기에 따라 달라질 수 있음
- copper wire가 physical link 설계에 사용됨

### Home Access : Cable (HFC, Hybrid Fiber Coax)
![](Pasted%20image%2020240924155950.png)
- DSL이 기존의 전화 회선을 이용해 인터넷 통신을 제공한 것처럼, cable Internet access는 케이블 TV회사의 인프라스트럭쳐를 활용하는 방식
	-  즉, Cable 인터넷의 사용자라면 그 사용자의 Cable TV 회사 역시 인터넷 회사와 동일한 회사겠구나!
- 광케이블(Fiber cable)이 neighborhood-level junctions들을 연결하고, 그렇게 연결된 junction 내부의 각 가정들은 동축케이블(Coaxial cable)을 통해 연결된다.

#### Cable head end
- Cable TV 와 Cable modem 서비스를 제공하기 위해 지역 Cable TV 사무실에 설치하는 설비
- 데이터국으로부터 수신된 신호를 가공, 증폭한 후 분배하는 시설

#### Cable modem
- DSL모뎀과 유사하게. cable modem은 가정용 PC를 Ethernet Port와 연결하는 외부 장치임
- **==케이블 head end에서 CTMS는 DSL의 DSLAM과 유사한 기능을 함 : 각 가정의 케이블로부터 보내진 신호를 digital format으로 변환==**
- Cable modem은 HFC Network를 downstream과 upstream 두 개의 채널로 분할함
	- 💡 downstream 채널이 upstream 채널에 비해 높은 전송률을 가지고 있음 (asymmetric)

#### shared broadcast medium
- Cable head end 로부터 전송된 데이터는 → 모든 link의 downstream 채널을 통해 가정으로 전달
- 각 가정으로부터 전송된 데이터는 → upstream 채널을 통해 Cable head end로 전달
- 따라서, **각 가정의 유저가 동시에 서로 다른 비디오 파일을 내려받는 경우, 각 유저가 할당받는 actual rate는 실제 downstream 전송률보다 낮음**
	`if several users are simultaneously downloading a video file on the downstream channel, the actual rate at which each user receives its video file will be significantly lower than the aggregate cable downstream rate.`
- 반면, 소수의 유저가 웹서핑을 하는 경우, 각 유저는 full cable downstream rate 수준을 할당받을 수 있음
	`if there are only a few active users and they are all Web surfing, then each of the users may actually receive Web pages at the full cable downstream rate. Because the users will rarely request a Web page at exactly the same time.`
- upstream 채널 역시 여러 사용자들이 공유하기 때문에, 여러 송수신들을 조정하고 충돌을 회피하기 위한 multiple access protocol이 요구됨

### Home Access : Fiber to the Home (FTTH)
- CO로부터 직접적으로 가정까지 광섬유 path를 연결하여 인터넷 서비스를 제공함
![](Pasted%20image%2020240925212657.png)

#### Direct fiber
- 각 가정으로 하나의 fiber를 연결하여 인터넷을 제공함
- 간단한 분배 방식이지만, direct fiber에 비해 fiber가 여러 가정에서 share되는 방식이 주로 사용되고 있음

#### Active Optical Networks (AONs)
- 교환 Ethernet 방식 (Switched Ethernet)

#### Passive Optical Networks (PONs)
- [Figure 1.7]이 PONs 방식의 FTTH 구조를 나타낸 것임
	1. 각 가정은 Optical Network Terminator(**ONT**)를 가지고 있으며, 각 ONT는 neightborhood **splitter**에 연결됨
	2. splitter는 각 가정을 단일 **shared optical fiber**로 연결하고, 이는 CO의 Optical Line Terminator(**OLT**)와 연결됨
	3. OLT는 optcial - electrical 신호간의 변환을 제공하는 장치임. OLT는 광신호로부터 수신한 optical 신호를 전기 신호로 변환하여, telco의 router를 통해 이 신호를 Internet으로 송수신함
- 각 가정에서는 ONT에 가정용 라우터를 연결하여 인터넷을 사용함

### Home Access : Other Cases
- DSL, Cable, FTTH를 사용할 수 없는 지역에서는 다른 인터넷 연결 방식을 채택함
	(예) Satellite link, Dial-up access
		ㄴ Dial-up access는 DSL과 유사한 구조를 가지지만 그 속도는 훨씬 느림

### Enterprise (and the home) Access : Ethernet and WiFi
- 대학 캠퍼스 혹은 오피스에서, local area network (LAN)이 edge router에 end system을 연결하기 위한 네트워크로 사용되고 있음

#### LAN
- 가까운 범위(학교, 직장) 내의 end system을 최적화하여 연결하는 고속 네트워크 (↔ WAN)
- **==wireless users는 enterprise's network에 연결된 access point로부터 packet을 송수신함. 그리고 이 access point는 wired internet으로 연결됨==**
- wireless LAN user는 access point로부터 몇십 미터 이내에 위치해 있어야 함

#### Ethernet
![](Pasted%20image%2020240925214646.png)
- **==가장 대표적인 방식의 LAN==** (Ethernet은 LAN의 구성 방식 중 하나)
- Ethernet switch와 연결하기 위해 twisted-pair copper wire를 사용함 (=유선 연결)
- Ethernet switch 혹은 이러한 방식으로 상호 연결된 스위치들은 larger internet으로 연결됨 (그림 참조)

#### WiFi
- IEEE 802.11을 사용한 무선 랜 접속 방식

>[!info] Ethernet과 Wifi의 차이점?
> **EtherNet**
> - 근거리 지역의 end system을 유선 케이블을 통해 하나로 연결하는 방식 (Wired LAN)
> - 비교적 고정적으로 설치되는 데스크탑, 서버, 프린터 등을 연결해 사용함
> - 이동성의 제약이 있으나 무선랜에 비해 빠른 속도가 장점
> - IEEE 802.3 표준
> 
> **WIFI**
> - 근거리 지역의 end system을 무선을 통해 연결하는 방식 (Wireless LAN)
> - 좋은 이동성 그러나 동시접속자가 늘어날 경우 네트워크 성능 이슈 O
> - IEEE 802.11 표준

![](Pasted%20image%2020240925215902.png)

- 위 그림과 같이, 많은 가정들은 안정적인 인터넷 연결망을 구축하기 위해 broadband residential access(Cable modems or DSL)와 wireless LAN technology를 혼합하여 사용함

### Wide-Area Wireless Access : 3G and LTE
- WiFi와 다르게, 3G/LTE 사용자는 base station으로부터 몇십 km 내에 있기만 하면 OK
- LTE는 3G에 뿌리를 두고 더 높은 속도를 가진 방식


<hr>


## 1.2.2 Physical Media
- physical medium은 다양한 형태를 가지고 있으며, 꼭 transmitter-receiver pair가 동일한 매체를 사용할 필요는 없음
- guided media (유도 매체) : 파동이 물리적 매체를 통해 전달됨
	- (예) fiber-optic cable, twisted-pair copper wire, coaxial cable
- unguided media (비유도 매체) : 파동이 공기를 매질로 하여 전파됨
	- (예)  wireless LAN, digital satellite channel

### Twisted-pair Copper line
- 가장 저렴하고 자주 사용되는 guided 매체
- 서로 꼬아진 wire는 electrical interference를 감소시키고, 이는 근처 회선들로부터의 교란을 피하게 함
- 데이터 전송률은 선의 두께와 송수신 지점 간의 거리에 따라 달라짐

### Coaxial Cable
- 높은 data transmission rate를 가짐
- cable television system에서 주로 사용되는 매체
- shared medium으로 사용할 수 있음 : 모든 end system은 다른 end system이 전송하는 모든 것을 수신
	`Specifically, a number of end systems can be connected directly to the cable, with each of the end systems receiving whatever is sent by the other end systems.`

### Fiber Optics
- 높은 data transmission rate를 가짐
- 전자기성 간섭에 영향을 적게 받음 → 장거리 통신을 위한 guided transmission media로 선호됨
- 가격이 비싸기 때문에 가정용 LAN 등 단거리 통신에서는 드물게 사용됨

### Terrestrial Radio Channels
- Radio channel은 전자기적 스펙트럼 신호를 전달함
- 장점
	- 물리적 회선을 설치하지 않아도 됨
	- 장애물을 통과할 수 있음
	- 모바일 사용자에게 연결을 제공할 수 있음
	- 원거리 통신에 사용될 수 있음
- 전파 환경과 송수신 장비 간의 거리에 크게 영향을 받음
	- path loss : 신호의 밀도 감소
	- shadow fading : 원거리를 이동하거나 장애물과 부딪혔을 때
	- multipath fading : 장애물에 신호가 반사되었을 때
	- interference : 다른 transmission 혹은 전자기적 신호와 만났을 때

### Satellite Radio Channels
- satellite는 transmission을 수신하고, 해당 신호를 repeater를 통해 증폭시킨 후 도착지점으로 signal을 전송

#### Geostationary satellite
- 지구 위의 고정된 지점에 satellite가 머무르고 있음 → 지표에서 멀리 떨어져 있기 때문에 가능
- 지표 상의 station에서 전파를 쏘고, satellite가 전파를 수신하고 다른 station으로 전파를 송신하는 일련의 과정에서 상당한 수준의 propagation delay 발생

#### Low-Earth Orbiting (LEO)
- Geostationary satellit에 비해 satellite가 지표면에 더욱 가깝게 위치해 있음 (placed in orbit)
- 지구 주변을 돌며 satellite끼리 통신을 하기도 하고, 지표면의 stations과 통신하기도 함