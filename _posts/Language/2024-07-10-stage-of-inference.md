---
title:  "[24'] The Remarkable Robustness of LLMs: Stages of Inference?" 
excerpt: ""

categories:
  - Language
tags:
  - [Interpretability]

toc: true
toc_sticky: true
 
date: 2024-07-26
last_modified_at: 2024-07-26
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/af8b2a8a-e997-4d90-93d8-212149928499){: .align-center}

<br>

# 🔍 Abstract

저자들은 LLM의 **layer를 삭제하거나 그 순서를 바꾸는 등의 변화를 주어도 성능이 크게 떨어지지 않는다**는 것을 발견했다. 이와 추가적인 실험을 통해 저자들은 LLM이 Layer에 따라 **4단계의 Inference Stage를 거친다는 가설**을 주장한다. 이러한 Inference Stage는 **1) detokenization, 2) feature engineering, 3) prediction ensembling, 4) residual sharpening**으로 구성되어 있다. 각각의 Stage의 역할을 요약하면 다음과 같다.

1. **Detokenization**: Token이라는 Raw Representation을 적절히 통합하여 High-level Contextual Representation을 만든다.
2. **Feature Engineering**: 이를 이용해 Task-specific, Entity-specific feature를 추출한다.
3. **Prediction Ensembling**: Hidden representation은 이 단계에서 Vocabulary space와 Align되며, 다음 토큰으로 가능한 단어들을 예측한다.
4. **Residual Sharpening**: 이 단계에서는 Prediction을 Refine하고, 가장 가능성이 높은 단어를 선택한다.

이처럼 Transformer 내부를 분석하고, 설명하고자 하는 시도를 **Mechanical Interpretability(MI)**라고 한다. 해당 키워드에 관심이 생겨, 앞으로 관련된 논문들을 찾아보고자 한다.

<br>

# 1. Layer Lesion Study

