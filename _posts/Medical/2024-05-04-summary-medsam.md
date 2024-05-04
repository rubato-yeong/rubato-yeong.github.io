---
title:  "[23'~24'] SAM(Segment Anything) Models in Medical Domain" 
excerpt: ""

categories:
  - Medical
tags:
  - [Summary, Segmentation, SAM]

toc: true
toc_sticky: true
 
date: 2024-05-04
last_modified_at: 2024-05-04
---

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/1e5c228b-3bb1-44aa-9c8c-197d99bdba27">
</p>

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/98ec7646-f458-4922-ad38-c76160a1c8d6">
</p>

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/c78f5599-34d7-4fff-bb02-13bea75af8c6">
</p>

<br>


# 1. Introduction

## 1.1. SAM(Segment Anything)

이 글에서는 최근 medical domain에서 주목받고 있는 **SAM(Segment Anything)** 모델들을 정리하여 알아보겠다. **SAM(Segment Anything)**은 Meta AI에서 2023년 발표한 segmentation task에서의 **foundation model**로, **11M개의 image와 1B+개의 mask**를 활용하여 훈련하였다. 모델의 구조는 다음과 같다.

<p align="center">
  <img src="https://github.com/facebookresearch/segment-anything/raw/main/assets/model_diagram.png?raw=true">
</p>

SAM은 크게 3개의 component로 구성되어 있다.

1. **Image Encoder**: ViT-H encoder로 image embedding을 추출한다.
2. **Prompt Encoder**: Points, boxes, texts 등의 prompt를 embedding한다.
3. **Mask Decoder**: Lightweight transformer-based decoder를 사용해 빠르게 mask prediction을 수행한다.

SAM은 굉장히 무거운 image encoder를 사용하여 하나의 image embedding을 만들어내고, 가벼운 mask decoder를 사용하여 segmentation mask를 예측한다. 따라서 이러한 구조를 통하여 한 이미지에 대한 여러 prompt에 대해 빠르게 segmentation을 수행할 수 있게 되었다.

<p align="center">
  <img src="https://github.com/facebookresearch/segment-anything/raw/main/assets/masks2.jpg?raw=true">
</p>

<br>

## 1.2. SAM in Medical Domain

그러나 이러한 foundation model은 natural image에 대한 segmentation task에 대해 훌륭한 성능을 보이지만, **medical image에 대한 segmentation task에 대해서는 성능이 좋지 않다**. 이는 medical image의 특성상 natural image와는 다른 특성을 가지고 있기 때문이다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/daeb3bb4-6f03-4026-9ead-43fe63aa5bdd">
</p>

위 그림을 보아도 SAM은 medical image에 대해 부족한 성능을 보인다는 것을 알 수 있다. 따라서 연구자들은 medical domain에서의 SAM을 구현하기 위해 **방대한 medical image를 사용하여 훈련한 foundation model**을 제안하였다. 이 글에서는 그 중에서 다음 3가지 모델을 소개한다.

