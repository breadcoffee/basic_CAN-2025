# basic_CAN-2025
CAN 통신 - 자동차 신입이 알아야 할 모든 것(Feat. 존버매니아)

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
	- 즉, 0이면 서로 전압의 차이가 커지고 1이면 서로 전압의 차이가 작아짐

	![CAN 0과1](https://github.com/breadcoffee/basic_CAN-2025/blob/main/image/image5.png)

	- CAN 통신에서는 0은 dominant(우성 값), 1을 recessive(열성 값)으로 표현한다.
	- 0의 값이 더 우선순위가 높다. 동시에 전기적인 신호를 발생하면 0과 1이 충돌하면 0의 값을 우선한다.

## 2일차
- CAN Tranceiver(Transmitter + Receiver) & CAN Controller
	- 전자 장치들을 제어하기 위해선 제어기가 필요
	- 제어기 안에는 여러 MCU(Micro Controller)가 존재함
	- MCU 내부에는 CPU와 메모리를 비롯 다양한 하드웨어 장치가 존재 -> peripheral이라고 말함

	![제어기 내부](https://github.com/breadcoffee/basic_CAN-2025/blob/main/image/image6.png)

	- CAN Controller를 통해서 버스의 사용유무 감지, 메세지 보내기, 에러처리 등 통신에 필요한 직접적인 역할을 수행
	- CAN Transceiver는 CAN_H인지 CAN_L인지 해석해 전압을 출력하는 역할을 함(들어오는 신호와 나가는 신호 둘 다 해석해줌)
	- CAN Controller 에서 나가는 신호는 CAN TX 핀으로 들어오는 신호는 CAN RX 핀을 통해 들어옴

	- CAN Controller : 메세지에 담기는 값을 처리 (Data Link Layer)
	- CAN Transceiver : 실제로 전선에 출력되는 전압을 출력 (Physical Layer)

- CAN Transceiver 데이터 시트 보기 (TJA1043)
	- General description : 기본적인 정의 설명
	- Functional description : 기능에 대한 설명
	- Pinning information : 핀에 대한 설명, **제일 중요**
	- Application information : 사용 예시에 대한 설명

- BaudRate
	- Baud Rate : 통신속도
	- 단위 : bps(bit per second), bps가 크면 클수록 1초에 더 많은 bit를 보낼 수 있음
	- 버스에 참여하고 있는 제어기들은 모두 같은 Baud Rate로 통신해야한다.
	- CAN DB : 완성차 회사에서 필요한 Spec을 적어놓은 DB
	
	![BaudRate](https://github.com/breadcoffee/basic_CAN-2025/blob/main/image/image7.png)

	- 프로토콜 별로 최대 속도가 정해져있다. (기본값은 High-Speed CAN임)
	- 속도가 빨라질수록 Bus의 길이가 짧아진다.

- Sampling Point
	- Sampling Point : 하나의 비트 값이 0인지 1인지 판단하는 지점을 의미, 단위는 %이다.
	- 이것도 OEM(제조사)에서 Spec으로 정해준다.

	![Sampling Point](https://github.com/breadcoffee/basic_CAN-2025/blob/main/image/image8.png)

	- CAN에서는 1bit를 위 그림처럼 Sync, TSEG1, TSEG2 3개의 구간으로 분류한다.
	- TSEG1, TSEG2의 길이를 세는 단위는 Time Qunta이다. (Time Qunta는 100Hz이면 0.01초, 1000Hz이면 0.001초)
	- Sync는 길이가 고정이고 Prop와 Phase1, Phase2의 길이를 조절해 Sample Point의 지점을 정해준다.
	- 속도가 1bps이면 1초에 1bit이고 10Hz라고 정의하면 1bit에 Time Qunta는 10 Time Qunta가 된다.
	- Sampling Point는 Time Qunta 값을 Phase1,2에 정의해서 정함

- Synchronization
	- CAN 통신은 BroadCast로 통신하기 때문에 모든 제어기는 동시에 신호를 받을 수 있어야함
	- 제어기마다 신호를 다르게 받아서 동작하게되면 오류가 생길 수 있음
	
	- Tseg1. 클락속도가 빠를 때
		- 한 제어기에 오류가 있어서 Clock 속도가 빠르면 신호를 더 빠르게 받게됨
		- 1bit 앞에는 Sync 신호가 있어 빠르게 받은 시간만큼 Phase1의 신호를 연장함

	- Tseg2. 클락속도가 느릴 때
		- 한 제어기에 오류가 있어서 Clock 속도가 느리면 신호를 더 느리게 받게됨
		- 1bit 앞에는 Sync 신호가 있어 느리게 받은 시간만큼 Phase2의 신호를 줄여서 시간을 맞춤

	- 길이를 늘리고 줄일 수 있는 최대 길이를 SJW(Synchronization Jump Width)라고 함

## 3일차
- 메시지의 ID

	![메시지 구조](https://github.com/breadcoffee/basic_CAN-2025/blob/main/image/image9.png)
	
	- CAN 메시지 내부에는 Tranmitter, Receiver 정보가 따로 없다. -> 시작 주소, 도착 주소가 없다.
	- broadcast 방식으로 메시지를 보내 누가 보낸건지 알 수 없다. -> 그래서 메시지 ID를 사용함
	- ID 별로 메세지의 이름을 붙이고 그 메시지를 누가 보낼건지 OEM에서 사전 정의해둔다.
	- 이런 정보가 담겨있는 자료를 CAN DB, Can DataBase, Commmuniation Matrix, K-Matrix 등으로 부른다.
	- 한 메시지 ID는 그 네트워크 안에서 하나의 제어기만 송신해야한다.

	- CAN 통신에서 메시지의 형태를 ID의 길이에 따라서 메시지를 2가지 포멧으로 구분한다.
	- Standard Format : 11bit
	- Extended Format : 29bit
	- 하나의 네트워크에서 혼용이 가능하다.

- 메시지의 우선순위 & Bus Load
	- BUS 안에서는 한번에 하나의 메시지만 보낼 수 있다.
	- ID의 값이 낮을수록 메시지의 우선순위가 높다.
	- 단. 우선순위는 동시에 보내질 때 우선순위에 따라서 정해진다.(이미 보내고 있는 메시지를 무시하면서 보내지는건 아니다.)
	- ID의 값은 메시지 필드에서 Arbitration(중재) Field에 들어간다.

	- 우선순위가 정해지는 방식
		- BUS에 동시에 메시지를 보내면 ID의 값를 이진수로 내보낸다.
		- CAN Controller는 자신이 보낸 값이 BUS에 전기적 출력이 잘되는지 확인한다.
		- 여러 제어기가 1를 내보내더라도 하나의 제어기가 0를 출력하면 BUS에는 0값만 적용이 된다.
		- 그러면 Controller는 자신이 보낸 값으로 바뀌지 않으면 자신보다 우선순위가 높은 제어기가 있구나 판단
		- 더이상 메시지는 보내지 않게 됨

	- BUS LOAD (단위 : %)
		- 전체 네트워크에서 일정한 시간동안 메시지가 송수신된 점유율을 말한다.
		- Bus Load가 높으면 통신이 힘들어질 수 있다.
		- 일반적으로 OEM에서는 최대 Bus Load를 정해준다.
		- Bus Load를 낮추기 위해선 제어기의 메시지의 주기를 낮춰준다.
		- Message Transmission Time/Period = %

## 4일차
- 메세지의 DLC와 시그널
	- 메세지에는 정보들이 담겨있다. 정보 하나하나를 시그널이라고한다.
	- 메세지 영역에서 다른 곳은 단위가 bit인데 Data 영역만 byte 단위이다.(최대 8byte)
	- 데이터 영역이 고정이 아니기 때문에 DLC 영역(Data Length Code)에서 데이터의 영역의 크기를 입력함
	 
	 ![메시지 영역](https://github.com/breadcoffee/basic_CAN-2025/blob/main/image/image10.png)

	- 데이터 영역에서 시그널의 자세한 정보가 필요
	- Signal Detail Information에서 데이터에 맞게 정보를 정리
	- Start bit, Length, Unit(축약어), signal comment(부가정보), Factor, Offset

	![Signal Information](https://github.com/breadcoffee/basic_CAN-2025/blob/main/image/image11.png)

- Factor & offset
	- 시그널에서 실수형 데이터는 Float으로 4byte를 차지
	- 데이터 영역의 낭비를 줄이기 위해서 사용

	- 보내려는 값이 실수일 때
	- Target Voltage : 12.4V, Factor = 0.1, Offset = 0
	- Transmitter : (Real Value - offset) / Factor => Tx Signal Value : 124
	- Receiver : (Rx Value x Factor) + offset => Receiver Interprete 12.4
	- 12.4 = Float type = 4byte = 32bit 인데 124 = 1111100 7bit 로 낭비를 줄임
	- Factor 값을 더 작게 설정할수록 결과 값을 더 정밀하게 사용할 수 있음
	- 단, 적절한 값을 사용 낭비를 줄일 수 있음

	- 반대로 보내려는 값이 클때
	- Target Voltage : 152,000V, Factor = 1000, Offset = 0
	- Tx Signal Value : (152,000-0)/1,000 = 152(8bit)
	- Receiver Interprete : (152 * 1,000) + 0 = 152,000(14bit)
	- 6bit를 절약할 수 있음

	- 마이너스 값을 보낼 때
	- Tx Signal : -300,000 <= Speed <= +300,000
	- Factor : 1000, Offset : -300,000
	- Tx Signal Value : -152,000-(-300,000) / 1,000 = 148
	- Receiver Interprete : (148 * 1,000) - 300,000 = -152,000
