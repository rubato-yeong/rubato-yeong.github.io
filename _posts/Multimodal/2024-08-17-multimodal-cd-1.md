---
title:  "[Summary] Multimodal Contrastive Decoding Variants (1)"
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

![image](https://i.namu.wiki/i/zXj0eCxblbIdq8d1381qKvxrFhJZqQln7TtArIOF21IwA1F7cEy66g-7lzbw0z5WV1Zk23fjpnXh0UJ55UGr4A.webp){: .align-center}

<br>

# 🔍 Abstract

저번 글에서는 LMM에서 Image의 역할에 대해 고찰했던 **IBD(Image-biased Decoding)**에 대해 알아보았다. 이번 글에서는 이어서 3월에 발표된 세 개의 논문을 살펴보려 한다. 

각 논문의 내용을 요약하면 다음과 같다.

1. **Contrastive Region Guidance: Improving Grounding in Vision-Language Models without Training** (March 2024, ECCV 2024): **Visual Prompt Fine-tuning 없이도** Visual Prompt를 잘 이해할 수 있도록 Visual Prompt가 있는 이미지를 사용한 LMM과 해당 부분을 **Masking한 LMM** 간의 Contrastive Decoding을 수행하여 **Region-biased Benchmark**의 성능을 개선하였다. 이는 일종의 LMM의 **Language Prior Bias를 교정**하는 방법으로 해석할 수 있다.
2. **Pensieve: Retrospect-then-Compare Mitigates Visual Hallucination** (March 2024): Visual Input이 항상 정확한 정보만을 제공하는 것이 아니라 **Non-existence content를 동시에 제공**한다는 것을 밝혀냈다. 따라서 저자들은 **Semantic, Appearance가 유사한 이미지를 Retreival**하여 Contrastive Decoding을 수행하여 Visual Input이 잘못 인식한 Visual Hallucination을 개선하였다.
3. **ICD: Mitigating Hallucinations in Large Vision-Language Models with Instruction Contrastive Decoding** (March 2024, ACL Findings 2024): Role Prefix와 같은 Instruction을 주면 LMM이 **Language Prior 또는 Pretraining Data Bias**에 취약해져 Hallucination이 심해지는 현상을 발견했다. 따라서 기존 LLM과 추가적인 **Disturbance Instruction을 준 LLM** 간의 Contrastive Decoding을 수행하여 **Object/Attribute Hallucination**을 개선하였다.

<br>

# 1. 

<br>

# 💡 Summary



<br>

# 📃 Reference

* [[24' ECCV] Contrastive Region Guidance: Improving Grounding in Vision-Language Models without Training](https://arxiv.org/abs/2403.02325)
* [[24'] Pensieve: Retrospect-then-Compare Mitigates Visual Hallucination](https://arxiv.org/abs/2403.14401)
* [[24' ACL Findings] ICD: Mitigating Hallucinations in Large Vision-Language Models with Instruction Contrastive Decoding](https://arxiv.org/abs/2403.18715)

<br>