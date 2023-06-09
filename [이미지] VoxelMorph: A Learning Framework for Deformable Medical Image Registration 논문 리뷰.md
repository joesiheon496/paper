[해당 블로그 글 따라 쓰기](https://pebpung.github.io/registration/2022/02/03/Registration-2.html)
[원본](https://arxiv.org/abs/1809.05231)

# Deformable Registration 이란?

![image](https://github.com/joesiheon496/paper/assets/56191064/b3c525ec-202c-47fb-8763-4dd2ad5ebd55)

* Deformable Registration이란 뒤틀려져 있는 정보를 정합할 수 있는 매우 유연한 Registration 기법
* Medical 이미지는 연속체에서 어떤 힘이나 시간 흐름에 따라 기하학적인 변화
* rigid, affine 변화처럼 linear한 공간만 다루는 registration 기법은 정확한 정합을 기대하기 힘듭니다.
* Deformable Registration은 공간 자체를 warping 시기 위한 Nonlinear registration map을 얻어서 위의 사진과 같이 기하학적인 변화
* **대부분의 사진 정합에서 높은 정확도로 Registration이 될 수 있다는 장점에도 불구하고 Deformable Registration은 실제로 사용하기 어렵다는 단점**
* Deformable field를 계산하기 위한 computational resource가 많이 든다
* 특히 3D 영상의 경우 각 pair에 대해서 dense하고 non-linear한 correpondance가 이루어지기 때문에 계산량이 많아지게 됩니다.
* 이러한 문제를 해결하기 위해서 최근에는 CNN을 도입한 Deformable registration 기법이 등장

# VoxelMorph
* VoxelMorph는 기존 방법론에서 2가지의 문제를 해결하는데 초점을 맞췄습니다
## 문제점
  - Deep learning 없이 Nonlinear registration mapping을 구하는 계산량이 엄청나게 높음.
  - 기존의 Nonlinear registration은 ground truth를 이용하여 Supervised learning으로 학습을 진행했음.
# 방법론

![image](https://github.com/joesiheon496/paper/assets/56191064/d1b3d827-7312-44a0-9f64-a6acbf2e8387)

* 입력으로는 Moving 3D image와 Fixed 3D image
* 이 두개의 이미지는 미리 affine alignment를 수행
* 두개의 input을 Unet으로 넣어주게 됩니다.

# VoxelMorph를 사용하는 이유

![image](https://github.com/joesiheon496/paper/assets/56191064/b114b56c-cdf9-4532-a685-eeaf04f2425d)

* ANTs모듈의 SyN의 경우 CPU에서 9059s 즉, 2시간 반 정도의 시간이 소모
* 이에 반해서 VoxelMorph는 성능은 비슷하지만 57초로 매우 빠른 성능

# VoxelMorph를 사용해서 Parcellation 수행

![image](https://github.com/joesiheon496/paper/assets/56191064/db2afbdd-fdc3-4f09-86fe-0a190db1cdfc)

> Parcellation이란 Brain의 구조를 분획화
* Registration를 사용해서 Parcellation을 하는 방식은 기존 Segmentation 방법론과는 전혀 다른 방식
* Segmentation을 사용할 때는 위에서 드린 설명과 같이 입력을 넣으면 픽셀 단위로 인식하는 단계가 필요합니다. 
* Registration은 픽셀 단위로 인식 하는 단계 없이, 기존 Segmentation Mask정보를 변형해서 원하는 작업을 수행

![image](https://github.com/joesiheon496/paper/assets/56191064/bc65947f-13be-4ca2-a2a4-eb676711bcf4)

* Moving image인 y1과 Segmentation mask z1이 입력 되었다고 가정하면, y1을 Fixed image인 x와 registration(정합)될 수 있는 하는 registration field T를 계산
> Moving image란 Fixed image와 비슷한 형태로 변형이 되는 대상
* y1의 Segmentation mask z1에 T를 적용해서 x의 Segmentation mask T(z1)을 얻게됩니다

![image](https://github.com/joesiheon496/paper/assets/56191064/eff32666-ca04-4924-a5ac-79b793bd3c92)

* Moving 3D image인 m, Segmentation mask 정보인 Sm, Fixed 3D image인 f가 주어져 있습니다.
* Moving 3D image인 m을 Fixed 3D image인 f로 정합하는 Registration field 찾는 것이 VoxelMorph를 이용한 Parcellation 방식의 핵심
* 이렇게 찾은 Registration field를 사용해서 m의 Segmentation mask 정보인 Sm을 Fixed 3D image f의 Segmentation mask로 변형시켜야함

![image](https://github.com/joesiheon496/paper/assets/56191064/b7bf5bd5-2b48-4172-83e2-f1ebfd508d98)

* 위와 같이 파란색 박스의 정보를 얻게 되면 Moved Segmentation 정보를 Fixed 3D image f의 Segmentation Mask로 활용

---

# VoxelMorph: A Learning Framework for Deformable Medical Image Registration
- CNN based Deformable Registration 대표 논문

## Image Registration이란
- Registration은 다른 각도나 크기, 위치에 있는 두 영상을 매칭 시켜주는 영상 정합을 말한다.
- 두 영상 간의 매칭점을 찾아 주고, 매칭점을 바탕으로 Transformation Matrix를 예측한다.
- 예측된 Matrix는 영상을 변환시켜줄 때 사용한다.
    - 변환의 기준의 방법은 iteration closest point(ICP)가 있다.
- 움직이는 영상을 Moving, Source image라고 부르고 고정된 영상은 Fixed, Target, Refence Image라고 부른다.
- Registration이 완료된 이미지는 Moved, Result, Transformed image로 쓰인다.

## Registration 사용 이유
- 동일한 환자, 동일한 모달
    - 동일한 환자, 동일한 모달의 경우 과거와 현재 영상을 비교하기 위해 Registration을 사용한다.
    - 시간의 흐름에 따라 환자의 영상에 어떤 변화가 있는지 파악하고 싶을때 사용한다.
- 동일한 환자, 다른 모달
    - 좀더 풍부한 정보를 얻기위해서 Registration을 사용한다.
    - 동일한 모달의 영상이 아니라 다양한 모달의 영상을 같이 사용하는것이 좋다
- 다른 환자, 동일한 모달
    - label fusion을 위해서 registration을 사용한다.
        - Label Fusion이란 기존에 존재하는 label(segmentation mask)정보를 활용해서 label이 없는 새로운 영상에 label을 해주는 기법이다.
        - medical 이미지 처럼 큰 변화가 없는 영상에 주로 사용된다.
        - registration을 사용해서 자동으로 annotation을 수행하면 비용 절감 효과가 있다.

## Registration 종류
- 여러 Matrix의 조합으로 이루어져 있음
- 대표적으로 Translation(이동), Rotation(회전), Scaling(크기), Shear(전단)
    - Rigid
        - 이동 + 전단
    - Similarity
        - 이동 + 회전 + 크기
    - Affine
        - 이동 + 회전 + 크기 + 전단
        - 계산량이 가장 많음
        - 다양한 이미지에 대해서 유연하게 대응할 수 있음
    - Non-rigid
        - 뒤틀려져 있는 정보를 정합할 수 있음

## Deformable Registration이란
- 뒤틀려져 있는 정보를 정합할 수 있는 유연한 Registration 기법
- 이미지는 연속체에서 어떠한 힘이나 시간의 흐름에 따라 기하학적인 변화가 생김
- 공간 자체를 warping(뒤틀림) 시기 위한 Nonlinear registration map을 얻어서 기하학적인 변화를 일으킴
- 하지만 계산하기 위한 computational resource가 많이 들어 사용하기 어령룬 단점 존재
- 최근 CNN을 도입한 Deformable registration 기법이 등장함

## VoxelMorph
### 접근방식
- Deep Learning 없이 Nonlinear registration mapping을 구하는 계산량이 높음
- 기존의 Nonlinear registration은 ground truth를 이용하여 Supervised Learning으로 학습 진행
    - Supervised Learning에 대한 문제점 제기
        - Supervised learning은 ground truth 정보가 필요
        - 3차원 상에서의 전체 이미지의 Nonlinear registration map을 얻는 작업 또한 계산량이 많이 듦
        - UnSupervised Learning 방식을 제안
- Moving 3D image와 Fixed 3D image 필요
    - 두개의 이미지는 미리 affine alignment 수행후 Unet으로 넣어줌
    - 기존의 ANTs 모듈과 비교해본 결과 ANTs모듈은 2시간 30분 정도 소요되지만 VoxelMorph는 57초

## Registration을 이용해 Parcellation(뇌의 구간 구분) 수행
- 기존 Segmentation과는 다른 방식
- Segmentation은 입력을 넣으면 픽셀 단위로 인식하는 단계가 필요하지만
- Registration은 픽셀 단위로 하지 않고 기존 Segmentation Mask 정보를 변형해서 원하는 작업을 수행
    1. Moving Image(변형이 되는 대상)와 Segmentation Mask를 Fixed image와 정합될 수 있게 하는 registration field T를 계산
    2. Segmentation Mask에 T를 적용해서 Fixed image의 segmentation 정보를 얻음

## VoxelMorph를 사용해서 Parcellation(뇌의 구간 구분) 수행
1. Moving 3D image와 segmentation mask 정보와 Fixed 3D image가 주어짐
2. Moving 3D image와 Fixed 3D image와 정합하는 Registration field를 찾는것
3. 찾은 Registration field를 사용하여 Moving 3D image의 segmentation을 Fixed 3D image의 Segmentation으로 변형해야함
