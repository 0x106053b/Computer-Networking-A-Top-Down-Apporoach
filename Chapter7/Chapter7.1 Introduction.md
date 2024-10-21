![](https://i.imgur.com/r9IU7lD.png)
- wireless hosts : end system devices. mobile일 수도 있고 아닐 수도(=stationary) 있음
	- 💡 wireless가 꼭 mobile을 의미하는 것은 아님!
- wireless communication link
	- host는 wireless communication link를 통해 base station(기지) 혹은 다른 host와 연결될 수 있음.
	- wireless link는 네트워크 edge에 위치한 host를 더 넓은 network infrasturcture와 연결
	- wireless link는 network 내의 router, switches, 그리고 다른 네트워크 장비들을 연결하는 역할을 하기도 함
- base station : base station과 연결된 wireless host의 데이터 송수신을 책임짐
	(예) ceulluar network에서의 cell tower
	(예) 802.11 wireless LANs에서의 access point

>[!question] "Associated" with a base station?
>1. host가 base station의 무선 통신 범위 내에 속해있는 경우
>2. host가 base station을 매개로 더 넓은 network로 데이터를 송수신 하는 경우

### Infrastructure mode
- base station을 가진 host들은 infrastructure mode로 작동한다고 볼 수 있음
- `Traditional network services are provided by the network to which host is connected via the base station.`

### ad hoc networks
- 반대로 ad hoc networks는 연결할 infrastructure를 갖지 않음
- infrastructure의 부재로 인해, host들은 routing, address assignment, DNS-like name translation과 같은 서비스를 셀프로 제공함

### Handoff ( = Handover)
- host가 이동하면서 연결된 base station을 변경하는 것

### Wireless Network Taxonomy

|                       |                                 single hop                                 |                                  multiple hops                                   |
| :-------------------: | :------------------------------------------------------------------------: | :------------------------------------------------------------------------------: |
|  **infrastructure**   | host가 base station에<br>연결되어 있으며,<br>base station은<br>larger Internet으로 연결됨 | host는 여러 wireless nodes를 거쳐(=relay)<br>larger Internet으로<br>데이터를 송수신함 (mesh net) |
| **no infrastructure** |              base station이 없기 떄문에 larger Internet으로 연결되지도 않음               |                 base statino이 없기 때문에 larger Internet으로 연결되지도 않음.                 |
- single hop + infrastructure based
	- ==larger wired network와 연결된 base station==
	- base station과 wireless host간의 통신은 single wireless hop 안에서 이루어짐
	- 802.11 networks, 4G LTE

- single hop + infrastructure less
	- bluetooth networks

- multiple hops + infrastructure based
	- larger Internet과 연결된 base station O
	- base station과 통신하기 위해 여러개의 다른 wireless nodes를 거쳐야 함

- multiple hops + infrastructure less
	- 노드들 간의 connectivity가 변할 수 있음
	- mobile ad hoc networkds (MANETs)
	- vehicular ad hoc networks (VANETs)


