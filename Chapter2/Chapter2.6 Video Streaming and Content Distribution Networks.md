
## 2.6.1 Internet Video
- pre-recorded된 비디오들이 서버에 저장되어있고, 유저는 비디오를 보고싶을 때마다(on demand) server에 request를 보냄

### Video?
- 일련의 이미지 집합
- constant rate로 화면에 표현됨
- 인코딩된 이미지는 픽셀 어레이로 구성되어, 각 픽셀은 픽셀의 밝기와 색상을 나타내는 비트로 인코딩
- 비디오의 특징은 압축이 가능하다는 것. 단 비디오의 화질과 trade off 관계
- 전송될 때 bit rate 소모가 매우 큼
- ==**streaming video의 품질을 측정하기 위해서는 end-to-end throughput을 고려해야 함**==
	- 영상이 끊기지 않고 계속 재생되게 하려면, 네트워크는 streaming application에 compressed video의 bit rate이상의 throughput을 제공하여아 함


<hr>


## 2.6.2 HTTP Streaming and DASH
0.  HTTP streaming 환경에서, 비디오는 다른 파일들(object)과 동일하게 URL로 참조되며 HTTP server에 저장되어 있음
1. 유저가 비디오를 보고자 한다면, HTTP server와 TCP connection을 생성
2. 비디오 URL에 대해 GET request를 보냄
3. Server는 비디오 파일을 포함한 response를 네트워크 프로토콜과 트래픽이 수용가능한 선에서 최대한 빠르게 전송
4. client는 도착한 비디오 데이터 byte를 buffer에 저장
5. buffer의 용량이 threshold를 초과하면, client의 video application은 영상 재생을 시작
	1. streaming video application은 주기적으로 client buffer에서 video frame을 꺼내와서
	2. video frames의 압축을 해제
	3. user의 화면에 영상을 디스플레이

- 위의 HTTP streaming은 client가 사용가능한 bandwidth의 범위가 달라질 수 있음에도 불구하고 항상 동일한 비디오 인코딩 값을 수신해야 한다는 단점 → DASH라는 대안

### Dynamic Adaptive Streaming over HTTP
- DASH에서 비디오는 다른 bit rate를 가진( = 다른 비디오 품질을 가진) 여러 버전으로 인코딩됨
- client는 특정 길이의 video segment chunk를 동적으로 request
	- amount of available bandwidth is high : client는 high-rate version의 chunk를 요청

- client는 HTTP GET request message를 보낼 때마다 어떤 version의 chunk를 요청할지 결정
- **==즉, DASH는 client의 Internet access rate에 따라 다른 encoding rate로 비디오를 스트리밍할 수 있게 함==**
	(예) `Clients with fiber connections can receive a high-quality version.`

- available end-to-end bandwidth가 session도중에 바뀌어도 client가 available한 bandwidth에 대응하는 인코딩 방식을 받아볼 수 있도록 함
- **==DASH에서, 각 video version은 다른 URL을 가지고 HTTP server에 저장됨==**

#### Manifest File
- bit rate에 따라 구분되는 여러 video file version의 URL을 담은 파일
- client는 manifest file에 먼저 request하여 여려 version을 확인하고, 요청하고자 하는 version의 URL과 byte range를 결정하여 HTTP GET request를 보냄
- 파일을 다운로드 받는 동안에도, client는 수신 bandwidth를 측정하고 다음 request chunk의 version을 결정하기 위한 알고리즘을 수행


<hr>


## 2.6.3 Content Distribution Networks
- 하나의 ㅈㄴ 큰 datacenter를 만들어서 모든 비디오를 때려 저장하고 전 세계의 client에게 직접적으로 비디오를 스트리밍 해주는 방식이 가장 단순함. 
	- client가 data center에 멀리 떨어져 있다면, server-to-client packet이 수많은 communication link와 ISPs를 거쳐야 하는데 만약 이 links 중 하나라도 video consumption rate보다 낮은 transmission rate를 가진다면 end-to-end throughput 역시 매우 작아질 것
	- popular video는 동일한 communication link를 여러번 지나게 되는데, 이는 대역폭의 낭비일 뿐만 아니라 전송한 bytes 수만큼 ISP에 비용을 지불해야 하는 video company에게도 금전적으로 손해임
	- single data center, a single point of failure. centralized된 서버의 문제점

### Content Distribution Networks (CDNs)
- CDN은 지리적으로 분산된 server들을 관리하고, 비디오 및 여러 웹 콘텐츠를 server에 저장하고, 사용자가 최적의 경험을 누릴 수 있는 CDN location으로 user request를 연결함
- **private CDN** : google, YouTube와 같은 content provider에 의해 운영되는 사설 CDN
- **third-party CDN** : 여러 content provider를 대신하여 콘텐츠를 분산시키는 CDN

>[!info] CDN philosophies
>**1.Enter Deep**
>- ISP의 access network 속에 server cluster를 위치시킴으로써 user와의 거리를 줄임. user와 CDN사이의 link와 router를 줄여 user-perceived delay와 throughput을 향상 (link의 개수가 늘어날수록 병목현사응로 인해 throughput이 낮아질 가능성 多 이므로). 하지만 highly distributed design으로 인해 cluster를 유지/관리하기 어렵다는 단점
>
**2. Bring Home**
>- access network에 cluster를 위치시키는 대신, 더 적은 수의 cluster를 큰 규모로 설계. CDN을 Internet Exchange Points(IXPs)에 위치시킴. Enter Deep philosophy와 비교했을 때 유지/관리에 소모되는 자원이 적으나 delay와 throughput 측면에서는 뒤쳐짐

