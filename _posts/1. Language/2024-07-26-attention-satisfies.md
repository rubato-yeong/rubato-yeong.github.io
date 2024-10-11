---
title:  "[24' ICLR] Attention Satisfies: A Constraint-Satisfaction Lens on Factual Errors of Language Models" 
excerpt: ""

categories:
  - Language
tags:
  - [Interpretability, Hallucination]

toc: true
toc_sticky: true
 
date: 2024-07-26
last_modified_at: 2024-07-26
---

![image](https://github.com/user-attachments/assets/e3cb7d68-fe36-43dc-97e3-354fa114d303){: .align-center}

<br>

# 🔍 Abstract

이 논문은 LLM의 **Factual Error**를 Constraint Satisfaction Problem으로 해석하고, 이를 해결하기 위한 방법을 제시한다. 저자들은 **LLM이 Factual Token에 집중하는 Attention의 정도와 실제 LLM이 생성한 대답의 Factual Accuracy 사이에 아주 강한 상관관계**가 있다는 것을 발견했다. 이것만으로도 놀라운 발견인데, 저자들은 추가로 이를 활용한 Hallucination 감지 방법을 제안한다. 즉, **Attention을 통해 Factual Error를 감지하고, 이를 해결하는 방법을 제시**하며 이를 **SAT Probe**라고 명명하였다.

<br>

# 1. Introduction

이 논문의 핵심 발견을 한 줄로 요약하면 다음과 같이 말할 수 있다.'

**LLM의 Factual Error는 Attention을 통해 감지할 수 있다.**
{: .notice}

예를 들어 LLM에게 영화의 감독(Director Name)과 영화가 받은 상(Award Name)을 알려주고 그 영화를 맞추라는 문제를 낸다고 생각해보자. 이때 저자들은 이처럼 Director Name, Award Name을 LLM이 알아야 하는 **Constraint** $C_i$로 설정하고, 이를 LLM이 잘 인지하는지 **Attention**을 통해 확인한다. 그리고 잘 인지했다면 $V(C_ i) = 1$로, 그렇지 않다면 $V(C_i) = 0$으로 표기한다. 이를 저자들은 **CSP(Constraint Satisfaction Problem)**이라고 하며, 모든 **Constraint**에 대해 $V(C_i) = 1$일 때 **Factual Correctness가 높다**는 것이 이 논문의 핵심이다. 아래 그림에서 왼쪽 Column은 실제로 올바른 정답을 말했을 때 모든 $V(C_i) = 1$이 되는 것을 보여주고, 가운데와 오른쪽 Column은 틀린 정답을 말했을 때 적어도 하나의 $V(C_i) = 0$이 되는 것을 보여준다.

![image](https://github.com/user-attachments/assets/3114bf31-5dc6-4ecf-9d25-e69384cb20c8){: .align-center}

<br>

# 2. Factual Queries as Constraint Satisfaction Problems

위에서 말한 내용을 아주 Formal하게 정리하면 다음과 같은 **Definition**으로 설명할 수 있다.

![image](https://github.com/user-attachments/assets/8bd3dafe-69ec-4dfa-963e-17dec17d70f4){: .align-center}

저자들은 먼저 Factual Queries의 특징이 LLM의 정확도에 어떠한 영향을 미치는지 실험을 통해 확인했다. 그 결과를 요약하면 다음과 같다.

![image](https://github.com/user-attachments/assets/aa4f0a93-9370-4615-98db-4b6cc7fdd025){: .align-center}

1. **Popularity vs Correctness**: 여러 농구선수가 태어난 연도를 물어보는 질문의 정확도를 측정하였는데, 그 농구선수가 유명할수록, 즉 **훈련 데이터셋에 더 많은 비율로 포함되었을 확률이 높을수록 정확도가 높다는 것**을 알 수 있다.
2. **Constrainedness vs Correctness**: 특정 알파벳으로 시작하고 특정 알파벳으로 끝나는 단어를 하나 제시하라고 하여 정확도를 측정하였는데, 해당하는 단어의 수가 절대적으로 적을수록 정확도가 낮았다. 즉, **Constraint가 더 Strict할수록 정확도가 낮다**는 것이다.

<br>

# 3. Understanding Factual Errors via Attention to Constraints

저자들은 LLM이 constraint를 어떻게 처리하는지 이해하기 위해 **constraint token에 대한 attention**을 활용하였다. 아래 식의 용어를 정리하자.

![image](https://github.com/user-attachments/assets/6df87412-efb1-4d0f-8d30-40e6a111b69a){: .align-center}

* **Attention Weight** $A_ {c, T} ^ {\ell, h}$: Query, Key를 곱해 Softmax를 취한 값을 말한다. 이때 최종적으로는 **last token prediction에 constraint token이 참조되는 정도**를 계산해야 하기 때문에 **query는 last token** $x_ {T+1}$이고, **key, value는 constraint token** $x_ c$이다.
* **Attention Contribution** $a_ {c, T} ^ {\ell, h} \in \mathbb{R}^ {d}$: Attention Weight에 Value, Output Matrix를 곱해 **실제 Residual Stream에 Constraint Token이 기여하는 정도**를 말한다. 각 Head의 contribution을 marginal하게 더하기 위해서는 단순히 $a_ {c, T} ^ {\ell} = \sum_ h a_ {c, T} ^ {\ell, h}$로 계산하면 된다.

이제 **Factual Queries, Attention, Factual Correctness 사이의 관계**를 살펴보자.

![image](https://github.com/user-attachments/assets/0558821e-0cd2-49f9-973b-b17f4e5361e3){: .align-center}

**Attention predicts popularity**. 먼저 저자들은 단순히 Attention Weight의 Norm을 계산하여 Popularity와 연관되는지를 확인하였고, 통계적으로 유의한 결과를 얻었다. 즉, **Attention이 높을수록 Popularity가 높다**는 것이다. 그러나 이러한 관계가 명확하지는 않았기에, **Attention Contribution**을 통해 **Factual Correctness**와의 관계를 확인하였다. 그 결과는 다음과 같다.

![image](https://github.com/user-attachments/assets/ae00c337-3fe5-4cec-80ef-78b6404bb64a){: .align-center}

**Attention correlates with confidence and LLM’s correctness**. 저자들은 LLM의 Confidence, 즉 $\hat{\mathbb{P}} (Y \vert X)$가 가장 높은 25개와 가장 낮은 25개 샘플에 대한 Attention Contribution을 측정하였다. 그 결과, **Confidence가 높은 샘플에서는 Attention Contribution이 높았고, Confidence가 낮은 샘플에서는 Attention Contribution이 낮았다**. 이는 **Attention Contribution이 Factual Correctness와 강한 상관관계**가 있다는 것을 의미한다. 사실 정확하게는 Confidence가 높다고 Correctness가 높은 것은 아니다. 그래서 위의 마지막 Column에는 **Attention이 높으면 Accuracy가 높다는 것**을 명확히 보여주었다. 이를 통해 **constraint에 대한 attention이 LLM의 정확도를 측정하는 데 유용하다**는 것을 확인할 수 있다.

![image](https://github.com/user-attachments/assets/98be058b-65c0-4b9b-8c3c-25652ed099f0){: .align-center}

**Language models grow larger, pay more attention, and succeed more**. 추가로, LLM의 크기가 커질수록 Constraint에 대한 Attention이 더 명확해지고 따라서 Factual Correctness가 높아진다는 것을 확인하였다. 이는 **LLM이 커질수록 더 많은 Constraint를 처리할 수 있고, 이를 통해 더 정확한 답을 생성할 수 있다**는 것을 의미한다.

지금까지의 내용을 정리하면 다음과 같다.

* **Factual Queries, 즉 Constraints에 대한 Attention이 높은 것과 LLM의 Factual Correctness 사이에는 강한 상관관계**가 있다.
* 따라서, Attention을 사용하여 **Constraint가 성립하는지 여부를 간접적으로 확인**할 수 있으며, 이를 바탕으로 **Factual Error를 감지**할 수 있을 것이다.

<br>

# 4. Predicting Factual Errors using Attention to Constraints

저자들은 특정 Constraint를 만족하는지 여부를 **Attention을 사용한 Linear Probe**로 예측하도록 하였다. 한가지 의문이 있다면 왜 지금까지 분석에 사용한 **Attention Contribution을 사용하지 않고 다시 Attention Weight으로 회귀하였는가** 하는 것인데, Appendix에서 밝히길 성능이 이게 더 좋았기 때문이라고 한다.

![image](https://github.com/user-attachments/assets/2b41df4d-976a-4a23-8425-845aa957ef7c){: .align-center}
![image](https://github.com/user-attachments/assets/257e3dc1-260c-4a3e-9be1-f2066db97d9d){: .align-center}


그리고 이를 모든 Constraint에 대해 적용하여 **Factual Error를 감지**하도록 하였다. 이를 **SAT Probe** 방법이라 한다.

![image](https://github.com/user-attachments/assets/fbd99921-ba66-46fd-9108-9b9cd22cbcf1){: .align-center}

이 Probability는 Logistic Regression에 의해 결정되므로, 어떤 경우 Constraint를 만족했다고 할 것인지, 그 **Threshold를 결정할 수 있는 권한이 hyperparameter**로 존재한다. 따라서 저자들은 이러한 불편함을 제거하기 위해 **AUROC**를 활용하여 모든 threshold에 대한 성능을 전반적으로 측정하였다. 비교를 위해 $\hat{\mathbb{P}} (Y \vert X)$를 사용한 **Confidence Probe**를 baseline으로 설정하였다.

![image](https://github.com/user-attachments/assets/92cc04ae-5203-40c0-8ed0-c5d00c987d2b){: .align-center}

결과적으로 **SAT Probe**는 **Confidence Probe**와 비슷하거나 약간 아쉬운 성능을 보여준다. 그리고 이 둘을 합쳤을 때 가장 좋은 성능을 보이는데, 이는 **Attention과 Confidence가 상호보완적**이라는 것을 뜻한다.

![image](https://github.com/user-attachments/assets/387cfeca-daac-478b-b7d1-122362c3a558){: .align-center}

위 그림을 보면 Attention이 틀리는 부분과 Confidence가 틀리는 부분이 각각 존재한다. 즉, **LLM이 overconfident한 부분에서 사실은 attention이 없고 실제로 factual error인 부분이 있고, attention이 있지만 confidence가 낮아 실제로 factual error인 부분이 있다**는 것이다. 이러한 상호보완적 속성 또한 이 논문에서 처음 제시한 contribution으로 볼 수 있다.

<br>

# 💡 Summary

지금까지의 내용을 요약하면 다음과 같다.

* **Factual Queries, 즉 Constraint에 대한 Attention이 높은 것과 LLM의 Factual Correctness 사이에는 강한 상관관계**가 있다.
* 따라서 Attention Weight을 통해 Factual Error를 감지할 수 있으며, 이를 **SAT Probe**라고 명명하였다. 이는 기존에 Hallucination을 감지하기 위해 사용하던 LLM의 **Confidence와 상호보완적**이다.

<br>

# 📃 Reference

* [[24' ICLR] Attention Satisfies: A Constraint-Satisfaction Lens on Factual Errors of Language Models](https://arxiv.org/abs/2309.15098)

<br>