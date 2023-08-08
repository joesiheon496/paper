# Bio‑signal based motion control system using deep learning models: a deep learning approach for motion classification using EEG and EMG signal fusion
딥러닝 모델을 이용한 생체신호 기반 동작 제어 시스템: EEG와 EMG 신호 융합을 이용한 동작 분류를 위한 딥러닝 접근법

## abstract
생체전기 시간 신호는 시간에 따른 장기 간 전위차를 통해 측정할 수 있는 신호입니다.

뇌파(EEG) 신호 및 근전도(EMG) 신호는 의료 진단 및 동작 분류에 사용되는 가장 잘 알려진 생체 전기 신호 중 하나입니다. 

전통적인 기계 학습 방법에서는 두 생체 전기 신호에서 고유한 패턴과 특징을 추출하는 작업이 어렵고 이러한 신호의 비선형, 비정지 및 복잡한 특성을 연구하기 위해 특정 전문 지식이 필요합니다. 

최근 딥 러닝 방법이 발전함에 따라 손으로 만든 특징 없이 원시 데이터에서 특징을 추출할 수 있습니다.

신호 분류를 위한 하이브리드 시스템을 제안했습니다. 

실험은 다중 채널 EEG 신호의 데이터 세트에서 테스트되었습니다.

신호 분류를 위한 하이브리드 시스템을 위해 
- Convolution Neural Networks CNN 모델,
- Long Short Term Recurrent Neural Networks 모델 LSTM,
- Combined CNN-LSTM 모델 등 3가지 딥러닝 모델을 제안하였다. 

실험은 손과 손목 동작을 디코딩하기 위해 다중 채널 sEMG 신호와 병합된 다중 채널 EEG 신호의 데이터 세트에서 테스트되었습니다.

실험 결과는 제안된 딥 러닝 모델이 다른 전통적인 기계 학습 최신 방법을 능가하는 높은 분류 정확도를 달성하여 최대 3.5%의 개선 비율을 달성함을 의미하며 이는 접근 방식의 유망한 적용을 나타냅니다.

결과적으로 이 작업은 주로 팔다리 움직임 분류를 위한 바이오 로봇 응용 프로그램의 실시간 제어를 용이하게 하고 개선하는 자동 분류에 기여합니다.

![image](https://github.com/joesiheon496/paper/assets/56191064/b3cd4419-b597-4247-a973-c30bb92d89f5)

![image](https://github.com/joesiheon496/paper/assets/56191064/b6578824-a592-4e75-b341-4b67929b0204)

![image](https://github.com/joesiheon496/paper/assets/56191064/348c4747-f9c9-43bb-92a6-94715d5012aa)

![image](https://github.com/joesiheon496/paper/assets/56191064/f17d0777-0dba-42a2-8fd4-4d022d9701e8)

![image](https://github.com/joesiheon496/paper/assets/56191064/402fd623-53fa-426c-b7d9-72f1c6edee85)

![image](https://github.com/joesiheon496/paper/assets/56191064/af849efa-d47f-467b-9a25-7d7391ef8689)

![image](https://github.com/joesiheon496/paper/assets/56191064/86f81659-e879-44d4-b4c5-ae1a709be5d8)

![image](https://github.com/joesiheon496/paper/assets/56191064/ff828f21-4034-4737-a33d-bfda734b6bbc)

![image](https://github.com/joesiheon496/paper/assets/56191064/ce0a99b3-b3ad-432b-ae2d-9073027cce87)

![image](https://github.com/joesiheon496/paper/assets/56191064/ab131ac0-65a6-4c79-bdba-9ae1d90b9337)

![image](https://github.com/joesiheon496/paper/assets/56191064/0d4b06f0-3631-4583-a154-c38141c3109d)

![image](https://github.com/joesiheon496/paper/assets/56191064/ae7ebef0-5dd0-4071-969c-ca2190e525a9)

![image](https://github.com/joesiheon496/paper/assets/56191064/2565128e-3511-4281-b0e2-ab43826be5a6)

![image](https://github.com/joesiheon496/paper/assets/56191064/a313d025-a7c7-4752-b1b8-748c8dd56443)


## Conclusions

본 논문에서는 손과 손목 동작 제어의 성능을 향상시키기 위해 신호 융합을 사용하는 하이브리드 시스템에서 딥 러닝 접근 방식을 제안했습니다. 

하이브리드 시스템에서 딥러닝의 효율성을 조사하고 기존 기계와 같은 수동 기능 엔지니어링 없이 손과 손목 움직임을 분류하기 위한 하이퍼 매개변수 튜닝의 효과를 연구하기 위해 CNN, LSTM 및 CNN+LSTM의 3가지 딥러닝 모델을 개발했습니다. 

전체적인 결과는 개발된 LSTM 모델이 평균 95.2% 이상의 정확도로 우수한 성능을 보였으며, CNN과 CNN-LSTM 모델은 전 과목에서 평균 93.7%의 정확도를 보였다.

문헌의 다른 최신 방법과 비교하여 제안된 딥러닝 모델은 상당한 테스트 시간으로 최대 3.5%의 개선 비율로 문헌의 다른 방법을 능가하는 높은 분류 정확도를 달성했습니다.

실험 결과의 표준 편차는 하이브리드 시스템을 위한 기능 학습 및 분류의 경쟁 도구로 사용할 수 있는 제안 모델의 안정성을 보여주는 다른 주제에 따른 정확도에 현저한 변화가 없음을 나타냅니다.

이 시스템은 동적으로 적응하고 대규모 데이터 세트로 잘 확장됩니다. 이 백서에 제시된 작업은 주로 상지 운동을 위한 바이오 로봇 응용 프로그램의 제어를 개선하는 데 잠재적인 관심을 가지고 있습니다. 얻은 높은 분류 정확도는 접근 방식의 유망한 적용을 나타냅니다.

향후 작업에서 이 연구의 확장은 근육에 영향을 미치지 않고 근육에 영향을 미치는 근병증과 같은 신경근 질환이 있는 상완 절단 환자의 다양한 상지 움직임을 분류하기 위해 신호 융합을 통해 제안된 딥 러닝 접근 방식의 성능을 구현하고 검사하는 것입니다. 

신경계 또는 신경근 접합부 장애, 그리고 뇌 또는 척수에서 연결된 근육으로 신호를 전달하는 운동 뉴런에 선택적으로 영향을 미치는 운동 뉴런 질환 또는 신경병증 장애. 

이러한 신경근 질환은 상지 의지의 움직임 제어에 사용되는 EMG 및 EEG 신호에 영향을 미칩니다.

