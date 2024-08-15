---
title:  "[Summary] Recent Contrastive Decoding Variants" 
excerpt: ""

categories:
  - Language
tags:
  - [Summary, Contrastive Decoding]

toc: true
toc_sticky: true
 
date: 2024-08-15
last_modified_at: 2024-08-15
---

![image](https://github.com/user-attachments/assets/1df618a6-2331-4c85-854c-cf47fede40be){: .align-center}
<br>
![image](https://github.com/user-attachments/assets/a70370a4-3354-42db-b475-93b5d608f624){: .align-center}
<br>
![image](https://github.com/user-attachments/assets/f7fefc09-a8bb-4d9c-8f9c-98d4e0436571){: .align-center}

<br>

# 🔍 Abstract

ACL 2023에서 **Contrastive Decoding**이 발표된 후, 이후 여러 연구들이 이를 확장하거나 변형하여 새로운 방법을 제안하고 있다. 이번 포스트에서는 최근 Contrastive Decoding을 활용한 연구들 중 주목할 만한 연구들을 소개하고자 한다. 각 연구들의 내용을 간략하게 소개하면 다음과 같다.

1. **Contrastive Decoding Improves Reasoning in Large Language Models**: **Contrastive Decoding의 식을 변형**하여 더 **일반화**된 Contrastive Decoding 방식을 제안하고, Task별로 Contrastive Decoding의 효과를 분석하여 Reasoning Task에서 Contrastive Decoding이 효과적임을 보여준다.
2. **Trusting Your Evidence: Hallucinate Less with Context-aware Decoding**: Contrastive Decoding을 활용하여 Hallucination을 줄이기 위해 Context가 있는 LLM과 **Context가 없는 LLM**을 사용한 Contrastive Decoding을 제안한다.
3. **Alleviating Hallucinations of Large Language Models through Induced Hallucinations**: Contrastive Decoding을 활용하여 Hallucination을 줄이기 위해 **Hallucination을 강제로 발생시키도록 Fine-tuning한 LL**M을 사용한 Contrastive Decoding을 제안한다.

<br>

# 1. Generalized Contrastive Decoding [23' ArXiv]

## 1.1. Methods

저자들은 기존 Contrastive Decoding의 식을 정리하고, 더욱 일반화하였다. 기존 Contrastive Decoding의 식은 다음과 같다.

![image](https://github.com/user-attachments/assets/64e13e26-04a2-4aac-88e0-0ed9110d128d){: .align-center}
![image](https://github.com/user-attachments/assets/c593d41b-b7eb-4abd-9d5f-132536fdd2df){: .align-center}

저자들은 probability 대신 logit을 사용하여 비슷한 식을 만들었다.

![image](https://github.com/user-attachments/assets/4ab16293-2dcb-4695-a71a-3bc93e5b0eec){: .align-center}

$\alpha$-mask는 CD의 **APC(Adaptive Plausibility Contraint)**와 동일하다. 한편 $\beta$는 새로 도입되는 hyperparameter로, 이는 **Contrastive Decoding의 효과를 조절**하는 역할을 한다. 위 식을 다시 나타내면 다음과 같다.

![image](https://github.com/user-attachments/assets/e531c9df-a8e3-4949-8201-3047e4e09639){: .align-center}

이로부터 $\beta \rightarrow 0$이면 contrastive penalty가 없어 **기존 decoding 방식과 동일**하고, $\beta \rightarrow \infty$이면 probability distribution이 $\arg \max p_ e ^ {(i)} / p_ a ^ {(i)}$로 수렴하게 된다. 즉, **기존 contrastive decoding과 완전히 동일한 방식**이 된다. 따라서 기존 contrastive decoding은 $\beta \rightarrow \infty$로 수렴하는 특별한 경우라고 볼 수 있다. 저자들은 이러한 **지나친 정도의 contrastive decoding은 오히려 성능을 해칠 수 있다**고 주장하며, $\beta = 0.5$ 정도의 적절한 값을 사용하는 것이 좋다고 주장한다.

![image](https://github.com/user-attachments/assets/3c3663af-756b-4512-b87f-e0c832cef6d6){: .align-center}

<br>

## 1.2. Experiments

![image](https://github.com/user-attachments/assets/49ab96d8-7d45-44a4-96d3-16fbabaf57dd){: .align-center}

저자들은 **Reasoning Task**에서 Contrastive Decoding의 효과를 분석하였고, 위과 같이 Reasoning Task에서 높은 성능 향상을 보였다. 저자들은 이처럼 CD가 Reasoning에서 효과적인 이유를 Expert LM은 Reasoning 능력이 높은 반면 Amatuer LM은 Reasoning 능력이 낮기 때문에 CD에서 **Expert LM의 Reasoning 능력을 강조하게 되기 때문**이라고 설명한다.

![image](https://github.com/user-attachments/assets/cf49f621-583e-4c9f-adeb-786f6335b8a2){: .align-center}

추가적으로 저자들이 분석한 CD의 장단점을 정리하면 다음과 같다.

* **장점**
  * CD는 **Prompt Copying을 줄이고**, 더 풍부한 Output을 생성하도록 한다.
  * Reasoning 성능을 높이는 다른 방식에 비하여 **FLOP efficiency**가 높다.
* **단점**
  * CD는 일반적인 Reasoning에서 대개 효과적이나 **Arithmetic(숫자 계산)에는 효과가 떨어진다**.
  * CD는 **Factual Recall을 줄이는 문제**를 가지고 있다.

<br>

# 2. Context-aware Decoding [24' NAACL]

## 2.1. Methods

![image](https://github.com/user-attachments/assets/e71a9b12-4ea4-4c65-ade8-c40284cbe3dc){: .align-center}

LLM은 종종 Input Context를 잘 고려하지 않고 Parametric Knowledge에 기반하여 대답하기 때문에 Hallucination을 유발한다. 저자들은 이러한 문제에서 착안하여 **Context-aware Decoding(CAD)**를 제안한다. CAD는 **Context가 있는 LLM**과 **Context가 없는 LLM**을 사용하여 Contrastive Decoding을 수행하여 **Input Context를 고려하는 정도를 증폭**시켜 Context를 더 잘 고려하도록 한다. 이를 식으로 나타내면 다음과 같다.

![image](https://github.com/user-attachments/assets/f1043cd6-487e-467f-bda6-1f11f635fb2f){: .align-center}

저자들은 이 식이 Diffusion에서의 CFG(Classifier-Free Guidance)와 유사하다고 설명한다. 여기서 Condition $\mathbf{c}$은 **Context**이다. 이러한 CAD를 통해 Context를 강조할 수 있게 된다.

<br>

## 2.2. Experiments

![image](https://github.com/user-attachments/assets/97b9a4a9-8b45-48c9-883e-dd25b17638de){: .align-center}
![image](https://github.com/user-attachments/assets/f545d192-1a3b-4156-96c1-7f48d42cdff7){: .align-center}

이러한 CAD(Context-aware Decoding)를 통해 **Summarization, Knowledge Conflicts** Dataset에서 상당한 성능 향상을 보였다.

![image](https://github.com/user-attachments/assets/53d4b73d-0902-4d8b-89e2-da2c41e166a1){: .align-center}

또한, 이러한 CAD Decoding 방법은 굉장히 Efficient하며 Robust해서 여러 Parameter Size의 LM Model에서 전부 다 잘 작동했다.

![image](https://github.com/user-attachments/assets/a4b2c78a-4608-4af0-966f-2344276b7fc1){: .align-center}

추가로, 저자들은 Hyperparameter $\alpha$ (Generalized Contrastive Decoding에서의 $\beta$와 동일)을 조절하여 보면서 $\alpha = 0.5$ 정도를 최적의 값으로 제안하였다. 이는 이전 논문과 **동일한 결과**이다.

<br>

# 3. Induce-then-Contrast Decoding [23' ArXiv]

## 3.1. Methods

![image](https://github.com/user-attachments/assets/ffa95025-6dd8-47a6-b7ac-ed8ec0a4d60f){: .align-center}

저자들은 Hallucination을 줄이기 위해 오히려 **Hallucination을 유도하는 Data를 생성하고 이를 사용해 Fine-tuning한 LLM**을 만든다. 그리고 기존 LLM과 이렇게 Fine-tuning된 LLM을 사용해 Contrastive Decoding을 수행한다. 이러한 방식을 **Induce-then-Contrast Decoding**이라고 부른다. 저자들은 이러한 방식으로 Hallucination을 줄이는 것이 가능하다고 주장한다.

저자들은 ChatGPT를 사용하여 Hallucination을 유도하는 Data를 생성하였다. 예를 들어 사실인 문장 *ACL 2024 will be held in Bangkok.*에 대하여 *ACL 2024 will be held in Singapore.* 또는 *ACL 2023 will be held in Bangkok.*과 같은 Hallucination을 유도하는 Data를 생성하고, LoRA와 같은 Fine-tuning 방법으로 학습시킨다.

![image](https://github.com/user-attachments/assets/68d1d1de-ce05-4a38-9c90-74864674b372){: .align-center}

그리고 이렇게 Fine-tuning된 LLM을 사용하여 Contrastive Decoding을 수행한다.

![image](https://github.com/user-attachments/assets/30c5630a-4ea5-4046-b43a-8064cc80735f){: .align-center}

<br>

## 3.2. Experiments

![image](https://github.com/user-attachments/assets/a359644b-7b49-49d5-b305-b2bf0af76116){: .align-center}

전체 결과는 위와 같다. **Induce-then-Contrast Decoding**은 Hallucination을 줄이는 데 효과적이었으며, 다른 Contrastive Decoding 방법보다 더 나은 성능을 보였다.

![image](https://github.com/user-attachments/assets/c1a3a961-aae2-4e24-a9cd-54b3dcddc5aa){: .align-center}

추가로, **Open-ended Factual Generation Task**인 FACTSCORE에서는 기존 Contrastive Decoding 방식은 오히려 성능이 떨어졌지만, Induce-then-Contrast Decoding은 성능이 향상되었다. 이처럼 다른 방법들에 비해 Robust한 성능을 보이는 것은 **Fine-tuned LLM이 Hallucination을 굉장히 잘 생성**하여 Contrastive Decoding이 효과적으로 작동하기 때문이라고 추정된다.

![image](https://github.com/user-attachments/assets/4c62c697-1916-4192-8572-2e47af4d9ad4){: .align-center}

또한, **기존 Benchmark의 성능을 떨어뜨리지 않는다**는 점을 보여 해당 방법이 상당히 robust하다는 점을 보여주었다.

![image](https://github.com/user-attachments/assets/3281a4da-06b2-41c2-acfc-f94518649185){: .align-center}

추가로 저자들은 **Induce-then-Contrast Decoding**이 모델의 크기가 커질수록 효과적임을 보여주었다. 이는 CD에서도 보여준 결과인데, 이 결과를 해석해보자면 **Large LM일수록 신뢰할만한 좋은 answer candidate를 잘 생성**하고, 따라서 contrastive decoding 시에 더 좋은 성능을 보이게 된다는 것으로 이해할 수 있다.

![image](https://github.com/user-attachments/assets/9133d380-7d20-43f1-bf0a-1fcdbfd44659){: .align-center}

한편, **Factual Data를 단순히 LLM에 Fine-tuning**하고 그 LLM을 사용하는 것은 예상과는 달리 **오히려 Hallucination을 유발**했다. 이는 SFT 과정에서의 학습 동안 해당 LLM의 knowledge boundary를 넘어서는 데이터를 학습시켜 오히려 **behavior cloning**이 일어났기 때문으로 추정된다.

<br>

# 💡 Summary

지금까지 세 개의 Contrastive Decoding 연구를 소개하였다. 각 연구들은 Contrastive Decoding을 활용하여 Hallucination을 줄이거나, Reasoning Task에서 성능을 향상시키는 방법을 제안하였다. 각 논문의 내용들을 간략히 요약하면 다음과 같다.

* [[23'] Contrastive Decoding Improves Reasoning in Large Language Models](https://arxiv.org/abs/2309.09117)
  * Contrastive Decoding의 정도를 조절할 수 있는 새로운 hyperparameter $\beta$를 도입하여 일반화된 Contrastive Decoding 방식을 제안함
  * Reasoning Task에서 CD가 효과적임을 보여주었고, CD의 장단점을 분석함
* [[24' NAACL] Trusting Your Evidence: Hallucinate Less with Context-aware Decoding](https://arxiv.org/abs/2305.14739)
  * Input Context를 잘 고려하지 않아 Hallucination이 일어나는 문제를 해결하기 위해 Context가 없는 LM과 대조하여 Context를 증폭시키는 Context-aware Decoding을 제안함
* [[23'] Alleviating Hallucinations of Large Language Models through Induced Hallucinations](https://arxiv.org/abs/2312.15710)
  * Hallucination 문제를 해결하기 위해 Hallucination을 유도하는 Data를 생성하고 이를 사용해 Fine-tuning한 LLM을 사용하여 Contrastive Decoding을 수행하는 Induce-then-Contrast Decoding을 제안함
  * Induce-then-Contrast Decoding은 Hallucination을 줄이는 데 효과적이었으며, 다른 방법들보다 성능이 좋고 강건함

<br>

# 📃 Reference

* [[23'] Contrastive Decoding Improves Reasoning in Large Language Models](https://arxiv.org/abs/2309.09117)
* [[24' NAACL] Trusting Your Evidence: Hallucinate Less with Context-aware Decoding](https://arxiv.org/abs/2305.14739)
* [[23'] Alleviating Hallucinations of Large Language Models through Induced Hallucinations](https://arxiv.org/abs/2312.15710)
* [Openreview: Contrastive Decoding Improves Reasoning in Large Language Models](https://openreview.net/forum?id=SzV37yefM4)

<br>