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
- 

