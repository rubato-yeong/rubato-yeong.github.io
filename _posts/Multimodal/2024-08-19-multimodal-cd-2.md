---
title:  "[Summary] Multimodal Contrastive Decoding Variants (2)"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Summary, Hallucination, Contrastive Decoding]

toc: true
toc_sticky: true
 
date: 2024-08-19
last_modified_at: 2024-08-19
---

![image](https://i.namu.wiki/i/zXj0eCxblbIdq8d1381qKvxrFhJZqQln7TtArIOF21IwA1F7cEy66g-7lzbw0z5WV1Zk23fjpnXh0UJ55UGr4A.webp){: .align-center}

<br>

# 🔍 Abstract

이번 글에서는 마지막으로 **최근 (5~6월) 에 발표**된 4개의 논문에 대해 소개하겠다. 해당 논문들은 아직 특정 학회에 Accept되거나, 명확한 증명이 된 것이 아니기 때문에 가볍게 **아이디어 위주로 파악**하고자 한다.

1. **AvisC: Don't Miss the Forest for the Trees: Attentional Vision Calibration for Large Vision Language Models** (May 2024): LMM이 정답과는 무관한 부분의 Attention을 높게 주는 현상을 발견하고 이러한 토큰을 **blind token**이라 명명하였다. 그리고 기존 LMM과 **Blind Token만 볼 수 있도록 한 LMM** 간의 Contrastive Decoding을 수행하여 **Object Hallucination**을 개선하는 AVISC(Attentional Vision Calibration)을 제안하였다.
2. **RITUAL: Random Image Transformations as a Universal Anti-hallucination Lever in LVLMs** (May 2024): Image Transformation을 통해 Augmented된 Image를 사용한 LMM과 원본 Image를 사용한 LMM의 Probability를 **Ensemble**하여 **Object Hallucination**을 개선하였다.
3. **CODE: Contrasting Self-generated Description to Combat Hallucination in Large Multi-modal Models** (June 2024): LMM이 **Image Description을 생성한 것으로 Image를 대체**하는 경우 Semantic Information이 충분하지 않아 **Hallucination이 발생**하는 것을 관찰하였다. 따라서 기존 LMM과 **Self-generated Description을 사용한 LMM** 간의 Contrastive Decoding을 수행하여 **Object/Complicated Reasoning Hallucination**을 개선하였다.
4. **AGLA: Mitigating Object Hallucinations in Large Vision-Language Models with Assembly of Global and Local Attention** (June 2024): LMM의 Visual Attention을 분석하여 특정 토큰에 대한 Attention만 높다는 것을 발견하고, 이를 **Global Attention**이라 명명하였다. 이는 위 논문인 **AVISC와 동일한 관찰**이다. 저자들은 GradCAM을 사용하여 이러한 토큰을 Masking하고, 다른 토큰들 즉 **Local Attention**에 집중하도록 하였다. 결과적으로 AGLA(Assembly of Global and Local Attention)을 제안하여 **Object Hallucination**을 개선하였다.

<br>

# 1. AvisC [24']

![image](https://github.com/user-attachments/assets/d425ab0b-187c-44b9-bec1-2230c838a2a5){: .align-center}

<br>

## 1.1. Key Idea: Blind Token of Visual Input

![image](https://github.com/user-attachments/assets/4e04b5ec-da48-418c-9961-24611a06aa5e){: .align-center}

저자들은 먼저 Image Token별로의 Attention을 분석하여 **Blind Token**이라는 것을 발견하였다. 즉, 동일한 Intensity를 가진 Uniform Image임에도 불구하고 특정 Image Token에만 Attention이 높은 것을 발견하였고, 따라서 저자들은 LMM이 특정 Image Token에만 **Biased Attention**을 준다고 판단하였다.

![image](https://github.com/user-attachments/assets/cceddebe-1c9d-421a-bd81-381df5bcb74f){: .align-center}

그러나 이러한 Blind Token은 성능에 악영향을 주는 경우가 많았고, 오히려 **Blind Token이 없을 때 성능이 향상**되는 것을 발견했다. 반대로 **Blind Token이 아닌 Token들은 실제로 Output을 낼 때 중요한 역할**을 하고 있는 것을 발견하였다. 즉, Blind Token은 오히려 Output을 방해하는 역할을 한다는 것이다.

<br>

## 1.2. Method: AvisC (Attentional Vision Calibration)

따라서 저자들은 기존 LMM과 **Blind Token만 볼 수 있도록 한 LMM** 간의 Contrastive Decoding을 수행하여 **Object Hallucination**을 개선하는 AvisC(Attentional Vision Calibration)을 제안하였다.

![image](https://github.com/user-attachments/assets/d31ce0f6-db98-4b30-b6a7-953b4391aa4f){: .align-center}

이제 문제는 **Blind Token**을 어떻게 정의할까 하는 것이다. 지금까지 분석에서는 모든 Layer의 Attention을 단순히 평균내어 사용하는 방식을 이용했지만, 저자들은 더 나아가서 **단순 평균보다는 Attention이 높은 Layer에서만 Blind Token을 정의하는 것이 더 효과적**일 것이라고 생각하였다. 그러나 모델마다 Attention의 정도가 Layer-wise로 달라 Dynamic Layer Selection을 진행하여야 한다.

![image](https://github.com/user-attachments/assets/c1132356-6186-4090-8abb-5771edf80eac){: .align-center}

저자들은 **Attention Proportion**을 다음과 같이 정의하였다. 여기서 $\mathbf{a}_ {h, q, k} ^ {i}$는 $i$번째 Layer의 $h$번째 Head의 Query $q$와 Key $k$의 Attention을 의미한다.

![image](https://github.com/user-attachments/assets/be414ef9-2d35-437d-99c2-56325e0c2c89){: .align-center}

그리고 여기서 **Top-P Sampling**을 진행한다. 즉, 합이 $\gamma$ 이상이 될 때까지 Layer를 선택하는 것이다. 이를 통해 **Dynamic Layer Selection**을 수행할 수 있다. 저자들은 $\gamma = 0.5$를 사용하였다고 한다.

![image](https://github.com/user-attachments/assets/42fb7b12-bbab-40b0-a82f-8c791b3fe071){: .align-center}

그리고 선택된 Layer에서 각 Image Token의 Attention을 계산하고, **Attention이 높은 Token을 Blind Token**으로 정의하였다.

![image](https://github.com/user-attachments/assets/bd13a86e-6fe5-464a-bd17-aeb0fb8c013c){: .align-center}
![image](https://github.com/user-attachments/assets/82e0bdc3-d2e8-4d07-afb6-756ea5e3d745){: .align-center}

<br>

## 1.3. Evaluation: POPE, MME, AMBER

![image](https://github.com/user-attachments/assets/42a2e465-4154-4485-851b-c32ef3c2fc8a){: .align-center}
![image](https://github.com/user-attachments/assets/1e436f68-15c9-4417-a541-7184ec398f63){: .align-center}
![image](https://github.com/user-attachments/assets/1699eafc-4865-44f0-9666-257f8e824c1c){: .align-center}

전체 결과는 위와 같다. 분석에 따르면 **AvisC**는 Object Discrimination 혹은 Object Detection에 특히 강점을 보였다고 한다. 이러한 **Blind Token Approach가 아예 Image를 보지 않는 것보다 효과적인 이유는 무엇일까?** 저자들은 명확한 답을 제시하지는 않았다. 다만, Blind Token은 어느 정도 Image 정보를 많이 가지고 있고, 이 Token은 **Image 중에서도 Hallucination을 주로 일으키는 원인**이 된다. 따라서 이러한 Token은 Image를 아예 보지 않는 것보다 Hallucination을 선택적으로 잘 유발할 수 있다. 즉, VCD의 경우 Language Hallucination만 주로 일으키는 반면, AvisC의 경우 여기에 더해 **Visual Hallucination까지 유발할 수 있다**는 것이다.

<br>

## 1.4. Ablation: Masking

![image](https://github.com/user-attachments/assets/642b7e4e-856b-439e-b8e6-50a4fe2a0613){: .align-center}

저자들은 이러한 Hallucinated LMM을 만들 때 기본적으로 Blind Token 외에는 0으로 Masking을 진행하였다. 여기서는 Alternative Approach를 통하여 모두 효과가 있음을 확인하였다.

<br>

# 2. RITUAL [24']

![image](https://github.com/user-attachments/assets/0d88a4d4-7796-4f23-b7b5-830ed50f2f6f){: .align-center}

<br>

## 2.1. Key Idea: Random Image Transformation

![image](https://github.com/user-attachments/assets/826ea82c-07bd-4f02-95d2-2a7502d4e69d){: .align-center}

AvisC와 동일한 연구실에서 동일한 시간대에 제출된 논문이라는 점이 주목할 만하다. Approach는 아주 직관적인데, **Image Augmentation**을 수행하여 얻은 Output이 때로는 Contrastive Decoding보다 도움이 될 때가 있다는 것이다.

![image](https://github.com/user-attachments/assets/2f15eb63-7742-4bcb-a4db-bbd015d3679c){: .align-center}

다만 위와 같이 오로지 Augmented Image를 사용하는 것은 오히려 성능을 떨어뜨리고, **Ensemble**을 통해 Augmented Image와 원본 Image를 모두 사용하는 것이 효과적임을 확인하였다.

<br>

## 2.2. Method: RITUAL (Random Image Transformations)

![image](https://github.com/user-attachments/assets/e55da9c1-d0bc-456e-8d19-0c5734dbe9ec){: .align-center}

따라서 저자들은 **Image Transformation**을 통해 Augmented된 Image를 사용한 LMM과 원본 Image를 사용한 LMM의 Probability를 **Ensemble**하여 **Object Hallucination**을 개선하였다. 식을 통해 보면 다음과 같다.

![image](https://github.com/user-attachments/assets/1569532c-f4d5-4188-aba0-62dcba41b021){: .align-center}

<br>

## 2.3. Evaluation: POPE, MME, CHAIR

![image](https://github.com/user-attachments/assets/e8af0cfd-c90a-4a3f-bdb9-5eab98d9ae70){: .align-center}
![image](https://github.com/user-attachments/assets/dd395bc6-c330-4a60-9ff5-a245fb5325f9){: .align-center}
![image](https://github.com/user-attachments/assets/7bbc9875-125a-4ceb-a526-7dc798d58854){: .align-center}

해당 방법은 **VCD 또는 M3ID와 같은 방법론과 병용**하여 사용할 수 있다는 점이 장점이다. 따라서 이러한 병용 사용의 경우 성능이 약간 더 향상되는 것을 확인할 수 있다. 표가 길어 일부는 생략되었다.

<br>

## 2.4. Limitation: Nature of Image Transformation

![image](https://github.com/user-attachments/assets/ab790fba-4f86-4a21-b3e4-e9fc6fc4edfc){: .align-center}

그러나 이러한 Image Transformation의 어쩔 수 없는 특성 때문에 오히려 Hallucination이 유발되는 문제가 발생할 수 있다. 위 그림에서는 Crop에 의해 Object가 Image에서 벗어나 Hallucination이 발생하는 것을 확인할 수 있다. 따라서 **Image Transformation을 통해 Hallucination이 유발되는 경우가 있음**을 주의해야 한다.

<br>

# 3. CODE [24']

![image](https://github.com/user-attachments/assets/5cec3461-e38b-4e2d-92fb-77c1e2a90ac6){: .align-center}

<br>

## 3.1. Key Idea: Limitation of Self-generated Description

저자들은 Vision Input과 Language Input의 **Information Density** 차이에 주목했다. 이를 정리하면 다음과 같다.

1. **Vision Input**: Information Density가 **낮다**. 즉, 이미지의 일부 부분이 가려져 있어도 사람은 이미지 전반을 쉽게 이해할 수 있다.
2. **Language Input**: Information Density가 **높다**. 즉, 문장의 일부 단어가 가려져 있으면 (특히 핵심 단어가 가려져 있으면) 그 단어를 예측하고 이해하는 것이 어렵다.

따라서 LMM이 **Image Description을 생성한 것으로 Image를 대체**하는 경우 Semantic Information이 충분하지 않아 **Hallucination이 발생**하는 것을 관찰하였다.

![image](https://github.com/user-attachments/assets/3034c8c6-f6fd-444d-9c3e-09e25b4c855b){: .align-center}

그러나 이는 동시대의 논문과 상충하는 부분이 있다. [VDGD](https://rubato-yeong.github.io/multimodal/vdgd/) 논문에 따르면 Image Description 후에 Visual Reasoning을 수행하는 것이 성능을 향상시킨다고 한다. 이는 겉으로 볼 때는 상충되는 것처럼 보이나 **Benchmark의 차이**에 따라 설명할 수 있을 것 같다. VDGD에서 주로 다룬 Benchmark는 Knowledge-based Question으로 Vision과 Language Reasoning이 결합된 Task이다. 반면, CODE에서 주로 다룬 Benchmark는 MMVP, LLaVA-Bench와 같이 Vision Reasoning이 이루어져야 하는 시각적으로 복잡한 Task이다. 따라서 **Vision Reasoning이 이루어져야 하는 Task에서는 Image Description을 사용하는 것이 불충분한 정보를 제공해 Hallucination을 유발할 수 있다**는 결론을 내릴 수 있다.

<br>

## 3.2. Method: CODE (COuntering DEscription Contrastive Decoding)

Contrastive Decoding은 굉장히 직관적이다.

![image](https://github.com/user-attachments/assets/20356e16-a7c6-4008-8003-f40379e46360){: .align-center}

다만, 저자들은 여기서 $\alpha_ t$ 값을 **adaptive**하게 조절하도록 하여 novelty를 확보하였다. 저자들은 $v$와 $d$의 Token Distribution이 유사할 경우에는 오히려 이러한 Contrastive Decoding이 Hallucination을 유발할 수 있다고 판단하였다. 따라서 Token Distribution이 충분히 다를 때만 Contrastive Decoding을 수행하도록 하였다. 이때 Token Distribution은 **Bounded Divergence** 식을 사용하였다.

$$
\mathcal{D}_ {\text{bd}} (P \Vert Q) = \frac{1}{2} \sum_ {i=1} ^ n (p_ i + q_ i) \log_ 2 \left( \vert p_ i - q_ i \vert ^ k + 1 \right)
$$

이러한 $\mathcal{D}_ {\text{bd}}$는 두 분포 $P$와 $Q$의 차이를 측정하는 방법으로, 값이 $0 \leq \mathcal{D}_ {\text{bd}} \leq 1$ 사이에 있다는 편리한 특징이 있다. 따라서 $\alpha_ t = 1 - \mathcal{D}_ {\text{bd}} (P_ t ^ v \Vert P_ t ^ d)$로 설정하였다.

추가로 Adaptive Plausibility Constraint 대신 새로운 **Adaptive Information Constraint**을 사용하였다. 여기서 $\beta_ t = \mathcal{D}_ {\text{bd}} (P_ t ^ v \Vert P_ t ^ d)$로 설정하여 두 Distribution이 다르면 다를수록 candidate token을 늘려 판단하도록 한다.

![image](https://github.com/user-attachments/assets/540e465d-495c-43cb-80e4-f315bd764609){: .align-center}

<br>

## 3.3. Evaluation: POPE, MMVP, LLaVA-Bench, MMHal-Bench

![image](https://github.com/user-attachments/assets/f6aa254b-fbac-493f-b822-e4af2278a23f){: .align-center}

POPE, MMVP와 달리 LLaVA-Bench와 MMHal-Bench는 GPT-4 Assisted Evaluation이 필요하다. 전체적으로 **SOTA**를 달성하였다.

<br>

## 3.4. Ablation: Dynamic Restriction & Adaptive Information Constraint

![image](https://github.com/user-attachments/assets/cc18ffab-a172-4fc3-9dee-ea6f29d3d572){: .align-center}

저자들은 기존 논문들에서 사용된 값인 $\alpha = 1.0$과 $\beta = 0.1$을 사용하는 것보다 **Dynamic Restriction**과 **Adaptive Information Constraint**을 사용하는 것이 더 효과적임을 확인하였다. 그러나 다시 말하면, 적절한 Parameter Adjustment 없이는 오히려 Greedy Decoding보다도 성능이 떨어질 수 있다는 것을 알 수 있다. 따라서 **충분히 Robust한가**에 대해서는 의문이 남는다.

![image](https://github.com/user-attachments/assets/57002c1a-17ef-40f6-b5f6-969fc4414b11){: .align-center}

또한, VCD에 비해 Image Description을 생성하고, 다시 Probability Distribution을 계산해야 하기에 **Computational Cost**가 높다는 단점이 있다.

<br>

# 4. AGLA [24']

![image](https://github.com/user-attachments/assets/bca03a2d-2491-4624-bc68-a3d32c4a8f8e){: .align-center}

<br>

## 4.1. Key Idea: Global Attention vs. Local Attention

![image](https://github.com/user-attachments/assets/b495c107-cc72-4bec-9007-6c2bcdb11364){: .align-center}

저자들은 LMM의 Visual Attention을 분석하여 특정 토큰에 대한 Attention만 높다는 것을 발견하였다. 이를 **Global Attention**이라 명명하였다. 이는 위 논문인 **AvisC와 동일한 관찰**이다. AvisC에서는 이를 Blind Token으로 명명한 반면, AGLA에서는 이를 Global Attention으로 명명하였다. 저자들은 이러한 **Global Attention이 Prompt-independent**하다는 것을 관찰하였고, 따라서 **prompt-dependent한 Local Attention이 무시**되고 있어 Hallucination이 발생한다고 판단하였다.

따라서 저자들은 이러한 Global Attention을 Masking하고, 다른 토큰들 즉 **Local Attention**에 집중하도록 하였다. 이때 **GradCAM**을 사용하여 Global Attention을 Masking하였다.

![image](https://github.com/user-attachments/assets/5d3b86de-1594-4f42-91a4-72f5cf1462e4){: .align-center}

그 결과, **Prompt-dependent한 Local Attention의 Behavior**를 관찰할 수 있었다.

![image](https://github.com/user-attachments/assets/8cb507ef-4c26-4780-819f-af6712f9055a){: .align-center}

<br>

## 4.2. Method: AGLA (Assembly of Global and Local Attention)

![image](https://github.com/user-attachments/assets/81767c40-1e13-4e36-97c6-a59908b39e94){: .align-center}

따라서 저자들은 AGLA(Assembly of Global and Local Attention)을 제안하여 **Object Hallucination**을 개선하였다. Global Attention과 Local Attention을 모두 사용하는 것으로, 식으로 표현하면 다음과 같다.

![image](https://github.com/user-attachments/assets/8be61bbd-d69a-48ee-8a87-fe36303f0438){: .align-center}
![image](https://github.com/user-attachments/assets/9c21e475-c100-4bea-a0f0-f4288fb1003f){: .align-center}

<br>

## 4.3. Evaluation: POPE, MME, CHAIR, LLaVA-Bench

![image](https://github.com/user-attachments/assets/5e67a534-3ebd-4777-9def-55a760e81b1f){: .align-center}
![image](https://github.com/user-attachments/assets/a7cf0427-6c28-47f9-a78a-bcd0af56fb8f){: .align-center}
![image](https://github.com/user-attachments/assets/8d4b518e-3693-4d62-90b5-76039f79dedd){: .align-center}

성능은 위에서 제안된 다른 모델들과 비슷한 수준이다.

<br>

# 💡 Summary

지금까지 최근의 4가지 논문에 대해 소개하였다. 해당 논문들을 간단히 요약하면 다음과 같다.

* [[24'] AvisC: Don't Miss the Forest for the Trees: Attentional Vision Calibration for Large Vision Language Models](https://arxiv.org/abs/2405.17820)
  * Visual Hallucination을 유발하는, 과도한 Attention을 가진 Image Token을 발견하고 이를 Blind Token이라 명명함
  * Blind Token만을 보는 Hallcinated LMM을 만들어 Contrastive Decoding을 수행하여 Object Hallucination을 개선함
* [[24'] RITUAL: Random Image Transformations as a Universal Anti-hallucination Lever in LVLMs](https://arxiv.org/abs/2405.17821)
  * Image Transformation을 통해 Augmented된 Image를 사용한 LMM과 원본 Image를 사용한 LMM의 Probability를 Ensemble하여 Object Hallucination을 개선함
* [[24'] CODE: Contrasting Self-generated Description to Combat Hallucination in Large Multi-modal Models](https://arxiv.org/abs/2406.01920v1)
  * Image Description을 생성한 것으로 Image를 대체하는 경우 Semantic Information이 충분하지 않아 Hallucination이 발생하는 것을 관찰하고, Self-generated Description을 사용한 LMM과 Contrastive Decoding을 수행하여 Object/Complicated Reasoning Hallucination을 개선함
  * Image Description은 Vision과 Language Reasoning이 결합된 Task에서는 더 효과적일 수 있으나, Vision Reasoning이 이루어져야 하는 시각적으로 복잡한 Task에서는 Hallucination을 유발할 수 있음을 [VDGD](https://rubato-yeong.github.io/multimodal/vdgd/) 논문과 비교하여 작성하였음
  * Contrastive Decoding 시 Token Distribution에 따라 Parameter를 조절하는 Dynamic Restriction $\alpha_ t$, Adaptive Information Constraint $\beta_ t$을 사용하여 Hallucination을 더 정밀하게 개선함
* [[24'] AGLA: Mitigating Object Hallucinations in Large Vision-Language Models with Assembly of Global and Local Attention](https://arxiv.org/abs/2406.12718)
  * Image Attention을 Prompt-independent한 Global Attention과 Prompt-dependent한 Local Attention으로 나누고, 기존 LMM이 Global Attention 값이 높아 Local Attention을 무시하고 있어 Hallucination이 발생한다는 것을 관찰함
  * Global Attention을 Masking하고, Local Attention에 집중하도록 한 LMM과 기존 LMM을 Assembly하여 Object Hallucination을 개선함

<br>

# 📃 Reference

* [[24'] AvisC: Don't Miss the Forest for the Trees: Attentional Vision Calibration for Large Vision Language Models](https://arxiv.org/abs/2405.17820)
* [[24'] RITUAL: Random Image Transformations as a Universal Anti-hallucination Lever in LVLMs](https://arxiv.org/abs/2405.17821)
* [[24'] CODE: Contrasting Self-generated Description to Combat Hallucination in Large Multi-modal Models](https://arxiv.org/abs/2406.01920v1)
* [[24'] AGLA: Mitigating Object Hallucinations in Large Vision-Language Models with Assembly of Global and Local Attention](https://arxiv.org/abs/2406.12718)

<br>