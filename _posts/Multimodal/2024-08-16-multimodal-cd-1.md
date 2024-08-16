---
title:  "[Summary] Multimodal Contrastive Decoding (1)"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Summary, Hallucination, Contrastive Decoding]

toc: true
toc_sticky: true
 
date: 2024-08-16
last_modified_at: 2024-08-17
---

{: .align-center}

<br>

# 🔍 Abstract

1. **VCD: Mitigating Object Hallucinations in Large Vision-Language Models through Visual Contrastive Decoding** (November 2023, CVPR 2024): Forward Diffusion Process를 통해 **Visual Uncertainty**를 부여한 모델에서는 **Language Prior와 Statistical Bias가 증폭**되어 Hallucination이 발생하는 것을 확인하였다. 따라서 저자들은 기존 LMM과 **Distorted Image를 사용한 LMM** 간의 Contrastive Decoding을 수행하여 **Object Hallucination**을 개선하였으며, 일반적인 Multimodal Benchmark 성능도 향상시켰다.
2. **M3ID: Multi-Modal Hallucination Control by Visual Information Grounding** (March 2024, CVPR 2024): 저자들은 Output이 Visual Input에 얼마나 의존하는지 측정하는 척도인 **PDM(Visual Prompt Dependency Measure)**를 만들고, 생성되는 문장이 길어짐에 따라 **PDM은 감소하고 Hallucination은 증가**하는 것을 확인했다. 저자들은 이것을 **conditioning dilution** 또는 **fading memory effect**라고 명명하였다. 따라서 저자들은 **Visual Input이 있는 경우와 없는 경우**에 대한 Contrastive Decoding을 수행하여 문장이 길어져도 **PDM을 유지**하며 **Object Hallucination**을 개선하였다.
3. **IBD: Alleviating Hallucinations in Large Vision-Language Models via Image-Biased Decoding** (February 2024): **Image Attention을 내부적으로 조작**하여 **Image-biased LMM**을 만들고, 이를 기존 LMM과 Contrastive Decoding을 수행하여 **Object Hallucination**을 개선하였다. 이때 Decoding에 Image가 필요한 경우와 필요하지 않은 경우를 통계적으로 분석하여 **Dynamic Adjustment**를 수행하였다.
4. **Contrastive Region Guidance: Improving Grounding in Vision-Language Models without Training** (March 2024, ECCV 2024): **Visual Prompt Fine-tuning 없이도** Visual Prompt를 잘 이해할 수 있도록 Visual Prompt가 있는 이미지를 사용한 LMM과 해당 부분을 **Masking한 LMM** 간의 Contrastive Decoding을 수행하여 **Region-based Benchmark**의 성능을 개선하였다. 이는 일종의 LMM의 **Language Prior Bias를 교정**하는 방법으로 해석할 수 있다.
5. **Pensieve: Retrospect-then-Compare Mitigates Visual Hallucination** (March 2024): Visual Input이 항상 정확한 정보만을 제공하는 것이 아니라 **Non-existence content를 동시에 제공**한다는 것을 밝혀냈다. 따라서 저자들은 **Semantic, Appearance가 유사한 이미지를 Retreival**하여 Contrastive Decoding을 수행하여 Visual Input이 잘못 인식한 Visual Hallucination을 개선하였다.
6. **ICD: Mitigating Hallucinations in Large Vision-Language Models with Instruction Contrastive Decoding** (March 2024, ACL Findings 2024): Role Prefix와 같은 Instruction을 주면 LMM이 **Language Prior 또는 Pretraining Data Bias**에 취약해져 Hallucination이 심해지는 현상을 발견했다. 따라서 기존 LLM과 추가적인 **Disturbance Instruction을 준 LLM** 간의 Contrastive Decoding을 수행하여 **Object/Attribute Hallucination**을 개선하였다.
7. **HIO: Alleviating Hallucinations in Large Vision-Language Models through Hallucination-Induced Optimization** (May 2024): Contrastive Decoding이 잘 작동하는 조건을 이론적으로 분석하고, **DPO를 이용해 Hallucination을 구체적으로 증폭**하는 HIO(Hallucination-Induced Optimization)을 제안하였다. 이를 통해 Hallucinated LMM을 만들고 Contrastive Decoding을 수행하여 **Object Hallucination**을 개선하였다.
8. **AvisC: Don't Miss the Forest for the Trees: Attentional Vision Calibration for Large Vision Language Models** (May 2024): LMM이 정답과는 무관한 부분의 Attention을 높게 주는 현상을 발견하고 이러한 토큰을 **blind token**이라 명명하였다. 그리고 기존 LMM과 **Blind Token만 볼 수 있도록 한 LMM** 간의 Contrastive Decoding을 수행하여 **Object Hallucination**을 개선하는 AVISC(Attentional Vision Calibration)을 제안하였다.
9. **RITUAL: Random Image Transformations as a Universal Anti-hallucination Lever in LVLMs** (May 2024): Image Transformation을 통해 Augmented된 Image를 사용한 LMM과 원본 Image를 사용한 LMM의 Probability를 **Ensemble**하여 **Object Hallucination**을 개선하였다.
10. **CODE: Contrasting Self-generated Description to Combat Hallucination in Large Multi-modal Models** (June 2024): LMM이 **Image Description을 생성한 것으로 Image를 대체**하는 경우 Semantic Information이 충분하지 않아 **Hallucination이 발생**하는 것을 관찰하였다. 따라서 기존 LMM과 **Self-generated Description을 사용한 LMM** 간의 Contrastive Decoding을 수행하여 **Object/Complicated Reasoning Hallucination**을 개선하였다.
11. **AGLA: Mitigating Object Hallucinations in Large Vision-Language Models with Assembly of Global and Local Attention** (June 2024): LMM의 Visual Attention을 분석하여 특정 토큰에 대한 Attention만 높다는 것을 발견하고, 이를 **Global Attention**이라 명명하였다. 이는 위 논문인 **AVISC와 동일한 관찰**이다. 저자들은 GradCAM을 사용하여 이러한 토큰을 Masking하고, 다른 토큰들 즉 **Local Attention**에 집중하도록 하였다. 결과적으로 AGLA(Assembly of Global and Local Attention)을 제안하여 **Object Hallucination**을 개선하였다.

