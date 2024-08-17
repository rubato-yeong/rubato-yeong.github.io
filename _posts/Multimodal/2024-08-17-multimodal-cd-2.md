---
title:  "[Summary] Multimodal Contrastive Decoding (2)"
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

1. **IBD: Alleviating Hallucinations in Large Vision-Language Models via Image-Biased Decoding** (February 2024): **Image Attention을 내부적으로 조작**하여 **Image-biased LMM**을 만들고, 이를 기존 LMM과 Contrastive Decoding을 수행하여 **Object Hallucination**을 개선하였다. 이때 Decoding에 Image가 필요한 경우와 필요하지 않은 경우를 통계적으로 분석하여 **Dynamic Adjustment**를 수행하였다.
2. **Contrastive Region Guidance: Improving Grounding in Vision-Language Models without Training** (March 2024, ECCV 2024): **Visual Prompt Fine-tuning 없이도** Visual Prompt를 잘 이해할 수 있도록 Visual Prompt가 있는 이미지를 사용한 LMM과 해당 부분을 **Masking한 LMM** 간의 Contrastive Decoding을 수행하여 **Region-based Benchmark**의 성능을 개선하였다. 이는 일종의 LMM의 **Language Prior Bias를 교정**하는 방법으로 해석할 수 있다.
3. **Pensieve: Retrospect-then-Compare Mitigates Visual Hallucination** (March 2024): Visual Input이 항상 정확한 정보만을 제공하는 것이 아니라 **Non-existence content를 동시에 제공**한다는 것을 밝혀냈다. 따라서 저자들은 **Semantic, Appearance가 유사한 이미지를 Retreival**하여 Contrastive Decoding을 수행하여 Visual Input이 잘못 인식한 Visual Hallucination을 개선하였다.
4. **ICD: Mitigating Hallucinations in Large Vision-Language Models with Instruction Contrastive Decoding** (March 2024, ACL Findings 2024): Role Prefix와 같은 Instruction을 주면 LMM이 **Language Prior 또는 Pretraining Data Bias**에 취약해져 Hallucination이 심해지는 현상을 발견했다. 따라서 기존 LLM과 추가적인 **Disturbance Instruction을 준 LLM** 간의 Contrastive Decoding을 수행하여 **Object/Attribute Hallucination**을 개선하였다.

<br>

# 1. 

<br>

# 💡 Summary



<br>

# 📃 Reference

* [[24'] IBD: Alleviating Hallucinations in Large Vision-Language Models via Image-Biased Decoding](https://arxiv.org/abs/2402.18476v1)
* [[24' ECCV] Contrastive Region Guidance: Improving Grounding in Vision-Language Models without Training](https://arxiv.org/abs/2403.02325)
* [[24'] Pensieve: Retrospect-then-Compare Mitigates Visual Hallucination](https://arxiv.org/abs/2403.14401)
* [[24' ACL Findings] ICD: Mitigating Hallucinations in Large Vision-Language Models with Instruction Contrastive Decoding](https://arxiv.org/abs/2403.18715)

<br>