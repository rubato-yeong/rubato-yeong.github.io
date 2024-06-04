---
title:  "[24'] Meta-Prompting: Enhancing Language Models with Task-Agnostic Scaffolding" 
excerpt: ""

categories:
  - Language
tags:
  - [Meta-Prompting]

toc: true
toc_sticky: true
 
date: 2024-06-03
last_modified_at: 2024-06-04
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/160a3b61-394a-4ff9-be82-195d13473769){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/e39828b0-c62c-4acc-8fd6-b635c95a429c){: .align-center}

**OpenAI**에서 나온 24년 초에 나온 논문으로, 저자들은 **meta-prompting**이라는 개념을 제시한다. 이는 하나의 LM이 2가지의 역할, 즉 **1) Multi-faceted conducter, 2) Distinct experts**를 수행하도록 만드는 것이다. 즉, meta-prompting을 통해 LM이 스스로 복잡한 task를 간단한 task로 분할하도록 만들고, 이를 각 expert role을 맡은 LM에게 전달하여 task를 수행하도록 한다. 이를 통해 **task-agnostic scaffolding**을 제공하고, 이를 통해 **zero-shot performance**를 향상시킨다. 이는 다른 **scaffolding method**와 비교하여 더욱 효과적이다. 추가로, Python Interpreter와 같이 external tools를 도입하여 더 활용성을 높였다.

<br>

# 1. Introduction

최근 LM의 operational cost가 줄어들면서, 단 한 번의 query만으로 LM이 정답을 예측하도록 하는 시스템보다는 **여러 LM queries를 leverage하는 방법들이 제안**되고 있다. 이 논문은 그러한 경향성 속에서 어떻게 효율적으로 LM query를 활용할지에 대한 연구라고 생각할 수 있다.

논문에서는 **task-agnostic nature**를 매우 강조하고 있다. 즉, 지금까지의 **scaffolding methods**는 각 task에 맞춰 제작한 특정한 instruction이나 example이 필요했다. 하지만, 이 논문에서는 여러 task에 한 번에 적용될 수 있는 **high-level instruction**을 제시하였다. 따라서 본 논문의 가장 중요한 contribution은 **single LM을 leverage하는 task-agnostic scaffolding system**을 제안한 것이다.

<br>

# 2. Meta-Prompting

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/a841432b-51f8-4309-91a6-228775312537){: .align-center}

**Meta-prompting**은 위와 같은 그림으로 직관적으로 이해할 수 있다. **Meta-prompting**은 single LM에 각각 일종의 페르소나를 부여하여 **1) Meta Model, 2) Various Experts**의 역할을 부여한다. 먼저 **Meta Model**은 유저의 instruction과 지금까지의 대화 문맥을 보고 **적절한 expert를 결정하여 호출하고, 그 expert에게 적절한 prompt를 만들어 전달**한다. 이때 **expert**는 **meta model이 전달한 prompt를 받아 실제로 역할을 수행**한다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/724ef712-791a-4697-8cf7-4414cf5d1167){: .align-center}

이를 알고리즘으로 보면 위와 같다. $\mathbb{S}$는 string의 집합을 추상적으로 나타낸 것이다. 먼저 $x \in \mathbb{S}$는 **user instruction**이며, $t_ {\text{init}}$을 통해 formatting되어 $\mathcal{H}_ 1$이 된다. 이후 **Meta Model**에 의해 prompt $y_t \leftarrow \text{LM} (\mathcal{H}_ t)$를 얻고, 이것이 적절한 prompt라면 expert model에 전달된다. 마지막으로 **final answer signal**을 받으면 모든 과정을 종료하고 최종 결과를 출력한다.

<br>

# 3. Main Results

## 3.1. Baselines

저자들은 다른 prompting method와 meta-prompting을 비교하였는데, 실제로 비교한 prompting은 다음과 같다.

* **Standard prompting**: 가장 단순한 형태의 prompt이다.
* **Zero-shot CoT prompting**: "Let's think step by step"이라는 prompt를 붙여 주는 방법이다.
* **Expert prompting**: expert identity를 생성하여 예측하도록 하는 prompt 방법이다.
* **Multi-persona prompting**: SPP(Solo-performance prompting)이라고도 불리며, 여러 persona를 만들어 ensemble하는 방법이다.

<br>

## 3.2. Overall Performance

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/4f9dd30b-1c87-49d9-9e19-e35f13dc7ac6){: .align-center}

전체적으로 **meta-prompting의 성능이 굉장히 뛰어난 것**을 알 수 있다. 특히 Python Interpreter와 같은 external tool을 사용하여 더욱 높은 성능을 보였다. 특히, 휴리스틱하거나 trial-and-error 방식으로 문제를 푸는 경우에 아주 효과적이었다. 즉, meta-prompting은 **여러 expert persona를 적절히 결합하는 방식을 활용하여 더 효율적으로 문제를 해결**하였다. 한편 MGSM, Geometric Shapes 문제는 크게 효과적이지 않았다.

