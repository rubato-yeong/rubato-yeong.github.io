---
title:  "[24'] ConvLLaVA: Hierarchical Backbones as Visual Encoder for Large Multimodal Models"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Visual Encoder]

toc: true
toc_sticky: true
 
date: 2024-07-05
last_modified_at: 2024-07-05
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/725942a7-0906-41b4-abd3-37f86efd9cea){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/7cf49823-4d18-4ac6-b76d-8585f551e949){: .align-center}

Alibaba에서 2024년 5월에 발표한 논문이다. LMM(Large Multimodal Model)에서는 **High-resolution input**을 주는 것이 성능 개선에 도움이 된다는 것이 알려져 있다. 그러나 **ViT**은 이러한 High-resolution input을 처리할 때 **Quadradic Complexity**를 가지기 때문에 계산량이 많다. 또한 ViT의 태생적인 문제로 **Visual Token의 수가 많고**, 이를 처리하는 것이 복잡하다. 대신, 저자들은 **ConvNeXt**를 Visual Encoder로 사용하여 **1536x1536 input을 단 576개의 visual token으로 변환**하였다. 이러한 방법으로 기존 LMM의 성능을 개선할 수 있다는 것을 보였다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/6c75366a-51cc-4c12-8ce4-ad1b41101a64){: .align-center}

<br>

# 1. ConvLLaVA

## 1.1. Pre-Experiment

기존 모델들은 대개 학습 시 **Vision Encoder는 Freeze**한 상태로 학습을 진행한다. 그 이유는 여러 가지가 있는데, 대개 (1) **성능**이 더 좋거나 (2) 더 **효율적**이기 때문이다. 그러나, 저자들은 **ConvNeXt를 Freeze**하는 것은 **suboptimal**하다는 것을 발견했다. 따라서, 이러한 내용을 초기 실험을 통하여 증명하고자 한다. 먼저 전체 모델의 구조는 다음과 같다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/4a1df744-4a01-460f-9523-eddcbec6ecc0){: .align-center}

그리고 Visual Encoder를 **학습시키지 않는 경우(Table 12)와 학습시키는 경우(Table 13)**에 대한 성능을 비교하였다. 일반적인 상식과 조금 다른 점은 한 번에 Encoder부터 LLM까지 학습시키는 한 번의 Step을 사용한 것이 아니라 **Encoder Training Stage**, **Visual Instruction Stage**를 따로 분리했다는 것이다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/e4e9a606-dc60-425b-bdb5-f69c4ebaa2ce){: .align-center}

결과적으로 **ConvNeXt Training**은 굉장히 효율적이었다. Table 2에 비해 Table 3의 결과가 크게 향상된 것을 확인할 수 있다. 따라서, **ConvNeXt를 학습시키는 것이 중요**하다는 것을 알 수 있다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/176c15b1-9aef-432f-8b29-21be8e108a43){: .align-center}

<br>

## 1.2. ConvLLaVA

한편 이런 경우 한 가지 문제가 있다. ConvNeXt는 Input에 따라 Visual Token 수가 늘어나므로, **High-resolution input**을 처리할 때 Visual Token 수가 많아진다는 것이다. 위 실험에서는 768x768 Input에 의해 576개의 Token이 생성되었다. 저자들은 이 수를 줄이기 위해 **ConvNeXt의 Last Layer (Stage 5)에 추가 Layer**를 달아 아래와 같이 Visual Token 수를 줄였다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/9377f22c-8624-4843-816c-5b68a107ac2e){: .align-center}

High-resolution Input의 정당성을 위해 **동일한 Visual Token 수**로 실험을 진행하였다. 결과적으로 **High-resolution input**을 사용하는 것이 성능을 향상시킬 수 있다는 것을 확인하였다. 다만, 이는 일부 Benchmark에 대해 실험한 것으로 실제로는 큰 성능 차이가 없는 경우도 많았다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/453822f7-0977-4349-a6f8-1ee1a706f21d){: .align-center}

대신, 이러한 방법은 **ConvNeXt Stage 5도 처음에 Adapter Pre-training 시 같이 학습**시켜야 한다. 따라서 최종 모델과 학습 단계를 다음과 같이 설정하였다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/d7bcfcf5-e466-497e-8d4a-70d80688dbf2){: .align-center}

<br>

# 2. Experiment

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/275db0b5-26f2-4e53-b0aa-8700561b0cc4){: .align-center}

전체 결과는 다음과 같이 SOTA를 달성했다는 이야기이다. 특히 **REC와 같이 Vision-centric Task에서 그 성능이 돋보인다**.

<br>

# 💡 Summary

**ConvNeXt**를 Visual Encoder로 사용하여 **High-resolution input**을 처리하는 방법을 제안한 논문이다. 실험을 통해 **High-resolution**, **More Visual Token**, **Vision Encoder Training**이 성능 향상에 도움이 된다는 것을 보였다. 또한, **ConvNeXt Stage 5에 추가 Layer를 달아 Visual Token 수를 줄이는 방법**을 제안하였다.

<br>

# 📃 Reference

* [[24'] ConvLLaVA: Hierarchical Backbones as Visual Encoder for Large Multimodal Models](https://arxiv.org/pdf/2405.15738)

<br>