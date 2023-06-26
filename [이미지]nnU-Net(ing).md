[원본](https://arxiv.org/abs/1809.10486)
# nnU-Net: Self-adapting Framework for U-Net-Based Medical Image Segmentation
## Abstract
* 본 논문에서는 2D 및 3D 바닐라 U-Net을 기반으로 하는 robust하고 self-adapting하는 프레임워크를 의미하는 nnU-Net("no-new-Net")을 소개합니다.
  >  self-adaptation stage 방법이란 예측을 수행하기전 Masked input을 backcasting 하는 방법입니다.
  >> backcasting 30년 후와 같이 먼 장래(미래)를 생각해서 당시에 어떤 식으로 성공하고 있을지 구체적으로 서술하고, 현재 상태와의 차이를 직시하고 창조적으로 어떤 길을 걸을지 로드맵을 그려서 실현시켜가는 것
* 우리는 제안된 많은 네트워크 설계에서 불필요한 bells와 whistles을(noise) 제거하는 강력한 사례를 주장하고 대신 방법의 성능과 일반화 가능성을 만드는 나머지 측면에 초점을 맞춥니다.

![image](https://github.com/joesiheon496/paper/assets/56191064/aeebc8e3-d1aa-4b20-96fd-b8c353ce2576)

Fig. 1. U-Net Cascade(적용 가능한 데이터 세트에만 해당). 1단계(왼쪽): 3D U-Net이 다운샘플링된 데이터를 처리하고 결과 분할 맵이 원래 해상도로 업샘플링됩니다. 2단계(오른쪽): 이러한 분할은 전체 해상도 데이터에 대한 원-핫 인코딩으로 연결되고 두 번째 3D U-Net에 의해 정제됩니다.

## Method
* 의료 이미지는 일반적으로 3차원을 포함하므로 2D U-Net, 3D U-Net 및 U-Net Cascade로 구성된 기본 U-Net 아키텍처 풀을 고려합니다.
* 2D 및 3D U-Net이 전체 해상도에서 분할을 생성하는 동안 cascade는 먼저 저해상도 분할을 생성한 다음 이를 세분화합니다.
> cascade: 여러 개의 classifier를 concat하는 방식의 앙상블 학습의 한 종류
* **U-Net의 원래 공식과 비교하여 아키텍처 수정은 거의 무시할 수 있으며 대신 이러한 모델에 대한 자동 train 파이프라인을 설계하는 데 노력을 집중합니다.**
* 그것의 인코더 부분은 감소된 spatial(공간) 정보를 희생시키면서 semantic 정보를 연속적으로 aggregates(집계)한다는 점에서 전통적인 분류 CNN과 유사하게 작동합니다.
* U-Net은 디코더를 통해 이를 수행하는데, 디코더는 'U' 하단에서 의미론적 정보를 수신하고 이를 건너뛰기 연결을 통해 인코더에서 직접 얻은 고해상도 기능 맵과 재결합합니다.
* 원래 U-Net과 마찬가지로 인코더의 풀링과 디코더의 전치 합성곱 연산 사이에 두 개의 일반 합성곱 레이어를 사용합니다.
* 우리는 ReLU 활성화 함수를 새는 ReLU(neg. 기울기 1e−2)로 대체하고 더 널리 사용되는 배치 정규화 대신 인스턴스 정규화를 사용한다는 점에서 원래 아키텍처에서 벗어납니다.

### 2D U-Net
직관적으로 3D 의료 영상 분할 맥락에서 2D U-Net을 사용하는 것은 z축을 따라 귀중한 정보를 집계하고 고려할 수 없기 때문에 차선책으로 보입니다. 그러나 데이터 세트가 이방성인 경우 기존의 3D 분할 방법이 성능이 저하된다는 증거가 있습니다(cf. Decathlon 챌린지의 전립선 데이터 세트).

### 3D U-Net
3D U-Net은 3D 이미지 데이터에 적합한 선택 방법인 것 같습니다. 이상적인 세상에서 우리는 전체 환자의 이미지에 대해 그러한 아키텍처를 훈련할 것입니다. 그러나 실제로는 이미지 패치에서만 이 아키텍처를 훈련할 수 있는 GPU 메모리의 양에 의해 제한됩니다. 이는 뇌종양, 해마 및 이 챌린지의 전립선 데이터세트와 같은 더 작은 이미지(환자당 복셀 수 측면에서)로 구성된 데이터세트의 경우 문제가 되지 않지만, 훈련을 방해할 수 있습니다. 이것은 예를 들어 간 부분을 다른 기관의 부분과 정확하게 구별하기 위한 충분한 맥락 정보를 수집할 수 없는 구조의 제한된 시야 때문입니다.

### U-Net Cascade

이미지 크기가 큰 데이터 세트에서 3D U-Net의 이러한 실질적인 단점을 해결하기 위해 계단식 모델을 추가로 제안합니다. 따라서 3D U-Net은 먼저 다운샘플링된 이미지에 대해 학습됩니다(1단계). 그런 다음 이 U-Net의 분할 결과는 원래 복셀 간격으로 업샘플링되고 추가(1개의 핫 인코딩된) 입력 채널로 두 번째 3D U-Net에 전달되며 전체 해상도(2단계)에서 패치에 대해 교육됩니다. 그림 1을 참조하십시오.

 
### Dynamic adaptation of network topologies

![image](https://github.com/joesiheon496/paper/assets/56191064/0b1b2548-8ecf-4d0b-8459-f6fc5ba06de8)

Table 1. Medical Segmentation Decathlon 챌린지의 7단계 1 작업을 위해 자동으로 생성된 네트워크 토폴로지. 3D U-Net lowres는 U-Net 캐스케이드의 첫 번째 단계를 나타냅니다. U-Net Cascade의 2단 구성은 3D U-Net과 동일하다.

이미지 크기의 큰 차이(간의 중앙값 모양 482×512×512 대 해마의 경우 36×50×35)로 인해 입력 패치 크기와 축당 풀링 작업 수(따라서 암묵적으로 컨볼루션 레이어 수)는 공간 정보를 적절하게 집계할 수 있도록 각 데이터 세트에 자동으로 적용됩니다. 이미지 지오메트리에 적응하는 것 외에도 고려해야 할 사용 가능한 메모리와 같은 기술적 제약이 있습니다. 이와 관련하여 우리의 기본 원칙은 배치 크기와 네트워크 용량을 동적으로 교환하는 것입니다. 자세한 내용은 아래에 나와 있습니다.

1. 우리는 하드웨어 설정과 함께 작동하는 것으로 알고 있는 네트워크 구성으로 시작합니다. 2D U-Net의 경우 이 구성은 입력 패치 크기가 256×256이고 배치 크기가 42이고 최상위 계층에 30개의 기능 맵이 있습니다(기능 맵의 수는 다운샘플링할 때마다 두 배가 됨).
2. 네트워크가 전체 슬라이스에서 효과적으로 학습할 수 있도록 각 데이터 세트의 중간 평면 크기에 이러한 매개 변수를 자동으로 적용합니다(여기서 우리는 평면 내 간격이 가장 낮고 가장 높은 해상도에 해당하는 평면을 사용함).
> 2D U-Net과 마찬가지로 3D U-Net은 최고 해상도 레이어에서 30개의 기능 맵을 사용합니다.
> 여기서는 입력 패치 크기 128×128×128 및 배치 크기 2의 기본 구성으로 시작합니다. 메모리 제약으로 인해 입력 패치 볼륨을 $128^3$보셀 이상으로 늘리지 않고 대신 입력 패치의 종횡비를 일치시킵니다. 크기를 복셀 데이터 세트의 중앙값 크기로 조정합니다.
3. 데이터 세트의 중간 모양이 $128^3$보다 작으면 중간 모양을 입력 패치 크기로 사용하고 배치 크기를 늘립니다(처리되는 총 복셀 수가 128×128×128 및 배치 크기 2와 동일하도록 )
4. 2D U-Net과 마찬가지로 피처 맵의 크기가 8이 될 때까지 각 축을 따라 풀링(최대 5회)합니다.
5. 모든 네트워크에 대해 옵티마이저 단계당 처리되는 총 복셀 수(입력 패치 볼륨과 배치 크기를 곱한 값으로 정의됨)를 데이터 세트의 최대 5%로 제한합니다. 초과 사례의 경우 배치 크기를 줄입니다(하한값 2).

## preprocessing
전처리는 우리의 방법이 구성하는 완전히 자동화된 세분화 파이프라인의 일부이므로 아래에 제시된 단계는 사용자 개입 없이 수행됩니다.

### Cropping
모든 데이터는 0이 아닌 값의 영역으로 잘립니다. 이것은 간 CT와 같은 대부분의 데이터 세트에 영향을 미치지 않지만 뇌 MRI의 크기(따라서 계산 부담)를 줄입니다.

### Resampling
* CNN은 기본적으로 복셀 간격을 이해하지 못합니다. 의료 이미지에서 서로 다른 복셀 간격을 가진 데이터 세트를 생성하는 것은 서로 다른 스캐너 또는 서로 다른 획득 프로토콜에 대해 일반적입니다. 
* 우리의 네트워크가 공간적 의미론을 적절하게 학습할 수 있도록 모든 환자는 해당 데이터 세트의 중간 복셀 간격으로 리샘플링됩니다. 여기서 3차 스플라인 보간은 이미지 데이터에 사용되고 가장 가까운 이웃 보간은 해당 분할 마스크에 사용됩니다.
* U-Net Cascade의 필요성은 다음 휴리스틱에 의해 결정됩니다. 리샘플링된 데이터의 중앙값 모양이 3D U-Net(배치 크기 2)에서 입력 패치로 처리할 수 있는 복셀의 4배 이상인 경우 , U-Net Cascade에 적합하며 이 데이터 세트는 추가로 더 낮은 해상도로 리샘플링됩니다.
  > 휴리스틱: 문제를 해결하거나 불확실한 사항에 대해 판단을 내릴 필요가 있지만, 명확한 실마리가 없을 경우에 사용하는 편의적 발견적인 방법
* 데이터 세트가 비등방성인 경우 고해상도 축은 저해상도 축/축과 일치할 때까지 먼저 다운샘플링된 다음 모든 축이 동시에 다운샘플링됩니다.
  > 비방등성: 방향에 따라 물체의 물리적 성질이 다른 것
* 1단계의 다음 데이터 세트는 설명된 휴리스틱 세트에 속하므로 U-Net 캐스케이드의 사용을 트리거합니다: 심장, 간, 폐 및 췌장


### Normalization
* CT 스캔의 강도 척도가 절대적이기 때문에 모든 CT 이미지는 전체 개별 데이터 세트의 통계를 기반으로 자동으로 정규화됩니다. 데이터 세트의 해당 json 설명자 파일의 양식 설명이 'ct'를 나타내는 경우, 훈련 데이터 세트가 수집되고 전체 데이터 세트는 이러한 강도 값의 [0.5, 99.5] 백분위수로 클리핑하여 정규화되고, 수집된 모든 강도 값의 평균 및 표준 편차를 기반으로 z-점수 정규화가 이어집니다.
* MRI 또는 ​​기타 영상 양식의 경우(즉, 양식에 'ct' 문자열이 없는 경우) 간단한 z-점수 정규화가 환자에게 개별적으로 적용됩니다.
* 자르기로 인해 데이터 세트(복셀)의 평균 환자 크기가 1/4 이상 줄어들면 정규화가 0이 아닌 요소의 마스크 내에서만 수행되고 마스크 외부의 모든 값은 0으로 설정됩니다.
* 여기서 사용된 주사위 손실 공식은 다중 클래스 적응입니다. 다른 변형보다 이 공식을 선호합니다. dice 손실은 다음과 같이 구현됩니다.
$$L_{dc}=-\frac{2}{|K|}\sum_{k\in K}\frac{\sum_{i\in I}u_{i}^{k}v_i^k}{\sum_{i\in I} u_i^k + \sum_{i\in I} v_i^k}$$
* 여기서 $u$는 네트워크의 소프트맥스 출력이고 $v$는 실측 분할 맵의 원 핫 인코딩입니다. $u$와 $v$는 $I × K$ 모양을 가지며 $i ∈ I$는 트레이닝 패치/배치의 픽셀 수이고 $k ∈ K$는 클래스입니다.

* ## Training Procedure
* 모든 모델은 처음부터 훈련되고 훈련 세트에 대한 5중 cross validation을 사용하여 평가됩니다. dice와 교차 엔트로피 손실의 조합으로 네트워크를 훈련합니다.
$$L_{total}=L_{dice}+L_{CE}$$
* 거의 모든 환자에서 작동하는 3D U-Net의 경우(U-Net 캐스케이드의 첫 번째 단계 및 캐스케이드가 필요하지 않은 경우 3D U-Net) 배치의 각 샘플에 대한 주사위 손실을 계산하고 배치에 대한 평균을 계산합니다.
* 다른 모든 네트워크의 경우 배치의 샘플을 pseudo-volume으로 해석하고 배치의 모든 복셀에 대한 주사위 손실을 계산합니다.
  > voxel: 복셀은 체적 요소이며, 3차원 공간에서 정규 격자 단위의 값을 나타낸다. 복셀이라는 용어는 부피 와 픽셀 을 조합한 혼성어이다. 이것은 2차원 이미지 데이터가 픽셀로 표시되는 것에 대한 비유이다. 복셀은 의료 및 과학 데이터 시각화 및 분석에 자주 사용된다

### Data Augmentation
* When training large neural networks from limited train- ing data, special care has to be taken to prevent overfitting.
* We address this prob- lem by utilizing a large variety of data augmentation techniques
* The following augmentation techniques were applied on the fly during training: random rota- tions, random scaling, random elastic deformations, gamma correction augmen- tation and mirroring.
* Applying three dimensional data augmentation may be suboptimal if the maximum edge length of the input patch size of a 3D U-Net is more than two times as large as the shortest.
* For datasets where this criterion applies we use our 2D data augmentation instead and apply it slice-wise for each sample.
* The second stage of the U-Net Cascade receives the segmentations of the previous step as additional input channels
* To prevent strong co-adaptation we apply random morphological operators (erode, dilate, open, close) and randomly remove connected components of these segmentations.

### Patch Sampling
* To increase the stability of our network training we enforce that more than a third of the samples in a batch contain at least one randomly chosen foreground class.

### Inference
* Due to the patch-based nature of our training, all inference is done patch-based as well. Since network accuracy decreases towards the border of patches, we weigh voxels close to the center higher than those close to the border, when aggregating predictions across patches. Patches are chosen to overlap by patch size / 2 and we further make use of test time data augmentation by mirroring all patches along all valid axes. Combining the tiled prediction and test time data augmentation result in segmentations where the decision for each voxel is obtained by aggregating up to 64 predictions (in the center of a patient using 3D U-Net). For the test cases we use the five networks obtained from our training set cross-validation as an ensemble to further increase the robustness of our models.

### Postprocessing
* A connected component analysis of all ground truth segmentation labels is per- formed on the training data. If a class lies within a single connected component in all cases, this behaviour is interepreted as a general property of the dataset. Hence, all but the largest connected component for this class are automatically removed on predicted images of the corresponding dataset.

### Ensembling and Submission
* To further increase the segmentation performance and robustness all possible combinations of two out of three of our models are ensembled for each dataset. For the final submission, the model (or ensemble) that achieves the highest mean foreground dice score on the training set cross-validation is automatically chosen.


## Experiments and Results
* We optimize our network topologie using five-fold cross-validations on the phase 1 datasets. Our phase 1 cross-validation results as well as the corresponding submitted test set results are summarized in Table 2. - indicates that the U-Net Cascade was not applicable (i.e. necessary, according to our criteria) to a dataset because it was already fully covered by the input patch size of the 3D U-Net. The model that was used for the final submission is highlighted in bold. Although several test set submissions were allowed by the platform, we believe it to be bad practice to do so. Hence we only submitted once and report the results of this single submission. As can be seen in Table 2 our phase 1 cross-validation results are robustly recovered on the held-out test set indicating a desired absence of over-fitting. The only dataset that suffers from a dip in performance on all of its foreground classes is BrainTumour. The data of this phase 1 dataset stems from the BRATS challenge [16] for which such performance drops between validation and testing are a common sight and attributed to a large shift in the respective data and/or ground-truth distributions.

![image](https://github.com/joesiheon496/paper/assets/56191064/2028042d-53c4-4567-82ca-1673d6b3c64f)

## Discussion
* In this paper we present the nnU-Net segmentation framework for the medi- 
 cal domain that directly builds around the original U-Net architecture [6] and dynamically adapts itself to the specifics of any given dataset. Based on our hy- pothesis that non-architectural modifications can be much more powerful than some of the recently presented architectural modifications, the essence of this framework is a thorough design of adaptive preprocessing, training scheme and inference. All design choices required to adapt to a new segmentation task are done in a fully automatic manner with no manual interaction. For each task the nnU-Net automatically runs a five-fold cross-validation for three different automatically configures U-Net models and the model (or ensemble) with the highest mean foreground dice score is chosen for final submission. In the con- text of the Medical Segmentation Decathlon we demonstrate that the nnU-Net performs competitively on the held-out test sets of 7 highly distinct medical datasets, achieving the highest mean dice scores for all classes of all tasks (ex- cept class 1 in the BrainTumour dataset) on the online leaderboard at the time of manuscript submission. We acknowledge that training three models and picking the best one for each dataset independently is not the cleanest solution. Given a larger time-scale, one could investigate proper heuristics to identify the best model for a given dataset prior to training. Our current tendency favors the U-Net Cascade (or the 3D U-Net if the cascade cannot be applied) with the sole (close) exceptions being the Prostate and Liver tasks. Additionally, the added benefit of many of our design choices, such as the use of Leaky ReLUs instead of regular ReLUs and the parameters of our data augmentation were not properly validated in the context of this challenge. Future work will therefore focus on systematically evaluating all design choices via ablation studies.

## Conclusion
* All design choices required to adapt to a new segmentation task are done  in  a  fully  automatic  manner  with  no  manual  interaction. 
* For  each  task the  nnU-Net  automatically  runs  a  five-fold  cross-validation  for  three  different automatically configures U-Net models and the model (or ensemble) with the highest mean foreground dice score is chosen for final submission
