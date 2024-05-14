---
title:  "[24'] A Survey on Multimodal Large Language Models" 
excerpt: ""

categories:
  - Language
tags:
  - [Summary, MLLM]

toc: true
toc_sticky: true
 
date: 2024-05-14
last_modified_at: 2024-05-14
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/2dd1b4f9-c063-41f0-a0c9-5b3ba0a67af2){: .align-center}

<br>

# 1. Introduction

**MLLM(Multimodal Large Language Model)** 분야 공부의 첫 걸음으로 Survey 논문을 읽고 정리하였다. 정리한 내용을 바탕으로 근간이 되는 논문들을 선정하고 차근차근 읽어나갈 예정이다. 여기서는 키워드와 분류 위주로 정리하였다.

<br>

## 1.1. Background

최근 빠르게 발전하고 있는 **LLM**에는 **emergent ability**, 즉 창발적인 특성이 있다. **Instruction following, In-Context Learning (ICL), Chain of Thought (CoT)**와 같은 것들이 이에 해당한다. 이러한 특성은 **LLM**이 다양한 task를 수행할 수 있게 해주었다. 그러나 LLM은 **vision input은 이해할 수 없다**. 한편 **LVM(Large Vision Model)**은 vision input을 잘 이해하지만 **reasoning**에 제약이 있다. 따라서 **MLLM**은 **LLM**과 **LVM**의 장점을 결합하여 **vision input을 이해하고 reasoning을 수행**할 수 있는 모델을 만들어야 한다는 필요에 의해 탄생하였다.

MLLM 이전에도 multimodal model에 대한 여러 연구가 진행되었고, 크게 **(1) discriminative models, (2) generative models**로 나눌 수 있다.