1. **[24' Nat Commun] MedSAM**: 거의 최초의 medical domain에서의 SAM 모델로, 그 contribution을 인정받아 2024년 Nature communicaitons에 게재되었다. SAM의 구조를 medical image를 사용하여 fine-tuning하였다.
2. **[23'] SAM-Med2D**: MedSAM과 유사한 시도이나 데이터의 수가 더 많다. 그리고 prompt로 bounding box만 가능했던 MedSAM과는 달리 추가로 point, mask 등의 prompt도 사용할 수 있다.
3. **[23'] SAM-Med3D**: SAM의 구조를 3D로 확장시켜 scratch부터 학습시킨 모델이다. CT, MRI와 같은 많은 의료영상이 3D 구조를 가지고 있기에 이러한 3D 관계를 잘 반영하기 위해 제안되었다. 참고로 SAM-Med2D와 동일한 그룹에서 연구되었다.

<br>

# 2. MedSAM

## 2.1. Methods

**MedSAM**은 SAM의 구조를 동일하게 사용하며, 저자들이 얻은 medical image를 최대한 활용하여 SAM을 fine-tuning하였다. SAM이 2D image에 대해 훈련되었기 때문에, **MedSAM 또한 2D medical image를 사용하여 훈련**되었다. 데이터셋과 모델 구조에 대한 개요는 아래와 같다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/527accf5-f744-4397-84e4-05e3af08f7a7">
</p>

데이터셋은 총 **1.6M개의 image-mask pair**를 사용하였다. 구체적으로 데이터셋을 보면 **대부분이 MRI, CT 이미지**이고, 그 다음으로는 Endoscopy, US, X-ray 등이 있다. Endoscopy, US, X-ray는 2D 이미지이기 때문에 MedSAM에 적합하다. 그러나 MRI, CT는 3D 이미지이기 때문에 MedSAM에 바로 input으로 넣을 수 없고, **2D slice의 형태로 변환하여 사용**한다. 개인적으로는 이 부분이 **치명적인 단점**이라고 생각한다. 2D slice로 정보를 처리하게 되면 **3D image에서 얻을 수 있는 correlation을 잃게 되기 때문**이다. MRI와 CT 이미지의 수가 대부분을 차지하는 이유 중에는 3D image를 2D slice로 만들면서 이미지의 수가 늘어나기 때문인 부분도 있을 것이다.

한편 input shape에 대해 의문이 생길 수도 있다. Endoscopy와 같은 이미지는 RGB 3 channel을 가지고 있고, CT, MRI, X-ray와 같은 이미지는 grayscale이기 때문이다. 이 문제를 해결하기 위해 2D image의 경우는 모두 png 파일로 변환하여 **3 channel로 강제 변환**하였고, CT나 MRI의 경우 2D slice로 만든 뒤 **channel을 3번 반복**하는 방식을 사용하였다.

한편 모델의 구조는 완전히 SAM과 동일하다. 다만 **prompt는 bounding box로 한정**하였는데, 이는 medical image는 정확한 segmentation을 수행하는 것이 생명이다 보니 point, text 등의 prompt는 비교적 부정확한 결과를 가져온다고 판단했기 때문인 것으로 보인다. SAM과 동일한 구조를 사용한 것은 **fine-tuning을 간단히 수행**하기 위함이다.

<br>

## 2.2. Results

MedSAM에서는 validation을 위해 기존의 natural image를 사용한 **SAM**과 **modality-wise specialist**로 훈련된 segmentation model들과 결과를 비교하였다. 결과는 **SAM의 경우가 가장 불안정**했고, **specialist보다 MedSAM의 경우가 더 성능이 좋고 robust한 것**으로 나타났다. 그러나 endoscopy와 같은 비교적 natural image에 가깝고, RGB로 된 이미지에 대해서는 SAM도 높은 성능을 보였다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/9246a5e3-a1f6-47b6-8bc9-d0fbe627289c">
</p>

예상할 수 있는 것과 같이 training data의 수가 늘어날수록 모델 성능이 높아지는 **scaling rule**을 따르는 것으로 나타났고, 실제 전문가에게 MedSAM을 이용한 annotation 작업을 시켰더니 82% 정도의 **시간 단축**이 있었던 것으로 나타났다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/81a4da0e-15cd-4eca-b83b-e4f07cfcb394">
</p>

한편 저자들은 대부분의 데이터가 CT, MRI, 내시경 이미지에 치우쳐진 **modality imbalance**가 있다는 점과 bounding box만 prompt로 한정하고 있어 **vessel-like structure를 잘 segmentation하지 못한다**는 것을 한계점으로 언급하였다.

<br>

# 3. SAM-Med2D

## 3.1. Methods

앞에서 언급한 대로 **SAM-Med2D**의 방법론은 **MedSAM**과 유사하다. 다만 **prompt의 종류**가 더 다양하고 **데이터셋의 수**가 더 많으며 **fine-tuning**의 방법이 다르다. Prompt의 경우 **bounding box, point, mask**를 사용할 수 있으며, 데이터셋의 수는 MedSAM의 경우 1.6M image-mask pair를 사용한 반면 SAM-Med2D의 경우 **4.6M image, 19.7 mask**를 사용하였다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/80df1f0a-1d62-4119-a9e3-ab53cfac6cde">
</p>

그리고 SAM-Med2D는 fine-tuning을 위해 **adapter layer**를 사용하였다. 이는 **pre-trained model의 parameter를 고정**하고, **adapter layer만을 fine-tuning**하는 방식이다. Adapter technique는 최근 굉장히 많이 사용되고 있는 fine-tuning 방법 중 하나이다. 이러한 방식의 장점은 모든 parameter를 fine-tuning하는 것보다 **경제적**이라는 것이다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/bd7c6e15-2df5-4bfe-9066-4662b3dab377">
</p>

## 3.2. Results

결과를 비교하기 위하여 **SAM**, 그리고 encoder는 가만히 둔 채 mask decoder만 fine-tuning한 **FT-SAM**, 그리고 encoder와 decoder를 모두 fine-tuning한 **SAM-Med2D**를 비교하였다. 논문을 쓸 당시에 MedSAM의 code가 없어 재현이 불가능했던 것인지 SOTA model들과의 비교가 없다는 점은 아쉽다. 어쨌든 결과는 다음과 같이 **SAM-Med2D가 가장 좋은 성능**을 보였다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/e2883433-ecde-4846-b747-df415dbade14">
</p>

저자들은 특히 FT-SAM과 SAM-Med2D을 비교하였는데, 이를 통해 저자들이 주장하는 것은 2가지이다. 첫째는 **SAM-Med2D의 adapter layer를 사용한 encoder fine-tuning이 성능 향상에 중요하다**는 것이고, 둘째는 **prompt로 point mode도 bounding box mode와 비슷한 수준의 성능을 보일 수 있다**는 것이다. Point의 개수가 1개면 성능이 떨어지지만, 5개가 되면 bbox의 수준과 거의 비슷한 것을 확인할 수 있다. Organ에 따라 point prompt가 더 성능이 좋은 경우도 있음을 확인할 수 있다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/ee930ccc-043e-4d12-9359-c274b84c98a5">
</p>

이 논문에서 수상한 점은 **generalization validation** 부분인데, **SAM-Med2D에서 adapter layer를 제거한 경우가 더 성능이 좋았다**. 논문에서 언급하지는 않았지만 adapter layer가 training dataset에 **overfitting**되어 generalization 성능이 떨어지는 것으로 추측된다. 사실 이 때문에 **adapter technique를 main contribution이라고 할 수 있을지 의심**스럽다. 어쨌든 아직 arxiv에만 올라온 논문이니...

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/206aee19-b35f-40d4-af3c-4916765adc52">
</p>

저자들은 아직 **complex shape, low contrast object**에 대한 성능이 떨어진다는 점을 한계점으로 언급하였다. 그리고 데이터의 수가 **SAM에 비해서는 아직 부족**하고, foundation model이라 하기에는 무리가 있다는 점을 언급하였다.

<br>

# 4. SAM-Med3D

## 4.1. Methods

**SAM-Med3D**의 경우 지금까지의 모델이 SAM의 구조를 거의 그대로 사용하여 fine-tuning을 진행한 것과는 달리 SAM의 구조를 3차원으로 확장하여 **3D image input**을 받아 learning을 진행하였다. 3D image를 그대로 input으로 사용하면 **3D spatial information을 보존**할 수 있기 때문에 2D slice로 변환하는 MedSAM, SAM-Med2D보다 더 좋은 성능을 기대할 수 있다. 저자들은 데이터로 **21K 3D image와 131K mask**를 사용하였다. 2D image에 비해서는 데이터의 수가 적은 것처럼 보이지만, 보통 3D image를 2D slice로 변환하면 100~300개의 slice가 나오기 때문에 4.6M의 2D image를 사용한 SAM-Med2D와 **절대적인 데이터의 수는 비슷**하다고 볼 수 있다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/305de5fa-5a55-496e-afe1-30c9917f6352">
</p>

SAM-Med3D의 구조는 아래와 같이 단순히 **SAM의 구조를 3D로 확장**한 것이다. 다만 3D image는 보통 MRI, CT 등의 **grayscale image**이기 때문에, 2D 때와는 달리 **1-channel input**을 사용한다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/0561b21c-35d1-4ea3-9313-4920f89c3f9b">
</p>

한편 학습을 위해 SAM의 pre-trained weight을 사용하는 방법(2D SAM weight을 3D에 복사하여 사용)도 시도해보았지만 성능이 오히려 떨어지는 결과를 얻었다고 한다.  따라서 저자들은 **learning from scratch**를 통해 모델을 학습하였다.

<br>

## 4.2. Results

SAM, SAM-Med2D, SAM-Med3D의 성능을 비교하였는데, external evaluation이 포함되었는지 확실하지 않아 결과에 대해서는 신뢰하기 어렵다. 그러나 **SAM-Med3D가 가장 좋은 성능**을 보였다고 한다. 또한, **prompt points의 수가 늘어날수록 성능이 향상**되는 것을 확인할 수 있었다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/69f8a89c-d05d-4e90-9734-5b1bf8fb89e4">
</p>

저자들은 SAM-Med3D의 가장 큰 장점으로 **annotation efficiency의 향상**을 꼽는다. 2D slice로 annotation을 진행하려면 Z축으로 N개의 slice에 대해 각각 prompt를 찍어주어야 하지만, 3D input을 사용하면 **한 번의 prompt로 3D volume에 대한 annotation을 진행**할 수 있기 때문이다. 그리고 아래 그림은 **3D spatial information을 잘 반영**하여 segmentation을 수행하는 것이 왜 중요한지 보여준다. 즉, 3D input을 그대로 넣어 **inter-slice consistency**를 유지할 수 있게 된다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/59e10872-9556-4969-ae97-edaf7a9d0bcb">
</p>

본 논문에서 흥미로운 실험 중 하나는 **transferability evaluation**이다. SAM-Med3D의 pre-trained weight을 사용하여 UNETR segmentation model을 학습시킨 경우가 더 좋은 성능을 보였다고 한다. **SAM-Med3D의 encoder의 feature extraction 능력**을 잘 보여준다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/604704b5-c8e5-40dd-9a16-5cdbccb69fb3">
</p>

개인적으로는 **point prompt에 대해서만 실험을 진행**한 점이 아쉽다. SAM-Med2D에서는 5 point prompt와 bounding box prompt가 거의 비슷한 성능을 보이는 것을 확인할 수 있었는데, SAM-Med3D에서는 구현상의 이유인지 bounding box prompt를 구현하지 않아 이 정확성의 차이를 확인할 수 없었다. SAM-Med2D에서 저자들의 주요 주장 중 하나가 point prompt도 point의 개수가 많아지면 가장 정확한 prompt로 흔히 알려진 bounding box와 비슷한 성능을 보인다는 것이었는데, 이를 확인할 수 없어 아쉽다.

<br>

# 💡 Summary

지금까지 3가지 모델 **MedSAM, SAM-Med2D, SAM-Med3D**에 대해 알아보았다. 이들은 모두 **SAM의 구조를 기반**으로 하고 있으며, **medical domain에서의 segmentation task**에 대해 성능을 향상시키기 위해 제안되었다. 각각의 특징을 표로 정리해 보았다.

| Model       | MedSAM        | SAM-Med2D         | SAM-Med3D       |
| ----------- | ------------- | ----------------- | --------------- |
| Data        | 1.6M 2D       | 4.6M 2D           | 21K 3D          |
| Mask        | 1.6M 2D       | 19.7M 2D          | 131K 3D         |
| Input       | (256, 256, 3) | (256, 256, 3)     | (128, 128, 128) |
| Prompt      | Bbox          | Bbox, Point, Mask | Point           |
| Fine-tuning | Full          | Adapter           |                 |
| Open-source | Yes           | Yes               | Yes             |

<br>

# 📃 Reference

* <a href="https://www.nature.com/articles/s41467-024-44824-z" target="_blank">[24' Nat Commun] MedSAM: Segment Anything in Medical Images</a>
* <a href="https://arxiv.org/abs/2308.16184" target="_blank">[23'] SAM-Med2D</a>
* <a href="https://arxiv.org/abs/2310.15161" target="_blank">[23'] SAM-Med3D</a>

<br>