저자들은 이러한 성공이 다음과 같은 **3단계의 순서**를 전략적으로 이용했기 때문이라고 보았다. **1) Specialized knowledge, 2) self-collaboration, 3) implicit verification**이다. 저자들은 이것이 **집단지성(wisdom of the crowd)**과 비슷한 현상이라고 보았다. 즉, 개개인의 전문가(expert)보다 여러 비판가(critical thinker)의 의견을 모은 것이 더 효과적이라는 것이다. 또한, 여러 전문가들을 호출하여 **다방면으로 문제를 바라보도록 하여** 해결 가능성이 높아졌다고 보기도 했다.

저자들은 또한 **fresh eyes**라는 개념을 도입하여 LM의 **overconfidence** 문제를 해결했다. 이는 **multipersona prompting**과 **meta-prompting**의 중요한 차이이기도 하다. 즉, expert LM이 모든 맥락을 보지 못하고, 오로지 meta model의 prompt에만 의존하도록 하여 **새로운 insight를 제공할 가능성을 높이는 것**이다. 오히려 fresh eye를 가진 expert LM이 새로운 solution을 제공하고, error를 판별할 가능성이 높다.

<br>

## 3.3. Additional Analysis

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/1f5e5782-4243-48db-8501-ad1b9e072cd4){: .align-center}
![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/0b1a09e7-1691-4999-98b3-292c3026da5c){: .align-center}

**Python Expert 존재 여부에 따른 Expert Type 분석.** Python expert가 있는 경우에는 많은 경우 Python expert를 사용하고, 그렇지 않은 경우에는 expert를 더욱 다양하게 사용하는 것을 알 수 있다. 여러 방법으로 분석할 수 있는데, 개인적으로는 **Python expert와 같은 external tool이 중요**한 것을 간접적으로 보여주는 사례라고 생각한다.

**Task에 따른 평균 Round 수.** Word Sorting이나 Checkmate-in-One과 같은 간단한 task의 경우 대략 3~4회 안에 정답을 도출했고, Python Programming Puzzle과 같은 복잡한 task는 약 6회 정도의 round가 필요했다. 이처럼 **task의 복잡도와 round 수가 비례**하는 것을 통해 Meta Model이 얼마나 사람처럼 생각하는지 간접적으로 이해할 수 있다.

**Meta-prompting의 몇 가지 결과 분석.** 저자들은 Meta Model의 **verification 과정이 성능을 향상**시키며, 강건성을 향상시킨다는 것을 알아냈다. 그리고 Meta Model은 **overconfidence를 비교적 덜 가지고 모델이 모른다는 사실을 더 잘 인정**했다. 그리고 GPT-3.5로 실험을 진행하였을 때 성능이 하락하는 것으로 보아 **기본 모델의 성능이 높아야 Meta-prompting이 잘 작동**한다는 것을 알 수 있다.

<br>

# 4. Limitations

저자들은 **6가지의 limitation**을 언급했다. 개인적으로 좋은 분석이라고 생각한다.

1. **Cost Efficiency**: 여러 번 모델을 호출해야 하므로 비용이 많이 든다.
2. **Scalability**: 기본 모델의 성능이 일정 수준 이상이 아니면 meta-prompting이 잘 작동하지 않는다.
3. **Operational Linearity**: 모델을 순서대로 호출하므로 병렬화가 어렵다.
4. **Domain Restriction**: closed-domain system의 고질적인 한계가 있다. 다만, external resource를 사용하여 이를 어느 정도 해결할 수 있다.
5. **Information Transfer Challenge**: Meta Model이 prompt를 잘 전달하지 못하면 성능이 떨어진다. 실제로 그런 경우가 많이 있어 해결이 필요했다.
6. **Response Patterns**: 모델이 사과(apology)를 같이 출력하는 경우가 많았으며, 이 경우 성능이 낮게 측정된다. 이러한 response를 instruction tuning 등으로 해결해야 할 것이다.

<br>

# 💡 Summary

본 논문에서는 **meta-prompting**이라는 개념을 제시하였다. 이는 **single LM을 하나의 meta model과 여러 expert model로 나누어 각각의 역할을 수행**하도록 하는 것이다. 이는 일종의 집단지성을 활용한 것으로, **1) Specialized knowledge, 2) self-collaboration, 3) implicit verification**의 세 과정을 통해 성능과 강건성, 일반화 성능을 모두 향상시켰다. 아직 개선될 부분이 많은 전략으로 보이나, 앞으로 LM을 가동시키는 비용이 떨어짐에 따라 이러한 multi-step의 접근 방법이 더욱 활발하게 논의될 것으로 예상된다.

<br>

# 📃 Reference

* <a href="https://arxiv.org/abs/2403.09611" target="_blank">[24'] Meta-Prompting: Enhancing Language Models with Task-Agnostic Scaffolding</a>

<br>