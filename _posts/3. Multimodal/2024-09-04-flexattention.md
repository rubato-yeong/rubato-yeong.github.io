---
title:  "[24' ECCV] FlexAttention for Efficient High-Resolution Vision-Language Models"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Efficiency, Attention]

toc: true
toc_sticky: true
 
date: 2024-09-04
last_modified_at: 2024-09-04
---

![image](https://github.com/user-attachments/assets/ec182c98-b9cf-4815-8226-c514f39c6fea){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/user-attachments/assets/42577fce-8571-4880-82e9-b5f05cce879a){: .align-center}

* **Problem**
  * 현재 High-Resolution Vision-Language Model은 아주 긴 Image Token을 사용하여 성능을 높이고 있다.
  * 하지만 이러한 방식은 **계산량이 매우 많아** 효율적이지 못하다.
* **Method**
  * 저자들은 **FlexAttention**이라는 flexible attention mechanism을 제안한다.
  * 이는 Low-resolution Token에서 High Attention을 받는 Token 위치에 해당하는 High-resolution Token만을 선택적으로 활용하는 방법이다.
* **Result**
  * 이를 통해 **계산량을 40% 가량 줄이면서도** 오히려 선택적인 Token을 활용하여 **High-resolution VQA**에서 **성능을 향상**시킬 수 있었다.
  * 대신, 이 방법은 **FlexAttention**이라는 Module을 따로 추가하여 **학습시켜야 한다**는 번거로움이 있다.

<br>

# 1. FlexAttention

![image](https://github.com/user-attachments/assets/a2e6c0a0-ee82-45b2-83ff-6f3e363d7bdd){: .align-center}

**FlexAttention** Module은 Decoder Layer 중간부터 시작하며, 이전까지의 Layer 수를 $N_ {SA}$, 이후의 Layer 수를 $N_ {FA}$라고 하자. 이때, **FlexAttention**은 두 개의 Module로 이루어져 있다. (1) **High-resolution Feature Selection Module**과 (2) **Hierarchical Self-Attention Module**이 그것이다.

<br>

## 1.1. High-resolution Feature Selection Module

![image](https://github.com/user-attachments/assets/302d63c8-d302-472a-84e3-d52864ad84c5){: .align-center}

**High-resolution Feature Selection Module**은 **Low-resolution Token**에서 **High Attention**을 받는 Token 위치에 해당하는 **High-resolution Token**만을 선택적으로 활용하는 방법이다. 이를 위해 **Attention Score**를 계산하고, 이 중 특정 Threshold 이상의 Token만을 선택하여 활용한다. 따라서, 원래는 Low-resolution Token의 수 $f_ {LR}$과 High-resolution Token의 수 $f_ {HR}$을 모두 활용했다면, 이제는 $f_ {LR}$과 **일부 선택된 High-resolution Token** $f_ {SHR}$만을 활용한다. 이때 Attention Score를 계산할 때는 단순히 **Last Text Feature**의 것을 사용한다. 여기에는 **근거가 명확하지 않다**.

<br>

## 1.2. Hierarchical Self-Attention Module

그렇다면 이렇게 뽑은 $f_ {SHR}$을 어떻게 활용할까? **Hierarchical Self-Attention Module**은 이를 활용하는 방법을 제안한다. 수식은 조금 복잡해 보이지만, 결과적으로 **Key, Value**에 해당하는 $f_ {SHR}$을 활용하여 일종의 **Cross-Attention**을 수행한다. 이때 **Query**는 $f_ {LR}$과 이후 Text가 사용된다.

![image](https://github.com/user-attachments/assets/e2574d98-9fea-45c3-a29d-4da31f44f4f1){: .align-center}

이후, 여기에서 계산된 **Attention Score**를 이용하여 다시 Feature Selection을 진행한다.

![image](https://github.com/user-attachments/assets/4832b96f-0fd0-4054-99ae-0ab0c9f1dd87){: .align-center}

정확히는, 원래 $Q \in \mathbb{R}^ {N \times d}$, $K_ {all} \in \mathbb{R}^ {(N + M) \times d}$라고 하면 $Map^ \prime$은 $\mathbb{R}^ {N \times (N + M)}$이 된다. 우리는 이 중에서 $\mathbb{R}^ {N \times N}$만을 선택하여 활용하는 것이다. 전체 알고리즘은 다음과 같다.

![image](https://github.com/user-attachments/assets/0ce184f6-5921-4e2a-b6f6-cd389d736907){: .align-center}

<br>

# 2. Experiment

## 2.1. Main Results

![image](https://github.com/user-attachments/assets/ed90ea6a-9e08-4a65-b8de-70f4bf333603){: .align-center}
![image](https://github.com/user-attachments/assets/624c67c1-a42e-4290-a48e-0969306e3dae){: .align-center}

**Table 1 & 2**. **FlexAttention**을 적용한 결과이다. **FlexAttention**을 적용하면 **40% 가량의 계산량을 줄일 수 있으면서도** 성능을 향상시킬 수 있었다. 저자들은 LLaVA-1.5-HD를 Baseline으로 잡고 있는데, 사실 이것이 정당한 비교인지에 대해서는 논란이 있을 수 있다. **LLaVA-1.5-HD**는 Resolution으로 448을 사용하고 있지만, **FlexAttention**은 1008을 사용하고 있기 때문이다. 다만 TFLOP 기준에서 봤을 때는 이미지의 해상도가 더 높기는 하지만, **FlexAttention**이 훨씬 효율적이긴 하다.

**Table 3**. Table 3의 경우 어떤 것을 비교한 것인지 잘 이해되지 않는다. LLaVA-1.5보다는 **당연히 좋아야** 하는 것이 아닌가 싶다.

![image](https://github.com/user-attachments/assets/52ec0763-1f5c-4b05-86b3-1ee0aa944651){: .align-center}

<br>

## 2.2. Ablation Study

![image](https://github.com/user-attachments/assets/1f421eda-fecf-4def-9bea-eb90816a78d6){: .align-center}

**Selection Strategy**. Random, Center Sampling보다 Attention Score를 이용한 방법이 훨씬 효과적이다. 이는 **LMM 내에서의 Attention Map이 실제 중요한 Token을 잘 반영**하는 것을 시사한다.

**Impact of Resolution**. 당연히 해상도가 높아지면 성능이 향상되고, 기존 해상도보다 높다고 성능이 향상되는 것은 아니다.

**Impact of Object Size**. Object Size가 작았을 때 **FlexAttention**이 효과적이다. 그러나 이 보고에는 약간의 Hallucination이 있다. LLaVA는 원래 Low resolution Token을 사용하고 있기 때문에, Object size가 작은 경우 성능이 떨어지는 것이 당연하다. 따라서 이것은 **FlexAttention**이 효과적이라기보다는 **LLaVA-HD**가 효과적일 수 있음을 의미한다. 정당한 비교를 위해서는 **LLaVA-HD**와 **FlexAttention**을 비교해야 한다.

<br>

# 💡 Summary

해당 논문의 내용을 간단히 요약하면 다음과 같다.

* Low-resolution Input에서의 Attention Score를 기반으로 High-resolution Token을 선택적으로 활용하는 FlexAttention을 제안함
* 해당 방법은 Training-free가 아니며, Evaluation 과정이 과장되어 있음

<br>

# 📃 Reference

* [[24' ECCV] FlexAttention for Efficient High-Resolution Vision-Language Models](https://arxiv.org/abs/2407.20228)
  
<br>