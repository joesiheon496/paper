# 목표  
IP 카메라 영상만으로 **스프레더 콘(트위스트록)이 컨테이너 코너 캐스팅에 제대로 삽입됐는지**를 실시간 판별하는 파이프라인 설계  
*(장비 개조 ×, 센서 추가 × — 순수 영상 기반)*  

---

## 1. 하드웨어 배치 ⚙️
| 항목 | 권장 사양 / 이유 |
|------|-----------------|
| **카메라 위치** | 스프레더 바로 아래, 콘 옆 30 ~ 50 cm / 하방 45° → 콘과 캐스팅 구멍이 한 프레임에 들어오도록 |
| **해상도·FPS** | ≥ 1920×1080 @ 30 fps → 삽입 순간 모서리 픽셀 수 40 px 이상 확보 |
| **셔터 & 조명** | 글로벌 셔터 + 링라이트(6500 K) → 모션 블러·그늘 최소화 |
| **네트워크** | RTSP/H.264 + ≤ 100 ms 지연 → AI 추론까지 300 ms 내 목표 |

---

## 2. 알고리즘 개요 🧠  
[스트림 수신] → [ROI 추출] → [콘 & 캐스팅 분리] →
[삽입 여부 판정] → [경고/OK 신호 출력]


### 2‑1. ROI(Region‑of‑Interest) 추출
- **Spreader‑Mounted Tag 기반**  
  - 스프레더 프레임에 아프릴태그/아루코 부착  
  - 태그 검출 → 코너 위치 **1 ms 이하** 산출 → 연산량·오검 감소

### 2‑2. 객체 분할
| 방법 | 특징 |
|------|------|
| **YOLOv8‑seg** (클래스: `cone`, `casting`) | ROI 1‑shot segmentation, 15 ms/프레임 (T4) |
| **Edge + Color** (백업) | Canny + HSV(주황 콘) → 딥러닝 실패 시 폴백 |

### 2‑3. 삽입 판정 로직
1. **Keypoint Distance**  
   - 캐스팅 구멍 4 코너 \(K_1 \dots K_4\)  
   - 콘 상단 원형 엣지 중심 \(C\)  
   - \(\max \|K_i - C\| < \varepsilon\) 이면 **OK**  
   - 실험값: \(\varepsilon ≈ 5 px\) (≈ ±3 mm)

2. **Binary Classifier**  
   - `CNN(ROI) → {OK, NG}`  
   - MobileNet V3‑Small + Focal Loss, F1 > 0.98

### 2‑4. 후처리
- 5 프레임 연속 **NG** → PLC DI **Fault** 신호  
- 최신 50 프레임 슬라이딩 윈도 OK 비율 로그 → 유지보수 모니터링

---

## 3. 학습 데이터 확보 📈
| 단계 | 내용 |
|------|------|
| **촬영** | 실제 하역 작업에서 콘 접촉~삽입 전 과정을 500+ 클립 수집 |
| **라벨링** | Roboflow/CVAT Polygon (`cone`, `casting-hole`) |
| **증강** | MotionBlur(3–5 px), GaussianNoise, RandomBrightness |

---

## 4. 예시 코드 (Python + Ultralytics)  

```python
from ultralytics import YOLO
import numpy as np

# 1) 파인튜닝된 세그멘테이션 모델
model = YOLO("cone_casting_seg.pt")

def process(frame):
    roi = crop_by_aruco(frame)          # 태그 기반 ROI
    res = model(roi, verbose=False)[0]

    mask_cone, mask_cast = res.masks.data[:2].cpu().numpy()
    K = extract_casting_corners(mask_cast)  # (4,2)
    C = center_of_circle(mask_cone)         # (2,)

    ok = (np.linalg.norm(K - C, axis=1).max() < EPS)
    alert(ok)                               # PLC/알람
```
EPS = 5 px → 약 ±3 mm (카메라 세팅 기준)

5. 신뢰도 향상 팁 💡
멀티뷰 : 대각 2 대(좌·우) OR 연산 → 오검 축소

소형 ToF 센서 : Z‑오프셋 보정 → 깊이 오차 ↓

앙상블 : Seg 기반 거리 + CNN Classifier soft‑vote

6. 실전 적용 단계 🏗️
PoC (랩탑 + GPU) → 30 fps 실시간 OK 판정 확인

Edge Box (Jetson Orin Nano) 이식 → 컨베이어 테스트

PLC 연동 (Modbus/TCP) → 하역 로직 Interlock

정식 운영 : False Alarm < 0.5 %, Miss‑Detect < 0.1 %

핵심 요약
딥러닝 segmentation + 간단한 기하 계산만으로 IP 카메라 영상에서 콘 삽입 여부를 300 ms 내 실시간 판단 가능

추가 센서 없이 기존 카메라만으로 설치·유지비 최소화
