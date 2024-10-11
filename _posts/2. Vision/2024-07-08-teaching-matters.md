---
title:  "[23' CVPR] Teaching Matters: Investigating the Role of Supervision in Vision Transformers"
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

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/6bc25af5-747f-448f-baea-5a2a998c8728){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/3f0d3538-12aa-4d5d-b8d4-5e4bf9691ed0){: .align-center}

CVPR 2023 논문이다. 저자들은 세 부분으로 나누어 ViT에 대한 총체적인 분석을 진행하였다. 이때, **Supervised (FS, CLIP), Contrastive Self-supervised (DINO, MoCo), Reconstruction Self-supervised (MAE, BEiT)**의 특성을 각각 분석한다.

1. **How: Attention**: 어떻게 ViT이 정보를 처리하는지 MHA를 시각화하여 분석한다.
2. **What: Features**: 실제로 우리가 사용하는 Feature가 어떻게 생겼는지 분석한다.
3. **Why: Downstream Task**: 왜, 혹은 어떠한 downstream task에 어떠한 ViT을 사용해야 하는지 분석한다.

<br>

# 1. How: Attention

## 1.1. Attention Visualizations

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/569c78d7-a949-4059-bf13-b61aa035cc99){: .align-center}

저자들은 `[CLS]` Token의 attention map을 분석하는 것으로부터 시작했다. Visualization을 보면 **Mid-to-final layer**에서 그 차이가 드러나는데, 각각의 특징을 분석해보면 다음과 같다.

* **Supervised**: **Sparse Repeating Patterns**를 보인다. 겉보기에는 특별한 spatial meaning이 없는 attention map을 생산해내는 것이다. 이는 `[CLS]` Token이 아닌 Spatial Token에서도 마찬가지이다. 그리고 이러한 패턴은 여러 Head와 여러 Layer에서 반복적으로 나타난다. 저자들은 이러한 패턴이 **Supervised Learning**에서만 나타나는 이유로, mid-to-final layer에서는 더 이상 scene structure를 처리하는 것이 아니라 해당하는 task에 대한 **final decision**을 처리하는 것이라고 추측한다. 예를 들어 Fully supervised setting이라면, 이미지를 이해하기보다는 Classification Label을 예측하고 처리하는 데 더 집중한다는 것이다. CLIP도 text-superivsed learning이라는 점에서 이러한 패턴을 보인다.
* **Contrastive Self-supervised**: **Salient Foreground Object**에 집중하여 **object-centered blob** 형태를 보인다.
* **Reconstruction Self-supervised**: 다양한 **spatial patterns**을 보인다. 이는 여러 위치의 **reconstruction loss**를 최소화하기 위해 다양한 spatial patterns을 통합하는 작업이 이루어지고 있기 때문이다.

특히 **Sparse Repeating Patterns**는 **Supervised Learning**에서만 나타나는 특징으로, fully-supervised ViT가 self-supervised ViT보다 **segmentation 성능이 떨어지는 이유**로 설명할 수 있다.

<br>

## 1.2. Emergence of Offset Local Attention Heads

ViT는 MHA를 사용하여 일부 head에서는 **local attention**을 주로 수행하고, 일부 head에서는 **global attention**을 주로 수행한다. 그러나 모든 Head의 Attention을 각각 분석하는 것은 쉽지 않다. 저자들은 **local attention**을 더 잘 분석하기 위해 *Aligned Aggregated Attention Maps (AAAMs)*라는 **Visualization 방법을 제안**한다. ImageNet 이미지 5,000개에 대해 각 Spatial Token Attention Map을 구하고, 각 Spatial Token이 배열 중심에 오도록 재배열하여 평균을 구하는 것이다. 이러한 방법을 사용하면 **Global Attention 정보는 거의 무시되고 Local Attention 정보**만 남게 된다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/bbd4be39-180b-48ea-a00a-f2a0bdc4791e){: .align-center}

이를 통해 여러 Head의 역할을 나누어 분석해볼 수 있었다. 거의 자기 자신에만 집중하는 **Strict Local Attention Heads**, 주변에 집중하는 **Soft Local Attention Heads**, 그리고 Axial Feature에 집중하는 **Axial Local Attention Heads** 등 다양한 Head가 있었지만, 저자들은 **Offset Local Attention Heads**에 주목했다. 

**Offset Local Attention Heads**는 Spatial Token 바로 옆 칸에 집중하는 Head로, 저자들은 이들이 **ViT에 꼭 필요**하다고 설명한다. CNN과 달리 ViT의 알고리즘은 **특정 Spatial Arrangement**를 분간할 방법이 없다. 심지어 **Soft Local Attention Heads**의 경우 symmetric attention pattern을 가지기 때문에 왼쪽, 오른쪽 등을 구분할 수 없다. 따라서 **Offset Local Attention Heads**가 이러한 문제를 해결하는 데 중요한 역할을 한다.

<br>

## 1.3. Average Attention Distance

**Average Attention Distance**는 특정 Head가 Local Attention을 수행하는지, Global Attention을 수행하는지를 판단하는 방법이다. 이는 각 Spatial Token과 다른 Token 간의 거리를 Attention map을 사용한 Weight을 부여하여 계산된다. 즉, **AAD(Average Attention Distance)가 작을수록 Local Attention을 수행하는 Head**에 가깝다고 볼 수 있다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/2524a67c-514d-4f39-a8ba-c7340770490b){: .align-center}

초기 Layer에서는 **Global Attention과 Local Attention이 모두** 수행되고, 중간 Layer에서는 **Local Attention**이 더 많이 수행된다. 그리고 나중 Layer에서는 **Global Attention**이 더 많이 수행된다. 다만 학습 방법에 따라 그 분포는 달라질 수 있다. 이는 어느 정도 수긍 가능한 결과이다.

