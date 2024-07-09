---
title:  "[24' ICML] Cascade-CLIP: Cascaded Vision-Language Embeddings Alignment for Zero-Shot Semantic Segmentation"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Contrastive Learning, Zero-shot Segmentation]

toc: true
toc_sticky: true
 
date: 2024-07-09
last_modified_at: 2024-07-09
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/2e355896-d579-4d23-8a63-e2f5347ae82d){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/b0d5f371-6931-4091-a7c1-f68c79b56d31){: .align-center}

CLIP의 **Intermediate Layer**에는 **rich object detail**이 들어 있다. 지금까지의 CLIP-based Zero-shot Semantic Segmentation 방법은 모두 CLIP의 last layer만 사용하기에 suboptimal하다는 것이 저자들의 주장이다. 그러나 단순히 multi-level visual feature를 합치는 것은 오히려 성능이 저하되었고, 저자들은 이를 효과적으로 합치기 위해 (1) **Cascaded Vision-Language Embedding Alignment**와 (2) **Neighborhood Gaussian Aggregation**을 제안한다. 해당 방법은 기존 모델에 쉽게 추가로 적용할 수 있다는 점에서 유용하다.

<br>

# 1. Introduction

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/cef77ffd-3daa-4cb9-a23e-9e25f674f815){: .align-center}

저자들은 Multi-level Feature를 사용하기 위해 처음에는 단순히 **concatenation**을 사용하는 단순한 Fusion 방식을 제안했다. 그러나 이는 오히려 성능이 저하되었고, 이를 해결하기 위해 **Cascade-CLIP** 방식을 제안했다. 이제 자세한 구조를 알아보자.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/49c8784a-51c0-4b29-9307-9ccd14012c51){: .align-center}

<br>

# 2. Method

## 2.1. Revisiting ZegCLIP

저자들은 [ZegCLIP](https://rubato-yeong.github.io/multimodal/zegclip/)을 기반으로 모델을 확장하였다. 다시 **ZegCLIP**의 표기를 정리하면 다음과 같다.

* Text Embedding of $C$ classes: $\mathbf{T} = \left[ \mathbf{t}_ 1, \mathbf{t}_2, \cdots, \mathbf{t}_C \right] \in \mathbb{R}^ {C \times d}$
* Image Embedding of $N$ Patches: $\mathbf{H} \in \mathbb{R}^ {N \times d}$
* Global Image Embedding `[CLS]`: $\mathbf{g} \in \mathbb{R}^ {1 \times d}$
* Relationship Descriptor (Final Text Embedding) $\hat{\mathbf{T}} = \text{concat} (\mathbf{T} \odot \mathbf{g}, \mathbf{T}) \in \mathbb{R}^ {C \times 2d}$
* Masks $\mathbf{M} = \text{Softmax} (\mathcal{D} (\phi_ q (\hat{\mathbf{T}}), \phi_ k (\mathbf{H})) \in \mathbb{R}^ {C \times N}$ ($\mathcal{D}(\cdot)$: Text-Image Decoder)

이때 Text Embedding $\mathbf{T}$가 아닌 Relationship Descriptor $\hat{\mathbf{T}}$를 사용하는 이유는 **Overfitting을 방지하기 위해서**이다.

<br>

## 2.2. Motivation

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/87405bfb-10be-4c77-800e-61cf0cbaec84){: .align-center}

Introduction에서 밝혔듯이 단순 Multi-level Feature를 합치는 것은 성능이 저하되었다. 한편 CLIP Encoder를 CKA(Center Kernel Alignment)를 통해 확인한 결과, **Intermediate Layer**와 **Late Layer** 사이의 Feature Alignment가 잘 되어 있지 않다는 것을 발견할 수 있었다. 따라서 단순히 합치는 것은 **Feature 간의 Dissimilarity**로 인해 바람직하지 않다. 대신 저자들은 (1) **Cascaded Vision-Language Embedding Alignment**과 (2) **Neighborhood Gaussian Aggregation**을 통해 이를 자연스럽게 해결하고자 한다.

<br>

## 2.3. Architecture

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/500e2d03-b234-4256-8046-6070fc9d4010){: .align-center}

