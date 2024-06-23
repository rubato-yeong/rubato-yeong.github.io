---
title:  "[Summary] Analysis of Parameter-Efficient Fine-Tuning Techniques for Large Language Models"
excerpt: ""

categories:
  - Language
tags:
  - [PEFT, Summary, Analysis]

toc: true
toc_sticky: true
 
date: 2024-06-24
last_modified_at: 2024-06-24
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/5a425fad-a6b7-4329-ac55-982ec8c6b2de){: .align-center}
<br>
![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/d3a4e922-4a4f-4175-b2a2-038e26d7e9e4){: .align-center}
<br>
![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/a1a903d1-d402-4848-ade5-74badcd8e893)

<br>

# 🔍 Overview

지금까지 Language Model을 효율적으로 Fine-tuning하기 위하여 다양한 **PEFT(Parameter-Efficient Fine-Tuning)** 방법론이 제안되어 왔다. 그러나 PEFT 방법론을 어떻게 사용하는 것이 좋고, 어떤 방법론이 우위에 있는지에 대해 체계적으로 분석하는 것은 쉽지 않다. 이 글에서는 그 중에서도 PEFT 방법론들을 체계적으로 분석한 **3개의 논문을 종합하여 정리**해 PEFT에 대해 깊이 이해하고자 한다. 해당 논문들은 다음과 같다.

1. [23' Nature Machine Intelligence] Parameter-efficient fine-tuning of large-scale pre-trained language models: 100개가 넘는 NLP task에 대해 주로 **PT(Prompt Tuning), PF(Prefix Tuning), LR(LoRA), AP(Adapter)** 방법론의 **성능, 수렴성** 등을 비교하고, **이를 같이 사용(combination)한 경우**의 성능 변화도 분석하였다. 또한, **모델이 커짐에 따라 PEFT 방법론의 성능이 어떻게 변화하는지**에 대해서도 분석하였다. 그 외에도 다양한 분석을 수행하여 정보량이 아주 많은 논문이다.
2. [23' ICLR Workshop] Empirical Analysis of the Strengths and Weaknesses of PEFT Techniques for LLMs: 주로 **(IA)3, LoRA, BitFit**을 비교하고, 추가로 **어느 level의 layer에 PEFT를 하는 것이 유리한지**에 대해 분석하였다.
3. [23' EMNLP] LLM-Adapters: An Adapter Family for Parameter-Efficient Fine-Tuning of Large Language Models: **Prefix Tuning, Serial Adapter, Parallel Adapter, LoRA**에 대해 비교하고, **In-Distribution task와 Out-of-Distribution task**에 대한 성능을 분석하였다.

<br>

# 1. Paper 1 [23' Nature Machine Intelligence]

## 1.1. Performance, Convergence and Efficiency

**Setting**: 기본적으로 **pretrained T5-BASE (220M)**를 사용하였고, FT(Fine-Tuning)과 4가지 PEFT 방법인 **PT(Prompt Tuning), PF(Prefix Tuning), LR(LoRA), AP(Adapter)** 방법론을 비교하였다.
{: .notice}

이로부터 얻을 수 있었던 결론을 요약하면 다음과 같다.

### Performance Analysis

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/aa4f9b50-d4e8-4ba4-b0da-ad51a01417e1){: .align-center}

1. FT(Fine-Tuning)에 비해 tunable parameter 수가 굉장히 많이 줄어들었음에도 불구하고, **대부분의 경우에는 PEFT가 FT에 준하는 성능**을 보여주었다. 즉, 실제 PEFT의 목적을 잘 달성하고 있다는 것을 재확인한 것이다.
2. 또한, **tunable parameter를 늘린다고 성능이 향상되지는 않았다**. 따라서, downstream task의 complexity에 따라 **적절한 parameter 수를 선택**하는 것이 중요하다는 것을 알 수 있다.
3. 평균적인 성능은 **FT > LR > AP > PF > PT** 순으로 나타났다. 그러나, **LR, AP, PF**는 서로 더 잘하는 task가 달라 순위를 간단히 매기기는 어려웠다. 심지어 어떤 경우에는 **FT를 뛰어넘기도** 했다. 한편, **PT**는 대부분의 task에서 다른 3가지에 비해 **성능이 떨어졌다**.
4. 한편, **동일한 category에 속해 있는 task**의 경우 PEFT를 시행하고 zero-shot transferring performance를 측정했을 때 **성능이 꽤 좋았다**. 그러나 다른 category인 경우에는 잘 맞지 않는 경우가 많았다.

### Convergence Analysis

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/ba5aaba5-37dd-41c1-959d-15255509797d){: .align-center}

1. 수렴 속도는 **FT > AP ~ LR > PF** 순으로 나타났다. 전체적으로 Fine-tuning의 경우가 가장 수렴이 빨랐으며, **PEFT 방법들은 수렴이 느리게** 이루어졌다.
2. Tunable parameter 수의 차이에 의해 수렴 속도가 크게 달라지지는 않았다. 즉, **수렴 속도는 parameter 수보다는 다른 요인에 의해 결정**되는 것으로 보인다.

### Efficiency Analysis

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/84c868fd-aef1-4abc-853c-27279329f310){: .align-center}

