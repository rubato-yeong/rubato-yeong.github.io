---
title:  "[24' CVPR] PerceptionGPT: Effectively Fusing Visual Perception into LLM"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Visual Perception, Detection, Segmentation, Analysis]

toc: true
toc_sticky: true
 
date: 2024-07-02
last_modified_at: 2024-07-03
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/af0e02fd-5e13-45ca-89d5-114876d18c2c){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/acf00038-b032-4a7f-b7d9-21d9c933d24a){: .align-center}

PerceptionGPT는 **visual perception**을 **LLM**에 효과적으로 통합하는 방법을 제시한다. Shikra에서 보여준 text-based bounding box representation이나 Kosmos-2에서 보여준 discrete token-based bounding box representation은 suboptimal하며, 이러한 discrete representation 대신 **dynamic token embedding인 `<vis>` token 단 하나를 이용하는 것이 성능이 더 좋고 학습에도 유리하며 속도도 빠르다**는 것을 보였다. 이러한 관찰은 이 분야의 중요 논문인 [LISA](https://rubato-yeong.github.io/multimodal/lisa/)의 *embedding-as-mask paradigm*과도 일치한다. 그 외에도 해당 논문의 **분석** 부분이 꽤나 가치 있어서, 해당 논문을 깊이 있게 이해해보고자 한다.

<br>

# 1. Introduction

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/b305b3f2-5b3b-4a83-a650-46c16d11e0bc){: .align-center}

해당 논문에서는 지금까지의 Visual Grounding 또는 Visual Perception 모델들을 크게 두 가지 갈래로 나누어 각각의 문제점을 지적한다.

1. **Two-stage Vision-Language Assistant**: [LISA](https://rubato-yeong.github.io/multimodal/lisa/)로 대표되는 해당 모델군은 SAM과 같은 External API를 사용하여 bounding box 또는 segmentation mask를 생성한다. 그러나 이러한 방법은 **SAM과 같은 expert model이 필요**하며, **visual prompt를 input으로 사용하는 것이 어렵다**는 단점이 있다.
2. **Perception-Enhanced Vision-Language Model**: [Shikra](https://rubato-yeong.github.io/multimodal/shikra/), [Kosmos-2](https://rubato-yeong.github.io/multimodal/kosmos-2/) 등으로 대표되는 해당 모델군은 **discrete bounding box representation**을 사용한다. 이러한 방법은 어느 정도의 성공을 거두었으나 **suboptimal**하다. 대신 논문에서는 **dynamic token embedding**인 `<vis>` token을 사용한다.

<br>

# 2. Method

## 2.1. Dynamic Token Embedding

**PerceptionGPT**에서는 LISA와 같이 하나의 토큰인 `<vis>`를 사용하여 **bounding box 또는 segmentation mask**를 나타낸다. 기존에 이러한 token embedding 방법과 차별화되는 점 중 하나는 이러한 `<vis>` token을 **input으로도** 사용할 수 있고, **output으로도** 사용할 수 있다는 점이다. 이러한 방법은 **dynamic token embedding**이라고 불린다.

<br>

## 2.2. Lightweight Visual Perception Encoder-Decoder

저자들은 `<vis>` token의 encoding과 decoding을 위해 **lightweight visual perception encoder-decoder**를 사용한다. Bounding box와 segmentation mask에 대해 각각 다른 encoder-decoder를 사용한다. 일반적인 encoder-decoder에 비해 굉장히 간단한데, bounding box encoder-decoder는 3-layer MLP로 구성되어 있고, segmentation mask encoder-decoder는 ResNet 및 Bidirectional Transformer로 구성되어 있다.

<br>

## 2.3. Multi-layer Visual Feature Fusion

저자들은 일반적인 VLM Task와 달리 detection이나 segmentation task에서는 **multi-layer visual feature fusion**이 중요한 역할을 한다고 주장한다. 즉, CLIP의 last layer는 fine-grained visual feature가 적기 때문에 이전 layer를 같이 사용하는 것이 효율적이라는 것이다. 따라서 다음과 같이 **trainable weight**을 사용한 **weighted sum**을 사용한다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/ac039793-9dde-4015-b58b-b5dbf1e9b073){: .align-center}

<br>

## 2.4. Training

Training 시에는 **Language Loss, Detection Loss(L1 + GIoU), Segmentation Loss(BCE + Dice)**를 모두 사용한다. Dataset의 경우 RefCOCO, Flicker30k, Visual Genome 등의 referring expression dataset을 사용했고 추가로 LLaVA-curated COCO 등의 captioning dataset을 제작하여 사용했다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/f3ec841d-490b-44c9-98dc-f50e02008828){: .align-center}

