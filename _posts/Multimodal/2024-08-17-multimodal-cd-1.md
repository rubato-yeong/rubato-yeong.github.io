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

1. **Contrastive Region Guidance: Improving Grounding in Vision-Language Models without Training** (March 2024, ECCV 2024): **Visual Prompt Fine-tuning 없이도** Visual Prompt를 잘 이해할 수 있도록 Visual Prompt가 있는 이미지를 사용한 LMM과 해당 부분을 **Masking한 LMM** 간의 Contrastive Decoding을 수행하여 **Region-based Benchmark**의 성능을 개선하였다. 이는 일종의 LMM의 **Language Prior Bias를 교정**하는 방법으로 해석할 수 있다.
2. **Pensieve: Retrospect-then-Compare Mitigates Visual Hallucination** (March 2024): Visual Input이 항상 정확한 정보만을 제공하는 것이 아니라 **Non-existence content를 동시에 제공**한다는 것을 밝혀냈다. 따라서 저자들은 **Semantic, Appearance가 유사한 이미지를 Retrieval**하여 Contrastive Decoding을 수행하여 Visual Input이 잘못 인식한 Visual Hallucination을 개선하였다.
3. **ICD: Mitigating Hallucinations in Large Vision-Language Models with Instruction Contrastive Decoding** (March 2024, ACL Findings 2024): Role Prefix와 같은 Instruction을 주면 LMM이 **Language Prior 또는 Pretraining Data Bias**에 취약해져 Hallucination이 심해지는 현상을 발견했다. 따라서 기존 LLM과 추가적인 **Disturbance Instruction을 준 LLM** 간의 Contrastive Decoding을 수행하여 **Object/Attribute Hallucination**을 개선하였다.

<br>

# 1. Contrastive Region Guidance [24' ECCV]