<br>

## 1.4. Attention Alignment with Salient Content

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/28c9867d-bf0d-4a1d-9bb7-d1a82d9fe7b3){: .align-center}

Attention Analysis의 마지막으로 저자들은 **Attention Alignment with Salient Content**를 분석했다. 이는 **Attention Map과 Image의 Salient Object**를 비교하여, 어떤 모델이 Salient Object에 더 잘 집중하는지를 분석하는 것이다. 결과적으로 Self-supervised Model과는 달리 Supervised Model은 **Sparse Repeating Patterns**로 인해 후반부 layer에서 Salient Object에 집중하지 못하는 것을 확인할 수 있다.

<br>

# 2. What: Features

## 2.1. Last Block Feature Comparisons

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/da54b4ab-02ac-4d29-bb85-93ef4cf7a3cc){: .align-center}

저자들은 먼저 **CKA(Center Kernel Alignment)**를 사용하여 **Feature의 유사성**을 분석했다. 정확히는 **Batched CKA**를 사용하여 여러 모델 사이의 Feature를 어느 정도 Align하여 비교하였다. 결과적으로 `[CLS]` Token의 Feature는 유사한 Supervision 방법을 사용한 모델끼리는 유사하게 나타났다. 또한, **Supervised Model**과 **Contrastive Self-supervised Model**은 **Reconstruction Self-supervised Model**보다 **Feature가 더 유사**하게 나타났다. 한편 Spatial Token의 경우 **MAE, MoCo, DINO 간의 Feature가 유사**하게 나타났다.

<br>

## 2.2. Depth-Wise CKA Analysis

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/02fda905-d2b6-40e8-9935-8d266b9d1135){: .align-center}

한편 동일한 모델 내부에서 Feature의 유사도를 비교해보면, **Supervised Model** 및 **Contrastive Self-supervised Model**은 크게 **two-stage**로 특성을 처리하는 것으로 보이지만 **Reconstruction Self-supervised Model**은 완전히 다른 **X pattern**을 보인다. 이는 초반부 Layer와 후반부 Layer에서 하는 행동이 유사하기 때문인데, 이는 reconstruction이라는 pre-text task의 특징 때문이다.

<br>

## 2.3. Feature Clustering for Global Semantics

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/18b3d056-03df-429b-820c-e0b59b7d2cfa){: .align-center}

저자들은 각 Token이 Global Semantic Information을 얼마나 잘 추출하는지 확인하기 위해 5000개의 ImageNet Image에 대해 **Feature Clustering**을 수행했다. 그리고 실제 GT Label과 Cluster를 비교하여 **Purity**를 비교하였다. Purity가 높을수록 Global Semantic Information을 잘 추출한다는 것이다. 결과적으로 **Supervised Model**이 **Self-supervised Model**보다 **Global Semantic Information을 더 잘 추출**한다는 것을 확인할 수 있었다. 그리고 후반부 Layer에서 더 높은 Purity를 보인다.

<br>

## 2.4. Feature Clustering for Local Semantics

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/377390ab-aef9-45e0-a462-7308f9b3e069){: .align-center}

한편 Local Feature의 경우 **Supervised Model**, **Contrastive Self-supervised Model**이 좋은 성능을 보이는 것을 확인할 수 있었다. 다만, 해당 분석의 경우 Object 및 Part에 대해 인식하는 Feature Map을 분석한 것으로 Local Feature를 실제로 잘 인지하는지에 대한 분석이라고 보기에는 어려워 보인다.

<br>

# 3. Why: Downstream Task

## 3.1. Global Tasks

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/c8cb872b-9c69-454f-a8dc-547d7f8c60a3){: .align-center}

저자들은 Global Task를 비교하기 위해 **ImageNet Classification**과 **Image Retrieval**을 수행하였다. 결과는 Global Semantic Feature를 비교했던 Figure 7과 거의 유사한 그래프를 그리며 해당 분석의 결과를 재확인했다.

<br>

## 3.2. Local Tasks

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/2b5ccc6a-6605-450c-b942-b7eecc5d1caf){: .align-center}

전반적으로 Local Task는 바로 마지막 Layer보다는 중간 Layer에서 더 잘 수행되는 경향을 보인다. 이는 **Local Feature를 중간 Layer에서 더 잘 추출**하기 때문이다.

<br>

# 💡 Summary

지금까지의 내용을 요약하면 다음과 같다.

1. Supervised ViT는 mid-to-late layer에서 **Sparse Repeating Patterns**를 보인다. 이는 **final decision**을 내리는 데 집중하기 때문이고, 따라서 Classification Task에서는 성능이 개선되는 반면 Dense Prediction Task에 해당 layer는 적합하지 않다.
2. 모든 ViT에서 **Offset Local Attention Heads**가 나타나며, 이는 **Local Attention**을 수행하는 데 중요한 역할을 한다.
3. ViT은 **local 및 global information**을 supervision method에 따라 다른 방식으로 처리한다.
4. 모든 ViT에서 **foreground object**에 집중하는 layer는 **early-to-mid layer**에 나타난다.
5. **Supervised ViT**의 특성이 **semantic information**을 가장 잘 담고 있으나, contrastive self-supervised ViT도 어느 경우에는 비슷한 성능을 보인다.
6. **Localized Task**에서는 **mid-to-late layer**가 가장 좋으나, final layer보다는 중간 layer에서 더 좋은 성능을 보인다.

<br>

# 📃 Reference

* [[23' CVPR] Teaching Matters: Investigating the Role of Supervision in Vision Transformers](https://arxiv.org/pdf/2212.03862)

<br>