전체 구조는 위와 같다. 대부분의 구조는 **ZegCLIP**과 같으며, ZegCLIP의 많은 복잡한 부분이 생략되어 있다. 몇 가지만 짚어보면 (1) **Relationship Descriptor** $\hat{\mathbf{T}}$를 얻는 과정이 생략되어 있으며, (2) 모든 CLIP Layer마다 **DPT(Deep Prompt Tuning)을 진행**하는 과정이 잘 드러나 있지 않다. 이러한 점을 고려하며 새로 추가된 Component를 살펴보자.

<br>

### NGA(Neighborhood Gaussian Aggregation)

먼저 **NGA(Neighborhood Gaussian Aggregation)**는 **주변 Layer의 Feature를 자연스럽게 합치는 방법**이다. 기준 Layer로부터 멀어질수록 해당 Layer와의 유사도가 낮기 때문에, 적은 Weight을 주어 합치는 방식이다. $s$번째 Decoder에서 Transformer Layer $d$개를 사용하여 Layer를 합치고자 한다면, Layer Feature $\mathbf{H}_ l$의 Weighted Sum을 다음과 같이 계산한다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/f4833967-fe1c-4683-85b8-f8dfc36f3faa){: .align-center}

이러한 방법은 단순 Multi-level Feature를 합치는 것보다 **Feature 간의 Dissimilarity**를 줄여주는 효과가 있다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/e651c00f-5358-429b-8eee-da3e78bcd513){: .align-center}

<br>

### Cascaded Vision-Language Embedding Alignment

이제 각 Level의 Mask를 생성해 보자. 먼저 Text Embedding $\hat{\mathbf{T}}$로부터 MLP를 통해 얻은 $\hat{\mathbf{T}}_ s$와 NGA를 통해 얻은 Image Embedding $\mathbf{Z}_ s$를 사용하여 각 Level의 Mask $\mathcal{D}(\hat{\mathbf{T}}_ s, \mathbf{Z}_ s)$를 생성한다. 이를 모두 더한 뒤 Softmax를 취해 최종 Mask를 얻는다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/35c681c8-61ac-49fa-8935-32a984f9c6ed){: .align-center}

이렇게 Feature를 통합할 때는 너무 **초반부 Layer는 성능에 악영향을 끼쳤으며, Mid-to-Late Layer들이 중요한 역할**을 했다. 그리고 Last Layer를 NGA를 통해 이전 Layer와 합치는 것은 성능을 저하시켰는데, 저자들은 **Last Layer가 Text Embedding과의 Contraastive Loss를 학습하기 위해 상당히 다른 Feature Space를 가지고 있을 것**이라고 추측한다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/32bf3be7-714d-48b2-b742-1f4f760d7a69){: .align-center}

<br>

# 3. Experiments

## 3.1. Main Results

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/9f304c00-498a-497b-a32a-b5f5d16e7c10){: .align-center}

저자들은 이러한 **Multi-layer Feature Fusion** 방법을 통해 **Zero-shot Semantic Segmentation**에서 다른 모델들에 쉽게 추가될 수 있었고, 이를 통해 꽤나 성능을 향상시킬 수 있었다.

<br>

## 3.2. Feature Analysis

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/8298ba4f-11a5-442d-aec9-e85507b29fe6){: .align-center}

실제로 **Last Layer인 Layer 12보다 Layer 11이 더 Boundary를 포함한 Localization에 효과적**인 것으로 보인다. 이는 실제 성능으로도 확인된다.

<br>

# 💡 Summary

지금까지의 내용을 정리하면 다음과 같다.

* CLIP의 Intermediate Layer가 Rich Object Detail을 포함하고 있음을 확인하고, 단순 Multi-level Feature를 합치는 것은 오히려 성능을 저하시킨다고 주장함
* 이를 해결하기 위해 Cascaded Vision-Language Embedding Alignment과 Neighborhood Gaussian Aggregation을 제안함


<br>

# 📃 Reference

* [[24' ICML] Cascade-CLIP: Cascaded Vision-Language Embeddings Alignment for Zero-Shot Semantic Segmentation](https://arxiv.org/abs/2406.00670v2)

<br>