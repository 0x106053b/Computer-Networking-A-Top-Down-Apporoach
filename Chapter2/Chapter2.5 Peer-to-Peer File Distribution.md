- 상호 연결된 host인 peers는 server와 연결되지 않고 직접적으로 서로와 소통할 수 있음
- client-server architecture로 대용량 파일을 분배해야 한다면, server는 파일을 복사하고 모든 peers들에게 전송해주어야 하는데 이는 큰 server 부하와 bandwidth 사용을 초래함
- p2p architecture에서는 파일의 각 portion을 peer들이 나눠 내려받고 서로의 peer에게 재분배함으로써 분배의 부담을 줄일 수 있음

### Scalability of P2P Architectures
![](https://i.imgur.com/uxiZfT7.png)
- Settings
	- access link를 통해 여러 peers들과 server가 Internet에 연결된 상황을 가정
	- server의 access link upload rate : $u_S$
	- i번째 peer의 access link upload rate : $u_i$
	- i번째 peer의 access link download rate : $d_i$
	- 분배되고자 하는 파일의 크기 : $F$ bits
	- 파일의 복사본을 내려받고자 하는 peer의 개수 : $N$
	- Internet core의 bandwidth는 충분하다고 가정
	- 💡 distribution time : $N$개의 모든 peer들이 파일을 내려받는 데 소요되는 시간

### Distribution Time for Client-Server Architecture ($D_{cs}$)
- server는 N개의 peer에 Fbits의 파일을, 총 NF bits의 파일을 전송해야 함
	- server의 upload rate는 $u_s$ 이므로 총 $NF/u_s$ 초가 소요됨
- $d_{min} = min(d_1, d_2,  ..., d_N)$ 이라고 한다면, 각 peer들을 향한 distribution 자체에 소요되는 시간은 $F/d_{min}$ 초 (가장 느린 peer가 파일을 모두 내려받기까지의 시간

- 따라서, 아래와 같은 결과를 얻을 수 있음
	- 충분히 큰 N에 대해 $D_{cs}$는  $NF/u_s$ 에 가까워지고, N의 크기에 비례하여 선형적으로 증가
![](https://i.imgur.com/7r6rxjz.png)

### Distribution Time for P2P Architecture ($D_{P2P}$)
- peer가 file의 일부 portion을 내려받으면, 그 data를 다른 peers들로 재분배 하기 위해 upload capacity를 사용할 수 있음
- 분배가 처음 시작된 시점에 file은 server만이 가지고 있으므로, peers로의 재분배를 위해서는 server는 최소 1회 file bits를 upload link로 전송해야 함. min distribution time은 $F/u_s$ 초
	- server-client architecture와 다르게, 한번 upload access link로 비트가 전송되면 그 비트들은 peers들 간에 알아서 공유될 것이므로(P2P) server입장에서는 한 번 전송을 완료한 비트를 다시 전송할 필요 없음
- server-client architecture와 동일하게 가장 느린 download rate를 가진 peer가 모든 파일 Fbits들 다운받는 데에 소요되는 시간은 $F/d_{min}$ 초
- total upload capacity of the sys : $u_{total} = u_s + u_1 + ... + u_N$ . system은 N개의 peer들에게 Fbits를 전송(upload)해야하고, 그 rate는 $u_{total}$ 보다 클 수 없음. 따라서 min distribution time은 최소 $NF/u_{total}$ 초

- 따라서, 아래와 같은 결과를 얻을 수 있음
![](https://i.imgur.com/pXvSBrs.png)

![](https://i.imgur.com/so1HyL0.png)
- client-server architecture에서는 N이 증가함에 따라 분배 시간이 선형적으로 증가하지만
- p2p architecture은 늘 client-server architecture보다 적은 분배 시간을 가짐
- 💡 application with the P2P architecture can be self-scaling

### BitTorrent
- 특정 file의 분배에 참여하는 모든 peer들의 집합을 torrent라고 부름
- torrent에 참여하는 peer들은 서로 다른 peer로부터 동일한 크기의 chunk를 다운로드함
	- 처음 torrent에 참여했을때는 chunk를 가지고 있지 않지만, torrent에 참여하는 시간이 길어질수록 가지고 있는 chunk가 점점 축적되는 구조
- chunk를 다운로드하면서 동시에 다른 peer에게 upload하기도 함
- peer가 여러개의 chunk를 수집하다가 전체 file의 다운로드를 완료하게 되면, torrent를 떠나거나 torrent에 남아 다른 peer들에게 chunk를 upload함
- 물론 file의 모든 chunk를 다운받지 못했더라도 torrent를 떠날 수 있으며, 이후 다시 참여하여 나머지를 다운받을 수 있음

#### Tracker
- BitTorrent의 각 torrent는 tracker를 가짐
- peer가 torrent에 참여하면, peer는 tracker에게 자신이 torrent에 등록되었음을 알리고, 주기적으로 tracker에게 자신이 torrent에 여전히 소속되어 있음을 알림

#### How BitTorrenet Works
![](https://i.imgur.com/lC0Jc52.png)
1. 새로운 peer A가 torrent에 참가하면 tracker은 torrent내부의 peers들 중 일부를 랜덤하게 선택하고, A에게 그 peers들의 IP주소를 전달함
2. A는 전송받은 IP 주소 집합 내의 모든 peer들과 TCP connection 생성을 시도함
	- 이 때, TCP connection 생성을 성공한 peers들을 neighboring peers라고 함

3. 시간이 흐르며 neighboring peer들은 torrent를 떠나기도 하며, 새로운 peer가 torrent에 참여해 A와의 TCP connection 생성을 시도하기도 함
4. A는 neighboring peer들에게 가지고 있는 chunk들의 list를 요청하고, 전달받은 list중 A가 가지고 있지 않은 chunk에 대한 전달을 요청할 수 있음

- **rarest first**
	- neighboring peer로부터 어떤 chunk를 요청할 것인지를 결정할 때 사용하는 기술
	- A가 가지고 있지 않은 chunk들 중에서, A의 neighboring peers들 역시 가장 드물게 가지고 있는 chunk를 우선적으로 요청
		❓ A가 그 'rarest chunk'를 가져오게 되면 자연히 A의 neighboring peers들도 A에게 그 chunk의 전송을 요청하여 해당 chunk가 고르게 퍼질 것이므로

- **To determine which requests to respond to**
	- A에게 도착한 여러개의 데이터 전송 요청 중 어떤 요청을 우선적으로 처리해야 할까?
	- A에게 가장 높은 rate로 데이터를 전송하고 있는 neighbor에게 우선순위 부여
	- 주기적으로 A에게 데이터를 공급하는 peer의 rate를 확인하여 우선순위 수정
		- 이 때 상위 우선순위 (top priority) peers을 **unchocked**되었다고 함
	- 또한, neighbor중에서 랜덤하게 한 peer를 골라 그 peer에게 데이터를 전송하기도 함
		- 이 때 선택된 peer를 **optimistically unchocked**되었다고 함
		- 임의의 peer와의 데이터 교역을 활성화 시키기 위함 (고정된 peer들과의 교역 X)
	- 이렇게 unchocked된 peer들을 제외하고는 교역하지 않음 (chocked)
		- chocked peers do not receive any chunks from A