* **Discriminative Models**
  * [21' ICML] **CLIP**: Learning transferable visual models from natural language supervision 
  * [21' NIPS] **ALBEF**: Vision and Language Representation Learning with Momentum Distillation
  * [20' ECCV] **Uniter**: Universal image-text representation learning
* **Generative Models**
  * [22' ICML] **OFA**: Unifying Architectures, Tasks, and Modalities Through a Simple Sequence-to-Sequence Learning Framework
  * [21' ICML] **VL-T5**: Unifying Vision-and-Language Tasks via Text Generation
  * [21'] **SimVLM**: Simple Visual Language Model Pretraining with Weak Supervision

**OFA**로 대표되는 generative model과 **MLLM**은 모두 **sequence-to-sequence** 형식으로 multimodal 데이터를 처리한다는 공통점을 가지고 있다. 그러나 MLLM은 **OFA**와 달리 **large-scale**이며 multimodal instruction tuning과 같은 새로운 **training paradigm**을 사용한다.

* **Multimodal Instruction Tuning**
  * [21'] **FLAN**: Finetuned Language Models Are Zero-Shot Learners
  * [23' NIPS] **LLaVA**: Visual instruction tuning

<br>

## 1.2. MLLM Timeline

초기 MLLM 연구는 **text prompt + image / video / audio로부터 text content를 생성**하는 컨셉의 연구가 주를 이루었다. 이후 연구는 이를 확장하면서 이루어졌다. **(1) Granularity support**, 즉 전체 image가 아닌 bounding box나 point 등도 인식할 수 있도록 하는 연구가 진행되었다. **(2) Input 및 output modalities 확장 연구**가 진행되었다. Image, video, audio, point cloud 등으로 확장되었다. **(3) 더 다양한 language support**를 지원하기 위한 연구가 진행되었다. **(4) 더 많은 분야**에 적용하기 위한 연구가 진행되었다. 예를 들면 medical domain으로 확장하거나, real world에서 사용하기 위한 연구가 진행되었다. 최근까지의 연구를 요약한 타임라인은 다음과 같다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/537c6eee-b676-4590-bfcf-27b7bd842c7a){: .align-center}

<br>

# 2. Architecture

MLLM은 크게 3가지의 module로 나눠볼 수 있다. **(1) Pre-trained modality encoder, (2) pre-trained LLM, (3) modality interface**이다. Modality interface는 (1)과 (2) 사이의 간극을 이어주는 역할을 한다. 필요에 따라 text가 아닌 output을 생성하기 위한 **(4) optional generator**가 추가될 수 있다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/5ede29f6-b4ad-4c65-afbb-91f833bacbeb){: .align-center}

<br>

## 2.1. Modality Encoder

**Modality Encoder**는 **input modality**를 **LLM**이 이해할 수 있는 형태로 변환하는 역할을 한다. 일반적인 VLM(Vision-Language Model)의 경우 **CLIP**과 같이 **image-text alignment**가 가능한 모델을 사용하며, 보통 **pre-trained encoder를 그대로 사용**한다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/7d752373-b21a-4cbf-aeb3-a03c995d018c){: .align-center}

Encoder를 선정할 때 고려할 수 있는 요소는 **(1) resolution (2) parameter size (3) pretraining corpus** 등이 있다. 여러 실험 결과 **(1)은 성능에 큰 영향을 미치지만 (2)와 (3)은 그렇지 않다**. 즉, input resolution이 높을수록 성능이 높아진다. 따라서 input resolution을 높이는 시도들이 진행되었고, 크게 **(1) direct scaling과 (2) patch-division method**로 나눌 수 있다.

* **Direct Scaling**: High-resolution input을 바로 encoder에 넣는 방식 (encoder 변경 필요)
  * [23'] **Qwen-VL**: A Versatile Vision-Language Model for Understanding, Localization, Text Reading, and Beyond
  * [23'] **Improved LLaVA**: Improved Baselines with Visual Instruction Tuning
* **Patch-Division Method**: High-resolution input을 patch로 나누어 encoder에 넣는 방식 (encoder 변경 불필요)
  * [23'] **Monkey**: Image Resolution and Text Label Are Important Things for Large Multi-modal Models
  * [23'] **SPHINX**: The Joint Mixing of Weights, Tasks, and Visual Embeddings for Multi-modal Large Language Models
* **Experiments**
  * [24'] **MM1**: Methods, Analysis & Insights from Multimodal LLM Pre-training

<br>

## 2.2. Pre-trained LLM

**LLM** 역시 **pre-trained**된 모델을 사용한다. 흔히 사용되는 public model은 다음과 같다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/eb60a1a3-d284-456f-9651-3509deedec45){: .align-center}

이 중 **Flan-T5** 모델은 비교적 early LLM 모델로, BLIP-2나 InstructBLIP 등에서 사용되었다. **LLaMA**와 **Vicuna** 모델은 최근 대표적인 LLM 모델로 주목받고 있다. 이 외에도 계속 SOTA LLM 모델이 나오고 있기에 앞으로 등장하는 모델들로 계속 대체될 가능성이 높다.

* **Flan-T5**
  * [22'] **Flan-T5**: Scaling Instruction-Finetuned Language Models
  * [23'] **BLIP-2**: Bootstrapping Language-Image Pre-training with Frozen Image Encoders and Large Language Models
  * [23'] **InstructBLIP**: Towards General-purpose Vision-Language Models with Instruction Tuning
* **LLaMA and Vicuna**
  * [23'] **LLaMA**: Open and Efficient Foundation Language Models
  * [23'] **Llama 2**: Open Foundation and Fine-Tuned Chat Models
  * [23'] **Vicuna**: An Open-Source Chatbot Impressing GPT-4 with 90% ChatGPT Quality
  
Modality encoder에서 input resolution이 높아짐에 따라 성능이 향상되었던 것과 같이 **LLM**에서도 **parameter size**가 성능에 큰 영향을 미친다. 또한 최근 **MoE(Mixture of Experts)**와 같은 모델이 주목받고 있다. 이러한 모델은 parameter 수를 늘리면서도 실제 computational cost는 늘리지 않기 때문에 효과적으로 성능을 향상시킬 수 있다. 이를 **sparse architecture**라고 부르기도 한다.

![image](https://www.datocms-assets.com/96965/1695407447-image1.png){: width="50%"}{: .align-center}

# 💡 Summary


<br>

# 📃 Reference

* <a href="https://arxiv.org/abs/2306.13549" target="_blank">A Survey on Multimodal Large Language Models (1 Apr 2024, v2)</a>

<br>