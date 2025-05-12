# basic_CAN-2025
CAN 통신 기초

## 1일차
- CAN 통신 등장 배경 및 개요
	- 차량 내 전장 장치들과 제어기(조향장치, 현가장치, 구동장치, 네비게이션 등)들이 서로 정보를 주고 받을 필요가 생김
	- 차량 내에서의 장치들과 제어기들이 점점 많아지며 특화된 통신이 필요하게 됨
	- 1986년에 BOSCH 사에서 Controller Area Network(CAN) 개발함
	- [CAN Specification] http://esd.cs.ucr.edu/webres/can20.pdf

- CAN통신 토폴로지 & 전송방식
	- 차량 내의 장치와 제어기들의 통신을 위해 서로서로 유선으로 연결하게 되면 굉장히 비효율적이다.
	- 그래서 CAN 통신은 버스형 토폴로지 방식을 채택했다.

	![버스형 토폴로지](https://github.com/breadcoffee/basic_CAN-2025/blob/main/image/image1.png)

	- 가운데 공통으로 두고 다같이 쓰는 선을 "버스"라고 부른다.
	- 모든 메시지는 브로드캐스트 방식으로 송신(목적지를 특정하지 않음)
	- 장점 : 목적지를 특정하지 않아 장치를 얼마든지 추가해도 상관없다.
	- 단점 : 한 장치가 고장 나 이상한 신호를 계속해서 보내면 다른 제어기에도 영향을 줌
	- 보안 : 장치가 고장이 나면 Bus Off 해서 다른 장치들끼리는 통신이 가능하게 만듬

- CAN 커넥터
	- 모든 제어기들의 연결은 D-Sub 9pin 커넥터를 사용
	- CAN High, CAN Low 시그널로 통신을 하는데 편의상 CAN_H, CAN_L 로 표시함

![CAN 커넥터](https://github.com/breadcoffee/basic_CAN-2025/blob/main/image/image2.png)

	- CAN_L는 2번 핀에 연결하고 CAN_H는 7번 핀에 연결하기로 정해놓음
	- 모든 제어기는 버스에 2, 7번 핀으로 연결됨

- CAN 저항
	- 통신을 통해서 데이터를 보낸다는 것은 전선을 통해서 전기적 신호를 전달한다는 것
	- 이때 버스 양 끝단에서 신호가 반사가 되어 신호가 왜곡됨
	- 그래서 양 끝단에 120옴 저항을 달아 신호 왜곡을 방지함 

![CAN 저항](https://github.com/breadcoffee/basic_CAN-2025/blob/main/image/image3.png)

	- 실제 개발 시 시뮬레이션 장비를 사용하는데 그때엔 120옴 저항이 달린 D-Sub 커넥터를 달아서 사용함

- CAN High & CAN Low signal

![CAN H & L](https://github.com/breadcoffee/basic_CAN-2025/blob/main/image/image4.png)

	- CAN_H, CAN_L 간의 전압 차를 이용하여 데이터를 표현한다.
		- 전압차를 이용하면 Noise에 강하다.
		- 외부의 전압 Noise 값이 들어오면 CAN_H와 CAN_N 둘 다 영향을 받는다.
		- 하지만 두개의 값의 차로 값을 정하기 때문에 Noise 값에 영향을 받지 않는다.
		- 외부의 영향을 동시에 받기 위해선 CAN_H와 CAN_N의 전선을 꼬아서 사용한다.
	
	- CAN_H - CAN_L 값이 0.9 ~ 5V = 0으로 표현
	- CAN_H - CAN_L 값이 -0.1 ~ 0.5V = 1로 표현

![CAN 0과1](https://github.com/breadcoffee/basic_CAN-2025/blob/main/image/image5.png)

	- CAN 통신에서는 0은 dominant(우성 값), 1을 recessive(열성 값)으로 표현한다.
	- 0의 값이 더 우선순위가 높다. 동시에 전기적인 신호를 발생하면 0과 1이 충돌하면 0의 값을 우선한다.