1. PEFT는 back-propagation 과정의 계산을 줄여주어 **backwards time이 fine-tuning에 비해 크게 줄어들고**, **GPU memory 사용량도 줄어든다**. (여기서 BF는 BitFit이다.)
2. **Adapter**의 경우 Transformer layer에 sequencial하게 추가되므로 **inference latency**가 생긴다는 단점이 있다.

<br>

## 1.2. Combinations

**Setting**: 조합이 가능한 서로 orthogonal한 PEFT 방법들을 **조합**하여 성능을 비교하였다. 이때 사용한 방법은 **PT(Prompt Tuning), BF(BitFit), AP(Adapter)**이다.
{: .notice}

### Combination Strategy

저자들은 2가지 방법을 사용하여 PEFT 방법들을 조합하였다. 첫 번째는 **Simultaneous combination**으로, 모든 방법을 한 번에 적용하는 가장 간단한 방법이다. 두 번째는 **Sequential combination**으로, 먼저 한 방법을 적용하고, 이렇게 학습한 파라미터는 더 이상 학습하지 않고 다음 방법을 적용하는 순차적인 방법이다. 각각의 결론을 요약하면 다음과 같다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/03349bea-3495-4457-84b2-be69603847f6){: .align-center}

* **Simultaneous Combination**: **BF, AP는 서로 도움이 되었으나, PT는 다른 방법들과 조합하면 성능이 떨어졌다**.
* **Sequential Combination**: 특정 상황에서는 성능이 향상되었지만, 최적의 방법론은 찾기 어려웠다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/766150d3-cdd7-4cf0-a69d-4bae24280bd1){: .align-center}

### Generalization Gap

추가로 **Generalization Gap**에 대한 분석을 수행하였다. 이때 Generalization Gap은 **Training set과 Test set의 성능 차이**를 의미한다. 이를 통해 모델의 generalizability를 측정할 수 있다.

1. **Single PEFT**의 경우 항상 **Fine-tuning**보다 generalizaion gap이 적었다. 이는 fine-tuning이 over-parametrization되어 있다는 것을 의미한다.
2. **Combination**의 경우 **generalization gap이 증가**하였고, 이는 거의 fine-tuning의 generalization gap과 비슷했다. 즉, 이렇게 적은 파라미터로도 일부 downstream task에서는 overfitting이 일어날 수 있다는 것이고, 따라서 training set을 암기(memorization)하는 것으로도 task를 해결할 수 있는 경우 **아주 작은 모델만으로도 충분**하다는 것을 의미한다.

<br>

## 1.3. The Power of Scale

**Setting**: **모델의 크기**에 따른 성능 변화를 분석하였다. 이때 사용한 모델은 **T5-SMALL(60M), T5-BASE(220M), T5-XXL(11B)**이다.
{: .notice}

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/95a45297-befb-44b0-907a-dcf106ba9211){: .align-center}

1. **모델이 커짐에 따라 전반적인 성능은 향상된다**(scaling law). 그리고 **모델이 커질수록 PEFT 방법론 간의 성능 차이가 줄어든다**.
2. **모델이 커질수록 수렴 속도가 빨라진다**.

이는 **큰 모델일수록 상대적으로 작은 intrinsic dimensionality를 가지고 있기 때문**이다. 즉, 적은 수의 parameter로도 큰 모델이 충분히 학습 가능하기에 PEFT 방법론의 효과가 더 커지는 것이다. 한편, 모델이 커질수록 **large-scale pre-training은 PEFT 도중 local optima에 빠지는 것을 방지**하기에 수렴 속도가 빨라진다.

추가로 저자들은 last-layer tuning과 selective-module tuning의 성능을 비교하였다. 이때 **last-layer tuning**은 마지막 layer만 fine-tuning하는 것이고, **selective-module tuning**은 여러 layer의 특정 module만 fine-tuning하는 것이다. 이때 **selective-module tuning**이 **last-layer tuning**보다 성능이 약간 더 좋았다. 이로부터 prompt-tuning이나 last-layer tuning과 같이 **특정 layer만 PEFT하는 것이 좋은 방법이 아닐 수 있다**는 것을 알 수 있다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/7de9cc4c-c303-46bf-8643-95b0b0034f04){: .align-center}

