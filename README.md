# MultiCore

## CAN 통신이란?

**-정의**
- 자동차 및 산업 장비에서 사용하는 **직렬 데이터 통신 프로토콜**

**-특징**
- CAN_H 와 CAN_L의 전압차이로 ECU간 통신을 위해서 사용한다.
- 기존에 점대 점으로 연결된 차량 네트워크 시스템의 단점을 극복하고자 CAN 통신을 사용한다.
- ECU 안에 CAN TRANSCEIVER가 있고 그 CAN TRANSCEIVER가 CAN_H CAN_L 형태로 신호를 바꿔서 전달한다.

<img width="793" height="501" alt="image (3)" src="https://github.com/user-attachments/assets/9da40399-eb62-4d7a-8324-519c76177e49" />



## CAN FRAME 구조

<img width="1501" height="557" alt="image (4)" src="https://github.com/user-attachments/assets/3dcbf468-146e-4c74-a65c-09c7e680e5f5" />

- SOF 는 Start of Frame의 약자 , 메시지의 시작을 알리는 BIT 1에서 0으로 변하여 메시지의 시작을 알린다.
- Identifier :이 메시지가 어떠한 메시지 인지 알 수 있도록 나타낸다.
- RTR (Remote Transmission Request )이 값이 1이면 remote frame , 0이면 data frame을 나타낸다.
- DLC :Data Length Code의 약어로 메시지 안에 전달하는 Data 바이트 수를 표시한다.


## **Aurix Tc275 in Can Trancever Pin Map**
<img width="1512" height="452" alt="image (5)" src="https://github.com/user-attachments/assets/ae72768b-bb06-457a-9947-e36d55602454" />

- TC275에는 CAN Transceiver가 내장되어 있으며, MCU와의 연결은 P20.7이 RX, P20.8이 TX로 되어 있는 것을 확인하였다. 이후 TC275에서 제공하는 예제 코드를 활용하여 동작을 확인하고, 오실로스코프를 통해 TXD 핀 신호를 계측하였다.


<img width="1777" height="737" alt="image (6)" src="https://github.com/user-attachments/assets/6e75163e-6f77-403b-94cd-b39657c78fbe" />
- 왼쪽은 TC275의 CAN Transceiver와 관련된 TIE 9251V 칩의 데이터 시트이며, 오른쪽은 TC275에서의 TIE 9251 칩 배치 모습이다. 우리는 해당 TXD 메시지를 오실로스코프를 사용하여 계측하였다.


<img width="1048" height="593" alt="image (7)" src="https://github.com/user-attachments/assets/8b392f90-c579-4cd1-a77e-7e58d76905d2" />
- 핀맵을 보면 저 핀이 동작하기 위해서는 LOW로 입력되어야 한다고 나와 있다.
- 저 PIN은 MCU P20.6번 핀과 연결되어있다.
- P20.6번을 Digital Output Pin 속성을 LOW로 바꾸었다.

### 코드
<img width="1800" height="538" alt="image (9)" src="https://github.com/user-attachments/assets/1b84b498-e79a-4eae-be99-8ef4a93dc431" />


### 결과
<img width="1237" height="742" alt="image (10)" src="https://github.com/user-attachments/assets/dd7dc747-e905-43e3-8598-6445d38ae5e7" />



## CANOE 를 활용한 메세지 테스트

- CANOE란 차량 내 네트워크(예: CAN, LIN, FlexRay, Ethernet 등)를 시뮬레이션하고 분석하며, 테스트할 수 있는 강력한 플랫폼이다
- 우리는 VECTOR 사의 VN1630A를 활용해서 TC275에서 보낸 메시지를 확인했다



## **CAN Communication Aurix Tc275**

<img width="1310" height="444" alt="image (14)" src="https://github.com/user-attachments/assets/b317d249-53af-4c04-9764-0937b22f2ddd" />


- 메시지를 받을 때마다 인터럽트 처리를 했다.
- u32nuTemp 라는 변수에 CANOE에서 보낸 값을 저장한 후 10ms로 다시 CANOE에 TX 한다.



## 결과

<img width="1381" height="651" alt="image (15)" src="https://github.com/user-attachments/assets/0dbf2d33-20d9-4549-be1f-17f6c8badcaa" />


- CANOE(Tx)->Tc275 (Rx)
- Tc275 (Tx)->CANOE(Rx)

## TC275 에서 받은 값 검증

<img width="1261" height="583" alt="image (16)" src="https://github.com/user-attachments/assets/238332a7-7c3c-41ff-af82-82c8a2eab5f2" />


## Multicore

- Core 0 → CAN 통신
- Core 1 → EMB Ai Model

<img width="1035" height="503" alt="image (17)" src="https://github.com/user-attachments/assets/63d2eaed-9cb5-4160-a2ab-785dd8ffdd78" />


- CANOE에는 메시지를 받을 때마다 인터럽트를 처리하고 Processing Flag를 다시 1로 바꾸었다.
- CPU 1의 함수에서는 Processing Flag가 1인 경우에만 데이터를 처리하고 데이터 처리 이후는 다시 0으로 바꾸었다.

## **결과 및 문제인식**

<img width="1481" height="494" alt="image (18)" src="https://github.com/user-attachments/assets/f037fc20-b462-4401-bc74-406c45077644" />


- 값이 잘 들어가고 있지만, 값이 이상한 것을 발견하였다.
- CANOE에서 확인했을 때 원본 데이터와 값이 다르다.

## **매개변수 타입 불일치 문제 & 문제해결**

- AI 모델은 double(64비트) 값을 Input으로 받게 되어있는데 Tc275에서는 Float(32비트)씩 데이터를 받고 보내고 처리하게 되어있다.
- 비트연산을 이용해서 CANOE에서 날라온 float 형 데이터 2개를 합쳐서 Double로 만들어서 AI 모델에 Input 값으로 넣었다.
- DBC에 float 형으로 정의해놨다.

  
<img width="1539" height="573" alt="image (19)" src="https://github.com/user-attachments/assets/f67c55d2-21a8-40b9-a566-7019a15dfa5a" />



- CANOE-> TC275 데이터를 합쳐서 AI 모델에 맞는 데이터형으로 바꿔준다.
- memcpy를 활용해서 IQ,POS 값에 메모리를 복사해준다.
- TC275에서는 Float 형으로 데이터 처리하게 되어있어서 체크도 해제해주었다.
- TC275에서는 다시 데이터를 쪼개서 CANOE로 보내게 했다.

## DBC 수정
<img width="705" height="455" alt="image (20)" src="https://github.com/user-attachments/assets/cfe76576-4cbc-4c28-a71d-8ec08e8fcc68" />
<img width="721" height="465" alt="image (21)" src="https://github.com/user-attachments/assets/ab0c6885-a90c-4a1b-967f-0dd7148296c5" />
<img width="739" height="477" alt="image (22)" src="https://github.com/user-attachments/assets/fc1cbba9-203d-400c-9dba-bf31e25384d2" />



## **Simulink 블록과 Aurix 보드(CAN 통신) 결과 비교**

<img width="1248" height="861" alt="image (23)" src="https://github.com/user-attachments/assets/583be99f-0a18-4476-8109-9a2d6107e6ab" />


<img width="1575" height="814" alt="image (24)" src="https://github.com/user-attachments/assets/3392d769-f197-4ebe-a302-30bc2aed6838" />




