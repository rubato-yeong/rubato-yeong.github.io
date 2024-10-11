---
title:  "[24' ICML] Fool Your (Vision and) Language Model With Embarrassingly Simple Permutations"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Evaluation]

toc: true
toc_sticky: true
 
date: 2024-08-17
last_modified_at: 2024-08-17
---

![image](https://github.com/user-attachments/assets/7ff02dbd-bc07-415d-b1fb-988a5b69a045){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/user-attachments/assets/1d50096b-df9d-412a-9d02-37be11c34c12){: .align-center}

저자들은 **MCQA(Multiple-Choice Question Answering)** 상황에서 LLM과 VLLM이 **Permutation**에 굉장히 취약하다는 사실을 알아냈다. 충격적이게도 Permutation을 하는 경우 대부분의 경우 **Random Selection보다 정답률이 낮았다**. 저자들은 이러한 문제의 원인을 실험과 함께 다음과 같이 분석하였다.

1. **Position Bias**: 훈련 데이터에 따라 특정 문제에서는 특정 Option만 선택하는 Bias가 있다. 즉, Option의 내용과는 관계없이 **특정 위치에 있는 Option을 선택**하는 경향이 있다.
2. **Pattern of the Distractor Answers**: True Answer 주변에 Distractor Answer가 많은 경우 정답을 맞출 확률이 낮아질 수 있다. 즉, Position Bias만으로는 설명되지 않지만 **Multiple Choice의 배열 방식**에 따라 정답을 맞추기 어려워질 수 있다는 것이다.
3. **Symbol-Content Spurious Correlation**: 이상적으로는 Option 번호와 내용이 대응되어 LLM이 이를 이해하고 정답인 번호를 선택해야 하지만, 기존 Symbol A/B/C/D 대신 I/II/III/IV로 변경한 경우 정답으로 선택하는 위치가 달라지는 현상이 발생한다. 즉, **Symbol과 Content 간의 연관성이 명확하지 않다**는 것을 알 수 있다.

이와 같이 MCQA Task에서 LLM 또는 VLLM은 Permutation 및 기타 Setting에 **Invariant하지 않으며, 굉장히 복잡한 Bias를 가지고 행동**하는 것을 알 수 있었다. 저자들은 이를 해결하기 위한 기존 Technique, 즉 Majority Vote와 같은 Ensemble 방법을 활용했으나 이러한 방법들도 Permutation에 대한 **취약성을 완전히 해결하지 못했다**.

<br>

# 1. Adversarial Permutation

저자들은 먼저 Permutation Attack 전후의 정답률을 LLM 및 VLLM에서 비교하였다. 채점 방식을 수식으로 나타내면 다음과 같다.

![image](https://github.com/user-attachments/assets/97e138bd-4f66-4285-a312-8d1a760fa6a2){: .align-center}

저자들은 모든 Permutation, 즉 A/B/C/D인 경우 4!개의 Permutation을 생성하여 **모두 정답인 경우에만 정답**이라고 판정하였다.

![image](https://github.com/user-attachments/assets/4a124326-150b-4d58-91a4-7148728f02e4){: .align-center}
![image](https://github.com/user-attachments/assets/bd772c10-50e3-4b30-950a-3561afe3e4d5){: .align-center}

LLM 및 VLLM의 결과는 위와 같으며, **Random Chance보다 낮다**는 점은 굉장히 충격적이다.

![image](https://github.com/user-attachments/assets/11e38834-9da6-492b-bf0f-4ee895ed5849){: .align-center}

이는 Choice의 개수를 줄여도 마찬가지인데, 따라서 Yes/No Question에서도 LLM 및 VLLM은 **어떤 것이 정답인지 정확히 알지 못하고** 문제를 풀고 있다는 것을 알 수 있다.

<br>

# 2. Understanding Vulnerability Causes

## 2.1. Position Bias and Distractor Pattern

저자들은 Position Bias와 Distractor Pattern을 실험을 통해 확인하기 위해 다음과 같은 세팅을 고안했다.

1. **A/B/C/D Position Bias**: 기존 Option이 A/B/C/D였다면, 항상 정답이 A에 있도록 Rotate하여 실험을 진행하였다. 이렇게 실험하여 정답률을 확인하면, LLM이 얼마나 A라는 위치에 Bias가 있는지 확인할 수 있다.
2. **Permutation Attack**: Section 1에서 사용한 Setting이다.

![image](https://github.com/user-attachments/assets/b38edaf2-361c-488a-a573-bad80a383957){: .align-center}

그 결과는 위와 같다. 저자들은 **Position Bias가 존재**한다는 것을 확인했고, 그러나 그것만으로는 Permutation Attack의 낮은 정답률을 설명할 수 없다는 것을 주장했다. 따라서 Position Bias만으로는 설명할 수 없는 미묘한 요소가 존재한다는 것을 알 수 있고, 이를 **Distractor Pattern**이라 명명하였다. 즉, **Distractor가 어떻게 배열되어 있는지에 따라 정답률이 달라질 수 있다**는 것이다.

<br>

## 2.2. Symbol-Content Spurious Correlation

저자들은 A/B/C/D를 a/b/c/d 또는 I/II/III/IV로 변경하여 실험을 진행하였다. 그 결과는 다음과 같다.

![image](https://github.com/user-attachments/assets/a589c7ed-88fa-459a-87f6-7846b65f4be2){: .align-center}

정답률은 비슷하지만 다른 Symbol로 바꾸었을 때 정답의 **Correlation**이 그렇게 높지 않은 것을 알 수 있다. 즉, **Symbol과 Content 간의 연관성이 명확하지 않다**는 것을 알 수 있다. 즉 LLM은 A라는 정답을 선택할 때 A의 내용을 보고 선택하는 것이 아니라 **A라는 Symbol을 보고 선택**할 수도 있다는 것이다.

<br>

# 3. Exploring Mitigation Strategies

![image](https://github.com/user-attachments/assets/c41090b0-1034-454e-882d-5d1520b015e9){: .align-center}

저자들은 이를 해결하기 위해 MCQA의 정답률을 높이는 여러 기존 방법들을 사용해 보았다. 저자들은 **Majority Vote, Contextual Calibration, Maximum Confidence** 방법을 실험했다. 그러나 이러한 방법들도 Permutation Attack에 대한 **취약성을 완전히 해결하지 못했다**.

<br>

# 💡 Summary

지금까지의 내용을 요약하면 다음과 같다.

* MCQA(Multiple-Choice Question Answering) 상황에서 LLM과 VLLM이 Permutation에 굉장히 취약하며, 이는 Position Bias, Distractor Pattern, Symbol-Content Spurious Correlation 등의 요소로 인해 발생함

<br>

# 📃 Reference

* [[24' ICML] Fool Your (Vision and) Language Model With Embarrassingly Simple Permutations](https://arxiv.org/abs/2310.01651)

<br>