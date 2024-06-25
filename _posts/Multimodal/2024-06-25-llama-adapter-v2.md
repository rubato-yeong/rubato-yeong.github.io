---
title:  "[23'] LLaMA-Adapter V2: Parameter-Efficient Visual Instruction Model" 
excerpt: ""

categories:
  - Multimodal
tags:
  - [Prefix Tuning, Joint Training]

toc: true
toc_sticky: true
 
date: 2024-06-25
last_modified_at: 2024-06-25
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/98bfbabd-6167-4f0d-977e-40dfc194db88){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/e4774beb-b37d-4977-bec6-daab4838c686){: .align-center}

이 논문에서는 **LLaMA-Adapter**를 개선한 **LLaMA-Adapter V2**를 제안한다. 이 모델은 **visual instruction data 없이 language instruction data와 image-text data를 각각 disjoint parameter에 학습시켜 visual instruction을 효율적으로 학습**하는 joint training paradigm을 제시한다. 이때 disjoint parameter learning을 위해 visual feature를 **early fusion**하는 간단한 방법론을 제안한다. 또한, **expert system을 통합**하여 image understanding ability를 더 높일 수 있었다. LLaMA-Adapter 논문 리뷰는 [여기](https://rubato-yeong.github.io/language/llama-adapter/) 참고.

<br>

# 1. Architecture

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/b36b5ed6-91c7-457e-9a26-ce8bd3bd3c6c){: .align-center}

**LLaMA-Adapter V2**의 목적은 (1) **Visual Instruction Data를 따로 사용하지 않고서도 잘 작동**하고, (2) **Parameter-efficient fine-tuning**이 가능한 모델을 구축하는 것이다. 추가로, (3) **Expert Integration**을 통해 zero-shot reasoning ability를 더 높이고자 하였다. 각각에 대해 자세히 살펴보자.

<br>

## 1.1. Bias Tuning of Linear Layers

저자들은 Prefix Tuning이 external parameter tuning만 가능하다는 한계가 있어, 추가로 internal parameter를 더 학습하고자 하였다. 이를 위해 저자들은 (1) **Normalization Layer** (2) **Linear Layer의 Scale, Bias**를 학습하였다. 이때 Scale과 Bias는 각각 아래 식에서 $s, b$와 같다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/7522acf2-2f93-43bf-bd15-5d2bc59c46d0){: .align-center}

<br>

## 1.2. Joint Training with Disjoint Parameters

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/6a6c2dd4-77dc-4c3d-abac-58240b768576){: .align-center}

저자들은 **Visual Instruction Data 없이도 잘 작동**하는 모델을 만들기 위해 **Disjoint Parameter Learning**을 제안하였다. 즉, **Language Instruction Data와 Image-Text Data를 각각 disjoint parameter에 학습**시켜 서로의 불필요한 interference를 해결한다. 특히 이는 **Language Instruction Data와 Image-Text Data의 수가 크게 차이나는 상황** (52K vs 567K)에서 더 중요하게 작용한다. 이러한 방법을 이용해, MiniGPT-4나 LLaVA와 같이 visual instruction data를 따로 만들지 않고서도 visual instruction을 잘 따르는 모델을 훈련시킬 수 있었다.

<br>

## 1.3. Early Fusion of Visual Knowledge

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/a4687b7a-b704-46dc-b160-cc1c47d21f7b){: .align-center}

기존 LLaMA-Adapter에서는 단순히 visual feature를 prefix에 추가해주는 식으로 visual feature를 사용하였다. 하지만 이는 **visual feature의 중요성을 충분히 반영하지 못한다**는 단점이 있었다. 따라서 저자들은 **visual feature를 early fusion**하여 prefix와의 interference를 방지하였다.

<br>

## 1.4. Integration with Experts

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/99caf8f2-75ce-40ce-86ff-f93a5325df28){: .align-center}

그러나 적은 수의 데이터로 훈련하는 것에는 한계가 있고, 따라서 **image understanding ability는 다른 모델에 비해 떨어진다**. 따라서 captioning, detection, OCR expert model을 사용하여 prompt에 추가하여 zero-shot reasoning ability를 높였다.

<br>

# 2. Experiments

## 2.1. Language Instruction

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/a6137fca-5fbb-41e9-9b8d-0a8a58499afd){: .align-center}

**Parameter-efficient**한 것에 비해 기존 모델들과 비슷한 성능을 낸다는 것을 강조하고 있다.

<br>

## 2.2. Visual Instruction

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/f786c578-65e0-46e8-be54-a1199e8e773d){: .align-center}

**Visual Instruction Data 없이도** BLIP과 비슷한 성능을 보인다는 것을 강조하고 있다.

<br>

# 💡 Summary

**Disjoint parameter**를 이용한 **joint training**이 인상적인 논문이다. Parameter-efficient한 것은 장점이지만 실제 성능이 그렇게 좋지는 않아서 아쉬운 부분이 있다.

<br>

# 📃 Reference

* [[[23'] LLaMA-Adapter V2: Parameter-Efficient Visual Instruction Model](https://arxiv.org/pdf/2304.15010)

<br>