![image](https://github.com/user-attachments/assets/dbcdf92b-be50-4c3b-b251-2c6dd1997b36){: .align-center}

저자들은 본격적인 가설 설정에 앞서 **Layer Lesion Study**를 제시한다. 이는 LLM의 Layer를 삭제하거나 순서를 바꾸는 등의 변화를 주었을 때 성능이 어떻게 변하는지를 살펴보는 실험이다.

![image](https://github.com/user-attachments/assets/cf348fc3-69ad-409e-9e9c-5b25e47ed964){: .align-center}

각 지표에 대해 간략히 알아보자. KL Divergence는 기존 Output Probability와 Layer를 삭제하거나 순서를 바꾸었을 때의 Output Probability 사이의 차이를 측정한다. 즉, **KL Divergence가 낮다는 것은 LLM이 Robust하다**는 것을 뜻한다. **Relative Accuracy는 그와 반대**이고, Logit Entropy Difference는 KL Divergence와 유사하게 **Output Probability의 Entropy 차이를 측정**한다.

결과적으로 Early Layer, Late Layer가 아닌 **Intermediate Layer에서는 Swap, Ablation이 큰 영향을 미치지 않는다**는 것을 확인할 수 있다. 그렇다면 왜 LLM은 Layer-wise Intervention에 Robust한 것일까? 저자들은 Residual Connection의 영향이 크다고 주장한다.

![image](https://github.com/user-attachments/assets/3e3ceb5c-7c14-42ce-ae21-7e475dd07917){: .align-center}

Residual Connection은 **Shallow Sub-network를 여러 개 만들어 Ensemble**하는 역할을 하는 것으로 해석할 수 있다. 이러한 해석은 NeurIPS 2016 논문인 [Residual Networks Behave Like Ensembles of Relatively Shallow Networks](https://arxiv.org/abs/1605.06431)에서 제안된 것이다. 어쨌든 이러한 **Residual Connection 덕분에 각 Path에 대한 Dependency가 적게 되고, 따라서 Layer-wise Intervention에 Robust한 것**이라고 저자들은 주장한다.

<br>

# 2. Stages of Inference Hypothesis

저자들은 위 실험을 바탕으로 **Stages of Inference Hypothesis**를 제시한다. 이 가설은 **LLM이 Inference를 4단계로 나누어 진행한다**는 것이다. 저자들은 이 가설을 검증하기 위해 아래와 같은 실험들을 진행했다.

![image](https://github.com/user-attachments/assets/34623dde-66ba-4dc6-a72d-e6c36162aad6){: .align-center}

Layer Lesion Study를 제외한 다른 실험들에 대해 하나씩 알아보도록 하자.

<br>

## 2.1. Stage 1: Detokenization

위 실험에서 Early Layer는 Layer Lesion Study에 굉장히 민감했다. 이는 **Early Layer가 Intermediate Layer와 다른 어떤 중요한 역할을 한다는 것을 시사**한다.

지금까지의 연구에서 Transformer의 특정 Neuron이 N-gram과 같이 Local Information을 Aggregate한다는 증거는 계속 제시되어 왔다. 저자들은 이러한 Aggregation이 Early Layer에서 일어난다는 것을 Attention Mechanism으로 보이고, 이를 **Detokenization**이라고 명명한다.

![image](https://github.com/user-attachments/assets/bc6ba0de-230d-40ae-bf43-0e1e0bcbb1ef){: .align-center}

위 그래프를 보면 Early Layer에서 Local Attention, 즉 **Previous Five Token Attention의 비율이 높은 것**을 알 수 있다. 즉, 동일한 단어가 여러 Token으로 쪼개졌다면 이를 다시 **하나로 합치는 역할을 하는 것이 Early Layer**라는 것이다. 그러나 이를 명확히 증명하기 위해서는 동일한 단어, 또는 유사한 의미를 가지고 있는 Token Window가 실제로 하나로 합쳐지는지 Attention을 통해 확인하는 것이 필요하다고 생각한다. 따라서 이 부분에 대한 **추가적인 실험이 필요**하며, Detokenization에 대한 **증명은 아직 미흡**하다고 볼 수 있다.

<br>

## 2.2. Stage 2: Feature Engineering

![image](https://github.com/user-attachments/assets/a806dc78-1bd6-4c63-a613-56653ac76664){: .align-center}

저자들은 이후 Downstream Prediction에 유용한 Feature를 만드는 **Feature Engineering**이라는 단계가 존재한다고 생각했다. 이 단계에서는 syntactic feature가 점점 abstraction 되어 semantic feature가 된다. 저자들은 중간 Layer에서 Unembedding을 시행하여 Prediction을 예측하고, 실제 결과와 KL Divergence를 측정했다. 그 결과 이 Stage에서는 **KL Divergence가 높았다**. 즉, 여기서는 Prediction이 시행되어 정확한 Token을 예측하기보다는 많은 정보를 추출하는 역할을 한다는 것을 예상할 수 있다. 저자들은 이에 대한 더 많은 증거들을 참고문헌으로 제시하였는데 각각에 대해 다 살펴보지는 않았다.

<br>

## 2.3. Stage 3: Prediction Ensembling

![image](https://github.com/user-attachments/assets/a806dc78-1bd6-4c63-a613-56653ac76664){: .align-center}

이 단계에서는 각 Neuron들이 최종 Output에 대해 서로 일종의 투표를 시행한다. 따라서 **가능성 있는 Token들을 모두 예측하는 단계**로 이해할 수 있다. 저자들은 기존 논문의 방법론을 따라 **Prediction Neuron**과 **Suppression Neuron**이라는 것을 측정했다. Prediction Neuron은 가장 높은 확률을 가진 Token을 예측하는 일종의 Vote를 시행하는 Neuron이고, Suppression Neuron은 이 Vote를 억제하는 역할을 한다. 대략 85% 부분에서 이러한 Prediction Neuron은 최대가 되며, 이때에 맞춰 KL Divergence가 감소한다. 이는 **Prediction Neuron이 가장 가능성이 높은 Token을 예측하는 역할**을 한다는 것을 시사한다.

<br>

## 2.4. Stage 4: Residual Sharpening

이에 이어 Suppression Neuron이 모델의 Late Layer에서 등장하는 것으로부터 저자들은 **Residual Sharpening**이라는 단계가 존재한다고 주장한다. 이 단계에서는 Prediction을 Refine하고, 가장 가능성이 높은 Token을 선택하는 역할을 한다. 이는 **Prediction Ensembling 단계에서의 투표 결과를 정제하는 역할**을 한다고 볼 수 있다.

![image](https://github.com/user-attachments/assets/1b53e6bc-149e-48eb-968b-f726311c8a99){: .align-center}

**Logit Lens Entropy**를 볼 때, 이 단계에서는 **Entropy가 낮아지는 것**을 확인할 수 있다. 그러나 일부 모델에서는 마지막에 오히려 Entropy가 높아지는 경우도 있다. 이는 **overconfident prediction**을 **blunt**하게 만드는 역할이다. 즉, top-level token의 확률을 줄이고 prediction distribution을 더 평평하게 만드는 역할을 한다는 것이다. 이러한 결과는 기존에 **final layer에서 오히려 correct token이 incorrect token으로 바뀌는 문제, pruning과 같은 테크닉이 성능을 향상시키는 이유**를 설명할 수 있다. 

![image](https://github.com/user-attachments/assets/fa447a91-7783-47e7-93f7-ff97b8b4f8c2){: .align-center}

마지막으로 MLP Norm을 보자. **MLP Norm이 크다는 것은 각 Layer에서 Residual Stream에 MLP가 미치는 영향이 크다**는 뜻이고, 그만큼 Feature가 크게 변한다는 뜻이다. 위 그림을 보면 **Late Layer에서 Feature가 격변**하는 것을 볼 수 있고, 이는 그만큼 Residual Sharpening이 LLM에 있어 중요한 역할을 한다는 것을 시사한다.

<br>

# 💡 Summary

지금까지의 내용을 요약하면 다음과 같다.

* LLM의 Intermediate Layer는 **Layer-wise Intervention에 Robust**하다. 이는 **Residual Connection** 덕분이며, 이는 Shallow Sub-network를 여러 개 만들어 Ensemble하는 역할을 하기에 각 Layer의 **Dependency**를 줄일 수 있다.
* LLM은 **4단계의 Inference Stage**를 거친다고 주장한다.
  * **Detokenization**: Token을 적절히 통합하여 High-level Contextual Representation을 만든다.
  * **Feature Engineering**: Task-specific, Entity-specific feature를 추출한다.
  * **Prediction Ensembling**: 다음 토큰으로 가능한 단어들을 예측한다.
  * **Residual Sharpening**: Prediction을 Refine하고, 가장 가능성이 높은 단어를 선택한다.

물론 이 4단계의 Inference Stage는 일부 **겹치는 Layer**가 있을 수 있으며, Figure를 보면 알겠지만 모델마다 **이러한 Stage가 다른 위치에서 나타나고 완전히 consistent하지는 않다**. 이러한 부분은 저자들도 Limitation으로 언급하고 있으며, 이에 대한 추가적인 연구가 필요하다고 생각한다. 추가로 지금 수행한 실험들이 4단계의 Inference Stage를 **직접적으로 증명하는 실험은 아니라고 생각**한다. 그럼에도 불구하고 복잡한 Transformer의 구조 내부에서 이 정도의 실험을 구상하고 수행한 것은 꽤나 인상적이었다.

<br>

# 📃 Reference

* [[24'] The Remarkable Robustness of LLMs: Stages of Inference?](https://arxiv.org/abs/2406.19384v1)

<br>