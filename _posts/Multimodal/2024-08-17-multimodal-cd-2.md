---
title:  "[Summary] Multimodal Contrastive Decoding Variants (2)"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Summary, Hallucination, Contrastive Decoding]

toc: true
toc_sticky: true
 
date: 2024-08-17
last_modified_at: 2024-08-17
---

{: .align-center}

<br>

# 🔍 Abstract

1. **HIO: Alleviating Hallucinations in Large Vision-Language Models through Hallucination-Induced Optimization** (May 2024): Contrastive Decoding이 잘 작동하는 조건을 이론적으로 분석하고, **DPO를 이용해 Hallucination을 구체적으로 증폭**하는 HIO(Hallucination-Induced Optimization)을 제안하였다. 이를 통해 Hallucinated LMM을 만들고 Contrastive Decoding을 수행하여 **Object Hallucination**을 개선하였다.
2. **AvisC: Don't Miss the Forest for the Trees: Attentional Vision Calibration for Large Vision Language Models** (May 2024): LMM이 정답과는 무관한 부분의 Attention을 높게 주는 현상을 발견하고 이러한 토큰을 **blind token**이라 명명하였다. 그리고 기존 LMM과 **Blind Token만 볼 수 있도록 한 LMM** 간의 Contrastive Decoding을 수행하여 **Object Hallucination**을 개선하는 AVISC(Attentional Vision Calibration)을 제안하였다.
3. **RITUAL: Random Image Transformations as a Universal Anti-hallucination Lever in LVLMs** (May 2024): Image Transformation을 통해 Augmented된 Image를 사용한 LMM과 원본 Image를 사용한 LMM의 Probability를 **Ensemble**하여 **Object Hallucination**을 개선하였다.
4.  **CODE: Contrasting Self-generated Description to Combat Hallucination in Large Multi-modal Models** (June 2024): LMM이 **Image Description을 생성한 것으로 Image를 대체**하는 경우 Semantic Information이 충분하지 않아 **Hallucination이 발생**하는 것을 관찰하였다. 따라서 기존 LMM과 **Self-generated Description을 사용한 LMM** 간의 Contrastive Decoding을 수행하여 **Object/Complicated Reasoning Hallucination**을 개선하였다.
5.  **AGLA: Mitigating Object Hallucinations in Large Vision-Language Models with Assembly of Global and Local Attention** (June 2024): LMM의 Visual Attention을 분석하여 특정 토큰에 대한 Attention만 높다는 것을 발견하고, 이를 **Global Attention**이라 명명하였다. 이는 위 논문인 **AVISC와 동일한 관찰**이다. 저자들은 GradCAM을 사용하여 이러한 토큰을 Masking하고, 다른 토큰들 즉 **Local Attention**에 집중하도록 하였다. 결과적으로 AGLA(Assembly of Global and Local Attention)을 제안하여 **Object Hallucination**을 개선하였다.

<br>

# 1. 

<br>

# 💡 Summary



<br>

# 📃 Reference

* [[24'] HIO: Alleviating Hallucinations in Large Vision-Language Models through Hallucination-Induced Optimization](https://arxiv.org/abs/2405.15356v1)
* [[24'] AvisC: Don't Miss the Forest for the Trees: Attentional Vision Calibration for Large Vision Language Models](https://arxiv.org/abs/2405.17820)
* [[24'] RITUAL: Random Image Transformations as a Universal Anti-hallucination Lever in LVLMs](https://arxiv.org/abs/2405.17821)
* [[24'] CODE: Contrasting Self-generated Description to Combat Hallucination in Large Multi-modal Models](https://arxiv.org/abs/2406.01920v1)
* [[24'] AGLA: Mitigating Object Hallucinations in Large Vision-Language Models with Assembly of Global and Local Attention](https://arxiv.org/abs/2406.12718)

<br>