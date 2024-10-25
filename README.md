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
- **CAN 메시지 형식**: CAN 메시지를 통해 모터에 다양한 명령을 전송하여 제어 가능.
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

### 4. **CAN 메시지를 통한 모터 제어 (mode0 함수 설명)**
```cpp
void mode0(uint16_t can_id, uint8_t ena, uint8_t dir, uint32_t pulse_target, uint16_t pps) {
  uint8_t data[8];
  data[0] = 0x00; // Mode 0: 모터 동작 모드를 지정
  data[1] = ena; // Enable: 모터 활성화(1 = 활성화, 0 = 비활성화)
  data[2] = dir; // Direction: 모터의 방향 (0 = 정방향, 1 = 역방향)
  data[3] = (uint8_t) (pulse_target);      // Pulse Target의 하위 바이트
  data[4] = (uint8_t) (pulse_target >> 8); // Pulse Target의 중간 바이트
  data[5] = (uint8_t) (pulse_target >> 16); // Pulse Target의 상위 바이트
  data[6] = (uint8_t) (pps); // Pulse Per Second의 하위 바이트
  data[7] = (uint8_t) (pps >> 8); // Pulse Per Second의 상위 바이트

  // CAN 메시지 전송 (8바이트 데이터)
  CAN.sendMsgBuf(can_id, 0, 8, data);
}
```
- **`mode0()` 함수 설명**:
  - CAN 통신으로 모터에 명령을 전송하는 함수입니다.
  - **`can_id`**: CAN 메시지의 고유 식별자로, 특정 모터 또는 장치를 구분하는 데 사용됩니다.
  - **`ena`**: 모터 활성화 여부를 나타내는 플래그 (1: 활성화, 0: 비활성화).
  - **`dir`**: 모터의 방향 설정 (0: 정방향, 1: 역방향).
  - **`pulse_target`**: 모터가 이동해야 하는 목표 펄스 수입니다. 24비트(3바이트)로 분할되어 메시지에 포함됩니다.
  - **`pps`**: 초당 펄스 수(Pulse Per Second, PPS)로, 모터의 속도를 제어합니다. 16비트(2바이트)로 전송됩니다.
  - 이 메시지는 CAN 통신을 통해 모터에 전송되며, 모터는 해당 명령을 수신하여 동작하게 됩니다.


