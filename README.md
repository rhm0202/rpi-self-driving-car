# Raspberry Pi Self-Driving Car

> Raspberry Pi 자율주행 RC카 실습 | Python | 임베디드 애플리케이션 수업

임베디드 애플리케이션 수업에서 Raspberry Pi RC카로 진행한 주차별 실습과 과제 모음입니다.
GPIO·모터 제어에서 시작해 카메라 차선 인식, 딥러닝 기반 자율주행, 객체 탐지 정지까지 단계적으로 구현했습니다.

---

## 프로젝트 개요

| 항목 | 내용 |
|---|---|
| 언어 | Python |
| 환경 | Raspberry Pi + RC카 키트 (DC 모터 2채널, 카메라, 부저, 블루투스) |
| 기간 | 2025.10 ~ 2025.12 |

---

## 저장소 구조

```
rpi-self-driving-car/
├── Asgmt/              # 주차별 과제 (week3·4는 보고서 .docx만 포함)
│   ├── week7/          # 버튼 · DC 모터 제어
│   ├── week8/          # 블루투스 원격 조종
│   ├── week10/         # OpenCV 얼굴 검출, 노란 차선 마스크
│   ├── week11/         # 차선 시각화 주행, SDcar 모듈
│   ├── week12/         # 학습 데이터(dataset/), 모델 학습(train_ai_car.ipynb), AI 주행
│   └── week13/         # 최종 과제 — 13주 과제 완성/ 에 제출본과 시연 영상
├── exCode/             # 교재 예제 코드 (LED·버튼, 시리얼, 모터, 카메라, TensorFlow)
├── ex/                 # OpenCV 이미지·카메라 기초 예제
└── treading_ex/        # 스레딩 연습
```

---

## 주차별 과제 (`Asgmt/`)

| 주차 | 내용 |
|---|---|
| Week 7 | 버튼 입력 처리, 버튼으로 DC 모터 전진·후진·정지 제어 (gpiozero) |
| Week 8 | 블루투스 시리얼(UART) 명령으로 RC카 원격 조종, 수신 스레드 분리 |
| Week 10 | OpenCV 기초 — Haar Cascade 얼굴 검출, HSV 마스크로 노란 차선 추출 |
| Week 11 | 카메라 영상에서 차선 마스크를 구해 시각화하며 주행 (`SDcar` 모듈화) |
| Week 12 | 주행 영상·조향 라벨 수집 → 차선 주행 모델 학습 → AI 주행 |
| Week 13 | **최종 과제**: AI 자율주행 + 객체 탐지 정지 |

---

## 최종 과제 (Week 13)

카메라 영상으로 조향을 예측해 차선을 따라가고, 특정 물체를 발견하면 멈추는 자율주행입니다.

```
카메라 프레임
  ├─▶ 차선 주행 모델 (Keras CNN, 하단 절반 200×66 입력, 3클래스: 직진/좌/우) ─▶ 모터 조향
  └─▶ SSD MobileNet v2 (OpenCV DNN, 별도 스레드) ─▶ 정지 대상 감지 시 정지 + 부저
```

코드는 `Asgmt/week13/`, 제출본과 주행·제어 화면 영상은 `Asgmt/week13/13주 과제 완성/`에 있습니다.

| 파일 | 역할 |
|---|---|
| `drive_collect_textbook_video.py` | 수동 주행하며 프레임과 조향 라벨 수집 |
| `drive_AI.py` | 학습한 모델로 자율주행 + 객체 탐지 연동 (중간 버전) |
| `drive_AI_final.py` | 최종본 — 객체 탐지 스레드, 비상 정지, 부저 |
| `modul/SDcar.py` | 모터·부저 제어 모듈 |
| `modul/` | 차선 주행 모델(`lane_navigation_*.h5`), SSD MobileNet v2 모델·COCO 클래스 목록 |

- 주행 제어 코드를 직접 개선 (week12 `drive_AI.py` → week13 `drive_AI.py` → `drive_AI_final.py`)
- 학습 데이터를 직접 수집해 모델을 여러 차례 재학습 (`lane_navigation_*.h5`)

**실행** (Raspberry Pi, `Asgmt/week13/13주 과제 완성/`에서): `python drive_AI_final.py`

| 키 | 동작 |
|---|---|
| `e` / `x` | AI 주행 시작 / 중지 |
| `t` / `r` | 객체 탐지 켜기 / 끄기 |
| `w` `a` `s` `d` | 수동 조작 |
| `Space` | 정지 |
| `q` | 종료 |

---

## 기술 스택

- **Language**: Python
- **Hardware**: Raspberry Pi, DC 모터 드라이버, Camera Module, 부저, 블루투스 모듈
- **Library**: gpiozero, RPi.GPIO, pyserial, OpenCV, TensorFlow/Keras, threading