<br>

# 2. Paper 2 [23' ICLR Workshop]

해당 논문에서는 **FLAN-T5-XL (1.3B)**를 사용하여 **(IA)3, LoRA, BitFit, Prompt Tuning**을 비교하였다. 이 논문의 특징은 **resource setting**을 다양하게 바꿔가며 성능 차이를 비교했다는 점인데, data scale을 **low resource** (100 points), **medium resource** (1k points), **high resource** (10k points)로 나누어 실험을 진행하였다.

<br>

## 2.1. Comparison of PEFT Techniques

### Performance Analysis

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/d696e413-dd67-4961-b8fb-378a34cbbf38){: .align-center}

Resource가 적은 상황에서는 **LoRA, BitFit**과 같은 PEFT 방법들의 성능이 좋고, Resource가 많은 상황에서는 **fine-tuning**이 가장 효과적이었다. 즉, **resource가 적은 상황일수록 PEFT 방법론이 더 효과적**이라는 것을 알 수 있다. 그러나 PEFT 내에서는 어떤 것이 가장 효과적인지에 대해서는 **일관된 결론**을 내리기 어려웠다. 참고로 가장 **memory-efficient**한 방법은 **(IA)3**이었다.

### Convergence Analysis

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/46762565-5303-431e-969c-4533bfa1b0a2){: .align-center}

위 그림에서 runtime은 수렴하는 데 걸린 시간을 의미한다. 이로부터 **low/medium resource setting**에서는 PEFT가 fine-tuning보다 **수렴이 느리다**는 것을 알 수 있다. 이는 Paper 1과 동일한 결론이다. 한편, **high resource setting**에서는 큰 차이가 없었다고 한다. 이는 fine-tuning은 쉽게 적은 데이터에 overfitting되는 반면 PEFT는 파라미터의 수가 적어 불안정하게 학습되기 때문이다. Performance와 convergence를 종합해보면, PEFT는 fine-tuning에 비해 low-resource setting에서는 **성능이 좋지만 수렴이 느리고**, 그 역도 성립한다는 것을 알 수 있다.

<br>

## 2.2. Ablation Study

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/510b863b-dc61-4c86-94b7-f9424adf5ccc){: .align-center}

논문에서는 추가로 어떠한 **layer나 component**가 가장 PEFT에 있어 중요한지 알아보기 위해 ablation study를 수행하였다. 그 결과는 다음과 같다.

1. 적용하는 **Layer, Component**를 바꾸는 과정에서 LoRA가 (IA)3보다 **robust**한 성능을 보인다.
2. **Attention Layer**에 PEFT를 적용하는 것이 성능에 굉장히 중요하다.
3. **Early layers보다 later layers를 PEFT하는 것이 더 효과적**이다. 이는 transfer learning paradigm과 비슷한 결과로 볼 수 있다. 즉, **early layers가 task-independent한 정보를 가지고 있는 반면 later layers가 task-specific한 정보를 가지고 있기 때문**에, later layers를 PEFT하는 것이 더 효과적이라는 것이다.

<br>

# 3. Paper 3 [23' EMNLP]

**Setting**: 해당 논문에서는 **LLaMA-7B**를 비롯한 다양한 LLM 모델과 **Serial Adapter, Parallel Adapter, Prefix Tuning, LoRA**를 비교하였다.

<br>

## 3.1. Placement and Configuration

### Placement Analysis

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/51880b04-aa8d-4218-8476-b510b3bd2e94){: .align-center}

먼저 논문에서는 **Serial Adapter, Parallel Adapter, LoRA**를 어느 module에 적용하는 것이 가장 효과적인지 분석하였다. 전반적으로 **Adapter 종류는 MLP에, LoRA는 모든 곳에** 적용하는 것이 효과적이었다. 이는 기존 PEFT 논문에서 주장한 것과 유사한 결과이다. 전체적인 결과는 **Serial Adapter보다는 Parallel Adapter와 LoRA가 더 효과적**이었다.

### Configuration Analysis

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/34ac44be-ad1e-4b39-bc5c-928390a00948){: .align-center}

한편 tunable parameter의 수를 얼마로 해야 하는가에 대해서는 일관적인 답을 얻기 어려웠다. 가능하다면 각각의 setting에서 parameter의 절대적인 수치를 알려주었다면 더 좋을 것 같다는 생각이 든다.

