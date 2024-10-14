---
title:  "[24' ACL] Cross-Modal Projection in Multimodal LLMs Doesn’t Really Project Visual Attributes to Textual Space"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Alignment]

toc: true
toc_sticky: true
 
date: 2024-10-14
last_modified_at: 2024-10-14
---

![image](https://github.com/user-attachments/assets/3e94f696-153c-4543-bfee-77920b1bfae4){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/user-attachments/assets/4f0b9b73-5a8e-4511-960c-86efbacd1962){: .align-center}

2024 ACL Short로 발표된 논문이다. 해당 논문은 Multimodal LLMs에서 Cross-Modal Projection이 실제로 Visual Attribute를 Textual Space로 Projection하지 않고, 대신 LLM과 Visual Feature 사이의 Alignment을 수행한다고 주장한다.

* **Problem**: Multimodal LLMs에서 Cross-Modal Projection이 어떠한 역할을 하는지 구체적으로 이해하지 못하고 있다.
* **Method**: 저자들은 Domain-specific Fine-tuning 이후 **Projector**를 통과한 Feature에 Probing을 하여 Feature Representation을 분석한다. 또한, **MLLM** Performance를 확인하여 Classification Performance를 확인한다.
* **Result**: Projection Feature Representation은 오히려 학습 전보다 더 저하되었다. 따라서, **Domain-specific attribute는 LLM에서 처리**되며, **Projector는 Feature를 LLM에 맞게 Align하는 역할**을 한다고 결론지었다.

<br>

# 1. Role of Projection

![image](https://github.com/user-attachments/assets/5d1c1eef-5c2f-444f-8bfc-f7085d19f4c4){: .align-center}

저자들은 Domain-specific image attribute를 projection layer가 학습하는지에 대해 궁금증을 가졌다. 따라서, 저자들은 다음과 같이 두 가지 척도를 측정하였다.

1. **Projector**: Feature Representation, 혹은 Expressive Power을 측정하기 위해 Projector에서 나온 Visual Feature Probing을 통해 Classification 성능을 확인한다.
2. **LLM**: MLLM에서 Classification 성능을 확인한다.

결과는 다음과 같다.

![image](https://github.com/user-attachments/assets/f1cbde33-921a-4f66-bde3-0d4bcc2b6fb3){: .align-center}

**FT-Proj**는 Projector만 Fine-tuning한 경우이며, **FT-E2E**는 Projector 및 LLM을 모두 Fine-tuning한 경우이다. 놀랍게도 두 경우 모두에서 Projection Feature Representation은 학습 전보다 더 저하되었다. 이를 통해 다음 결론을 얻을 수 있다.

1. **Projector**는 LLM Parameter가 Domain-specific Attribute를 더 잘 활용할 수 있도록 LLM에 맞게 Feature를 Align하는 역할을 한다.
2. **LLM**은 이렇게 Align된 Feature를 통해 Domain-specific Attribute를 처리한다. 즉, Visual Attribute를 처리하는 능력은 LLM에 달려 있다.


<br>

# 💡 Summary

해당 논문의 내용을 간단히 요약하면 다음과 같다.

* MLLM의 Projector는 LLM Parameter가 Domain-specific Attribute를 더 잘 활용할 수 있도록 Feature를 Align하는 역할을 하며, 실제 Visual Attribute를 처리하는 능력은 LLM에 달려 있다고 주장

<br>

# 📃 Reference

* [[24' ACL] Cross-Modal Projection in Multimodal LLMs Doesn’t Really Project Visual Attributes to Textual Space](https://aclanthology.org/2024.acl-short.60/)
  
<br>