![image](https://github.com/user-attachments/assets/222b69c1-e60c-46b7-9233-fce97c5455f4){: .align-center}

<br>

## 1.1. Method: Contrastive Region Guidance

![image](https://github.com/user-attachments/assets/761f2a8b-6507-40a5-868c-8dcdb5b0abe4){: .align-center}

최근 **Visual Prompt** 즉 Image에 **Bounding Box, Point, Mask, Arrow** 등으로 특정 Object를 표시하여 해당 위치에 LMM이 집중할 수 있도록 하는 방법들이 제시되고 있다. 그러나 이러한 방법들은 Visual Prompt에 집중시키기 위한 추가적인 Fine-tuning이 필요하다. 저자들은 이 대신 **Contrastive Decoding**을 통해 Visual Prompt를 이해하도록 하는 방법을 제시한다. 따라서, 저자들은 평가 또한 Visual Prompt가 있는 데이터셋 또는 Object Detection Module을 사용하여 주요 Object Bounding Box를 추출한 데이터셋을 사용하였다. 구체적인 Task는 아래 그림을 참고하자. Object Detector는 보통 *GroundingDINO*를 사용하였다.

![image](https://github.com/user-attachments/assets/3ee93ebe-3cfd-4e89-ae8a-7d9275bf9ba1){: .align-center}

**Contrastive Region Guidance**는 다음과 같은 식으로 이루어지며, 이는 기존 Contrastive Decoding과 크게 다르지 않다. 저자들은 LLM에서의 Contrastive Decoding 방법에서 얻은 hyperparameter 결과대로 $\alpha = 1.0$을 사용하였다.

![image](https://github.com/user-attachments/assets/30c47fdb-301d-4ecf-8edb-d3ae7af2f5d8){: .align-center}

<br>

## 1.2. Evaluation: Visual Prompt Following

![image](https://github.com/user-attachments/assets/6d2f1799-9411-40b4-88e6-ce743b7f4ef4){: .align-center}

저자들은 Visual Prompt Following을 측정하는 ViP-Bench에서의 결과를 보고하였다. 그 외에도 여러 Task에 대해 보고한 Table이 있으나 생략하였다.

<br>

## 1.3. Ablation: Region Masking

![image](https://github.com/user-attachments/assets/8da4d496-a2f2-4c71-b970-621a4b211931){: .align-center}

저자들은 CRG(Contrastive Region Guidance)에서 **Region Masking**을 수행한 방법과, 다른 방법들을 비교하였다. 이를 통해 알게 된 내용은 다음과 같다.

1. **Relevant Region만을 명확히 Blacking Out** 한 경우가 가장 성능이 좋다.
2. Visual Marker를 단순히 Overlay한 경우에는 성능이 떨어진다. 즉, **Contrastive Decoding이 더 효과적**이다.

<br>

# 2. Pensieve [24']

![image](https://github.com/user-attachments/assets/b6e1da43-b676-4ffd-8b2e-ecca5e78f221){: .align-center}

<br>

## 2.1. Key Idea: Visual Branch is Complicated

![image](https://github.com/user-attachments/assets/63cbf9d6-4d18-42b0-bd8b-18db9a0de0ad){: .align-center}

저자들은 VCD(Visual Contrastive Decoding)과 유사한 방법을 통해 **Visual Branch가 각 Token을 얼마나 지지하는지**를 조사하였다. 그리고 그 결과로 정확한 Token 뿐만 아니라 연관된 Hallucinated Concept까지 지지하는 것을 발견하였다. 이는 Visual Input이 항상 정확한 정보만을 제공하는 것이 아니라 **Non-existence content를 동시에 제공**한다는 것을 의미한다. 따라서 저자들은 **Semantic, Appearance가 유사한 이미지를 Retrieval**하여 Contrastive Decoding을 수행하여 Visual Input이 잘못 인식한 Visual Hallucination을 개선하였다.

분석 방법은 굉장히 직관적이다. 저자들은 last hidden state $\mathbf{h}_ t$와 Unembedding $E_ c (x_ i)$를 사용해 probability를 다음과 같이 표기하였다.

![image](https://github.com/user-attachments/assets/19fa4abb-491f-4300-82e7-7a73d39619a2){: .align-center}

그리고 Visual Encoder 이후 Noise $\Delta ve$를 추가하여 **Visual Hallucination**을 유도하였다.

![image](https://github.com/user-attachments/assets/0ec35466-daae-44b0-8ff0-69ce5e468a3d){: .align-center}

이때 저자들은 $\Delta h \cdot E_c ({x_ i})$를 **Visual Information이 특정 Semantic에 기여하는 정도**라고 해석하였다. 즉, 위 그림에서 Base Score는 $h \cdot E_c ({x_ i})$이고, Image Score는 $\Delta h \cdot E_c ({x_ i})$이다. 다시 한 번 위 그림을 보자.

![image](https://github.com/user-attachments/assets/63cbf9d6-4d18-42b0-bd8b-18db9a0de0ad){: .align-center}

저자들은 Image Score가 정확한 정답 `_arrow` 뿐만 아니라 non-existence content `_fe`, `_qu` 등에도 높은 contribution을 가지는 것을 확인하였다. 따라서, 저자들은 LMM이 완전히 정확한 Visual Information에 **Blind**한 것은 아니지만, 어느 정도 **정확하지 않은 Visual Information도 가지고 있다**는 것을 발견하였다.

대신, 저자들은 유사한 이미지를 몇 개 **Retrieval**하였고, 이 경우 non-existence content `_fe`, `_qu`는 여전히 contribution이 높지만 정확한 정답 `_arrow`에 대한 contribution은 낮다는 것을 확인하였다. 따라서, 저자들은 **Visual Branch의 Hallucination**을 Retrieval을 활용한 Contrastive Decoding을 통해 해결할 수 있을 것이라고 생각하였다.

<br>

## 2.2. Method: Pensieve

저자들은 Multimodal Hallucination이 정확히 두 개의 문제로 인해 발생한다고 해석하였다. 첫 번째는 아까 보았던 (1) **Visual Branch**의 문제, 그리고 (2) **Language Branch**의 문제이다. Language Branch의 문제는 이미 VCD(Visual Contrastive Decoding)을 통해 해결한 바 있기 때문에, 이에 더하여 Visual Branch의 문제를 해결하기 위해 **Retrieval**을 활용한 Contrastive Decoding을 제안하였다.

먼저 **실제 이미지**를 $v^ \tau$, **Retrieval 이미지**를 $\lbrace v^ {NN} \rbrace _ k$라고 하자. 그리고 Diffused Image, 즉 **Distorted Image**를 $v^ d$라고 하자. 이때 저자들은 다음과 같은 식을 제안하였다.

![image](https://github.com/user-attachments/assets/368c994a-e011-4268-932a-a6d13eefb9bf){: .align-center}

이때 마지막에서 두 번째 항은 Retrieval로 **Visual Branch Hallucination**을 유도하고, 마지막 항은 Noise로 **Language Branch Hallucination**을 유도한다. 그리고 Visual Branch의 Confidence가 높은 경우에는 Visual Branch Hallucination의 Contrast 정도를 줄이고, Language Branch Hallucination의 Contrast 정도를 높이는 방향으로 Adaptive Parameter를 조절하였다. 물론 APC(Adaptive Plausibility Constraint)도 사용하였다. 여기서는 APC에 대한 설명은 생략한다.

![image](https://github.com/user-attachments/assets/b299c278-d152-403c-9055-28c1e593cb6d){: .align-center}

<br>

## 2.3. Evaluation: POPE, MME, Whoops

![image](https://github.com/user-attachments/assets/6f409762-0f5b-4a39-9106-9b3fc9c0e8d9){: .align-center}
![image](https://github.com/user-attachments/assets/77a6aace-ad34-4740-9510-63511469abc6){: .align-center}

결과는 약간 Mixing되어 있어서 해당 방법론의 성능을 정확히 파악하기 어렵다는 점이 한계이다.

<br>

# 3. ICD [24' ACL Findings]

![image](https://github.com/user-attachments/assets/377bc2e7-26fd-49b0-8c44-bba08e168608){: .align-center}

<br>

## 3.1. Key Idea: Instruction Disturbance

저자들은 Instruction(Role Prefix)를 부여하면 LMM의 **Language Prior 또는 Pretraining Data Bias**에 취약해져 Hallucination이 심해지는 현상을 발견했다. Role Prefix로는 다음과 같이 Positive, Negative를 두 가지씩 사용하였다.

![image](https://github.com/user-attachments/assets/9caddb76-481b-41ff-80e4-01e8ef314b98){: .align-center}

아래 그림에서 기존 LVLM에 비해 Positive Prefix를 사용한 LVLM+와 Negative Prefix를 사용한 LVLM-에서 **Hallucination이 훨씬 많이 일어나는 것**을 확인할 수 있다. 놀라운 것은 Negative Prefix가 아닌 Positive Prefix를 사용한 경우에도 Hallucination이 많이 일어난다는 것이다. 따라서 저자들은 Instruction이 Visual Input에 집중하는 것을 방해하고, **Object/Attribute Hallucination**을 유발한다고 해석하였다.

![image](https://github.com/user-attachments/assets/25ff2e77-42d4-4e0b-897f-d230b88f9a7a){: .align-center}

<br>

## 3.2. Method: Instruction Contrastive Decoding

![image](https://github.com/user-attachments/assets/cad35bff-cb26-4c04-be92-3dd8f72e7900){: .align-center}

따라서 저자들은 기존 LLM과 **Instruction을 부여한 LLM** 간의 Contrastive Decoding을 수행하여 **Object/Attribute Hallucination**을 개선하였다. 이때 **Instruction을 부여한 LLM**은 **Disturbance Instruction**을 추가로 부여한 LLM을 의미한다. 식으로 살펴보면 다음과 같다.

![image](https://github.com/user-attachments/assets/562d5c4e-aed7-43fc-89dd-1c696d2fffb8){: .align-center}

<br>

## 3.3. Evaluation: POPE, MME

![image](https://github.com/user-attachments/assets/c1334a0c-6a35-47d1-9f22-737fce0fab4c){: .align-center}
![image](https://github.com/user-attachments/assets/0788a48d-57b3-4453-880a-a1a9e3719f08){: .align-center}

놀랍게도 이러한 방법은 **VCD**에 비해 상당한 성능 향상이 있었다. 두 방법의 차이를 살펴보면 다음과 같다.

1. **VCD**: **Visual Uncertainty**를 높여 Visual Hallucination을 유도한다.
2. **ICD**: Instruction을 부여하여 **Multimodal Alignment Uncertainty**를 높여 Visual Hallucination을 유도한다.

어떻게 보면 Visual Uncertainty를 부여하는 것보다 **Multimodal Alignment Uncertainty**를 부여하는 것이 더 효과적이라고 할 수 있다. 하지만 아직 직관적으로 VCD보다 ICD가 좋은 이유를 명확히 설명하기는 어렵다.

<br>

## 3.4. Ablation: VCD + ICD

![image](https://github.com/user-attachments/assets/1dfa686f-5c07-4d55-b44d-92b7ba596270){: .align-center}

저자들은 위 분석에서 VCD와 ICD가 Orthogonal하며 서로 보완적이라는 것을 발견하였다. 따라서 두 방법을 **동시에 사용**하였을 때 성능이 더욱 향상되는 것을 확인하였다.

<br>

# 💡 Summary

지금까지 **Contrastive Decoding**을 활용한 여러 LMM Decoding 방식에 대해 알아보았다. 위 세 가지 논문을 정리해보면 다음과 같다.

* [[24' ECCV] Contrastive Region Guidance: Improving Grounding in Vision-Language Models without Training](https://arxiv.org/abs/2403.02325)
  * Visual Prompt(Bounding Box 등)를 Fine-tuning 없이도 이해하도록 Masking 전후 LMM에 대해 Contrastive Decoding을 수행하여 성능을 개선함
* [[24'] Pensieve: Retrospect-then-Compare Mitigates Visual Hallucination](https://arxiv.org/abs/2403.14401)
  * LMM의 Visual Branch가 정확한 정보뿐만 아니라 Hallucinated Concept까지 지지하는 것을 발견하고, 이를 Retrieval을 활용한 Contrastive Decoding을 통해 개선함
  * Visual Branch의 Hallucination을 유도하는 Noise와 Language Branch의 Hallucination을 유도하는 Noise를 구분하여 Adaptive Parameter를 조절함
* [[24' ACL Findings] ICD: Mitigating Hallucinations in Large Vision-Language Models with Instruction Contrastive Decoding](https://arxiv.org/abs/2403.18715)
  * Instruction(Role Prefix)을 부여하면 LMM의 Language Prior 또는 Pretraining Data Bias에 취약해져 Hallucination이 심해지는 현상을 발견함
  * 기존 LLM과 추가적인 Disturbance Instruction을 준 LLM 간의 Contrastive Decoding을 수행하여 Object/Attribute Hallucination을 개선하였고, 이는 VCD보다 효과적이며 VCD와 보완적으로 사용할 수도 있음

<br>

# 📃 Reference

* [[24' ECCV] Contrastive Region Guidance: Improving Grounding in Vision-Language Models without Training](https://arxiv.org/abs/2403.02325)
* [[24'] Pensieve: Retrospect-then-Compare Mitigates Visual Hallucination](https://arxiv.org/abs/2403.14401)
* [[24' ACL Findings] ICD: Mitigating Hallucinations in Large Vision-Language Models with Instruction Contrastive Decoding](https://arxiv.org/abs/2403.18715)

<br>