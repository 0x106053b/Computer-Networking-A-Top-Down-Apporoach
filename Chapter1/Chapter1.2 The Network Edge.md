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
