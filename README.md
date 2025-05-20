# 🛗 RTOS-Based Elevator System  
**with STM32 + I2C LCD + Step Motor**  

---

## 📌 프로젝트 개요

- **목적**: RTOS 환경에서 **멀티태스킹 기반의 엘리베이터 제어 시스템** 구현
- **주요 기능**: 모터 구동, 층수 감지, 사용자 버튼 입력, 시간 표시, 애니메이션 출력 등
- **통신 방식**: I2C 프로토콜 직접 구현을 통한 LCD, RTC 연동

---

## 🔧 사용 부품

| 부품                     | 역할                             |
|--------------------------|----------------------------------|
| STM32F411RE              | 메인 MCU                         |
| Step Motor + Driver      | 엘리베이터 이동                  |
| Photoelectric Switch     | 각 층 감지 (정확한 위치 파악)   |
| Button (1~4)             | 층 선택                          |
| DS1302                   | 시간, 날짜 표시 (RTC)           |
| I2C LCD 1602             | 시간 및 층수 정보 출력          |
| Dot Matrix               | 현재 층 시각화                  |
| LED Bar                  | 이동 애니메이션, 층 선택 표시   |
| Buzzer                   | 도착 알림음 출력                |

---

## 🧠 시스템 구조

### FSM (상태 천이도)
- `IDLE`: 대기 상태, 모드 전환 가능
- `FORWARD`: 모터 전진 → 위층으로 이동
- `BACKWARD`: 모터 후진 → 아래층으로 이동

### 층수 선택 모드
- 버튼 0~3 → 1~4층 선택/취소
- 선택한 층을 배열에 저장 (`selected_floors`)
- 중복 선택 시 제거 처리 → 배열에서 순차 이동

---

## 🛠 주요 기능 설명

| 기능 구성              | 동작 요약 |
|------------------------|-----------|
| **Step Motor**         | 상태에 따라 전진/후진/정지 |
| **Photo Switch**       | 각 층마다 설치 → 위치 감지 및 current_state 갱신 |
| **LED Bar**            | 애니메이션 효과로 이동 방향 시각화 |
| **Dot Matrix**         | 현재 위치 및 모드 정보 표시 |
| **Buzzer**             | 도착 시 효과음 |
| **I2C LCD & DS1302**   | 날짜, 시간 출력 |
| **Interrupt**          | 외부 인터럽트 (PA0, PB1, PB2, PA4)로 층 감지 |
| **I2C**                | GPIO 직접 제어 방식 (bit-banging)으로 구현 |

---

## 🔄 I2C 통신 상세

- GPIOB의 SDA, SCL 직접 제어 (`GPIOB->ODR`, `GPIOB->IDR`)
- `i2c_start()`, `i2c_stop()`, `i2c_send_bit()`, `i2c_send_byte()` 직접 구현
- Clock Stretching 고려
- 데이터 수신 시 `ACK`, 종료 시 `NACK` 처리
- DS1302 및 LCD1602와의 통신 처리

---

## 🧵 RTOS Task 구성

| Task 이름        | 기능 설명 |
|------------------|------------|
| `defaultTask`    | `ds1302_main()` 실행 (RTC 시간 처리) |
| `myTask02`       | `dotmatrix_main_test()` 실행 |
| `myTask03`       | `stepmotor_main()` 실행 |
| `myTask04`       | 이동 방향에 따른 LED 애니메이션 |
| `myTask05`       | `fnd_display()`로 FND 층수 출력 |

---

## 📽 시연 영상

- 🔗 [Elevator 시연 영상 보기](https://youtube.com/shorts/RyurffD4WDc)

---

## 🧠 고찰

> 직접 I2C 프로토콜을 GPIO 제어로 구현하고, 외부 인터럽트 및 RTOS Task로 각 기능을 동시 처리함으로써, **임베디드 멀티태스킹 및 통신 구현 능력**을 실전으로 훈련할 수 있었다. 하드웨어 이벤트 기반의 FSM 제어와 타이밍 조정, 인터럽트 우선순위 설정 등을 통해 시스템 동기화의 중요성을 체감하였다.

---