<br>

# 3. Experiments

## 3.1. Qualitative Results

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/6f163f5e-f777-451b-bd61-10a22c245e9e){: .align-center}

Visualization 결과는 위와 같다. 놀라운 점은 **Reasoning Segmentation**을 따로 학습하지 않았음에도 LLM의 능력 덕분에 복잡한 질문에도 쉽게 segmentation mask를 대답할 수 있다는 것이다.

<br>

## 3.2. Quantitative Results

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/7528786a-60e7-4c93-b74f-717d1011f980){: .align-center}
![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/c8aae322-a45d-45e0-943a-4ed633714084){: .align-center}

결과는 **Segmentation, Detection, Captioning, VQA** 모두 기존 모델들에 비해 확연히 높은 성능을 보였다. 놀라운 점은 **SAM과 같은 visual expert를 사용한 LISA보다 성능이 좋다**는 점이다. 따로 복잡한 instruction tuning을 하지 않은 것으로 미루어 볼 때 이러한 성능 향상은 **`<vis>` token** 덕분인 것으로 보인다. 저자들은 이러한 `<vis>`를 더 분석했다.

<br>

## 3.3. Impact of `<vis>` Token

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/83afee49-1a15-482c-9371-689b3bae729c){: .align-center}

저자들은 `<vis>` Token의 **세 가지 장점**을 제시한다. (1) Box 또는 Mask를 표현하는 데 단 하나의 token만 필요하기 때문에, text나 multiple token을 사용하여 표현하는 방법에 비해 **Quantization Error가 적다**. 즉, 학습이 안정적이고 성능 향상에 도움이 될 수 있다. (2) Discrete token을 사용하는 방법은 causal relationship을 반영하거나 vision-specific loss function을 적용할 수 없으며, rich perception information을 담을 수 없다. 따라서 큰 **성능 차이**를 가진다. (3) 마지막으로 단 하나의 토큰만 사용하므로 **inference speed가 빠르다**. 아래는 **Numerical**([Shirka](https://rubato-yeong.github.io/multimodal/shikra/)의 방법), **Vocab**([Kosmos-2](https://rubato-yeong.github.io/multimodal/kosmos-2/)의 방법), **Embed**(PerceptionGPT)를 비교한 것이다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/dbde8e90-af91-4bd0-a3c4-2e3a3e86557e){: .align-center}
![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/5af901be-d48e-42ae-beb5-1bdc78e0b4c2){: .align-center}

<br>

## 3.4. Impact of Layer Fusion

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/d39a4fc8-6437-4a25-90e6-e2554833809b){: .align-center}

한편 저자들은 Visual Encoder의 **multi-layer fusion**이 성능에 미치는 영향을 분석했다. 결과는 위와 같다. **CLIP의 last layer만 사용하는 것보다 이전 layer를 같이 사용하는 것이 성능이 더 좋다**는 것을 알 수 있다. 특히, Captioning과 같은 Text-centric task보다 Detection, Segmentation과 같은 Vision-centric task에서 더 큰 성능 향상을 보인다. 이로 미루어 볼 때 **fine-grained visual feature**를 가진 **intermediate layer**가 **vision-centric task에 중요**하다는 것을 알 수 있다.

<br>

# 💡 Summary

**PerceptionGPT**는 **dynamic token embedding**인 `<vis>` token을 사용하여 **visual perception**을 **LLM**에 효과적으로 통합하는 방법을 제시한다. 이러한 방법은 **suboptimal**한 **discrete bounding box representation** 대신 **dynamic token embedding**을 사용함으로써 **성능 향상**과 **학습 속도 향상**을 이룰 수 있다. 이러한 방법은 encoder-decoder approach를 통해 input/output을 자유롭게 표현할 수 있다는 점에서도 중요하다. 놀라운 점 중 하나는 SAM과 같은 powerful API를 사용한 것과 큰 성능 차이가 없다는 점이다. 또한 **multi-layer visual feature fusion**을 통해 **fine-grained visual feature**를 가진 **intermediate layer**가 **vision-centric task에 중요**하다는 것을 보여주었다.

<br>

# 📃 Reference

* [[24' CVPR] PerceptionGPT: Effectively Fusing Visual Perception into LLM](https://openaccess.thecvf.com/content/CVPR2024/papers/Pi_PerceptionGPT_Effectively_Fusing_Visual_Perception_into_LLM_CVPR_2024_paper.pdf#page=7.46)

<br>