<br>

# 1. 

<br>

# 💡 Summary



<br>

# 📃 Reference
* [[24' CVPR] VCD: Mitigating Object Hallucinations in Large Vision-Language Models through Visual Contrastive Decoding](https://arxiv.org/abs/2311.16922)
* [[24' CVPR] M3ID: Multi-Modal Hallucination Control by Visual Information Grounding](https://arxiv.org/abs/2403.14003)
* [[24'] IBD: Alleviating Hallucinations in Large Vision-Language Models via Image-Biased Decoding](https://arxiv.org/abs/2402.18476v1)
* [[24' ECCV] Contrastive Region Guidance: Improving Grounding in Vision-Language Models without Training](https://arxiv.org/abs/2403.02325)
* [[24'] Pensieve: Retrospect-then-Compare Mitigates Visual Hallucination](https://arxiv.org/abs/2403.14401)
* [[24' ACL Findings] ICD: Mitigating Hallucinations in Large Vision-Language Models with Instruction Contrastive Decoding](https://arxiv.org/abs/2403.18715)
* [[24'] HIO: Alleviating Hallucinations in Large Vision-Language Models through Hallucination-Induced Optimization](https://arxiv.org/abs/2405.15356v1)
* [[24'] AvisC: Don't Miss the Forest for the Trees: Attentional Vision Calibration for Large Vision Language Models](https://arxiv.org/abs/2405.17820)
* [[24'] RITUAL: Random Image Transformations as a Universal Anti-hallucination Lever in LVLMs](https://arxiv.org/abs/2405.17821)
* [[24'] CODE: Contrasting Self-generated Description to Combat Hallucination in Large Multi-modal Models](https://arxiv.org/abs/2406.01920v1)
* [[24'] AGLA: Mitigating Object Hallucinations in Large Vision-Language Models with Assembly of Global and Local Attention](https://arxiv.org/abs/2406.12718)

<br>