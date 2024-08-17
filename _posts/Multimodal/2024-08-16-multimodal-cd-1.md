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

![image](https://i.namu.wiki/i/zXj0eCxblbIdq8d1381qKvxrFhJZqQln7TtArIOF21IwA1F7cEy66g-7lzbw0z5WV1Zk23fjpnXh0UJ55UGr4A.webp){: .align-center}

<br>

# 🔍 Abstract

이 글에서는 Multimodal Setting에서의 [Contrastive Decoding](https://rubato-yeong.github.io/language/cd/)을 활용한 연구들에 대해 알아보고자 한다. 크게 3개의 글에 나누어 Multimodal Contrastive Decoding을 다루고자 한다. 이를 통하여 **Hallucination**을 개선하고자 하는 **Multimodal CD**의 최신 연구 동향을 충분히 살펴볼 수 있을 것이다.

첫 글에서는 Multimodal Contrastive Decoding의 시초라고 할 수 있는 **VCD(Visual Contrastive Decoding)**와 **M3ID(Multi-Modal Hallucination Control by Visual Information Grounding)**을 같이 읽어보고자 한다. 두 논문 모두 **CVPR 2024**에 발표되었으며, Object Hallucination을 개선하기 위한 Contrastive Decoding을 다루고 있다.

두 번째 글에서는 이어서 2월에서 3월 경 발표된 **IBD(Image-Biased Decoding)**와 **Contrastive Region Guidance**, **Pensieve**, **ICD(Instruction Contrastive Decoding)**을 간단히 살펴보고자 한다.

마지막으로 세 번째 글에서는 5월에서 6월 경 발표된 **HIO(Hallucination-Induced Optimization)**, **AvisC(Attentional Vision Calibration)**, **RITUAL(Random Image Transformations as a Universal Anti-hallucination Lever in LVLMs)**, **CODE(Contrasting Self-generated Description)**, **AGLA(Assembly of Global and Local Attention)**을 살펴보고자 한다.

먼저 이번 글에서는 **VCD**와 **M3ID**에 대해 알아보고자 한다. 각 논문의 내용을 간단히 요약해보면 다음과 같다.

1. **VCD: Mitigating Object Hallucinations in Large Vision-Language Models through Visual Contrastive Decoding** (November 2023, CVPR 2024): Forward Diffusion Process를 통해 **Visual Uncertainty**를 부여한 모델에서는 **Language Prior와 Statistical Bias가 증폭**되어 Hallucination이 발생하는 것을 확인하였다. 따라서 저자들은 기존 LMM과 **Distorted Image를 사용한 LMM** 간의 Contrastive Decoding을 수행하여 **Object Hallucination**을 개선하였으며, 일반적인 Multimodal Benchmark 성능도 향상시켰다.
2. **M3ID: Multi-Modal Hallucination Control by Visual Information Grounding** (March 2024, CVPR 2024): 저자들은 Output이 Visual Input에 얼마나 의존하는지 측정하는 척도인 **PDM(Visual Prompt Dependency Measure)**를 만들고, 생성되는 문장이 길어짐에 따라 **PDM은 감소하고 Hallucination은 증가**하는 것을 확인했다. 저자들은 이것을 **conditioning dilution** 또는 **fading memory effect**라고 명명하였다. 따라서 저자들은 **Visual Input이 있는 경우와 없는 경우**에 대한 Contrastive Decoding을 수행하여 문장이 길어져도 **PDM을 유지**하며 **Object Hallucination**을 개선하였다.

<br>

# 1. VCD(Visual Contrastive Decoding) [24' CVPR]

![image](https://github.com/user-attachments/assets/02813b65-60c1-49ff-8cf0-204f15dce62b){: .align-center}

<br>

## 1.1. Key Idea: Visual Uncertainty

저자들은 먼저 Diffusion Process를 통해 **Visual Uncertainty**를 Image에 부여했다. 즉, LMM이 이미지를 잘 인식하지 못하도록 의도적으로 **Distorted Image**를 만든 것이다.

![image](https://github.com/user-attachments/assets/b1adb678-4ad0-4906-a35e-e4ee4da545a6){: .align-center}

그 결과는 위와 같았다. 실제 이미지의 `banana`는 `black`이지만, Distorted Image에서는 `Yellow`의 Probability가 높아지는 현상이 발생했다. 이는 LMM이 더 이상 Image에 의존하지 않고, **Language Prior**에 의존하게 되었다는 것을 의미한다.

![image](https://github.com/user-attachments/assets/7fa6867f-bc96-43bf-b2a8-ce884bd56579){: .align-center}

또한 저자들은 Visual Uncertainty가 있을 때에는 **Statistical Bias**가 증폭되어 Hallucination이 발생하는 것을 확인하였다. 위 그래프는 데이터 내에서 가장 많이 등장하는 Object 및 Co-occurrence가 가장 높은 Object이 잘못 등장한 비율을 나타낸 것이다. 결과적으로 Visual Uncertainty가 있는 경우에는 학습 데이터에 기반하여 **Object Hallucination**이 발생하는 것을 확인할 수 있었다.

지금까지의 내용을 정리하면, **Visual Uncertainty**를 부여한 모델에서는 (1) **Language Prior**와 (2) **Statistical Bias**가 증폭되어 Hallucination이 발생하는 것을 확인할 수 있었다.

<br>

## 1.2. Method: Visual Contrastive Decoding

![image](https://github.com/user-attachments/assets/04b67726-28f8-4b38-817e-05282014badc){: .align-center}

따라서 저자들은 Language Prior와 Statistical Bias에 의존하는 모델을 활용하여 **Contrastive Decoding**을 수행하여 이러한 Token들의 Probability를 **Penalize**할 수 있을 것이라고 가정하였다. 식으로 이를 표현하면 다음과 같다.

![image](https://github.com/user-attachments/assets/be54e82c-6ec8-402b-817d-53e8d42e8ff0){: .align-center}

이때 $v^ \prime$은 Distorted Image를 의미하며, $v$는 원본 Image를 의미한다. Hyperparameter $\alpha = 1.0$으로 두었다. 저자들은 기존 Contrastive Decoding과 동일하게 **APC(Adaptive Plausibility Constraints)**를 도입하였으며, $\beta = 0.1$로 설정하였다.

![image](https://github.com/user-attachments/assets/ca4a049b-65ff-44b0-a11c-02da9091b481){: .align-center}

<br>

## 1.3. Evaluation: POPE, MME, LLaVA-Bench

![image](https://github.com/user-attachments/assets/158e9fd4-4016-46a7-80b4-1a5ffadab131){: .align-center}
![image](https://github.com/user-attachments/assets/6bbd67df-61d6-4fb5-bd4a-2e0ed898fa25){: .align-center}
![image](https://github.com/user-attachments/assets/6f594470-95cb-49d7-9fd8-4fe8b604d36e){: .align-center}

**POPE, MME, LLaVA-Bench**에서의 성능을 살펴본 결과, **VCD**는 기존 LMM에 비해 **Object Hallucination**을 개선하였다는 것을 알 수 있다. LLaVA-Bench의 경우 GPT-4V Aided Evaluation을 진행하였다. 저자들은 이러한 간단한 Contrastive Decoding을 통해 Hallucination을 개선할 수 있음을 입증하였다.

<br>

# 2. M3ID(Multi-Modal Mutual-Information Decoding) [24' CVPR]

![image](https://github.com/user-attachments/assets/77a63600-322a-46ff-89a5-cd2f8505e592){: .align-center}

<br>

## 2.1. Key Idea: Visual Prompt Dependency Measure (PDM)

저자들은 VCD의 저자들과 유사한 문제 의식으로부터 출발했다. 바로 Output이 Language Prior에 너무 의존하여 Hallucination이 일어난다는 것이다. 따라서 저자들은 **Visual Prompt (=Visual Input)의 의존도를 높여야 한다**고 생각했고, 그 첫 걸음으로 그 의존도를 측정하는 **PDM(Visual Prompt Dependency Measure)**라는 척도를 만들었다. Motivation을 보기 위해, 먼저 아래 그림을 보자.

![image](https://github.com/user-attachments/assets/fee17b08-9bd0-4457-b01f-748932fe6be9){: .align-center}

위 그림은 Image를 Input으로 주었을 때와 주지 않았을 때의 Probability를 나타낸 것이다. 예를 들어 `chef`라는 Token은 이미지 없이는 거의 예측할 수 없기에 Visual Prompt에 의존도가 높다고 볼 수 있다. 반면 `kitchen`의 경우 이미 `chef`라는 토큰으로부터 예측할 수 있기에 Visual Prompt에 의존도가 낮다고 볼 수 있다. 이러한 의존도를 특정 값으로 나타내기 위해 저자들은 **Probability Distribution 사이의 Distance를 계산**하는 방법을 사용하였다.

![image](https://github.com/user-attachments/assets/9fb698ee-d59b-4b3f-8f45-f2bfdf938494){: .align-center}

즉, 이미지 $c$가 있는 경우와 없는 경우의 Probability Distribution 사이의 Distance를 계산하여 **Visual Prompt Dependency Measure**를 계산하고자 하였다. 여기서 Distance는 어떠한 것도 사용이 가능한데, 저자들은 간단히 **Hellinger Distance**를 사용하였고 이를 **PDM-H**라고 명명하였다.

![image](https://github.com/user-attachments/assets/fa170343-3698-4c5b-bcbc-887104787d53){: .align-center}

저자들은 이를 이용해 Hallucination과 PDM, Token 길이의 관계를 살펴보았다. 

![image](https://github.com/user-attachments/assets/6dc24a45-3038-4778-9a4b-501d8c53a492){: .align-center}

그 결과, **PDM이 길어질수록 Hallucination이 증가**하고, **Token 길이가 길어질수록 PDM이 감소**하는 것을 확인하였다. 저자들은 이를 **conditioning dilution** 또는 **fading memory effect**라고 하였고, 이는 토큰이 생성됨에 따라 Image의 기여도는 떨어지고 Language Prior에 의존하면서 Hallucination이 발생한다는 것이다.

<br>

## 2.2. Method: Multi-Modal Mutual-Information Decoding

![image](https://github.com/user-attachments/assets/ee53d934-6f75-4eba-a614-d35936b2869b){: .align-center}

저자들은 Mutual Information 식으로부터 유도하여 일종의 **Contrastive Decoding** 식을 유도하였는데, 여기서는 내부의 복잡한 수학적 전개보다는 결론만 살펴보기로 한다. 먼저 **이미지가 있는 경우의 Token의 Log Probability**을 $l_ c$, **없는 경우의 Token의 Log Probability**을 $l_ u$로 나타내었다. 그리고 Token이 생성됨에 따라 Image를 잊어버린다는 **conditioning dilution**을 보완하기 위하여 **forgetting rate** $\gamma_ t = \exp (-\lambda t)$를 도입하였다. 그리고 Token 길이가 길어짐에 따라 Image로부터의 Contribution을 높이기 위하여 아래 식을 이용하여 **Multi-Modal Mutual-Information Decoding**을 수행한다.

![image](https://github.com/user-attachments/assets/78a43c7e-d0f4-43c2-8e54-71eca66d8919){: .align-center}

즉, 기존의 Contrastive Decoding이 고정된 $\beta$ 값을 사용했다면, 여기에서는 Token의 길이에 따라 $\beta = (1 - \gamma_ t) / \gamma_ t$을 동적으로 조절하여 **Image의 기여도**를 높이는 방법을 사용하였다.

저자들은 APC를 사용하는 대신 해당 Token에 대한 Confidence가 일정 수준 $\alpha$ 이상이면 M3ID를 적용하지 않는 일종의 **Confidence Threshold**를 사용하였다. 효과는 유사하다. 따라서 전체 알고리즘을 다음과 같이 요약할 수 있다.

![image](https://github.com/user-attachments/assets/36a98fbf-f648-42b4-a878-cbb5de0401b1){: .align-center}

추가로 저자들은 DPO를 활용하여 RLHF 학습을 진행하였는데, 이 부분은 Contrastive Decoding과는 별개이기에 생략하였다.

<br>

## 2.3. Evaluation: CHAIR, POPE

![image](https://github.com/user-attachments/assets/b81afd9c-7167-422c-93e3-e7eeb68eb311){: .align-center}
![image](https://github.com/user-attachments/assets/876d7ff7-0a5e-4a8f-8ca2-1e2c7aa72998){: .align-center}

**CHAIR, POPE**에서의 성능은 상당히 많이 향상된 것을 확인할 수 있다. 특히 POPE에서는 Hallucination에 특화되어 Fine-tuning된 다른 모델들에 준하는 성능을 보여주었다. 이를 통해 **M3ID**가 Hallucination을 개선하는 데 효과적임을 입증하였다.

<br>

## 2.4. Ablation: Overcompensation

![image](https://github.com/user-attachments/assets/e9af3846-5467-48bb-b253-4b1ecbc115c1){: .align-center}
![image](https://github.com/user-attachments/assets/74238c01-67fe-49ba-91ad-1412fd553a61){: .align-center}

저자들은 $\alpha$와 $\lambda$ Hyperparameter를 조절하여 어떠한 변화가 일어나는지 확인하였다. $\alpha$의 경우 **Confindence Threshold**로 사용되었는데, 너무 $\alpha$ 값이 높으면 충분히 높은 confidence 하에서도 M3ID를 진행하게 되어 **linguistic fluency**가 감소하였다고 한다. $\lambda$의 경우 **forgetting rate**로 사용되었는데, 너무 $\lambda$ 값이 높으면 Image의 기여도가 너무 높아져 **overcompensation**이 발생하였다고 한다.

<br>

# 💡 Summary

지금까지 두 논문인 **VCD**와 **M3ID**에 대해 알아보았다. 두 논문 모두 **CVPR 2024**에 발표되었으며, Object Hallucination을 개선하기 위한 Contrastive Decoding을 다루고 있다. 각 논문의 내용을 간단히 요약해보면 다음과 같다.

* [[24' CVPR] VCD: Mitigating Object Hallucinations in Large Vision-Language Models through Visual Contrastive Decoding](https://arxiv.org/abs/2311.16922)
  * Distorted Image를 사용하여 Visual Uncertainty를 부여한 모델에서는 Language Prior와 Statistical Bias가 증폭되어 Hallucination이 발생하는 것을 확인
  * 기존 LMM과 Distorted Image를 사용한 LMM 간의 Contrastive Decoding을 수행하여 Object Hallucination을 개선
* [[24' CVPR] M3ID: Multi-Modal Hallucination Control by Visual Information Grounding](https://arxiv.org/abs/2403.14003)
  * Visual Input에 Output이 얼마나 의존하는지 측정하는 PDM(Visual Prompt Dependency Measure)를 만들고, 생성되는 문장이 길어짐에 따라 Image의 기여도가 떨어져 Hallucination이 증가하는 fading memory effect를 확인
  * 문장이 길어짐에 따라 Image의 기여도를 높이는 Dynamic Adjustment를 포함한 Contrastive Decoding을 수행하여 Object Hallucination을 개선

<br>

# 📃 Reference

* [[24' CVPR] VCD: Mitigating Object Hallucinations in Large Vision-Language Models through Visual Contrastive Decoding](https://arxiv.org/abs/2311.16922)
* [[24' CVPR] M3ID: Multi-Modal Hallucination Control by Visual Information Grounding](https://arxiv.org/abs/2403.14003)

<br>