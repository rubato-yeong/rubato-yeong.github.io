---
title:  "[24' CVPR] GROUNDHOG: Grounding Large Language Models to Holistic Segmentation" 
excerpt: ""

categories:
  - Multimodal
tags:
  - [Segmentation, Visual Grounding, Panoptic Segmentation]

toc: true
toc_sticky: true
 
date: 2024-07-01
last_modified_at: 2024-07-01
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/5a8e29a6-5254-4d76-aaca-2b18a0931ef0){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/9049e53f-1fc3-4637-aa54-e24a2d85c390){: .align-center}

CVPR 2024에서 발표된 논문이다. 기존의 language-to-object grounding은 일반적으로 **bounding box**를 output으로 받아 이루어졌다. [**Shikra**](https://rubato-yeong.github.io/multimodal/shikra/), **Ferret**과 같은 모델이 대표적이다. 그러나 이러한 방법은 pixel-level representation을 얻을 수 없어 fine-grained visual understanding에 적합하지 않다. 따라서 이 논문에서는 **segmentation level의 visual grounding**을 위한 **GROUNDHOG**를 제안한다. 이 논문의 contribution은 다음과 같다.

1. Model development: **GROUNDHOG**; masked feature extractor와 MLLM을 잘 결합하여 segmentation-level visual grounding을 수행한다. 이러한 방법은 기존 방식보다 **performance**가 높을 뿐만 아니라, **interpretability**도 높다.
2. Dataset: **M3G2**; segmentation-grounded dataset을 구축하여 학습에 사용하였다.

<br>

# 1. GROUNDHOG

## 1.1. Architecture

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/6975708b-33bb-4ad9-9ce7-5c47da941298){: .align-center}

전체 구조를 두 과정으로 나누어볼 수 있다. 일반적인 language grounding task가 2단계로 이루어진다는 것으로부터 착안한 것이다.

1. **Localization**: **Mask2Former**를 사용하여 segmentation mask **proposal**을 생성한다.
2. **Recognition**: 이렇게 얻은 mask를 **visual & language context**를 모두 고려하여 **retrieval**하여 segmentation-level grounding을 수행한다. Retrieval 시에는 `<GRD>` token과 visual entity token의 유사도를 계산하여 유사한 entity masks를 찾아 merge하여 최종 segmentation mask를 얻는다.

이처럼 (1) **entity mask proposal**과 (2) **language-guided grounding**을 decoupling하는 것은 여러 이점이 있다. 학습을 개별적으로, 다른 데이터를 사용해 진행할 수 있을 뿐 아니라 성능이 좋지 않은 경우 두 단계 중 어떤 단계가 문제인지를 쉽게 알 수 있다.

<br>

## 1.2. Input Modality Support

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/34e3a022-b8bf-4678-aa32-0f5722d9771f){: .align-center}

추가로 **input으로도 visual feature**를 받기 위해 위와 같이 SAM을 사용하여 mask를 추출하여 feature extractor를 거쳐 MLLM input으로 사용할 수 있다. 이를 **spatial prompt**라고 한다.

<br>

# 2. M3G2 Dataset

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/6e3923a5-e72f-42a6-af18-6936ae5f87f9){: .align-center}

이 논문에서는 **M3G2** dataset을 구축하여 학습에 사용하였다. 이 dataset은 **segmentation-grounded** dataset으로, 다양한 task를 모아둔 것이다. 각각을 정리하면 다음과 같다.

1. **Grounded Image Captioning (GIC)**: 이미지를 보고 이미지 전체의 narrative를 segmentation mask와 함께 설명하는 Task
2. **Referring Expression Segmentation (RES)**: 이미지 내에서 referring expression에 해당하는 segmentation mask를 찾는 Task
3. **Grounded Visual Question Answering (GVQA)**: 이미지 내에서 질문에 대한 답의 근거를 segmentation mask로 제시하는 Task
4. **Referential Dialogue (RD)**: User-provied spatial prompt에 대해 segmentation mask와 함께 대화를 진행하는 Task

각각에 대해 아래와 같은 open-source dataset을 모아 정리하여 **M3G2(Multi-Modal Multi-Grained Grounding)** dataset을 구축하였다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/619a16e6-0f5d-45a0-9cb9-7f2f0dc2b8ae){: .align-center}

<br>

# 3. Experiments

## 3.1. Main Results

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/596de07a-abcc-4988-a363-374c7fc066db){: .align-center}
![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/ef5d3222-7179-41cd-a33b-f51cbf581c4f){: .align-center}

기존 모델에 비해 꽤나 차이를 두고 성능이 높아진 것을 볼 수 있는데, 기존 모델과의 차이점은 **(1) Mask2Former 등의 Mask Proposal Network를 사용한 점, (2) 대형 데이터셋인 M3G2를 사용한 점**이 있다. 이러한 점이 성능 향상에 기여한 것으로 보인다. Mask2Former 형태의 모델들이 Natural Image에서 Mask Generation에 좋은 성능을 보여주는 만큼, 해당 모델의 활용의 기여도가 높았을 것으로 추정된다. 사실 Prompt-based Segmentation Mask Generation보다 Mask2Former Mask Generation이 당연히 더 좋은 성능을 보여주는 것이 사실이다.

<br>

## 3.2. Trustworthiness and Transparency

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/db3675a4-ad42-4bd3-9ce0-54d1942c0035){: .align-center}

기존 Bounding Box Paradigm의 Visual Grounding Model보다 **Fine-grained Visual Understanding**을 하기에 **Hallucination Benchmark**에서 더 성능이 좋다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/8ef2335e-9270-4938-a692-63b19d6c7a43){: .align-center}

또한, 위에서 언급했듯 (1) entity mask proposal과 (2) language-guided grounding을 decoupling하였기에 **어떤 단계에서 문제가 발생했는지 쉽게 파악**할 수 있다. 위 그림에서는 mask proposal은 잘 되었지만 retrieval이 잘 되지 않았다는 것을 알 수 있다. 따라서 틀린 정답을 도출한 경우에도 어떠한 문제인지 쉽게 파악할 수 있어 **explainability**가 높은 모델이라고 할 수 있다.

<br>

## 3.3. Ablation Study

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/3eb9758c-8eb2-4027-85b7-3714dee71fa8){: .align-center}

Ablation Study의 결과는 당연한 것들인데, 그 중에서는 특히 entity feature extraction 시 **CLIP과 DINOv2를 모두 사용**한 것이 눈에 띈다. 이러한 결과는 [Eyes Wide Shut?](https://rubato-yeong.github.io/multimodal/eyes-wide-shut/)에서 제안된 것과도 일치한다.

<br>

# 💡 Summary

이 논문은 기존의 language-to-object grounding을 segmentation-level로 확장한 **GROUNDHOG**를 제안하였고, 학습을 위해 **M3G2** dataset을 구축하였다. GROUNDHOG는 mask proposal과 language-guided grounding을 decoupling한 **two-stage model**로, Mask2Former와 같은 **mask proposal network의 강력함**을 사용한 것이 성능 향상의 주 원인이다.이러한 two-stage strategy를 사용해 **성능**이 높을 뿐만 아니라 **hallucination**이 적고, **explainability**가 높다는 장점이 있다.

<br>

# 📃 Reference

* [[24' CVPR] GROUNDHOG: Grounding Large Language Models to Holistic Segmentation](https://arxiv.org/abs/2402.16846)

<br>