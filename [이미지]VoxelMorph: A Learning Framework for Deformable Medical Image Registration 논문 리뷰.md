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
