<h1> 🧬 Zynq 기반 DSP 시스템을 STM32H723 Nucleo 보드로 이식 및 리팩토링한 실시간 신호처리 프로젝트</h1>
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
 또한, Python 기반 PC 모니터링 툴을 .exe 및 .bat 형태로 패키징하여,<br>
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
→ 실시간 DSP 루프 정지 없이 안전한 데이터 전송 구현


4. PC 프로그램 패키징 및 배포 :<br>
Python FastAPI + WebSocket 기반 웹 UI를 <code>pyinstaller</code>로 <code>.exe</code> 변환 및 <code>.bat</code> 런처 제작<br>
→ “가상모드(Synthetic)” / “실장비(Serial)” 2모드 지원 + 메뉴얼 제작<br>
→ 다른 PC 환경(비개발 환경)에서도 독립 실행 가능

5. 노이즈 트러블슈팅 :<br>
실제 보드 연결 시 RS-485 라인 노이즈로 <code>st</code> 토큰 전 인식 실패 발생<br>
→ 시리얼 로그 분석 후 더미값 프리패딩으로 파싱 안정화