<br>

## 3.2. In-Distribution and Out-of-Distribution Analysis

여기서는 **Fine-tuning된 LLaMA-13B 모델과 ChatGPT와의 성능을 비교**하였다. 이때 **In-Distribution(ID) task**는 LLaMA-13B pretraining에 사용된 데이터셋을 활용하여 fine-tuning한 task를 의미하고, **Out-of-Distribution(OOD) task**는 pretraining에 사용되지 않은 데이터셋을 활용하여 fine-tuning한 task를 의미한다. 결과는 다음과 같다.

* **ID Task**: PEFT를 사용하면 ChatGPT와 같은 거대 모델보다 **성능이 더 좋았다**. 즉, **ID에서는 PEFT가 잘 수렴하며, 효과적**이라는 것을 알 수 있다.
* **OOD Task**: PEFT를 사용하여 어느 정도 성능을 높일 수 있었으나, **ChatGPT와 같은 거대 모델의 성능에 미치지는 못했다**. 즉, large-scale pre-training을 하지 않은 근본적인 문제는 PEFT로 잘 해결되지 않는다는 것이다. 특히 **복잡한 task일수록 이러한 문제가 뚜렷**했다.

<br>

# 💡 Summary

지금까지 3개의 논문을 종합하여 PEFT에 대해 분석해보았다. 이를 종합하여 다음과 같은 결론을 얻을 수 있다.

1. 일반적으로 **PEFT와 FT를 비교**한 결과는 다음과 같다.
   1. 대부분의 경우, **PEFT는 FT에 준하는 성능**을 발휘할 수 있다.
   2. PEFT가 FT보다 더 **Generalizability가 높지만, 수렴 속도가 느리다**.
   3. PEFT는 FT에 비해 **계산 시간과 메모리 사용량이 적다**.
   4. **데이터가 적은 경우 PEFT가 유리**하지만, 데이터가 많은 경우 FT가 더 효과적이다.
2. **PEFT 방법 간 비교**에서는 다음과 같은 결론을 얻을 수 있다.
   1. **Adapter, LoRA, Prefix Tuning, BitFit, (IA)3** 등은 각각이 가장 좋은 task가 있으며, **어떤 것이 가장 효과적인지는 task에 따라 다르다**.
   2. 한편 **Prompt Tuning**은 가장 간단한 방법이지만, **성능이 떨어지는 경우가 많다**. 다른 방법들과 조합했을 때도 성능이 좋지 않았다.
   3. **Tunable parameter의 수**는 task-specific하게 결정되어야 한다. 일반적으로, 복잡한 task일수록 **더 많은 parameter가 필요**하다.
   4. **특정 하나의 layer만 PEFT하는 것은 좋은 방법이 아니며**, 여러 layer를 PEFT하는 경우 **Early layers보다 later layers를 PEFT하는 것이 더 효과적**이다.
3. **각 PEFT 방법의 특징**을 살펴보면 다음과 같다.
   1. **Adapter**: **Inference latency**가 생긴다는 단점이 있지만, 간단한 방법임에도 성능이 꽤 좋은 편이다. **MLP Layer**에 적용할 때 가장 효과적이다.
   2. **LoRA**: **Robust**한 성능을 보이며, **Attention Layer** 또는 **모든 Layer**에 적용하는 것이 효과적이다. 가장 많이 사용되는 방법 중 하나이다.
4. **그 외 정보**를 정리하면 다음과 같다.
   1. **모델이 커질수록 PEFT 방법론의 성능이 더 좋아지며 방법론 간 차이가 줄어들고, 수렴 속도가 빨라진다**.
   2. Transfer learning, 혹은 downstream task learning 상황에서 task가 pre-training과 비슷할수록 **PEFT가 효과적이다**. 즉, OOD 상황을 PEFT가 쉽게 해결해주지 않으며, 근본적으로 데이터셋의 크기를 키우는 것이 필요하다.
   
<br>

# 📃 Reference

* <a href="https://www.nature.com/articles/s42256-023-00626-4" target="_blank">[23' Nature Machine Intelligence] Parameter-efficient fine-tuning of large-scale pre-trained language models</a>
* <a href="https://arxiv.org/abs/2304.14999" target="_blank">[23' ICLR Workshop] Empirical Analysis of the Strengths and Weaknesses of PEFT Techniques for LLMs</a>
* <a href="https://arxiv.org/abs/2304.01933" target="_blank">[23' EMNLP] LLM-Adapters: An Adapter Family for Parameter-Efficient Fine-Tuning of Large Language Models</a>

<br>