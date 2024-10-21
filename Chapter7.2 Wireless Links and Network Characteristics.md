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

