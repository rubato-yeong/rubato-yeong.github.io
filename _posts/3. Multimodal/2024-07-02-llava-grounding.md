---
title:  "[23'] LLaVA-Grounding: Grounded Visual Chat with Large Multimodal Models"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Visual Grounding, Segmentation]

toc: true
toc_sticky: true
 
date: 2024-07-02
last_modified_at: 2024-07-02
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/af0f5868-616a-4c3b-ab43-344c7d41a8bf){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/658e0f4f-2d50-439f-a1e4-c2f2f28a8c5e){: .align-center}

LLaVA-Grounding은 **grounding**과 **segmentation** 데이터에 **overfitting**되는 **LLM-Aided Segmentation** 모델을 개선한다. **LLM의 conversation** 능력을 보존하면서 **grounding**을 잘 하는 모델을 만들기 위해 LLaVA에서 사용했던 **visual instruction tuning** 방법론을 그대로 사용한다. 즉, 지금까지의 LLM-Aided Segmentation Model은 투박한 instructization을 사용하여 conversation 능력이 떨어졌지만, 이번에는 GPT-4를 사용한 **grounded visual chat data**를 만들어서 conversation 능력을 낮추지 않고 segmentation을 수행하도록 한다. 저자들은 이 데이터셋을 **GVC(Grounded Visual Chat)**이라고 부르고, 이를 포함해 다양한 데이터를 여러 stage에 나누어 학습시킨다. 또한, 저자들은 external model을 추가하여 **click, box, mark 등의 input과 box, mask, mark 등의 output을 모두 지원**하도록 하였다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/0d0b1c4b-c0fe-480a-8d76-84ccd56132a5){: .align-center}

<br>

# 1. GVC(Grounded Visual Chat) Dataset

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/33a87e99-ec08-406e-93ff-60fdd85868f9){: .align-center}

Segmentation dataset으로 **LLaVA**와 같은 **visual instruction tuning**을 진행했다. GPT-4를 사용해 data annotation을 진행하였으며, 대략 15K개의 **grounded visual chat data**를 만들었다. 자세한 과정은 LLaVA와 동일하므로 [해당 논문](https://rubato-yeong.github.io/multimodal/llava/)을 참고하자.

<br>

# 2. LLaVA-Grounding (LLaVA-G)

## 2.1. Input/Output Format

Input/Output의 형태의 예시를 들어보면 다음과 같다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/86b2b96c-c089-4291-8d6a-d24de6fbfd34){: .align-center}

`<obj>`로 Input을 표시하고, `<gs>` 및 `<ge>`로 segmentation mask에 해당하는 object를 표시한다. Segmentation embedding은 `<seg>`로 표현한다.

<br>

## 2.2. Model Architecture

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/2eea307e-cd4b-4a1c-a48a-bfe3597e46cd){: .align-center}

LLaVA-G는 **LLM-Aided Segmentation** 모델을 개선한 모델이다. 위 그림에서 핵심은 **Visual Interaction Module**과 **Visual Grounding Module**일 것이다. 먼저 **Visual Interaction Module**에서는 **Semantic-SAM**을 prompt encoder로 사용하여 prompt token을 생성한다. 한편 **Visual Grounding Module**에서는 **OpenSeeD**를 사용하여 bounding box와 segmentation mask를 생성하도록 한다.

<br>

## 2.3. Training

학습 과정은 기존 LLaVA에서 한 단계가 추가되어 총 세 단계로 설명하고 있다. 그러나 실제로는 **네 단계**로 구분하는 것이 옳은 것 같다. 이러한 복잡한 단계를 거쳐야 한다는 점이 **LLaVA-G**의 단점이다.

1. **Visual Encoder Alignment Pretraining**: Caption-image 데이터를 사용하여 Projection Layer $\mathbf{W}$를 학습한다. Visual Interaction Module, Visual Grounding Module은 사용하지 않는다.
2. **Grounding Model Alignment Pretraining**: Segmentation & Detection 데이터를 사용하여 Visual Grounding Module을 학습한다. 즉 $\mathbf{W}, \mathbf{W}_ g, \phi_ g$를 학습한다. 이때 Visual Interaction Module은 사용하지 않는다.
3. **Grounded Visual Chat Instruction Tuning**: Grounding 데이터를 사용하여 Grounded Visual Chat을 학습한다. 즉 $\mathbf{W}, \mathbf{W}_ g, \phi, \phi_g$를 학습한다. 이때까지도 Visual Interaction Module은 사용하지 않는다.
4. **Extension to Visual Prompt**: 이제 Input에 Visual Prompt를 추가하여 학습한다. 즉 $\mathbf{W}_ p, \phi_ p$를 학습한다.

<br>

# 3. Experiments

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/20fb01a4-a3a3-4f39-b7ba-de26a9069275){: .align-center}

결과는 **Grounding(Segmentation)도, Conversation도 잘하는 모델**이라고 한다. 다만 conversation의 경우 저자들이 직접 만든 **Grounding-Bench**라는 평가 기준을 사용하였기에 부정확할 수 있다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/6bc6fe96-df42-48e1-b57e-901a7887d463){: .align-center}

<br>

# 💡 Summary

**LLaVA-Grounding (LLaVA-G)**는 **Grounded Visual Instruction Data**를 사용하여 **LLM-Aided Segmentation** 모델을 개선한 모델이다. 이를 통해 **LLM의 conversation** 능력을 보존하면서 **grounding**을 잘 하는 모델을 만들 수 있었다. 다만 학습에 **많은 데이터**가 소모되고, **복잡한 학습 과정**이 필요하다는 점에서 일반화되기는 어려워 보인다.

<br>

# 📃 Reference

* [[23'] LLaVA-Grounding: Grounded Visual Chat with Large Multimodal Models](https://arxiv.org/abs/2312.02949)

<br>