<h1> 🎨 Zynq 기반 DSP 시스템을 STM32H723 Nucleo 보드로 이식<br> 및 리팩토링한 실시간 신호처리 프로젝트</h1>
ZedBoard(Linux + Python FastAPI 기반)에서 구현된 실시간 DSP 파이프라인을

<code>STM32H723ZG Nucleo</code> 보드로 <b>포팅</b>하고, <b>RS-485 시리얼 통신</b> 기반으로 <b>PC ↔ PCB</b> 간<br>
<b>실시간 데이터 송수신 구조를 재설계</b>한 프로젝트입니다.


<br>

## 📑 프로젝트 요약



- 기간 : 2025.10.20 - 2025.10.31
- 인원 : 3명 (하드웨어 1 · 펌웨어/PM 1 · 소프트웨어/통신 통합 1)
- 설명 : <br> ZedBoard 환경에서 운영되던 AD4858 기반 실시간 DSP 시스템을<br>
 STM32 MCU(<code>CubeMX + HAL 기반 Bare-metal</code>) 환경으로 포팅하여,<br>
 <code>RS-485</code> <b>양방향 통신</b> 및 <b>실시간 데이터 파싱</b> 로직을 구현했습니다.<br><br>
 <b>PCB → PC</b> 방향의 실시간 DSP 데이터 전송 및 그래프 시각화는 정상 검증 완료되었으며,<br>
 <b>PC → PCB</b> 방향의 파라미터 송신 또한 정상 전송까지 확인했습니다.<br>
 다만, PCB 내부에서 해당 설정값을 실시간으로 DSP 연산에 반영하는 로직<br>
 (flag → parse → apply 경로) 은 현재 분리 설계 및 검증 중입니다.<br><br>
 또한, Python 기반 PC 모니터링 툴을 <code>.exe</code> 및 <code>.bat</code> 형태로 패키징하여,<br>
 Python 미설치 환경에서도 독립적으로 실행 가능한 배포용 프로그램으로 완성했습니다.<br>

<br>


## 🧑🏻‍💻 담당 역할

1. DSP 로직 포팅 :<br>
기존 ZedBoard C 코드(<code>iio_reader.c</code>) 기반 파이프라인을 STM32 환경으로 이식<br>
→ ADC(DMA) → 필터 → 평균 → Ratio → Polynomial 변환까지 모든 연산 단계 적용

2. RS-485 통신 프로토콜 설계 :<br>
<code>st|...|end</code> 프레이밍 규칙 정의 (PC→PCB 28필드 / PCB→PC 30필드)<br>
→ 설정 프레임(스칼라+배열)과 데이터 프레임(Meta+Payload)로 분리<br>
→ 노이즈 대응·길이 검증·파싱 안정화 로직 구현


3. 비차단(Non-Blocking) 구조 개선 :<br>
<code>HAL_Delay()</code> 제거 후 “벽시계 기반 전송(비차단 타이머)”으로 변경<br>
→ 실시간 DSP 루프 정지 없이 안전한 데이터 전송 구현<br>
→ ISR 경량화 : 인터럽트에서는 flag + buffer 처리만 수행 (무거운 파싱 제거)

4. PC 프로그램 패키징 및 배포 :<br>
Python FastAPI + WebSocket 기반 웹 UI를 <code>pyinstaller</code>로 <code>.exe</code> 변환 및 <code>.bat</code> 런처 제작<br>
→ “가상모드(Synthetic)” / “실장비(Serial)” 2모드 지원 + 메뉴얼 제작<br>
→ 다른 PC 환경(비개발 환경)에서도 독립 실행 가능

5. 노이즈 트러블슈팅 :<br>
실제 보드 연결 시 RS-485 라인 노이즈로 <code>st</code> 토큰 전 인식 실패 발생<br>
→ 시리얼 로그 분석 후 더미값 프리패딩으로 파싱 안정화

<br>

## 🧑🏻‍💻 직접 사용한 기술

