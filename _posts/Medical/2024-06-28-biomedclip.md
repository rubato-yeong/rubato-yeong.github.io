---
title:  "[23'] BiomedCLIP: A Multimodal Biomedical Foundation Model Pretrained from Fifteen Million Scientific Image-text Pairs" 
excerpt: ""

categories:
  - Medical
tags:
  - [Contrastive Learning]

toc: true
toc_sticky: true
 
date: 2024-06-28
last_modified_at: 2024-06-28
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/6e016be5-fb68-4fdc-b186-3e6b6ff8c84e){: .align-center}

<br>

# 🔍 Abstract

Microsoft Research에서 2023년에 발표한 논문이다. 논문의 형식을 보아 Nature 관련 Journal Publication을 준비하고 있는 것으로 보인다. 이 논문에서는 **BiomedCLIP**이라는 **pre-trained biomedical foundation model**을 제안한다. Architecture development가 있는 것은 아니고, dataset을 구축했다는 데 그 의의가 있다. 지금까지의 데이터셋은 기껏해야 200K 정도였는데, 본 논문에서는 PubMed로부터 Figure-caption 정보를 추출하여 **15M**의 image-text pair를 구축하였다. 이를 **PMC-15M**이라 하고, 이를 이용하여 **BiomedCLIP**을 학습하였다. 이 모델은 (1) Classification, (2) Retrieval, (3) VQA 등 다양한 task에서 robust한 성능을 보여준다.

<br>

# 1. Methods

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/ab69268a-8185-4da5-954b-034f0625bb60){: .align-center}

굉장히 Figure가 잘 되어 있어서, 이것만 보고도 방법론을 쉽게 이해할 수 있다. 저자들은 PubMed Central의 4.4M 개의 Open Access full-text paper로부터 Figure-caption pair 15M 개를 추출하여 **PMC-15M**을 생성하였다. 또한, 추가 분석을 위해 이 figure를 sub-figure로 잘게 쪼갠 **PMC-Fine-Grained-46M**도 생성하였다. 이는 **굉장히 다양한 dataset**을 담고 있어서, **biomedical domain의 foundation model**이라 불릴 만하다. 기존 CLIP에서 상세한 부분이 Biomedical data의 특성에 맞게 바뀌었는데, 정리하면 다음과 같다.

* **Text Side**: **PubMedBERT**를 사용하여 Domain-specific knowledge를 잘 해석하도록 했고, 비교적 biomedical text는 길다는 점을 고려하여 **context length를 77에서 256으로** 늘렸다.
* **Image Side**: **ViT-B/16-224**를 사용하였다. 384 x 384를 사용하지 않은 것은 어떤 이미지의 해상도는 너무 커서 이로부터 성능 향상이 있는 반면, 다른 이미지는 너무 작아서 성능이 떨어지는 문제가 있었기 때문이다. 따라서 computational cost를 줄이기 위해 224 x 224로 설정하였다. 다만, domain에 따라 더 큰 input size를 사용하는 것이 더 좋을 수도 있다. 

<br>

# 2. Experiments

저자들은 (1) **Classification**, (2) **Retrieval**, (3) **VQA** task에서 BiomedCLIP을 평가하였다.

<br>

## 2.1. Classification

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/1d3d3691-7287-4d10-8578-68831ef8a00a){: .align-center}

1. BiomedCLIP은 기존 모델보다 **전반적으로 높은 성능**을 보여준다.
2. **PLIP(Pathology-specific CLIP)**과 Pathology-domain Evaluation (PCam, LC25000, TIL) 에서** 비슷한 성능**을 보여준다. PCam의 경우 PLIP 학습에서 충분히 유사한 데이터가 학습되지 않아, 오히려 BiomedCLIP이 더 좋은 성능을 보여준다.
3. BioViL, GLoRIA 등 **CXR-specific CLIP보다** CXR-domain Evaluation (RSNA) 에서 **더 좋은 성능**을 보여준다.

<br>

## 2.2. Retrieval

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/753cd83b-8a46-4c47-be04-fdc826dcc429){: .align-center}

1. BiomedCLIP은 다른 어떠한 모델보다도 **월등한 성능**을 보여준다.
2. PubMedCLIP의 경우 CLIP보다도 성능이 떨어졌는데, 이는 **적은 수의 Radiology-specific Pair로 학습**하여 학습 효과가 떨어지고 Catastrohpic Forgetting이 발생했기 때문이다.

<br>

## 2.3. VQA

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/5d70ebdf-bac4-4cd5-8972-c3fad4b0cd9b){: .align-center}

1. VQA를 목적으로 훈련된 PubMedCLIP과 비슷하거나 더 좋은 성능을 보였다. 특히 **open-ended VQA**에서 더 좋은 성능을 보여주었다.
2. Example로부터 알 수 있듯이, BiomedCLIP은 틀린 답변을 내놓는 경우도 있지만, **적어도 plausible한 답변**을 내놓는 경우가 많았다.

<br>

# 💡 Summary

**BiomedCLIP**은 **PMC-15M**을 사용하여 학습하여 기존 **Radiology-specific Model (BioViL, GLoRIA)**, **Pathology-specific Model (PLIP)**, **VQA-specific Model (PubMedCLIP)** 보다 전반적으로 높은 성능을 보여주었다. 이러한 점에서 BiomedCLIP은 **Biomedical domain의 foundation model**로서의 역할을 할 수 있을 것이다.

<br>

# 📃 Reference

* [[23'] BiomedCLIP: A Multimodal Biomedical Foundation Model Pretrained from Fifteen Million Scientific Image-text Pairs](https://arxiv.org/abs/2303.00915)

<br>