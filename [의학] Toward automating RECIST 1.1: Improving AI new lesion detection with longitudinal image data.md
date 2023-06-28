ASCO ARTICLE CITATION

DOI: [10.1200/JCO.2023.41.16_suppl.e13545 Journal of Clinical Oncology 41, no. 16_suppl (June 01, 2023) e13545-e13545.](https://ascopubs.org/doi/abs/10.1200/JCO.2023.41.16_suppl.e13545?af=R)

Published online May 31, 2023.

Amanda Gong, Morgan Daly, Gabriel Melendez-Corres, Pang Yu Teng, Tanmay Sanjay Hukkeri, Wahi-Anwar Muhammad, Kathleen Ruchalski, Jonathan Goldin, Matthew S. Brown

David Geffen School of Medicine at UCLA, Los Angeles, CA; UCLA Department of Radiology, Los Angeles, CAABSTRACT
e13545

# Background

RECIST 1.1을 자동화하면 속도, 일관성 및 임상 시험 영상 판독 비용 면에서 이점을 얻을 수 있습니다.

new lesions의 탐지는 주관적이며 판독기 판단의 일반적인 소스입니다.

현재 AI lesions 감지는 일반적으로 단일 시점 이미지를 사용합니다.

AI에서 시간 데이터의 사용은 비의료 응용 분야에서 빠르게 진화하는 분야이며 응답 평가에서 새로운 병변 감지는 세로 다중 시점 이미지의 이점을 얻을 수 있습니다.

우리는 새로운 병변 감지를 위해 심층 신경망에 대한 세로 이미지 입력의 영향을 탐구합니다. 

개념 증명으로 CT에서 새로운 간 병변 검출의 단일 시점 입력과 이중 시점 입력을 비교합니다.

# Methods: 

두 개의 공개 데이터 세트에서 CT 이미지를 활용했습니다. 
  1) diseased liver (DL), a subset of DeepLesion (doi: 10.1117/1.JMI.5.3.036501)
  2) healthy liver (HL) from potential liver donors (Med Image Anal 69 (2021) 101950).. 

새로운 병변이 있는 공개적으로 사용 가능한 종단 이미지 데이터 세트가 없기 때문에 HL 스캔(sim 건강 기준선)을 DL 스캔(새로운 병변에 대한 시뮬레이션 후속 조치)으로 등록하고 잘 짝을 이루는 상호 정보를 사용하는 이미지 종단 연구를 시뮬레이션("sim")했습니다. 

각 스캔 쌍에서 간 내에서 하나의 양성(추적에 새로운 병변 포함) 및 하나의 음성(추적에 병변 없음) 6x6cm 이미지 패치를 선택하여 pos/neg의 균형 잡힌 수로 3296개 패치의 견본 데이터 세트를 생성합니다. 

우리는 새로운 간 병변의 존재를 분류하기 위해 두 개의 심층 신경망을 훈련했습니다.
  1) a conventional single timepoint model (ST) trained on DL patches only, i.e. 후속 스캔만 사용
  2) an experimental dual-timepoint model (DT) DL-HL 쌍 스캔에서 패치 쌍에 대해 훈련되었습니다.

모델 AUC와 정확도를 비교하기 위해5-fold cross validation과 paired t 테스트를 사용했습니다.

# Results: 

Of 10,594 CT studies (4,427 patients, pts) in DeepLesion, 우리는 1648개의 DL 스캔(간 병변이 있는 946점)을 20개의 등록된 HL 스캔(20점) 중 하나와 페어링했습니다. 

DT 모델이 ST 모델을 능가했습니다., increasing AUC from 0.914 ± 0.002 to 0.929 ± 0.005 (p=0.040), 비슷한 경향의 정확성 (dataset split, Table 1). 

# Conclusions: 

DT 모델은 new liver lesions의 검출에서 ST 모델을 능가했으며 시뮬레이션 세로 데이터를 사용하여 심층 신경망에 대한 다중 시점 입력의 이점을 예비적으로 제안합니다.

간격 변화 감지를 개선하기 위해 세로 이미지 데이터를 사용하는 것은 임상 시험에서 방사선학적 반응 평가를 자동화하는 단계입니다.

실제 임상 시험 종단 이미지에서 새로운 병변 감지를 자동화하기 위해 이 작업을 확장할 계획입니다.

결과(굵은 글씨)는 테스트 세트에서 across 5 folds 됩니다.

N 이미지 패치(ST) 또는 패치 쌍(DT).

Single timepoint (ST) vs dual timepoint (DT) models results.

![image](https://github.com/joesiheon496/paper/assets/56191064/10ec3f9b-5c2b-4988-ae0f-a23c91044847)
