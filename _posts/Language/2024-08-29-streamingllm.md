---
title:  "[24' ICLR] StreamingLLM: Efficient Streaming Language Models with Attention Sinks" 
excerpt: ""

categories:
  - Language
tags:
  - [Attention, Efficiency]

toc: true
toc_sticky: true
 
date: 2024-08-29
last_modified_at: 2024-08-29
---

![image](https://github.com/user-attachments/assets/68c3e5ab-b92b-4617-9598-e62bf68117c9){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/user-attachments/assets/378e13ac-34f4-4539-8c99-46b5703da302){: .align-center}

![image](https://github.com/user-attachments/assets/d43ae232-93ff-41b8-8351-d1d1aa1261a5){: .align-center}

* **Problem**
  * LLM이 Long Sentence를 생성하는 경우, **KV Cache는 상당한 Memory**를 차지하며 일반적으로 **Training Sequence Length보다 긴 문장은 처리할 수 없다**.
  * **Window Attention**과 같이 최근의 일부에만 Attention을 주는 방법은 직관적이고 효율적이지만, 실제로는 성능이 매우 낮다.
* **Insight**
  * 저자들은 대부분의 Layer에서 Attention이 초반 Token에 몰려있는 **Attention Sink**라는 현상을 발견했고, 이것이 **Attention Mechanism의 안정화**에 중요한 역할을 한다는 것을 알게 되었다.
  * 저자들은 이것이 **softmax** 때문임을 지적하고, 반드시 **Attention의 합이 1**이어야 하기 때문에 Attention이 많이 필요하지 않을 경우 무의미한 Token에 대한 Attention이 많이 발생한다고 설명한다.
* **Method**
  * **StreamingLLM**은 Window Attention을 사용하면서 동시에 **Attention Sink를 유지**하도록 설계되었다.
* **Result**
  * 저자들은 이러한 방법을 사용해 LLM의 성능을 거의 저하시키지 않으면서 **Memory Usage를 크게 줄였다**.
  * 추가로, 저자들은 Sink 역할을 할 수 있는 **placeholder token**을 추가하여 학습에 사용하여 Streaming Performance를 향상시켰다.

<br>

# 1. Attention Sink

## 1.1. Perplexity Surge

![image](https://github.com/user-attachments/assets/9b9e0cf1-5272-4731-b98c-fc1674e52281){: .align-center}

저자들은 기존의 **Dense Attention**과 **Window Attention**의 한계를 확인하기 위해 모델의 **Perplexity**를 측정했다. 그 결과, **Window Attention**은 KV Cache Size, **Dense Attention**은 Pre-training Sequence Length를 넘어가면 **성능이 급격히 떨어지는 것**을 확인할 수 있었다.

저자들은 이것이 초반의 High Attention 때문이라고 지적한다. 즉, **Attention Sink**라는 현상은 Attention Mechanism의 안정화에 중요한 역할을 한다는 것이다. 조금 더 자세히 들어가보면, **Attention Score**는 다음과 같이 계산된다.

![image](https://github.com/user-attachments/assets/cb459934-3100-44c4-814e-f826d8a287fa){: .align-center}

여기서 첫 번째 토큰의 $e^ {x_ 1}$이 굉장히 큰 값이기 때문에, 이를 고려하지 않게 되는 순간 softmax의 특성상 **Attention Distribution**이 굉장히 달라지게 된다. 이러한 현상이 **Attention Sink**라고 불리며, 이것이 **Window Attention**의 성능을 떨어뜨리는 원인이 된다. 그렇다면 첫 번째 토큰이 높은 Attention을 받는 이유는 무엇일까? (1) **Semantic Information**이 중요하기 때문인지, (2) 단순한 **Bias** 때문인지에 대해 확인하였다.

![image](https://github.com/user-attachments/assets/f771d852-ca9a-4834-87fe-3ccd40912380){: .align-center}

저자들은 단순히 `<s>` Token을 `\n` Token으로 바꾸었다. 그럼에도 불구하고 첫 번째 토큰이 높은 Attention을 받는 것을 확인할 수 있었다. 따라서 이러한 현상은 **Bias** 때문이라고 결론지었다.

<br>

## 1.2. Attention Sink

![image](https://github.com/user-attachments/assets/44e72b7f-6c3d-4d1b-a9f7-cc061107b42a){: .align-center}

그렇다면 이러한 **Attention Sink**는 왜 생기는 것일까? 저자들은 **softmax** 때문이라고 지적한다. softmax의 특성상 **Attention의 합은 1**이 되어야 한다. 그러나 embedding이 이미 충분한 **self-contained information**을 가지고 있다면, **Attention이 많이 필요하지 않을 수 있다**. 이 경우, 무의미한 Token에 대한 Attention을 통해 실제 semantic information을 가져오지 않고 self-contained information을 유지하게 된다. 저자들은 이러한 이유로 **Attention Sink**가 발생한다고 설명한다. 이러한 설명으로 초기 Layer에서는 **Attention Sink**가 발생하지 않는 이유를 설명할 수 있다.

그렇다면 왜 이러한 **Attention Sink**가 **initial token** 몇 개에만 생기는 것일까? 학습 과정에서, Causal Mask를 사용하므로 initial token은 **모든 Token이 관찰할 수 있고**, 따라서 여기에 Attention Sink를 주는 것이 안정적이고 효율적일 수 있다고 추측할 수 있다.

![image](https://github.com/user-attachments/assets/cc32da21-6c62-4969-a06e-5e2639aa6fdf){: .align-center}

**BERT**와 같은 **Encoder-based Model**에서도 이러한 Attention Sink가 일어나는데, 대개 `[SEP]`과 같이 **항상 존재하는 Token**에 일어나는 것이 이러한 가설을 지지한다.

![image](https://github.com/user-attachments/assets/ffd88d14-4c8f-4744-8f00-78bd41f0e63d){: .align-center}

따라서 저자들은 **Initial Token 4개**를 KV Cache에 포함시켜 계산하여 Attention Sink를 고려한 Window Attention 방식인 **StreamingLLM**을 제안한다. Initial Token을 1개만 포함시켰을 때는 성능이 좋지 않았는데, 이는 학습 과정에서 동일한 **starting token**을 사용하지 않아 근처의 **several initial token**에서 attention sink가 일어나기 때문이라고 보았다.

![image](https://github.com/user-attachments/assets/ea228e28-9968-48f6-b47d-22c6bd498e97){: .align-center}

<br>

# 2. StreamingLLM

저자들은 위에서 설명한 대로 **StreamingLLM**을 구현하였다. 여기서는 기존에 제안된 **SoftMax-off-by-One** 방법을 소개하고, **Learnable Sink Token**을 추가하여 성능을 안정화한 방법을 소개한다. 먼저 **SoftMax-off-by-One** 방법에 대해 알아보자.

![image](https://github.com/user-attachments/assets/60473e9e-7bc1-4b41-975e-ae70b843dcb7){: .align-center}

이 방법은 $e^ {x_ 1}$ 대신 1을 사용하여 softmax를 계산하는 방법이다. 이렇게 하면 강제로 Attention 값이 낮아지기 때문에 Attention Sink와 유사한 효과를 낼 수 있다. 저자들은 이 방법을 **Zero Sink**라고 편의상 명명하였다.

다음으로 **Learnable Sink Token**에 대해 알아보자. 이 방법은 **Sink Token**을 추가하여 학습을 진행해 성능을 향상시키는 방법이다. 이러한 방법을 사용하면 **Attention Sink**를 고려한 **StreamingLLM**을 구현할 수 있다. 저자들은 이 방법을 **Learnable Sink**라고 명명하였다. 참고로, 추가 Training 없이 Initial 4 Token을 고려한 것을 **Vanilla**이라고 명명하였다. 이때 결과는 다음과 같고, **Learnable Sink**가 가장 좋은 성능을 보였다.

![image](https://github.com/user-attachments/assets/31b02154-6dd6-420b-abf3-475d2efde501){: .align-center}

<br>

# 3. Experiments

## 3.1. Main Results

![image](https://github.com/user-attachments/assets/b1cb102c-a764-4859-87c4-989d21c7f9aa){: .align-center}
![image](https://github.com/user-attachments/assets/3702baff-30f4-411e-9d6a-f66811b47216){: .align-center}
![image](https://github.com/user-attachments/assets/e4a259e2-8703-4782-890e-f56b4887f4b3){: .align-center}

저자들은 Learnable Sink Token을 사용했을 때 **성능이 약간 향상**되는 것을 확인하였으며, 학습 과정에서도 기존 LLM의 학습과 **큰 차이 없이 학습**이 가능하다고 설명하였다. StreamingLLM은 기존 방법들과 달리 **Memory를 줄이면서** **성능을 유지**할 수 있었다.

![image](https://github.com/user-attachments/assets/4dee3602-f313-439d-a779-60e7373c910f){: .align-center}

<br>

## 3.2. Attention Visualization

![image](https://github.com/user-attachments/assets/d835ca98-3def-45ea-a4b5-0d63626a7914){: .align-center}

저자들은 Initial Token 1개가 아닌 4개를 사용한 이유가 학습 시 고정된 **Starting Token**을 사용하지 않기 때문이라고 설명하였다. 따라서, 저자들이 Learnable Sink Token을 사용하게 된 이후에는 해당 Token이 **Attention Sink** 역할을 전담하게 되는 것을 알 수 있다.

<br>

## 3.3. Ablation Study

![image](https://github.com/user-attachments/assets/5e47744f-beee-4d95-83be-72c9b94d20ab){: .align-center}

저자들은 **Cache Size**에 대한 Ablation Study를 진행하였는데, 놀라운 점은 **Cache Size가 크다고 꼭 성능이 좋아지는 것은 아니었다**는 점이었다. 즉, 중요한 부분에만 더 집중하는 것이 오히려 성능을 높일 수 있다는 것이다.

# 💡 Summary

지금까지 **24' ICLR**에 발표된 **StreamingLLM**에 대해 알아보았다. 해당 논문의 내용을 요약하면 다음과 같다.

* Attention Sink 개념을 최초로 제안하였으며, 이것이 Attention Mechanism의 안정화에 중요한 역할을 한다는 것을 밝힘
* 이를 사용하여 개량된 Window Attention 방식인 StreamingLLM을 제안하였으며, 이를 통해 Memory Usage를 크게 줄이면서 성능을 유지할 수 있었음

<br>

# 📃 Reference

* [[24' ICLR] StreamingLLM: Efficient Streaming Language Models with Attention Sinks](https://arxiv.org/abs/2309.17453)

<br>