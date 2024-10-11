---
title:  "[24' ACL] Spectral Filters, Dark Signals, and Attention Sinks" 
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

![image](https://github.com/user-attachments/assets/fa545841-15a9-4f6b-b438-01e389b45ace){: .align-center}

LLaMA-2의 **Unembedding** $W_ u$를 **SVD(Singular Value Decomposition)**로 분해하면 다음과 같이 나타낼 수 있다.

$$
W_ u = U_ u \Sigma_ u V_ u^ T
$$

LLaMA2 13B의 경우 $W_ u \in \mathbb{R}^ {32000 \times 5120}$이므로 $U_ u \in \mathbb{R}^ {32000 \times 32000}$, $\Sigma_ u \in \mathbb{R}^ {32000 \times 5120}$, $V_ u \in \mathbb{R}^ {5120 \times 5120}$이 된다. 여기서 **Singular Value**, 즉 $\Sigma_ u$를 나열하면 다음과 같다.

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

<br>

## 2.1. Spectral Filter: $\Phi_ {u, j:k}$

![image](https://github.com/user-attachments/assets/797c4897-0b5f-404a-9617-4361473b84ff){: .align-center}

이제 본격적으로 **Spectral Filter**를 적용해보자. 먼저, SVD를 통해 $W_ u$를 분해한 뒤, **Right Singular Vector**를 20개의 **band**로 분할한다. 이를 각각 $\lbrace V_ {u, 1}, V_ {u, 2}, \cdots, V_ {u, 20} \rbrace$라고 하자. 즉, $V_ {u, 1}$은 가장 큰 Singular Value를 가진 5%의 RSV이다. 그리고 $V_ {u, j:k} = [V_ {u, j}, V_ {u, j+1}, \cdots, V_ {u, k}]$로 정의하자. 이때 첫 번째 **Spectral Filter** $\Phi_ {u, j:k}$를 다음과 같이 정의하자.

$$
\Phi_ {u, j:k} = V_ {u, j:k} V_ {u, j:k}^ T
$$

이는 일종의 **Projection Vector**로 $V_ {u, j:k}$가 span하는 subspace로의 **Projection**을 나타낸다. 만약 **Dark subspace**로의 Projection을 얻고 싶다면, 벡터에 $\Phi_ {u, 20:20}$를 곱하면 된다. 지금까지는 Unembedding $W_ u$에 대해 전개했다면, **Embedding** $W_ e$에 대해서도 동일한 방법을 적용할 수 있다. 저자들은 Unembedding의 dark subspace를 **U-dark**, Embedding의 dark subspace를 **E-dark**라고 명명하였다. 각각 $\Phi_ {u, 20:20}$, $\Phi_ {e, 20:20}$를 통해 해당 성분을 얻을 수 있다.

<br>

## 2.2. Spectral Filter: $\Psi_ k$

![image](https://github.com/user-attachments/assets/30e325e7-423b-4bf4-8121-bcd81ba0e958){: .align-center}

이제 두 번째 **Spectral Filter** $\Psi_ k$를 소개한다. 이는 $W_ u$와 $W_ e$의 singular vector를 결합한 것으로, 두 Matrix에서 동시에 **Dark Subspace**에 해당하는 부분을 제거하는 역할을 한다. 이를 통해 **Dark Signal**을 제거한 뒤의 결과를 얻을 수 있다.

$$
\Psi_ k = (I - \Phi_ {e, (k+1):20} \Phi_ {u, (k+1):20})
$$

<br>

## 2.3. Spectral Filter Analysis

![image](https://github.com/user-attachments/assets/e21edf65-cfb1-47fe-9dde-370dce7905df){: .align-center}

저자는 (1) **MLP Output**, (2) **Residual Stream**에 각각 Spectral Filter를 걸어 NLL을 계산한 결과를 분석하였다. 결과는 아래와 같다.

<br>

### MLP

![image](https://github.com/user-attachments/assets/a0c7615a-65f4-4d01-ad53-b9b30ecc738f){: .align-center}
![image](https://github.com/user-attachments/assets/c152e6e7-7cff-48e1-a41b-733473e1e935){: .align-center}

rnd는 **Random**을 의미하며 random orthonormal basis를 선택한 경우를 의미한다. 이 경우 당연하게도 **basis vector의 수가 많아지면 NLL이 감소**한다. 한편 $\Phi_ e$의 경우 **Embedding**에 대한 Spectral Filter를 적용한 것이며, 엄밀하게는 $\Phi_ {e, 1:k}$에서 $k$를 증가시키며 NLL을 계산한 것이다. **13B/L0/MLP**를 보면 마지막까지 $\Phi_ e$의 NLL이 높은 것을 볼 수 있는데, 이는 L0의 MLP가 **E-dark subspace**를 통해 **Logit Probability에 영향을 주고 있다**는 것을 의미한다. $Phi_ u$의 경우도 마찬가지이다. **13B/L3/MLP**를 보면 마지막까지 $\Phi_ u$의 NLL이 높은 것을 볼 수 있는데, 이는 L3의 MLP가 **U-dark subspace**를 통해 **Logit Probability에 영향을 주고 있다**는 것을 의미한다. 이러한 현상은 **LLaMA2 13B L0, L3 뿐만 아니라 7B/L1, 70B/L2, L8에서도 관찰**되었다.

![image](https://github.com/user-attachments/assets/985a33e5-327e-4762-a1c4-31085e511307)

<br>

### Residual Stream

![image](https://github.com/user-attachments/assets/f9f3af30-d4d2-405d-ba19-5c7ce497831c){: .align-center}

Residual Stream에 대해 관찰한 결과는 위와 같다. 어느 정도 **MLP와 유사한 형상**을 보인다. 즉, **L3 이후 갑자기 NLL 값이 증가**하며, 이는 13B/L3/MLP에서 dark space를 통해 interaction한 것과 일치한다. 즉, 다음과 같이 생각할 수 있다. Layer 3에서 MLP는 Dark Subspace를 통해 **Logit Probability에 영향을 주고 있으며**, 이를 Spectral Filter로 제거할 경우 NLL에 심각한 문제가 생겨 **성능이 급격히 떨어진다**. 위 그림을 보면, 결국 **Dark Subspace**가 Prjection Basis에 포함되는 **마지막 Column**에서만 성능이 회복되는 것을 확인할 수 있다. LLaMA2-7B의 경우는 아래와 같으며, 2번째 Layer부터 값이 급증하는 것은 2번째 Layer의 MLP에서 **Dark Subspace**에 영향을 주었기 때문이다.

![image](https://github.com/user-attachments/assets/ff477b42-cca3-4ee9-870a-1add9d3dd57c){: .align-center}

따라서, **Dark Signal**은 단순히 쓸모없는 부분을 버리는 역할이 아니라 **Perplexity**를 줄이는 데 굉장히 중요한 역할을 하고 있음을 알 수 있다. 그렇다면 이것이 의미하는 바가 무엇일까?

<br>

# 3. Attention Sink

## 3.1. Interpretation as Dark Signal

**Attention Sink**는 [StreamingLLM](https://rubato-yeong.github.io/language/streamingllm/)에서 다룬 바 있다. 특정 **Attention Head**가 해당 context에서 활성화될 필요가 없는 경우, 해당 Attention Head의 Attention은 **Probability Distribution에 가장 적은 영향**을 끼치는 Sink Token에 집중되게 된다. 보통 이러한 Token은 `<s>`와 같은 **BoS Token**이 된다. 이를 Spectral Filter 관점에서 보면 다음과 같다.

![image](https://github.com/user-attachments/assets/2c8283d3-5dcc-4321-a2f7-41f512e18e10){: .align-center}

위 그림은 **BoS Token**에 대한 **U-Dark Subspace**로의 Projection의 Norm 값, 그리고 이것에 orthogonal한 **U-Light Subspace**로의 Projection의 Norm 값을 나타낸 것이다. Layer 3를 보면 갑자기 **U-Dark Subspace**로의 Projection의 Norm 값이 급격히 증가하는 것을 볼 수 있으며, 이는 **MLP**에 의한 것임도 알 수 있다. 따라서 다음과 같이 해석할 수 있다. L3의 MLP는 U-dark에 가까운 일종의 **large-norm vector**를 만들고, 이는 **Bos Token**의 Residual Stream에 추가된다. 이 vector는 **U-Dark**가 대부분이기 때문에 probability distribution에 큰 영향을 끼치지 않으며, 따라서 다른 token들은 Bos Token에 많은 attention을 주게 된다. 즉, 이러한 vector가 **sink가 필요한 Head**에 대하여 일종의 **attention collector** 역할을 하게 된다. 그리고 **Late Layer**에서는 MLP와 Attention에 의해 이러한 **vector가 지워지고**, next token prediction에 필요한 vector를 다시 만들게 된다.

<br>

## 3.2. Swap Experiment

![image](https://github.com/user-attachments/assets/02bf2c1c-1dd2-47ae-a415-22a8c1b85086){: .align-center}

저자는 아까와 같이 Spectral Filter를 적용하는데, 대신 **Dark Signal**을 제거하는 것이 아니라 서로 바꾸는 **Swap Experiment**를 수행하였다. 즉, 아예 서로 다른 Sample에 대하여 동일한 Position Token의 Dark Signal을 서로 교환한 뒤 NLL을 계산한 것이다. 또한, 저자는 **Bos Token에만 Spectral Filter를 적용**하여 보았다. 

![image](https://github.com/user-attachments/assets/4a62ef7c-a8da-4685-9bd3-03deb5940b43){: .align-center}

Swap Experiment에서는 Step Decrease가 나타나지 않았고, 한편 Bos Token에만 Filter를 적용해도 Step Decrease가 나타났다. 이 두 실험을 통해 **Dark Subspace**가 **Attention Sink**에 대한 중요한 역할을 한다는 것을 확인할 수 있다.

<br>

# 4. Sink-preserving Spectral Filtering

## 4.1. Spectral Filter $\Omega_ {u, k}$

![image](https://github.com/user-attachments/assets/8449eede-437f-41b1-a8e8-e737a8f37920){: .align-center}

이제 **Dark Subspace**가 중요하다는 것을 알았으니, **Sink-preserving Spectral Filtering**을 사용해보자. 이는 일반적으로 사용하던 $\Phi_ {u, 1:k}$ 대신 Dark Subspace Basis를 추가한 것이다.

$$
\Omega_ {u, k} = ([V_ {u, 1:k}; V_ {u, 20:20}])([V_ {u, 1:k}; V_ {u, 20:20}])^ T
$$

이를 적용하면 더 이상 **Dark Signal**이 사라지지 않으며, **Sink-preserving**이 가능해진다. 이런 경우 아주 기본적인 예상대로, Large Singular Value를 가진 **Singular Vector 일부**만 있어도 NLL이 크게 감소한다.

![image](https://github.com/user-attachments/assets/50dfdb58-31a3-4b4c-bde5-21fb423110f5){: .align-center}

![image](https://github.com/user-attachments/assets/d87ed52d-dafe-495a-9487-9d38e2e46043){: .align-center}

<br>

## 4.2. Generation Quality Analysis

![image](https://github.com/user-attachments/assets/f3bdbbf4-1d6c-46fc-81cd-34e962235098){: .align-center}

그렇다면 Attention Sink가 일어난 이후 **Spectral Filter**를 적용하면 실제 Generation Quality에는 어떠한 영향을 미칠까? 지금까지는 NLL이라는 간접적인 지표로 분석했다면, 여기서는 정성적인 분석을 진행한 것이다. 저자는 $\Psi$ Filtering을 사용하였을 때 많은 경우 **Repetitive Pattern**이 나타났음을 확인했다. 이는 많은 Attention Head가 **Copy Head**로서의 역할을 하기 때문인데, 원래라면 Attention Sink로 인해 아무런 역할도 하지 않아야 할 Head가 $\Psi$ Filtering을 통해 Dark Subspace를 제거하면서 **강제로 Copy 역할을 지나치게 수행**하게 된다는 것이다. 반면, Dark Subspace를 보존한 $\Omega$ Filtering을 사용하면 이러한 현상이 사라지며, **Generation Quality가 유지**된다.

<br>

# 5. Dark Signals and Attention Bars

Attention Matrix를 보면 **Bos Token**이 아니더라도 Attention Sink가 발생하는 것처럼 보이는 부분이 있다. 저자는 이를 **Attention Bar**라고 표현하였으며, **Bos Token**과 유사한 Residual Stream을 가지고 있기 때문에 **Attention Sink**가 발생하는 것으로 추정하였다. 이를 확인하기 위해, Attention Bar를 판정하는 **HMLV(High Mean Low Variance) Token**을 정의하였다. 즉, 평균적으로 Attention 값이 높고 Query에 따라 그 값이 크게 변하지 않는 Token을 Attention Bar로 보기로 한 것이다. 이 Threshold는 저자가 휴리스틱한 기준으로 결정하였다. 이렇게 결정한 **HMLV Token**에 대하여 **U-Dark Ratio**를 다음과 같이 정의하여 분석했다.

$$
udr(h^ l _ t) = \frac{\Vert \Phi_ {u, 20:20} h^ l _ t \Vert}{\Vert (I - \Phi_ {u, 20:20}) h^ l _ t \Vert}
$$

![image](https://github.com/user-attachments/assets/76cd1503-df3b-499d-aef8-f28a1dd723f2){: .align-center}

그 결과, HMLV Token은 Intermediate Layer부터 **U-Dark Ratio**가 높아 실제로 **Attention Sink**와 밀접한 관련이 있다는 것을 확인할 수 있었다. 또한, 이러한 Token은 다른 Token보다 BoS와의 **Similarity가 높았다**.

<br>

# 💡 Summary

해당 논문의 내용을 요약하면 다음과 같다.

* Unembedding Matrix를 SVD로 분해하여 Spectral Filter로 나타내는 새로운 분석 방법을 제안함
* Singular Value가 매우 낮은 Right Singular Vector가 Span하는 Dark Signal이 Attention Sink와 Generation Quality에 중요한 역할을 한다는 것을 밝힘
* HMLV(High Mean Low Variance) Token을 통해 Attention Bar를 정의하고, 이 Token이 Bos Token과 같이 Dark Subspace를 가지고 있음을 확인함

<br>

# 📃 Reference

* [[24' ACL] Spectral Filters, Dark Signals, and Attention Sinks](https://arxiv.org/abs/2402.09221)

<br>