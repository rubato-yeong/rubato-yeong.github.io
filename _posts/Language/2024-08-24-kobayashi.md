---
title:  "[Mechanistic Interpretability] Token-wise Attribution Analysis (History of Kobayashi)" 
excerpt: ""

categories:
  - Language
tags:
  - [Summary, Interpretability]

toc: true
toc_sticky: true
 
date: 2024-08-24
last_modified_at: 2024-08-24
---

![image](https://github.com/user-attachments/assets/4494420e-8265-470e-aa65-438e3654f581){: .align-center}

<br>

# 🔍 Abstract

**Mechanistic Interpretability**는 Model Behavior를 Model 내부의 Inner Working을 통해 설명하려는 방법론이다. 그 중에서도 **Attribution**은 Transformer Output에 각 Input이 얼마나 기여했는지를 분석하는 방법으로, 고전적으로는 **Attention Weight**이 이러한 분석에 사용되었고, 지금까지도 많이 사용되고 있다. 그러나 **Attention Weight**이 Model Behavior에 대한 설명을 충분히 하지 못하는 문제들이 제기되었고, 따라서 연구자들은 **Attention Weight**을 대체하여 더 유의미한 **Attribution**을 계산하는 방법을 연구하고 있다.

![image](https://github.com/user-attachments/assets/fcf02a38-2ee6-41ac-9bf2-cf5fa3b24a7f){: .align-center}

이번 글에서는 그 중에서도 **Token-wise Attribution Analysis**에 대해 소개한다. 이는 각 Layer에서 각 Hidden State, 즉 Token이 Layer의 최종 Output에 얼마나 기여했는지를 분석하는 방법론이다. 이 글에서는 그 중 중요하다고 생각되는 네 개의 논문을 소개하고자 한다. 이 중 세 개의 논문이 모두 *Goro Kobayashi*라는 연구자가 진행한 연구이기에, 부제로 *History of Kobayashi*라는 이름을 붙여보았다.

1. **Attention Weight**: 거의 최초로 Transformer의 Attention Weight을 분석한 논문이다. 이 논문은 **Attention Weight**을 통해 `[SEP]`과 같은 **deliminator token에 굉장히 많은 attention**이 있음을 발견하였고, 이를 실제로는 아무 효과가 없는 **no-op**로 해석하였다. (논문: [[19' ACL-WS] What Does BERT Look At? An Analysis of BERT's Attention](https://arxiv.org/abs/1906.04341))
2. **(Output-)Value-weighted Vectors**: **Attention Weight**이 Model Behavior를 설명하는 데 부족하다는 문제를 해결하기 위해 **Value-weighted Vectors**를 제안한 논문이다. 실제로 Model Output은 Attention Weight과 Value Vector가 곱해진 것이므로, 정확한 **Attribution** 계산을 위해서는 **Value-weighted Vectors**를 사용하는 것이 더 적절하다는 것이다. 실제로 **no-op**가 Value-weighted Vectors를 통해 더 잘 설명되는 것을 보였다. (논문: [[20' EMNLP] Attention is Not Only a Weight: Analyzing Transformers with Vector Norms](https://arxiv.org/abs/2004.10102))
3. **ATTNRESLN**: 저자들은 Multi-head Attention에 추가로 Residual Connection, Layer Normalization까지 고려하여 **ATTNRESLN**를 제안하였다. 이를 통해 지금까지 분석하던 Attribution이 사실은 과장되었으며, Residual Connection, Layer Normalization 등에 의해 사실은 그보다 적게 기여한다는 것을 보여주었다. (논문: [[21' EMNLP] Incorporating Residual and Normalization Layers into Analysis of Masked Language Models](https://arxiv.org/abs/2109.07152))
4. **ATBFFRESLN**: 저자들은 여기에 **Feed-Forward Layer**까지 고려하여 **ATBFFRESLN**을 제안하였다. 이를 통해 FFN이 **Input Contextualization**을 Modify하여 특정 **Linguistic Composition**을 강조한다는 점을 보여주었다. 추가로, 이러한 FFN의 역할은 이후 Residual Connection과 Layer Normalization에 의해 희석된다는 것을 보여주었다. (논문: [[24' ICLR] Analyzing Feed-Forward Blocks in Transformers through the Lens of Attention Maps](https://arxiv.org/abs/2302.00456))

<br>

![image](https://github.com/user-attachments/assets/fe57fb07-d55e-4fa9-acf4-fb54fd89424d){: .align-center}

# 1. Attention Weight [19' ACL-WS]

## 1.1. Methods

Transformer Model에서, **Layer** $l$, **Head** $h$에서 현재 **Query** $i$에 대해 **Key**들에 대한 **Attention Weight**는 다음과 같이 정의된다.

$$
\mathbf{a}_ {i} ^ {l, h} = \text{softmax} \left( \frac{(x_ i ^ {l-1} \mathbf{W}_ Q ^{l, h}) (\mathbf{X} ^ {l-1} \mathbf{W}_ K ^{l, h}) ^ T}{\sqrt{d_ h}} \right)
$$

이때 Hidden State $x_ i ^ {l-1} \in \mathbb{R}^ {d}$, Query Matrix $\mathbf{W}_ Q ^{l, h} \in \mathbb{R}^ {d \times d_ h}$, Key Matrix $\mathbf{W}_ K ^{l, h} \in \mathbb{R}^ {d \times d_ h}$이다. 저자들은 Attention Weight $a_ {i, j} ^ {l, h} = \mathbf{a}_ {i} ^ {l, h}[j]$을 Token $i$가 $j$를 **참조하는 정도**, 또는 $j$가 $i$에 **기여하는 정도**로 해석하였다. 저자들은 이를 **Global Perspective, Local Perspective**로 나누어 분석하였다.

1. **Global Perspective**: 전체적인 Attention Weight의 분포를 분석
2. **Local Perspective**: 각 Attention Head의 역할을 구체적으로 분석

<br>

## 1.2. Surface-Level Patterns in Attention

![image](https://github.com/user-attachments/assets/e1acff0d-f93c-4747-ae88-7b2b1a7b96af){: .align-center}

저자들은 **BERT** 모델을 대상으로 Attention Weight을 분석하였다. 그 결과를 요약하면 다음과 같다.

1. **Relative Position**: 일부 Head는 **Next Token, 혹은 Previous Token**에 대해 높은 Attention을 보이는 경향이 있었다. 전체 중에서 9개의 Head는 Relative Token에 대해 50% 이상의 Attention을 보였다.
2. **Separator Token**: `[SEP]`, `[CLS]`, `.`, `,` 과 같은 **Delimiter Token**에 대해 높은 Attention을 보이는 Head가 많았다. 특히 `[SEP]`에 대해 높은 Attention을 보이는 Head가 많았고, Gradient-based Analysis를 통해 `[SEP]`은 **Output에 큰 영향을 끼치지 못하는 것**으로 밝혀져 이러한 현상을 **no-op**로 해석하였다.
3. **Broad Attention**: Entropy 분석을 통해, 많은 Head는 대개 일부 Token에 집중된 Focused Attention을 보임을 확인하였다. 그러나 **Early Layer의 Head**는 **Broad Attention**을 보이는 경향이 있었는데, 이는 일종의 **bag-of-vectors representation**을 생산하는 역할을 한다고 해석했다. 또한, Query가 `[CLS]`인 경우 **Broad Attention**을 보였는데, 이는 Classification Task에 대한 전체적인 **Representation**을 생성하는 역할을 한다고 해석했다.

<br>

## 1.3. Probing Individual Attention Heads

저자들은 이후 고전적인 NLP 방법론들을 활용하여 Attention Head의 역할을 분석하였다. 그 결과, 해당하는 동사(verb)에 집중하는 Head, Pronoun에 집중하는 Head, 연결된 Object에 집중하는 Head 등 다양한 Head들이 존재함을 확인하였다. 그리고 해당 **Attention Head의 역할과 무관한 Query의 경우** 대개 `[SEP]`에 대해 **높은 Attention**을 보이는 경향이 있었다. 이는 **no-op hypothesis**를 더 강화하는 결과이다. 결과로 6개의 예시를 아래에 나타내었다.

![image](https://github.com/user-attachments/assets/5048cb69-3218-41a3-8829-f10224d38d76){: .align-center}

<br>

![image](https://github.com/user-attachments/assets/19921e15-726f-4984-bfbb-a8b68dd9e093){: .align-center}

# 2. Value-weighted Vectors [20' EMNLP]

## 2.1. Methods

![image](https://github.com/user-attachments/assets/53699bff-4acd-4789-8376-b3a32f242298){: .align-center}

저자들은 이전까지의 분석을 **Attention Weight**을 사용한다는 의미에서 **Weight-based Analysis**라고 명명하였다. 그러나 Attention Mechanism은 **Attention Weight**과 **Value Vector**의 곱으로 이루어져 있으므로, **Value Vector**를 고려하지 않는 것은 Model Behavior를 설명하는 데 부족하다는 것을 지적하였다. 따라서 저자들은 **Value-weighted Vectors**를 제안하였고, 이 Vector의 Norm을 계산하여 분석하였기에 **Norm-based Analysis**라고 명명하였다.

**Multi-head Attention** 이전의 Hidden State를 $x_ i ^ {l-1}$, 이후를 $x_ i ^ {\text{mid}, l}$이라고 할 때, 둘 사이의 관계를 다음과 같이 나타낼 수 있다.

$$
x_ i ^ {\text{mid}, l} = x_ i ^ {l-1} + \text{Attn}^ l (x_ i ^ {l-1})
$$

이때 Attention을 다음과 같이 풀어 쓸 수 있다.

$$
\begin{aligned}
\text{Attn}^ l (x_ i ^ {l-1}) &= \sum_ {h=1} ^ H \text{Attn}^ {l, h} (x_ i ^ {l-1}) \\
&= \sum_ {h=1} ^ H \sum_ {j=1} ^ N a_ {i, j} ^ {l, h} x_ j ^ {l-1} \mathbf{W}_ V ^{l, h} \mathbf{W}_ O ^{l, h} \\
\end{aligned}
$$

따라서 저자들은 다음 **Value-weighted Vectors** $\mathbf{v}_ {i, j} ^ {l, h}$를 각 Layer, Head에서 Token $j$가 $i$에 대해 **기여하는 정도**로 정의하였다.

$$
\mathbf{v}_ {i, j} ^ {l, h} = a_ {i, j} ^ {l, h} x_ j ^ {l-1} \mathbf{W}_ V ^{l, h} \mathbf{W}_ O ^{l, h} \in \mathbb{R}^ d
$$

![image](https://github.com/user-attachments/assets/d2ee4435-11e2-43ef-94c4-12b60d0c1bfd){: .align-center}

그러나, **Value-weighted Vectors**는 Attention Weight과 같은 Scalar가 아니기에 분석이 어렵다. 저자들은 단순히 **Norm** $\Vert \mathbf{v}_ {i, j} ^ {l, h} \Vert$을 계산하여 그 값을 일종의 Attribution으로 해석하였다.

<br>

## 2.2. Re-Interpretation of No-op Hypothesis

저자들은 지금까지의 **Attention Weight**이 Attribution의 요소 중 하나였을 뿐이라고 주장하며, 남은 하나의 요소인 **Value Vector**를 사용하여 **Interpretability**를 강화하였다고 주장하였다. 저자들은 그 증거로 **no-op hypothesis**를 다시 해석하였다.

![image](https://github.com/user-attachments/assets/0277b51e-e5d9-4b93-a4ee-635e98e321b7){: .align-center}

결론은, `[SEP]`과 같은 Token에서는 $\Vert \mathbf{v}_ {i, j} ^ {l, h} \Vert$이 매우 낮다는 것이다.

![image](https://github.com/user-attachments/assets/283c19b4-402d-4328-8a63-ecd1b778c0d9){: .align-center}

그리고 이는 두 요소인 Attention Weight $a_ {i, j} ^ {l, h}$과 Value Vector $f(x_ j ^ {l-1}) = x_ j ^ {l-1} \mathbf{W}_ V ^{l, h} \mathbf{W}_ O ^{l, h}$이 **상호보완적**이기 때문이다. 즉, 하나가 높으면 하나가 작아 Cancel이 일어나 실제로는 무의미한 정도의 Vector를 더한다는 것이다.

![image](https://github.com/user-attachments/assets/2a5fdce7-e5f8-4283-91e8-3e2d82e3bff8){: .align-center}

이와는 달리 `[CLS]`나 다른 토큰들은 Vector-Norm이 높게 나타났다. 이는 **no-op hypothesis**를 더 강화하는 결과이다.

<br>

## 2.3. Explanability for NMT System

저자들은 추가로 **Encoder-Decoder Setting**인 NMT(Neural Machine Translation) System에 대해 **Value-weighted Vectors**를 적용하였고, 충분한 설명력이 있는지 검토하였다.

![image](https://github.com/user-attachments/assets/cc20f5c9-db7a-49ad-a1bc-68bd580f29d9){: .align-center}

일반적으로 NMT에서는 Early Layer에서 적절한 Source Word에 집중하고, Late Layer에서는 Target Word에 집중해야 한다. 이를 바탕으로 고전적으로 **AER(Alignment Error Rate)**를 계산할 수 있으며, 낮은 AER일수록 **Explainability**가 높다고 해석할 수 있다.

![image](https://github.com/user-attachments/assets/e6fb8fa9-f419-4b5f-932a-901d23eab9fe){: .align-center}

결과적으로 **Value-weighted Vectors**를 사용한 **Norm-based Analysis**가 **Weight-based Analysis**보다 더 **Explainable**하다는 것을 보여주었다. 정성적으로는, 다음과 같이 기존에 `</s>`에만 집중하던 것처럼 보이던 것이 **Value-weighted Vectors**를 사용하면 실제로 중요한 Token에 집중하는 것을 확인할 수 있었다.

![image](https://github.com/user-attachments/assets/bf1d65ac-e417-4041-8122-be1ac7dd82df){: .align-center}

<br>

![image](https://github.com/user-attachments/assets/0a8a82d6-ca1d-4465-9ef8-159052efe717){: .align-center}

# 3. ATTNRESLN [21' EMNLP]

## 3.1. Methods

![image](https://github.com/user-attachments/assets/1d0e1604-0928-4411-a42a-99d4cb6db18b){: .align-center}

저자들은 지금까지의 연구가 **Attention Block 전체**를 고려하지 못했다고 주장하였다. 즉, 정확하게 Attention Block을 식으로 나타내면

$$
\tilde{x}_ i ^ l = \text{LN} (x_ i ^ {l-1} + \text{Attn}^ l (x_ i ^ {l-1}))
$$

이어야 하고, 이는 모두 **Linear Transformation**으로 이루어져 있어 **Linear Decompasition**이 가능하다. 따라서 저자들은 **Residual Connection, Layer Normalization**을 고려하여 해석하는 **ATTNRESLN**을 제안하였다.

먼저 Attention $\text{Attn}^ l$의 경우 이전 방법과 같이 다음과 같이 나타낼 수 있다.

$$
\text{Attn}^ l (x_ i ^ {l-1}) = \sum_ {h=1} ^ H \sum _ {j=1} ^ N a_ {i, j} ^ {l, h} x_ j ^ {l-1} \mathbf{W}_ V ^{l, h} \mathbf{W}_ O ^{l, h}
$$

여기서 Vector $f^ {l, h} (x_ j ^ {l-1})$를 다음과 같이 정의한다.

$$
f^ {l, h} (x_ j ^ {l-1}) = x_ j ^ {l-1} \mathbf{W}_ V ^{l, h} \mathbf{W}_ O ^{l, h}
$$

한편 Layer Normalization은 Input $y = \sum_ j y_j$라고 할 때 다음과 같이 정의할 수 있다.

$$
\begin{aligned}
\text{LN} (y) &= \sum _ j g_ {y} (y_ j) + \beta \\
g_ {y} (y_ j) &= \frac{y_ j - m(y_ j)}{s(y)} \odot \gamma \\
\end{aligned}
$$

다시 아래 식으로 돌아가자.

$$
\tilde{x}_ i ^ l = \text{LN} (x_ i ^ {l-1} + \text{Attn}^ l (x_ i ^ {l-1}))
$$

이제 각각 Linear Decomposition 결과를 모두 합치면 다음과 같이 나타낼 수 있다.

$$
\begin{aligned}
\tilde{x}_ i ^ l &= \text{LN} (\text{Attn}^ l (x_ i ^ {l-1}) + x_ i ^ {l-1}) \\
&= \text{LN} (\sum_ {h=1} ^ H \sum _ {j=1} ^ N a_ {i, j} ^ {l, h} f^ {l, h} (x_ j ^ {l-1}) + x_ i ^ {l-1}) \\
&= \sum_ {j=1} ^ N g_ y \left( \sum_ {h=1} ^ H a_ {i, j} ^ {l, h} f^ {l, h} (x_ j ^ {l-1}) \right) + g_ y (x_ i ^ {l-1}) + \beta \\
&= \sum_ {j \neq i} g_ y \left( \sum_ {h=1} ^ H a_ {i, j} ^ {l, h} f^ {l, h} (x_ j ^ {l-1}) \right) + g_ y \left( \sum_ {h=1} ^ {H} a_ {i, i} ^ {l, h} f^ {l, h} (x_ i ^ {l-1}) \right) + g_ y (x_ i ^ {l-1}) + \beta \\
\end{aligned}
$$

따라서 Output Representation에 Attention Block이 미치는 영향을 두 가지, 즉 (1) **Context-mixing effect**와 (2) **Preserving effect**로 나누어 분석할 수 있다.

먼저 **Context-mixing effect**는 다음과 같이 나타낼 수 있다.

![image](https://github.com/user-attachments/assets/4334235e-0973-4038-98a0-c0dd34ae6d7c){: .align-center}

한편 **Preserving effect**는 다음과 같이 나타낼 수 있다. Preserving Effect는 Self Token Attention, Residual Connection 두 가지 요소에 의해 결정된다.

![image](https://github.com/user-attachments/assets/697509d4-55f2-47d4-a9a9-ae1390c358dd){: .align-center}

저자들은 최종적으로 **context-mixing ratio**를 정의하고, 이를 통해 **Context-mixing effect**와 **Preserving effect**를 분석하였다.

![image](https://github.com/user-attachments/assets/97ee7cbc-fc42-47a9-99cc-e2f4556627b8){: .align-center}

참고로 지금까지도 사용되는 방법 중 하나인 **Attention Rollout** 논문에서는 Attention과 Residual Connection이 절반씩 기여한다고 생각하여 $r \approx 0.5$로 가정하고 모든 Layer에 대한 Attention을 Aggregation하여 해석하였다. 그러나 저자들은 **context-mixing ratio**가 **생각보다 작다**는 것을 발견하였다.

<br>

## 3.2. Low Mixing Ratio

결과적으로, 저자들은 다른 해석 방법보다 현저히 낮은 **context-mixing ratio**를 관찰하였다.

![image](https://github.com/user-attachments/assets/6df2ff0e-ed73-4a1e-887f-cc6f1868a2a5){: .align-center}

지금까지의 분석 중에 이것이 가장 **정확한** 해석이라고 가정한다면, 어떻게 이러한 결과가 나왔는지에 대한 해석이 필요하다. 위 Table을 통해 저자들은 **Residual Connection, Layer Normalization**이 Attention을 Shrink한다는 것을 알 수 있었다. 추가로, $\text{ATTN}$ 자체도 Residual Connection보다 크기가 작다는 것을 알 수 있었다. 이러한 이유로 **Attention Rollout**에서는 **Attention이 더 큰 역할**을 한다고 과대평가되었다고 주장하였다.

이러한 결과는 기존 연구들과도 연결된다.

1. **Token Identifiability**: Transformer에서 **Hidden State로부터 Initial Input Token을 예측할 수 있다**는 논문이다. 저자들은 낮은 Context Mixing Ratio로 인해 기존 Token이 잘 보존되어 이러한 결과를 얻어낼 수 있었을 것이라고 추측했다.
2. **MLM Objective**: BERT는 MLM(Masked Language Model) Objective를 통해 학습되었는데, 따라서 **Token이 잘 보존**되어야만 이러한 Objective를 잘 수행할 수 있을 것이다.
3. **Low Impact of Attention Discarding**: **Attention을 제거**하더라도 Model의 성능이 크게 떨어지지 않는다, 혹은 오히려 성능이 향상된다는 연구가 있다. 이는 **Attention의 기여도가 그렇게 크지 않다**는 결과를 지지한다.

<br>

## 3.3. Detailed Analysis

![image](https://github.com/user-attachments/assets/7db1107f-5a7d-427d-a5cb-5099724ecf36){: .align-center}

저자들은 여기서 Layer, Token Type에 따른 분석을 진행하였다. 그 결과, **Early Layer**에서 더 많은 Context Mixing이 일어난다는 점과 **Middle and Deep Layer**에서 `[MASK]` Token에서 Prediction을 위해 Context Mixing이 활발히 일어난다는 점을 확인하였다.

![image](https://github.com/user-attachments/assets/177d7052-2736-43cd-a410-b328bddad84d){: .align-center}

또한, **Token Type**에 따른 분석을 통해 Frequent Token일수록 **Preserving Effect**가 높다는 것을 확인하였다. 이는 Language Model 내부에서 **Frequent Token**이 Model Output에 많은 영향을 미치지 못하도록 하는 Discount가 있다는 것을 의미한다.

<br>

![image](https://github.com/user-attachments/assets/db7f03fe-7de1-4995-9706-4ee6fb2943fa){: .align-center}

# 4. ATBFFRESLN [24' ICLR]

## 4.1. Methods

![image](https://github.com/user-attachments/assets/1f3d122f-9a62-400b-ab5d-ffc07648d550){: .align-center}

저자들은 FFN이 지금까지 고려되지 않았다는 문제를 지적하였다. 그러나 FFN은 **Non-linear Function**이기 때문에, 지금까지와 같이 **Linear Decomposition**을 적용할 수 없다. 따라서 저자들은 2017년에 제안된 **Integrated Gradients**를 활용하여 Decomposition을 진행하였다. **Integrated Gradients**는 임의의 함수 $f: \mathbb{R}^ n \rightarrow \mathbb{R}$과 임의의 Input $\mathbf{x}^ prime = (x_ 1 ^ \prime, \cdots, x_ n ^ \prime) \in \mathbb{R}^ n$에 대해 다음과 같다. 이는 일종의 Approximation이 아닌 **정확한 값**이다.

![image](https://github.com/user-attachments/assets/92e95149-b3e1-4506-ab5e-2655473cd0e8){: .align-center}

따라서 FFN의 Non-linear Activation 직전의 값을 $\mathbf{F}_ i ^ {\text{Pre} g}$라고 하면 다음과 같이 **Decomposition**이 가능하다.

![image](https://github.com/user-attachments/assets/c5521ef6-0562-4161-aa8d-ebda76167cb9){: .align-center}

저자들은 이후 **ATTNRESLN**과 마찬가지로 **Residual Connection, Layer Normalization**을 고려하여 **ATBFFRESLN**을 제안하였다. 이를 통해 **FFN의 역할**을 분석하였다.

<br>

## 4.2. Contextualization Change via FFN

![image](https://github.com/user-attachments/assets/0c2e8450-4328-4343-a265-afa2a2aa960e){: .align-center}

저자들은 **Contextualization Change**를 계산하기 위하여 전후 벡터의 **Spearman Correlation** $\rho$를 계산하고, $1-\rho$를 **Contextualization Change**로 정의하였다. 이를 통해 분석한 결과 실제로 FFN은 **Input Contextualization**을 Modify하여 특정 **Linguistic Composition**을 강조한다는 것을 확인하였다. 그러나 이러한 결과는 **Residual Connection, Layer Normalization**에 의해 굉장히 희석된다.

![image](https://github.com/user-attachments/assets/5265af4d-b2ae-4bb6-ba3c-bd3f7c5acc7f){: .align-center}

Residual Connection의 경우 큰 값을 더하여 **Overwrite**하는 효과가 있고, Layer Normalization은 지나치게 높은 Outlier Dimension에 대한 **Shrink**를 담당한다. 

![image](https://github.com/user-attachments/assets/705a6a2c-fd00-4965-8ba5-a8ea1c072ff6){: .align-center}

<br>

## 4.3. Detailed Analysis

![image](https://github.com/user-attachments/assets/88531bcf-0f09-45b4-80a8-743cec5f03fb){: .align-center}

저자들은 여기에 추가로 FFN의 역할을 확인하기 위하여 위와 같은 **FF-amp Matrix**를 만들고 Top-k Query-key Pair를 확인하였다. 그 결과, 아래와 같이 FFN이 Subword와 같이 이어지는 단어에 대해 **Linguistic Composition**을 강조하는 것을 확인할 수 있었다. 이러한 경향은 Late Layer로 갈수록 분석하기 어려우며, 이는 실제로 FFN이 **Late Layer에서 중요하지 않거나** Abstract Representation을 사용하기에 **해석이 어렵다**는 것을 시사한다.

![image](https://github.com/user-attachments/assets/4543b761-9bb1-43fe-bbd1-e1474a9a6d15){: .align-center}

<br>

# 💡 Summary

지금까지 **Token-wise Attribution Analysis**에 대해 소개하였다. 각 논문의 핵심 주장과 방법을 정리하면 다음과 같다.

* [[19' ACL-WS] What Does BERT Look At? An Analysis of BERT's Attention](https://arxiv.org/abs/1906.04341)
  * BERT에서 처음으로 Attention Weight을 분석, 각 Head의 역할을 해석
  * 불필요한 경우 `[SEP]`과 같은 deliminator token에 높은 Attention을 보이는 경향이 있다는 것을 밝힘; 이로부터 no-op hypothesis 제시
* [[20' EMNLP] Attention is Not Only a Weight: Analyzing Transformers with Vector Norms](https://arxiv.org/abs/2004.10102)
  * Attention Weight만으로는 Model Behavior를 설명하는 데 부족하다는 문제를 제시하고, Value Vector를 고려한 Norm-based Analysis를 제안
  * No-op hypothesis를 Value Vector를 통해 다시 해석하여 Attention Weight과 Value Vector가 서로 Cancel하여 무의미한 Vector를 생성한다는 것을 밝힘
* [[21' EMNLP] Incorporating Residual and Normalization Layers into Analysis of Masked Language Models](https://arxiv.org/abs/2109.07152)
  * Attention Block 전체를 고려한 ATTNRESLN을 제안
  * Context-mixing effect와 Preserving effect를 분석하여 Attention의 Contribution이 그렇게 크지 않다는 것을 밝힘
* [[24' ICLR] Analyzing Feed-Forward Blocks in Transformers through the Lens of Attention Maps](https://arxiv.org/abs/2302.00456)
  * Non-linear Function인 FFN을 Integrated Gradients를 통해 Linearlize하고, 이를 사용한 해석 방법인 ATBFFRESLN을 제안
  * FFN이 Input Contextualization을 Modify하여 특정 Linguistic Composition을 강조한다는 것을 밝힘
  * Residual Connection, Layer Normalization에 의해 FFN의 역할이 희석된다는 것을 밝힘

<br>

# 📃 Reference

* [[19' ACL-WS] What Does BERT Look At? An Analysis of BERT's Attention](https://arxiv.org/abs/1906.04341)
* [[20' EMNLP] Attention is Not Only a Weight: Analyzing Transformers with Vector Norms](https://arxiv.org/abs/2004.10102)
* [[21' EMNLP] Incorporating Residual and Normalization Layers into Analysis of Masked Language Models](https://arxiv.org/abs/2109.07152)
* [[24' ICLR] Analyzing Feed-Forward Blocks in Transformers through the Lens of Attention Maps](https://arxiv.org/abs/2302.00456)

<br>