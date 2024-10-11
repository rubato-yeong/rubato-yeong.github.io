---
title:  "[21' ICCV] Understanding Robustness of Transformers for Image Classification"
excerpt: ""

categories:
  - Vision
tags:
  - [ViT, Analysis]

toc: true
toc_sticky: true
 
date: 2024-07-08
last_modified_at: 2024-07-08
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/b8f81da6-550b-44cf-90a3-7c86e9d17711){: .align-center}

<br>

# 🔍 Abstract

Google Research에서 2021년 ICCV에서 발표한 논문이다. 저자들은 갓 등장했던 ViT의 Robustness를 다양한 방법으로 확인하고자 했다. 크게 두 갈래로 나누어 정리할 수 있다.

1. **Input Perturbation**: Input에 Corruption, Perturbation, Adversarial Attack 등을 가해 Robustness를 확인한다.
2. **Model Perturbation**: Model Layer를 제거하거나, Attention Range를 변화시키는 등의 방법으로 Model의 Robustness를 확인한다.

이 글에서는 지금까지도 배울 점이 많다고 생각하는 **Model Perturbation**에 더 집중하여 정리하겠다.

<br>

# 1. Input Perturbation

저자들은 먼저 **Input Perturbation**을 통해 ViT의 Robustness를 확인했다. 이를 위해 **ImageNet Variant** 데이터셋들을 사용하였고, 각각의 특징을 정리하면 다음과 같다.

![image](https://production-media.paperswithcode.com/datasets/ImageNet-C-0000002319-23347bee_COZEcfL.jpg){: .align-center}

(1) **ImageNet-C**는 **Natural Corruption**을 반영한 ImageNet 데이터셋이다.

![image](https://production-media.paperswithcode.com/datasets/Screenshot_2021-01-29_at_12.42.41.png){: .align-center}

(2) **ImageNet-R**은 **Real-World Distribution Shifts**을 반영한 ImageNet 데이터셋이다.

![image](https://production-media.paperswithcode.com/datasets/ImageNet-A-0000002320-4df00f35_nUdgdfy.jpg){: .align-center}

(3) **ImageNet-A**는 **Adversarial Attacks**을 반영한 ImageNet 데이터셋이다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/f1034556-9b18-4eb7-baeb-33ca677e5f23){: .align-center}

결과는 다음과 같고, 전반적으로 **ResNet과 유사하거나 우수한 robustness**를 보였다. 이러한 특성은 많은 데이터를 학습하기 때문이라고 저자들은 설명한다. 이러한 robustness는 성능과 마찬가지로 model size가 커질수록 더 좋아지는 경향을 보인다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/54de7c5e-f37c-4fa8-9ba6-2fd8be1570c2){: .align-center}

<br>

# 2. Model Perturbation

## 2.1. Layer Correlations

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/6a51a5a0-cfd2-4f33-84fb-24111e5434a0){: .align-center}

저자들은 두 데이터셋에 대해 **Layer Correlations**를 분석하였다. 즉, Representation 값을 여러 Layer에 대해 비교하고 그 유사도를 측정한 것인데, Spatial Token의 경우 **late layer로 갈수록 layer-wise correlation이 높아지는 것**을 확인할 수 있었다. 즉 많은 **redundancy**가 존재한다는 것이다. 그리고 ViT은 Inductive Bias가 없음에도 Layer가 여러 Stage로 나뉘어지는 것처럼 보인다. 반대로 `[CLS]` Token은 초반에 비해 후반에 그 Feature가 급변하는 모습을 보인다. 저자들은 이러한 관찰로부터 **Early Layer에서는 Spatial Token끼리의 Interaction이 주로 일어나고, Late Layer에서는 그것들과 `[CLS]` Token과의 Interaction이 주로 일어난다**고 추측했다.

<br>

## 2.2. Lesion Study

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/7ea23105-05a4-4d6d-a45b-89434cc74cc3){: .align-center}

ViT에서 Layer를 제거하면 어떻게 될까? 놀랍게도 중반부 Layer를 제거하는 것은 성능에 큰 영향을 미치지 않았다. 이는 **Layer-wise Correlation**이 높아서 **redundancy**가 많기 때문이라고 저자들은 설명한다. 반면, 초반 Layer를 제거하면 성능이 급격히 떨어지는 것을 확인할 수 있었다. 이는 **Early Layer에서는 Spatial Token끼리의 Interaction이 주로 일어나기 때문**이라고 저자들은 설명한다. 또한, MLP Block보다 Self-Attention Block이 더 중요하다는 것도 확인할 수 있었다.

<br>

## 2.3. Restricted Attention

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/90b29a62-67fd-4941-957a-8037617789c0){: .align-center}

ViT의 Attention 거리를 제한하면 어떻게 될까? 저자들은 **Restricted Attention**을 통해 이를 확인하였다. ViT은 이론적으로 Global Attention이 가능하지만, 실제로는 **Local Attention**을 주로 수행하며 더 중요한 것으로 확인되었다. 한편, 저자들은 Patch 간의 Interaction을 제한하고 `[CLS]` Token과의 Interaction만 허용한 경우도 실험하였는데, 놀랍게도 Late Layer에서는 그런 상황에서도 정확도가 크게 떨어지지 않았다. 이는 **Late Layer에서는 주로 `[CLS]` Token과의 Interaction**이 주로 일어나기 때문이다.

<br>

# 💡 Summary

저자들의 Takeaway Summary를 정리한다.

1. ViT은 충분한 데이터 하에서 학습했을 때 ResNet과 유사하거나 우수한 **Robustness**를 보이며, 더 좋은 **Scalability**를 보인다.
2. ViT의 **Early Layer에서는 Spatial Token끼리의 Interaction이 주로 일어나고, Late Layer에서는 그것들과 `[CLS]` Token과의 Interaction이 주로 일어난다**.
3. ViT은 이론적으로 Global Attention이 가능하지만, 실제로는 **Local Attention**을 주로 수행하며 더 중요한 것으로 확인되었다.
4. ViT은 **Layer-wise Correlation**이 높아 **redundancy**가 많다. 특히 ViT의 mid-to-late layer를 제거하여도 성능에 큰 영향을 미치지 않았다.

<br>

# 📃 Reference

* [[21' ICCV] Understanding Robustness of Transformers for Image Classification](https://arxiv.org/pdf/2103.14586)

<br>