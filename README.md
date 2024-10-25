# Motor_Driving

LabView와 아두이노를 이용해 스텝모터를 제어하는 프로젝트입니다<br>


![Image Description](https://github.com/JungHoonKim-KR/motor-diriving/blob/main/SMC001.png)

## 모터 스펙 및 CAN 통신 요약

### 1. **모터 스펙**
- **스텝 모터**: 정확한 각도 제어가 가능한 모터, 펄스(PPS: Pulse Per Second)로 속도 제어.
- **Pulse per Revolution (PPR)**: 1회전 당 펄스 수, 예: 3200 PPR.
- **속도 제어**: `PPS`(Pulse Per Second) 값을 통해 속도를 제어하며, `PPS = 0`이면 모터 정지.
- **감속기 장착 가능**: 감속기를 통해 정밀한 제어 가능 (예: 100:1 감속기 사용).
- **가속/감속 제어**: `PPS` 값에 가속도(`acceleration`)를 적용해 부드러운 속도 제어 가능.

### 2. **CAN 통신**
- **CAN 버스**: 다중 장치가 연결된 네트워크에서 모터 및 제어 시스템 간의 통신을 담당.
- **모터 명령**: CAN 메시지를 통해 속도, 방향, 정지 등의 명령을 전달.
- **CAN 메시지 형식**: `mode0()`과 같은 함수로 CAN 메시지를 전송.
  - **ID**: CAN 메시지의 고유 식별자.
  - **Enable**: 모터 활성화 신호.
  - **Direction**: 모터의 방향 (예: 정/역방향).
  - **Pulse Target**: 목표 펄스 수.
  - **PPS**: Pulse Per Second, 모터 속도를 제어하는 값.
  
### 3. **모터 제어 방식**
- **속도 제어**:
  - **PPS 값**: 모터 속도를 제어하는 주요 값. `PPS = 0`이면 모터 정지.
  - **가속도(acceleration)**: 속도를 점진적으로 증가시키거나 감소시켜 부드러운 제어 가능.
  
- **정지 명령**:
  - **속도 0 설정**: `PPS = 0`으로 설정하여 모터 정지.
  - **비상 정지(E-STOP)**: 일부 모터 드라이버는 비상 정지 메시지로 즉시 멈추는 기능을 제공.

### 4. **CAN 버퍼 처리**
- **CAN 버퍼 초기화**: 수신된 메시지를 모두 읽어서 버퍼를 비움.
  - `while (CAN_MSGAVAIL == CAN.checkReceive())`로 버퍼에 남아 있는 메시지를 확인.
  - `CAN.readMsgBuf()`를 사용해 수신된 메시지를 읽고 버퍼를 비움.

### 5. **예시 코드 (모터 정지)**
```cpp
void stopMotor() {
  // 모터 속도를 0으로 설정하는 CAN 명령을 보냅니다
  mode0(0x001, 1, 0, 0, 0);  // ID = 0x001, Enable = 1, Direction = 0, Pulse Target = 0, Speed (PPS) = 0
}

void loop() {
  // 모터가 동작 중일 때
  if (/* 조건: 모터를 정지시켜야 할 때 */) {
    stopMotor();  // 모터 정지 명령
  }
}
```




