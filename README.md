# MultiCore

## CAN 통신이란?

**-정의**
- 자동차 및 산업 장비에서 사용하는 **직렬 데이터 통신 프로토콜**

**-특징**
- CAN_H 와 CAN_L의 전압차이로 ECU간 통신을 위해서 사용한다.
- 기존에 점대점으로 연결된 차량 네트워크 시스템의 단점을 극복하고자 CAN 통신을 사용한다.
- ECU 안에 CAN TRANSCEIVER 가 있고 그 CAN TRANSCEIVER 가 CAN_H CAN_L 형태로 신호를 바꿔서 전달한다.

![CAN 통신 구조](./images/image1.png)

## CAN FRAME 구조

![CAN Frame](./images/image2.png)

- SOF 는 Start of Frame 의 약자 , 메세지의 시작을 알리는 BIT 1에서 0으로 변하여 메시지의 시작을 알린다.
- Identifier :이 메세지가 어떠한 메세지 인지 알 수 있도록 나타낸다.
- RTR (Remote Transmission Request ) 이 값이 1이면 remote frame , 0이면 data frame 을 나타낸다.
- DLC :Data Lenght Code 의 약어로 메시지 안에 전달하는 Data 바이트 수를 표시한다.

## **Aurix Tc275 in Can Trancever Pin Map**

![TC275 Pin Map](./images/image3.png)

- TC275에는 CAN Transceiver가 내장되어 있으며, MCU와의 연결은 P20.7이 RX, P20.8이 TX로 되어 있는 것을 확인하였다. 이후 TC275에서 제공하는 예제 코드를 활용하여 동작을 확인하고, 오실로스코프를 통해 TXD 핀 신호를 계측하였다.

![TLE9251V 칩](./images/image4.png)

- 왼쪽은 TC275의 CAN Transceiver와 관련된 TLE9251V 칩의 데이터 시트이며, 오른쪽은 TC275에서의 TLE9251 칩 배치 모습이다. 우리는 해당 TXD 메시지를 오실로스코프를 사용하여 계측하였다.

### 측정결과

![측정 결과 1](./images/image5.png)

- 아무런 동작도 하지 않음을 발견했다.

![핀맵](./images/image6.png)

- 핀맵을 보면 저 핀이 동작하기 위해서는 LOW 로 입력이 되어야 한다고 나와있다.
- 저 PIN 은 MCU 의 P20.6번핀과 연결되어있다.
- P20.6 번을 Digital Output Pin 속성을 LOW 로 바꾸었다.

### 코드

![코드](./images/image7.png)

### 측정결과

![측정 결과 2](./images/image8.png)

## CANOE 를 활용한 메세지 테스트

- CANOE란차량 내 네트워크(예: CAN, LIN, FlexRay, Ethernet 등)를 시뮬레이션하고 분석하며, 테스트할 수 있는 강력한 플랫폼이다
- 우리는 VECTOR 사의 VN1630A 를 활용해서 TC275 에서 보낸 메세지를 확인했다

## 메시지 설정

![메시지 설정](./images/image9.png)

- 메시지 아이디는 0x200 이며 , dataLow , dataHigh 를 설정해주었다.

## DBC파일설정

![DBC 파일](./images/image10.png)

- DBC 파일을 생성할 때 메시지 ID를 0x200으로 설정하였으며, TC275에서 송신한 메시지도 동일하게 ID 0x200을 사용하였다.

## 결과

![결과](./images/image11.png)

## **CAN Communication Aurix Tc275**

![CAN 통신 코드](./images/image12.png)

- 메시지를 받을때마다 인터럽트 처리를 했다.
- u32nuTemp 라는 변수에 CANoe 에서 보낸 값을 저장한후 10ms 로 다시 CANoe 에 TX한다.

## 결과화면

![결과 화면](./images/image13.png)

- CANoe(Tx)->Tc275 (Rx)
- Tc275 (Tx)->CANoe(Rx)

## TC275 에서 받은 값 검증

![값 검증](./images/image14.png)

## Multicore

- Core 0 → CAN 통신
- Core 1 → EMB Ai Model

![Multicore 구조](./images/image15.png)

- Canoe 에는 메시지를 받을때마다 인터럽트를 처리하고 ProcessingFlag를 다시 1로 바꾸었다.
- Cpu 1의 함수에서는 ProcessingFlag 가 1인 경우에만 데이터를 처리하고 데이터 처리 이후는 다시 0으로 바꾸었다.

## **결과 화면 및 문제인식**

![문제 발견](./images/image16.png)

- 값이 잘 들어가고 있지만 값이 이상한것을 발견하였다.
- CANoe 에서 확인했을때 원본 데이터와 값이 다르다.

## **매개변수 타입 불일치 문제 & 문제해결**

- AI 모델은 double(64비트)값을 Input 으로 받게되어있는데 Tc275 에서는 Float(32비트)씩 데이터를 받고 보내고 처리하게 되어있다.
- 비트연산을 이용해서 CANOE 에서 날라온 float 형 데이터 2개를 합쳐서 Double 로 만들어서 AI모델에 Input 값으로 넣었다.
- DBC에 float 형으로 정의를 해놨다.

![코드 수정](./images/image17.png)

- CANOE-> TC275 데이터를 합쳐서 AI 모델에 맞는 데이터 형으로 바꿔준다.
- memcpy 를 활용해서 IQ,POS 값에 메모리를 복사해준다.
- TC275 에서는 Float 형으로 데이터 처리하게 되어있어서 체크도 해제 해주었다.
- TC275 에서는 다시 데이터를 쪼개서 CANOE로 보내게 했다.

## DBC 수정

![DBC 수정 1](./images/image18.png)

![DBC 수정 2](./images/image19.png)

![DBC 수정 3](./images/image20.png)

## **Simulink 블록과 Aurix 보드(CAN 통신) 결과 비교**

![Simulink 비교 1](./images/image21.png)

![Simulink 비교 2](./images/image22.png)
