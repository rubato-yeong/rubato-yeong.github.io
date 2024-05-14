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

### Multimodal Large Language Models

최근 빠르게 발전하고 있는 **LLM**에는 **emergent ability**, 즉 창발적인 특성이 있다. **Instruction following, In-Context Learning (ICL), Chain of Thought (CoT)**와 같은 것들이 이에 해당한다. 이러한 특성은 **LLM**이 다양한 task를 수행할 수 있게 해주었다. 그러나 LLM은 **vision input은 이해할 수 없다**. 한편 **LVM(Large Vision Model)**은 vision input을 잘 이해하지만 **reasoning**에 제약이 있다. 따라서 **MLLM**은 **LLM**과 **LVM**의 장점을 결합하여 **vision input을 이해하고 reasoning을 수행**할 수 있는 모델을 만들어야 한다는 필요에 의해 탄생하였다.

### Prior Multimodal Models

MLLM 이전에도 multimodal model에 대한 여러 연구가 진행되었고, 크게 **(1) discriminative models, (2) generative models**로 나눌 수 있다.

* **Discriminative Models**
  * [21' ICML] **CLIP**: Learning transferable visual models from natural language supervision 
  * [21' NIPS] **ALBEF**: Vision and Language Representation Learning with Momentum Distillation
  * [20' ECCV] **Uniter**: Universal image-text representation learning
* **Generative Models**
  * [22' ICML] **OFA**: Unifying Architectures, Tasks, and Modalities Through a Simple Sequence-to-Sequence Learning Framework
  * [21' ICML] **VL-T5**: Unifying Vision-and-Language Tasks via Text Generation
  * [21'] **SimVLM**: Simple Visual Language Model Pretraining with Weak Supervision

**OFA**로 대표되는 generative model과 **MLLM**은 모두 **sequence-to-sequence** 형식으로 multimodal 데이터를 처리한다는 공통점을 가지고 있다. 그러나 MLLM은 **OFA**와 달리 **large-scale**이며 새로운 **training paradigm**을 사용한다.

<br>

## 1.2. MLLM Timeline

초기 MLLM 연구는 **text prompt + image / video / audio로부터 text content를 생성**하는 컨셉의 연구가 주를 이루었다. 이후 연구는 이를 확장하면서 이루어졌다. **(1) Granularity support**, 즉 전체 image가 아닌 bounding box나 point 등도 인식할 수 있도록 하는 연구가 진행되었다. **(2) Input 및 output modalities 확장 연구**가 진행되었다. Image, video, audio, point cloud 등으로 확장되었다. **(3) 더 다양한 language support**를 지원하기 위한 연구가 진행되었다. **(4) 더 많은 분야**에 적용하기 위한 연구가 진행되었다. 예를 들면 medical domain으로 확장하거나, real world에서 사용하기 위한 연구가 진행되었다. 최근까지의 연구를 요약한 타임라인은 다음과 같다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/537c6eee-b676-4590-bfcf-27b7bd842c7a){: .align-center}

<br>

# 2. Architecture

MLLM은 크게 3가지의 module로 나눠볼 수 있다. **(1) Pre-trained modality encoder, (2) pre-trained LLM, (3) modality interface**이다. 추가로 text가 아닌 output을 생성하기 위한 **(4) optional generator**가 추가될 수 있다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/5ede29f6-b4ad-4c65-afbb-91f833bacbeb){: .align-center}



# 💡 Summary


<br>

# 📃 Reference

* <a href="https://arxiv.org/abs/2306.13549" target="_blank">A Survey on Multimodal Large Language Models (1 Apr 2024, v2)</a>

<br>