### Wireless가 Wired와 가지는 차이점
- 신호 세기의 감소 : 전자기파 신호가 퍼져나가는 과정에서 신호 세기가 약화됨 ( = path loss)
- 다른 source의 방해 : wireless network 주파수는 여러 디바이스가 공유하므로 여러 디바이스가 전파를 동시에 송신하는 경우 서로 간섭이 있음
- Multipath Propagation : 전자기파가 전파되는 과정에서 지면 혹은 물체에 부딪히면서 데이터의 목적지 도착 시각이 제각각
- 즉, wired방식에 비해 wireless방식이 bit error의 검출 가능성이 더 높음
	- CRC error detection code, link-level 신뢰적 데이터 전송 프로토콜의 오류 프레임 재전송
### SNR (Signal-to-Noise Ratio)
- host는 송신자로부터 원 신호가 조금 훼손된(degraded) 전자기 신호를 수신 + 노이즈
- SNR : 수신의 세기와 잡음의 비율
	- ==SNR ↑ = 잡음에 비해 수신의 세기가 강함. noise로부터 신호를 추출하기 쉬움==
![](https://i.imgur.com/hNlgIr9.png)
- BER(Bit Error Rate) : 전송된 bit가 수신측에서 에러로 검출될 확률
	- 즉, ==SNR↑ + BER↓이고 SNR↓ + BER↑==
- 하나의 physical layer에서는 : 출력 에너지 증가 ▶ SNR↑ ▶ BER↓
	- 3개의 physical layer는 SNR-BER에 대해 모두 반비례 관계를 가짐
- 동일 SNR 수준에서는 : ==physical layer 전송률↑+ BER↑==
	- ❓ 전송률이 높을수록 Error Rate가 높아지는 이유는 뭘까?

### Hidden Terminal Problem
![](https://i.imgur.com/GtFY4CI.png)
- A->B로 데이터를 전송하고 있는데 C->B도 데이터를 전송하고 있는 경우. 그리고 장애물에 의해 A와 C가 서로의 존재를 인식하지 못하고 있는 경우. B는 동시에 두 터미널로부터 데이터를 전송받으며 간섭 발생 (A and C are unaware of their interference at B)

### Fading
![](https://i.imgur.com/j5nn6aU.png)
- 무선 medium을 통해 데이터가 전달되는 과정에서 전파의 충돌이 발생하고 그 세기가 약화됨
- `A and C are placed such that their signals are not strong enough to detect each other’s transmissions, yet their signals are strong enough to interfere with each other at station B.`


---


## 7.2.1 CDMA
- channel partitioning protocol의 일부
- 무선 LAN, cellular 기술에서 필수적
- `In a CDMA protocol, each bit being sent is encoded by multiplying the bit by a signal (the code) that changes at a much faster rate (known as the chipping rate) than the original sequence of data bits.`

![](https://i.imgur.com/nzAqs8Z.png)
- settings
	-  $d_i$를 i번째 bit slot의 데이터라고 가정
	- value가 1인 data bit : $d_i = 1$
	- value가 0인 data bit : $d_i = -1$
	- 각각의 bit slot은 M개의 mini slots으로 분할됨
	- $c_m$를 CDMA code의 m번째 비트라고 가정

- **there is only 1 sender**
	1. 각 time slot의 비트 $d_i$ 를 CDMA code($c_1, c_2, ..., c_m$) 와 곱한다
		- $Z_{i, m} = d_i * c_m$
		- bit slot의 데이터 $d_i$가 M칸의 sequential한 bits로 표현될 수 있음
	
	2. receiver은 일련의 $Z_{i, m}$ 데이터를 수신
	3. 수신한 $Z_{i, m}$에 대해 아래 수식을 계산하여 원본 $d_i$를 복원
		- $d_i = (1/M)*\sum_{m=1}^{M}(Z_{i, m}\cdot c_m)$


- **there is multiple senders**
	- CDMA는 여러개의 송신자로부터 수신받은 비트가 간섭되는 경우 그 값들이 더해진다고 가정함
		(예) -1, 1, 1, 1 비트가 도착했다면 receiver는 (-1) + (+1) + (+1) + (+1) = 2를 인식
	
	1. 송신자 s는 sender가 1개일 때의 경우와 동일하게 Z를 계산
		- $Z_{i, m}^{s} = d_i * c_m^s$
	
	2. 수신자는 동시에 n개의 sender로부터 i번째 bit slots을 수신함
		-  $Z_{i,m}^* = \sum_{s=1}^{N}(Z_{i,m}^s)$
		- i번째 bit slot를 M번째 code로 변환한 데이터들의 합
	
	3. 수신자는 sender의 code를 통해서 데이터를 복원
	![](https://i.imgur.com/rUg8sXc.png)

- **CDMA의 한계**
	- receiver가 Z를 원 데이터비트로 변환할 수 있도록 CDMA코드를 잘골라야 됨(?)
	- 모든 sender의 신호 세기가 receiver쪽에서 동등해야 함 (불가능)