- cluster가 생성되면 CDN은 그 cluster에 비디오 콘텐츠의 복사본을 저장함
	- 각 cluster에 모든 video content의 복사본이 있는 것은 아님 - 어떤 비디오는 인기가 없을 수도 있고, 일부 지역에서만 인기 있을 수 있으므로

- 만약 client가 cluster가 가지고 있지 않은 video를 요청하는 경우, cluster는 central repository 혹은 이웃한 cluster로부터 video data를 pull해옴
	- client에게 video stream을 제공하는 동시에 데이터를 cluster에 locally 저장
		- Web Caching과 동일한 논리
		- cluster storage가 다 차면, 드물게 request된 비디오를 삭제시킴

### CDN Operation
1. User의 browser가 어떤 video를 불러오기를 요청 (identified by URL)
2. CDN은 해당 request에 대한 요청을 가로채기 함
3. client에게 최적의 CDN server cluster를 결정
4. client request를 1에서 결정된 cluster로 redirect
	💡 ==**대부분의 CDN는 request를 가로채기하고 redirect할 때 DNS를 사용**==

>[!example] DNS redirects a user's request to a CDN server
>![](https://i.imgur.com/9S9uUfd.png)
>1. user는 NetCinema 홈페이지를 방문
>2. 유저가 `http://video.netcinema.com/6Y7B23V` 링크를 클릭하면, 유저의 host는 `video.netcinema.com`에 DNS 질의를 보냄
>3. Local DNS server는 NetCinema의 Authoritive DNS server에 접근하기 위해 DNS query를 iterative하게 질의함. 
>4. Local DNS server가 Authoritive DNS server에 DNS query를 보냈을 때, Authoritive DNS server는 `http://video.netcinema.com`의 **==IP address를 반환하는 것이 아니라 CDN server의 domain을 반환함==**
>5. 이후 Local DNS server는 CDN server에 DNS query를 보내게 됨
>6. CDN DNS system은 Local DNS server에 content server의 IP address를 전달
>7. Local DNS server는 user's host에게 질의를 보낼 content server의 IP address를 전달
>8. client는 content server와 direct ==**TCP connection**==을 생성하여 HTTP GET request를 보내고 DASH를 통해 데이터를 내려받음

### Cluster Selection Strategies
- client를 server cluster 혹은 datacenter와 어떻게 연결할 것인지의 문제
- CDN이 Local DNS server로부터의 질의를 가로채는 과정에서 Local DNS server의 IP address를 확인하고, CDN은 이 IP address에 기반하여 적절한 CDN cluster를 배정함
- **geographically closet**
	- 각 Local DNS server는 지리적인 위치에 매핑되어 있음
	- Local DNS server로부터의 질의가 들어왔을 때, CDN은 지리적으로 가장 가까운 CDN cluster을 배정함
	- 그러나 지리적으로 가장 가까운 cluster가 network path의 hop 횟수 측면에서는 가장 가깝지 않을 수 있다는 한계
	- 일부 end user가 Local DNS server로부터 멀리 떨어져 있을 수도 있음!
	- internet path의 available bandwidth와 delay 변동을 무시한 strategy
	- 특정 client에게 항상 동일한 cluster를 배정

- real-time measurements
	- current traffic condition을 고려한 최적의 cluster를 배정하는 방식
	- CDNs은 주기적으로 cluster-client간의 delay/loss performance를 측정
		- cluster가 모든 Local DNS server에 ping message를 보내는 방식으로 구현되나, 대부분의 Local DNS server가 이러한 ping에 응답하지 않도록 설계되어있다는 큰(!) 한계


<hr>


## 2.6.4 Case Studies: Netflix and YouTube
### Netflix
- Netflix video distribution은 Amazon cloud & private CDN infrastructure로 구성
- Netflix Web Page의 여러 기능들은 Amazon cloud의 server 위에서 작동함
- Amazon cloud는 아래의 기능들을 제공함 :
	- **Content ingestion** : 고객들에게 video를 분배하기 전에, 영화의 studio master vesion을 받아서 Amazon cloud의 host에 비디오 파일을 저장
	- **Content processing** : client의 다양한 video player 종류에 대응할 수 있도록 (TV, game console, ...) 영화의 다양한 format 버전을 제작. ==**여러가지 player format과 bit rates에 따라 DASH를 이용한 HTTP 프로토콜 상에서 전송될 수 있도록 다양한 버전을 제작**==
	- **Uploading versions to its CDN** : movie의 새로운 versino이 만들어지면 Amazon cloud는 해당 버전을 CDN에 업로드함

#### How does Netflix server works
1. user가 재생하고자 하는 영화를 선택
2. Amazon Cloud에서 작동하는 Netflix software는 어떤 CDN server가 영화의 사본을 가지고 있는지를 확인
3. 영화의 사본을 가지고 있는 CDN servers 중, client request를 처리할 수 있는 최적의 서버 선택
4. CDN server가 선택되면, Netflix software는 manifest파일과 CDN server의 IP address를 client에게 전달
5. client와 CDN server는 DASH를 통해 적절한 버전으로 비디오 파일을 송수신

- DNS redirection 과정을 거치지 않고, Netflix software가 직접 통신할 CDN server를 지정해준다는 특징
- pull caching 대신에 push caching을 사용















