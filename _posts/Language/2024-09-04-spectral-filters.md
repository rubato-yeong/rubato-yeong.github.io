---
title:  "[24'] Spectral Filters, Dark Signals, and Attention Sinks" 
excerpt: ""

categories:
  - Language
tags:
  - [Attention, Interpretability]

toc: true
toc_sticky: true
 
date: 2024-09-04
last_modified_at: 2024-09-04
---

![image](https://github.com/user-attachments/assets/72543201-7f17-47c4-b617-8303f4eaea8a){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/user-attachments/assets/797c4897-0b5f-404a-9617-4361473b84ff){: .align-center}

저자는 Embedding, Unembedding Matrix를 SVD로 분해한 뒤 이들의 Singular Vector를 분할하여 **Spectral Filter**라는 것을 정의하였다. 그리고 여기서 가장 덜 중요한 성분, 즉 **Tail End** 부분을 **Dark Signal**로 정의하고 해당 부분이 **Attention Sink** 현상 및 성능과 직접적인 연관이 있다는 것을 밝혔다. 수학적인 해석이 많이 포함된 논문이어서 직관적이지 않고 이해하기 어려운 부분이 있지만, 최대한 단순화하여 이해해보도록 하자.

<br>

# 1. Model Parameter Properties

![image](https://github.com/user-attachments/assets/2bc969bf-dea5-482e-b8f7-87a79f37453c){: .align-center}

LLaMA-2의 **Unembedding** $W_ u$를 **SVD(Singular Value Decomposition)**로 분해하면 다음과 같이 나타낼 수 있다.

$$
W_ u = U_ u \Sigma_ u V_ u^ T
$$

LLaMA2 13B의 경우 $W_ u \in \mathbb{R}^ {32000 \times 5120}$이므로 $U_ u \in \mathbb{R}^ {32000 \times 32000}$, $\Sigma_ u \in \mathbb{R}^ {32000 \times 5120}$, $V_ u \in \mathbb{R}^ {5120 \times 5120}$이 된다.

여기서 **Singular Value**, 즉 $\Sigma_ u$를 나열하면 다음과 같다.

![image](https://github.com/user-attachments/assets/fc2d07b2-0140-4991-a0e6-f8870f3c3e0d){: .align-center}

따라서, 대부분의 Unembedding Matrix의 역할에는 일부 Singular Value가 큰 **Right Singular Vector**가 영향을 미치게 된다. 저자들은 반대로, 가장 작은 Singular Value를 가진 5%의 RSV(Right Singular Vector)에 주목하였고, 이를 **Dark Basis**라고 하였다.

더 나아가 Model Parameter를 다음과 같이 나누어 볼 수 있다. (참고로 LLaMA2의 FFN은 다음과 같이 정의된다.)

![image](https://github.com/user-attachments/assets/8903a286-9e43-4489-ad6d-2e5e8eb05e7b){: .align-center}

* Residual Stream으로부터 정보를 **읽어오는** Parameter:
  * Attention: $W_ q, W_ k, W_ v$
  * MLP: $W_ 1, W_ 3$
* Residual Stream으로 정보를 **전달하는** Parameter:
  * Attention: $W_ o$
  * MLP: $W_ 2$

각각의 상황에 맞춰 아래와 같이 **Right Singular Vector**로 Projection하여 볼 수 있다. 이를 통해, 각 Model Parameter가 $W_ u$의 어떠한 basis 부분에 영향을 끼치는지 확인할 수 있다.

![image](https://github.com/user-attachments/assets/252a3ee6-b006-46c9-812b-bfda010a0eb2){: .align-center}

![image](https://github.com/user-attachments/assets/0b6f20b6-bf9d-4a55-a635-10817db3c1b2){: .align-center}

결과는 위와 같다. **Attention Head**의 경우 굉장히 다양한 Spectrum이 있으며, 일부 Head의 경우에는 **dark subspace**에 가장 많은 영향을 끼치는 것을 확인할 수 있다. **MLP** 역시 마찬가지이다.

<br>

# 2. Spectral Filtering

그렇다면 **Dark Subspace**의 역할은 무엇인가? 가장 간단한 설명은 **Dark Subspace**는 완전히 쓸모없고, Vocabulary Logit에 영향을 주지 않는다는 것이다. 따라서, Model은 불필요한 경우 Output을 해당 Subspace로 보내 **버릴 수 있다**. 저자는 이러한 설명이 실제로 옳은지 확인하기 위해 **Spectral Filter**를 적용해보았다. 즉, Dark Signal을 제거한 뒤에도 **NLL(Negative Log Likelihood)**이 크게 변하지 않는다면, 해당 Subspace는 정말로 **불필요**하다는 것을 의미한다.

이제 본격적으로 **Spectral Filter**를 적용해보자. 먼저, SVD를 통해 $W_ u$를 분해한 뒤, **Right Singular Vector**를 20개의 **band**로 분할한다. 이를 각각 $\lbrace V_ {u, 1}, V_ {u, 2}, \cdots, V_ {u, 20} \rbrace$라고 하자. 즉, $V_ {u, 1}$은 가장 큰 Singular Value를 가진 5%의 RSV이다. 그리고 $V_ {u, j:k} = [V_ {u, j}, V_ {u, j+1}, \cdots, V_ {u, k}]$로 정의하자. 이때 첫 번째 **Spectral Filter** $\Phi_ {u, j:k}$를 다음과 같이 정의하자.

<br>

# 💡 Summary

해당 논문의 내용을 요약하면 다음과 같다.

* 

<br>

# 📃 Reference

* [[24'] Spectral Filters, Dark Signals, and Attention Sinks](https://arxiv.org/abs/2402.09221)

<br>