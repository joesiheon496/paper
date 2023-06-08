[해당 블로그 글 이해하기위해 따라적기](https://pebpung.github.io/registration/2022/01/30/Registration-1.html)

# Registration 이란?
- Registration은 다른 각도나 크기, 위치에 있는 두 영상을 매칭 시켜주는 영상 정합을 말합니다
- 두 영상 간의 매칭점을 찾아 주고, 매칭점을 바탕으로 Transformation Matrix를 예측해줍니다.
> Transformation Matrix: 선형 대수학에서 선형 변환은 행렬로 나타내는 것이 가능하다. 또한 역사적으로 행렬상에서 행렬을 변환시키는 다양한 표현방법이 조사되어왔다.
- 이렇게 예측된 Matrix는 영상을 변환시켜줄 때 사용됩니다.
- 보통 두 영상에서 움직이는 영상을 Moving, Source image라고 부르고, 고정된 영상은 Fixed, Target, Reference image라고 부릅니다. 또한 Registration이 완료된 이미지는 Moved, Result, Transformed image로 쓰여집니다.
![image](https://github.com/joesiheon496/paper/assets/56191064/178dd203-9e3f-4633-be88-4316028cc4a7)

# Registration을 사용하는 이유
1. 동일한 환자, 동일한 모달
2. 동일한 환자, 다른 모달
3. 다른 환자, 동일한 모달

* 동일한 환자, 동일한 모달의 경우 과거와 현재 영상을 비교하기 위해서 registration을 사용
* **이때 registration을 사용하면 과거와 현재 영상을 비교해서 분석하기가 쉬워집니다.**

![image](https://github.com/joesiheon496/paper/assets/56191064/21096c4e-1add-4d15-a115-73c680459292)

* 동일한 환자, 다른 모달의 경우 좀 더 풍부한 정보를 얻기 위해서 registration을 사용
> 예를 들어 딥러닝으로 뇌의 병변을 파악하기 위해서는 동일한 모달의 영상이 아니라 다양한 모달의 영상을 같이 사용하는 것이 좋습니다
> 위에서는 4개의 모달이 주어져있습니다. 하지만, 서로 다른 모달의 모양은 조금씩 다를 수 있습니다. 그렇기 때문에 모델의 입력으로 들어가기 전에 registration을 통해서 정합하는 과정이 필요

* 다른 환자, 동일한 모달의 경우 label fusion을 위해서 registration을 사용
> Label fusion이란 기존에 존재하는 label(segmentation mask) 정보를 활용해서 label이 없는 새로운 영상에 label을 해주는 기법
* medical 이미지 처럼 큰 변화가 없는 영상에서 주로 사용
* Medical 이미지의 경우 annotation을 수행하려면 많은 비용이 들게 됩니다. 여기서 registration을 사용해서 자동으로 annotion을 수행하면 비용 절감 효과가 있습니다.

# Registration 종류
* Rigid, Similarity, Affine, Non-rigid 방법

![image](https://github.com/joesiheon496/paper/assets/56191064/f5a3a196-22de-4d1e-989a-28213379f15f)

* Rigid, Similarity, Affine Registration은 여러 Matrix의 조합으로 이루어져 있습니다. 
* 위의 그림에서 보면 대표적으로 Translation, Rotation, Scaling, Shear가 보이게 됩니다.

![image](https://github.com/joesiheon496/paper/assets/56191064/520d2545-d958-4eb7-84c8-1767023f78b7)


x, y는 변경 전 이미지가 되고 x’, y’은 변경 후 이미지가 됩니다

* Rigid Registration: 이동 + 회전
* Similarity: 이동 + 회전 + 크기
* Affine: 이동 + 회전 + 크기 + 전단


* Affine이 가장 계산량이 많지만, 다양한 이미지에 대해서 유연하게 대응
* Rigid, Similarity, Affine 모두 linear transformation이므로 실제 환경의 왜곡된 정보를 표현할 수 없습니다.
* 
















