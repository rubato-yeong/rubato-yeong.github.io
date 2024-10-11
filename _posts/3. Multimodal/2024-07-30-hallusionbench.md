---
title:  "[24' CVPR] HallusionBench: An Advanced Diagnostic Suite for Entangled Language Hallucination and Visual Illusion in Large Vision-Language Models"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Hallucination, Benchmark]

toc: true
toc_sticky: true
 
date: 2024-07-30
last_modified_at: 2024-07-30
---

![image](https://github.com/user-attachments/assets/2d527873-cdbd-402b-b973-1a79993db883){: .align-center}

<br>

# 🔍 Abstract

CVPR 2024에서 **Multimodal LLM의 Hallucination을 평가하는 Benchmark**로 소개된 논문이다. 해당 논문의 Hallucination에 대한 접근이 굉장히 창의적이고 흥미롭다. 해당 논문에서는 두 가지 개념을 제시한다.

1. **Language Hallucination**: 모델이 Visual Input을 참고하지 않고 Output을 생성하는 현상
2. **Visual Illusion**: 모델이 Visual Input을 잘못 해석하여 잘못된 Output을 생성하는 현상

이를 분석하기 위해 저자들은 **HallusionBench**라는 Benchmark를 제안한다. 해당 Benchmark는 346개의 이미지와 1129개의 질문으로 구성되어 있으며, 기존의 벤치마크와 달리 human expert에 의해 manual하게 생성된 데이터셋이기에 더 신뢰성이 높다. 이제 이 Benchmark를 어떻게 구성했고, 평가의 결과는 어떤지 간략히 알아보자.

<br>

# 1. Benchmark Construction

![image](https://github.com/user-attachments/assets/1d9b5d60-c83d-4bb5-a516-6137ae794016){: .align-center}

먼저 저자들은 VQA Dataset을 크게 두 가지 부류로 분류하였다.

1. **Visual Dependent**: Visual Input을 참고해야만 답을 할 수 있는 질문
2. **Visual Supplement**: Visual Input을 참고하지 않아도 배경 지식을 활용하여 답을 할 수 있는 질문이나, Visual Input은 참고할 만한 정보를 제공하거나 잘못된 정답을 교정할 수 있는 기회를 제공함

저자들은 **Visual Dependent** 질문을 통해 model의 **parametric memory**가 대답에 얼마나 영향을 주는지 확인하고자 했다. 또한, **Visual Supplement** 질문을 통해 model이 **parametric memory**가 있는 경우 추가로 visual information을 참고하는지 확인하고, **parametric memory**가 없는 경우에도 hallucination을 하는지 확인하고자 했다.

전체 데이터셋을 VD(VIsual Dependent)와 VS(Visual Supplement)로 나누어 보면 대략 반반 정도의 비율을 가지고 있다. 저자들은 **VD**의 경우 **기존 질문(With Original)**과 해당 질문과 반대되는 visual input을 **직접 만든(With Edited)** **counterexample**을 만들어 추가하였다. 한편 **VS**에서는 이에 더하여 **이미지를 넣지 않고 질문하는 경우(Without Visual)**를 추가하였다. 다만 위 예시를 보면 알 수 있겠지만 VS의 counterexample은 **실제 결과와 다른 부분**이 있다. 즉, 실제 2008년 베이징 올림픽에서는 중국이 가장 많은 금메달을 획득했지만, counterexample에서는 미국이 가장 많은 금메달을 획득한 것이 사실인 양 나와 있다. 이는 **LLM의 factuality와, LMM의 hallucination이 충돌하는 부분**이라고 생각된다. 따라서 이 **counterexample에 대한 제대로 된 정답이 무엇인지에 대한 논란**이 생길 수 있다고 생각하고, hallucination을 측정하기 위한 적절한 방법은 아니라고 생각한다.

![image](https://github.com/user-attachments/assets/5316f206-e406-42c7-aa52-eb654f663a28){: .align-center}

<br>

# 2. Evaluation

## 2.1. Correctness

LMM이 Benchmark에 정확하게 대답하는지 확인하기 위하여 GPT-4를 이용한다. 저자들은 **GPT-4에 Model이 내놓은 정답과 Ground Truth(yes or no)를 비교**하도록 하고, GPT-4는 **Incorret(0) / Correct(1) / Unclear(2)** 중 하나로 대답한다. Unclear인 경우 대부분의 경우 Incorrect와 동일하게 0점을 부여했지만, Visual Input이 없는 VS(Visual Supplement)의 경우에는 Visual Input이 없어 제대로 대답하지 못하는 것이 오히려 정상적인 경우가 있을 수 있기에 1점을 부여했다.

저자들은 **All Accuracy**에 더해 각 Figure마다 있는 모든 질문에 모두 정확히 대답한 경우에 대한 Accuracy인 **Figure Accuracy**, 동일한 질문에 대해 VD(Visual Dependent)의 경우 기존 이미지와 변형 이미지, VS(Visual Supplement)의 경우 기존 이미지와 변형 이미지, 이미지 없는 경우 모두 맞춘 경우에 대한 Accuracy인 **Question Pair Accuracy**를 계산하였다. 또한, 기존 이미지에 대한 Accuracy를 **Easy Accuracy**, 변형 이미지에 대한 Accuracy를 **Hard Accuracy**로 나누어 계산하였다.

그 결과는 아래와 같고, 놀랍게도 굉장히 낮은 정확도를 보였다. **Question Pair Accuracy**의 경우 **GPT-4V, Claude 3과 같은 Closed model을 제외하고서는 그 결과가 처참할 정도**이다. **Hard Accuracy의 경우 Random Chance보다도 성능이 좋지 않다**. 아직 LMM이 Visual Perception에 있어 많은 한계를 가지고 있다는 것을 알 수 있다.

![image](https://github.com/user-attachments/assets/1cdca792-55ce-4211-bb81-3b34d5a704ff){: .align-center}

<br>

## 2.2. Analytical Evaluation

저자들은 이에 더해 세 가지 추가적인 평가를 진행하였다. **(1) Yes / No Bias Test, (2) Consistency Test, (3) Language Hallucination and Visual Illusion Test**이다.

(1) **Yes / No Bias Test**는 일반적으로 LMM 모델이 "yes"를 더 많이 출력하도록 학습되었다는 문제를 확인하기 위해 사용하였다. 측정하기 위하여 **Yes Percentage Difference (Pct. Diff)**와 **FP Ratio**를 사용하였으며, **Pct. Diff**의 경우 그 절댓값이 1에 가까울수록 biased되어 있다는 뜻이고 **FP Ratio**는 0.5에 가까울수록 robust하다는 뜻이 된다.

(2) **Consistency Test**는 각 Figure마다 있는 모든 질문에 잘 대답하였는지 확인한다. 예를 들어 첫 번째 질문이 "왼쪽이 오른쪽보다 긴가?"에 대한 대답이 "Yes"였다면, "왼쪽이 오른쪽보다 짧은가?"라는 질문에 대한 대답은 "No"여야 한다. 이러한 Consistency를 측정하기 위해 위에서 사용한 **Figure Accuracy**를 그대로 사용하였다.

(3) **Language Hallucination and Visual Illusion Test**는 LMM이 얼마나 hallucination을 하는지, 얼마나 Visual Input을 잘못 해석하는지 확인하기 위해 사용하였다. 이를 측정하기 위해 아래와 같은 방법을 사용하였다. 복잡한 Diagram이라 하나하나 설명하기에는 너무 길어지기에 생략하였으나, 각각을 따라가보면 나름 합리적인 이유로 **LH(Language Hallucination)와 VI(Visual Illusion)을 확인**하고 있다. 중요한 점은 Human annotation을 통해 **counterexample**을 사용하였기에 이러한 Diagram을 그릴 수 있고 측정할 수 있었다는 것이다. 참고로 $I_ {(i, 0)}$는 기존 이미지, $I_ {(i, j)}$는 변형 이미지(혹은 VS에서는 기존 이미지까지 포함하는 개념), $I_ 0$는 이미지가 없는 경우를 뜻한다.

![image](https://github.com/user-attachments/assets/f190e614-aa56-4ef3-a9bb-d64134f839e6){: .align-center}

그 결과는 아래와 같고, **Yes Bias가 Open-source Model에 많다는 것, Consistency가 거의 없다는 것**을 보여준다. 또한, Language Hallucination과 Visual Illusion의 합이 대략 비슷한 것을 보면 **해당 척도가 정확하지 않다**는 것을 유추할 수 있을 것 같다. Diagram에서는 정답이 완전히 똑같아야 Language Hallucination이 일어났다고 판단하는데, 실제로는 약간 다르지만 유사한 정답을 생성할 수 있고 이를 Visual Illusion으로 판단하는 것보다는 Language Hallucination으로 판단하는 것이 더 합리적일 수 있다. 예를 들어, **Qwen-VL의 경우 LH는 매우 낮고 VI가 매우 높은데, 이는 약간씩 다른 정답을 생성하는 해당 모델의 특성 때문**인 것으로 풀이된다.

![image](https://github.com/user-attachments/assets/1058527c-4e1e-42d1-b138-2a06fec4a750){: .align-center}

<br>

## 2.3. Additional Results

![image](https://github.com/user-attachments/assets/11e81b56-705d-49bb-ad62-cb988e666947){: .align-center}

추가로, Vision-Language Model에 Video Sequence를 단순히 넣어준 경우 굉장히 성능이 좋지 않았다. 이는 **LVLM 자체가 Video Sequence를 잘 이해하지 못한다**는 것을 뜻한다. 실패한 경우에 대한 예시를 아래와 같이 시각화하였다.

![image](https://github.com/user-attachments/assets/aa6f2523-a7cf-4811-86a5-147aa808b1b7){: .align-center}

<br>

# 💡 Summary

해당 논문의 개념적인 Contribution을 정리해보면 다음과 같은 two-fold이다.

1. **Visual Dependent vs. Visual Supplement**: Visual Input을 참고해야만 답을 할 수 있는 질문과 참고하지 않아도 답을 할 수 있는 질문을 구분하였다.
2. **Language Hallucination vs. Visual Illusion**: LMM의 Hallucination을 Language Hallucination과 Visual Illusion으로 나누어 분석하였다.

또한, 해당 논문의 분석 내용을 간략히 요약하면 다음과 같다.

1. **Language Hallucination**: LMM이 Visual Input을 참고하지 않고, Prior Knowledge로 대답을 생성하는 현상은 GPT-4V를 포함하여 대부분의 모델에서 나타났다.
2. **Visual Illusion**: LMM이 Parametric Memory를 가지고 있지 않은 Sample에 대해서도 Visual Input을 잘못 해석하여 잘못된 Output을 생성하는 현상이 쉽게 나타난다.
3. **Vulnerable to Image Manipulation**: Flipping, Masking, Color Editing 등의 간단한 manipulation에 의해서도 성능이 굉장히 저하되며, 이는 Random Chance보다도 성능이 낮다.
4. **Fail to Understand Video Sequence**: Video Sequence를 이해하는 데에 있어서 굉장히 어려움을 겪는다.

<br>

# 📃 Reference

* [[24' CVPR] HallusionBench: An Advanced Diagnostic Suite for Entangled Language Hallucination and Visual Illusion in Large Vision-Language Models](https://arxiv.org/abs/2310.14566)

<br>