| 구분 | 기술 |
| :--- | :--- |
| 📝 **Languages** | ![C](https://img.shields.io/badge/C-A8B9CC?style=for-the-badge&logo=c&logoColor=white) ![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white) ![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black) |
| 🔧 **Embedded / Hardware** | ![STM32H723ZG](https://img.shields.io/badge/STM32H723ZG%20(Nucleo)-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white) ![RS-485](https://img.shields.io/badge/RS--485-6E6E6E?style=for-the-badge&logo=serialport&logoColor=white) ![UART](https://img.shields.io/badge/UART-000000?style=for-the-badge&logo=serialport&logoColor=white) |
| 🧠 **Embedded Software** | ![STM32CubeMX](https://img.shields.io/badge/STM32CubeMX-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white) ![STM32%20HAL](https://img.shields.io/badge/STM32%20HAL-0096FF?style=for-the-badge&logo=stmicroelectronics&logoColor=white) ![Bare--metal](https://img.shields.io/badge/Bare--metal%20(No%20RTOS)-555555?style=for-the-badge&logo=arm&logoColor=white) |
| ⚙️ **Backend (PC)** | ![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white) ![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white) ![WebSocket](https://img.shields.io/badge/WebSocket-010101?style=for-the-badge&logo=socketdotio&logoColor=white) ![PySerial](https://img.shields.io/badge/PySerial-306998?style=for-the-badge&logo=python&logoColor=white) ![Uvicorn](https://img.shields.io/badge/Uvicorn-499848?style=for-the-badge&logo=python&logoColor=white) ![NumPy](https://img.shields.io/badge/NumPy-013243?style=for-the-badge&logo=numpy&logoColor=white) ![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white) |
| 🌐 **Frontend (Monitor UI)** | ![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white) ![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white) ![Chart.js](https://img.shields.io/badge/Chart.js-FF6384?style=for-the-badge&logo=chartdotjs&logoColor=white) |
| 🧰 **Testing / Debug** | ![SerialPortMon](https://img.shields.io/badge/SerialPortMon-555555?style=for-the-badge&logo=serialport&logoColor=white) ![RS-485%20Loopback](https://img.shields.io/badge/RS--485%20Loopback%20Test-6E6E6E?style=for-the-badge&logoColor=white) |
| 📦 **DevOps / Infra** | ![PyInstaller](https://img.shields.io/badge/PyInstaller%20(.exe)-3776AB?style=for-the-badge&logo=python&logoColor=white) ![Batch%20Script](https://img.shields.io/badge/.bat%20Launcher-4EAA25?style=for-the-badge&logo=windowsterminal&logoColor=white) ![VSCode](https://img.shields.io/badge/VSCode-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white) ![CubeIDE](https://img.shields.io/badge/CubeIDE-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white) ![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white) |


<br>

## ⚡ 기능 요약
- 🔄 실시간 DSP 파이프라인	<code>8ch ADC 입력 → LPF → TimeAvg → R, Ravg → y1, y2, y3, yt</code> 단계별 계산
- 💬 양방향 <code>RS-485</code> 통신	<code>PC↔PCB 간 28/30</code> 필드 구조의 프레임 송수신 및 실시간 파싱
- 🧠 비차단 구조 <code>HAL_Delay()</code> 제거 및 <code>ISR</code> 분리 → 벽시계 기반 <code>250ms</code> 주기 송신 구조로 리팩토링
- 🧩 시리얼 노이즈 트러블슈팅	ST 앞단 노이즈 감지 → 수신 동기화 보정 로직 임시 적용으로 파싱 안정화
- 💾 PC 모니터링 툴 패키징	pyinstaller 기반 <code>.exe</code> 빌드 및 Synthetic(가상테스트)/Serial(실장비) 모드 <code>.bat</code> 런처 제작
- 🧪 멀티 환경 검증	Python 미설치 PC에서도 정상 실행 확인


<br>



## ✨ 핵심 성과

- Zynq Linux 기반 DSP 시스템을 <code>STM32 Bare-metal MCU</code>로 완전 포팅 및 PC 연결
- RS-485 기반 실시간 양방향 통신 프로토콜 직접 설계 및 검증
- 비차단 송신 리팩토링으로 CPU 블로킹 해소 → RS-485 라인 노이즈 감소 및 데이터 전송 안정성 향상
- 클라이언트 납품 가능한 실행 패키지(<code>.exe</code> 및 <code>.bat</code>) 형태로 Python 웹 앱 1차 제품화



<br>


## 📡 시스템 아키텍쳐 및 통신 프로토콜
<img width="1920" height="1080" alt="제목을-입력해주세요_-003" src="https://github.com/user-attachments/assets/df159cd9-3530-4f56-8f1e-1df6a92362f6" />
<img width="1920" height="1080" alt="008" src="https://github.com/user-attachments/assets/0c5e8847-e193-4572-9141-ed8a5492fd1c" />
<img width="1920" height="1080" alt="009" src="https://github.com/user-attachments/assets/ebada7ee-7d77-4b1d-8cac-e44d07ce0a3c" />



<br>
<br>


## 🖼️ 이미지 자료

![image22](https://github.com/user-attachments/assets/4e7a52e2-15f3-4f89-97c9-8ede85a750df)
<img width="1045" height="356" alt="KakaoTalk_Photo_2025-11-02-15-25-07" src="https://github.com/user-attachments/assets/a13bc20b-d9fb-4061-b26d-c144a80227bb" />

<hr>

<img width="1720" height="840" alt="image" src="https://github.com/user-attachments/assets/92bee158-9cb0-4c9f-8446-e4c6ab1b4f9c" />
<img width="900" height="700" alt="제목을-입력해주세요_-001" src="https://github.com/user-attachments/assets/6b09eece-1719-4f33-9150-f3b4386f35c5" />

<hr>

<img width="864" height="565" alt="이미지설명_1" src="https://github.com/user-attachments/assets/3f822fa4-8e1b-4309-9182-ad4a911ddefc" />
<img width="864" height="565" alt="이미지설명_2" src="https://github.com/user-attachments/assets/43e393c7-7931-4f33-bcd7-3813eabe7f7f" />
<img width="1315" height="649" alt="이미지설명_3" src="https://github.com/user-attachments/assets/61ba2a6d-20bb-4cf5-b690-b011eecd25c4" />

<hr>

<img width="1909" height="899" alt="12321323" src="https://github.com/user-attachments/assets/6094f66b-6658-43ae-80ae-ad076f195fe3" />

