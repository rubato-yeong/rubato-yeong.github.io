---
title:  "[Summary] Recent Contrastive Decoding Variants (2)" 
excerpt: ""

categories:
  - Language
tags:
  - [Summary, Contrastive Decoding]

toc: true
toc_sticky: true
 
date: 2024-08-16
last_modified_at: 2024-08-16
---

![image](https://github.com/user-attachments/assets/fc055040-6e6f-4d63-a569-1091eead7a92){: .align-center}
<br>
![image](https://github.com/user-attachments/assets/4563d144-fbd8-4775-b93b-f1fd874d6f4a){: .align-center}

<br>

# 🔍 Abstract

이전 포스트에 이어 [Contrastive Decoding](https://rubato-yeong.github.io/language/cd/)을 활용한 최근 연구들을 소개한다. 특히, 이번에는 **CAD(Context-aware Decoding)**을 발전시킨 두 가지 논문을 소개하며 **Contrastive Decoding을 어떻게 활용하는 것이 적절할지**에 대해 생각해보고자 한다. CAD의 방법론의 경우 [이전 리뷰](https://rubato-yeong.github.io/language/cd-variants-1/)를 참고하자. 각 논문의 핵심 주장과 방법을 정리하면 다음과 같다.

1. **Enhancing Contextual Understanding in Large Language Models through Contrastive Decoding**: CAD에서는 contextual knowledge, 즉 **non-parametric knowledge를 parametric knowledge와 대조**하여 amplification하였다. 그러나 저자들은 contextual knowledge 또한 불완전하기 때문에 **non-parametric knowledge와 parametric knowledge가 모두 필요하다고 주장**하며 새로운 방식의 contrastive decoding을 제안한다. 저자들은 이를 **Multi-Input Contrastive Decoding**이라고 명명하였다.
2. **Adaptive Contrastive Decoding in Retrieval-Augmented Generation for Handling Noisy Contexts**: Context가 Retrieval 등으로 인해 Noisy한 경우, **오히려 Context가 최종 답변에 방해가 될 수 있다**. 저자들은 이러한 문제를 해결하기 위해 Context에 의해 답변이 혼란스러워지는 정도를 Entropy로 측정하고, Entropy에 따라 **Contrastive Decoding의 가중치를 조절**하는 **Adaptive Contrastive Decoding**을 제안한다.

<br>

# 1. Multi-Input Contrastive Decoding [24' NAACL]

## 1.1. Methods

![image](https://github.com/user-attachments/assets/38c604f7-21b7-4572-9688-deee0ecc15fb){: .align-center}

저자들은 non-parametric knowledge만 중요시했던 **CAD(Context-aware Decoding)**과는 달리 **parametric knowledge와 non-parametric knowledge가 모두 중요하다고 주장**한다. 따라서 relevant context $\mathbf{z}_ t ^ +$와 irrelevant context $\mathbf{z}_ t ^ -$를 대조하여 **non-parametric knowledge**를 사용하고, 여기에 context가 없는 경우 $\mathbf{z}_ t$를 사용하여 **parametric knowledge**를 사용한다. 이를 **Multi-Input Contrastive Decoding**이라고 명명하였다. 이를 식으로 나타내면 다음과 같다.

![image](https://github.com/user-attachments/assets/d543ca4a-0a3d-4c5a-9dd3-c6751bd0eded){: .align-center}
![image](https://github.com/user-attachments/assets/a031efea-d0d3-4fb9-a5de-7c37a01703cf){: .align-center}

또한, **Dynamic** $\alpha$를 사용하였다. 먼저 저자들은 Parametric Knowledge를 사용할 때의 **confidence** $C$와 Non-parametric Knowledge를 사용할 때의 **confidence** $C_ R$을 계산했다.

![image](https://github.com/user-attachments/assets/75a52d98-e3fd-48d2-9ab9-14ea0c392c1a){: .align-center}
![image](https://github.com/user-attachments/assets/62214c40-677b-4cbc-a94d-7e7a52a7cc34){: .align-center}

만약 Parametric Knowledge의 confidence가 더 높은 경우, non-parametric knowledge를 덜 사용하기 위해 $\alpha$를 줄이고, 반대의 경우에는 $\alpha$를 늘리도록 세팅하였다. 괜찮은 아이디어지만, $\alpha$의 **성능 scale을 정확히 파악하지 않고 0과 1 사이의 값으로 한정**한 것은 아쉬운 점이다. 실제로 이후 실험에서 $\alpha = 1$ 부근이 가장 좋은 성능을 보여주었다.

![image](https://github.com/user-attachments/assets/3805e01e-0734-4a82-9b4a-309b14f69d05){: .align-center}

<br>

## 1.2. Results

![image](https://github.com/user-attachments/assets/f68e0452-6c1b-4172-b078-a1bb9d9eae2e){: .align-center}

결과는 **Dynamic** $\alpha$를 사용한 **Multi-Input Contrastive Decoding(Ours-D)**가 다른 방법들보다 성능이 우수하다는 것을 보여준다. OPT 모델에서는 Dynamic $\alpha$를 사용한 것보다 Fixed $\alpha$를 사용한 Ours-F가 더 좋은 성능을 보여주었다.

![image](https://github.com/user-attachments/assets/412fe50a-61ab-4ea8-bac8-7c8bde9f5fef){: .align-center}

저자들은 주장을 강화하기 위해 특히 **knowledge conflicts**, 즉 **non-parametric knowledge와 parametric knowledge가 충돌하는 상황**에서 Multi-Input Contrastive Decoding의 성능을 보여주었다. 여기서 CAD는 non-parametric knowledge만을 강조하기에 오히려 성능이 떨어지는 반면 Multi-Input Contrastive Decoding은 더 좋은 성능을 보여주었다.

<br>

# 2. Adaptive Contrastive Decoding [24']

## 2.1. Methods

**ACD(Adaptive Contrastive Decoding)** 또한 **Context가 불완전할 수 있다는 가정**으로부터 시작한다. 저자들은 Context가 얼마나 최종 답변을 Noisy하게 만드는지, 즉 혼란스럽게 만드는지에 따라 Context의 가중치를 조절한다. 먼저 CAD와 유사하게, 다음과 같은 식으로 Decoding을 수행한다.

![image](https://github.com/user-attachments/assets/68d8673f-ac4a-4fea-b11a-c1e4064cef09){: .align-center}

여기서 $\alpha$ 값을 조절하기 위해, Context가 주어진 경우의 Probability Distribution의 Entropy $H(Y_ t ^ c)$와 Context가 주어지지 않은 경우의 Probability Distribution의 Entropy $H(Y_ t)$를 계산한다. 이를 통해 **Context가 주어진 경우의 Entropy가 더 높다면 Context가 주어진 경우의 가중치를 줄이고, 반대의 경우에는 가중치를 늘린다**. 이를 식으로 나타내면 다음과 같다.

![image](https://github.com/user-attachments/assets/60ee31b1-3a66-4960-a9c9-53a71685a888){: .align-center}

<br>

## 2.2. Results

![image](https://github.com/user-attachments/assets/3be6d549-7def-40fc-8a14-3ee1b485f518){: .align-center}

저자들은 기존 CAD, 그리고 위에서 소개한 MICD(Multi-Input Contrastive Decoding)와 비교하여 **ACD(Adaptive Contrastive Decoding)**의 성능을 측정하였다. 결과는 위와 같고, **Context가 Noisy한 경우에 특히 ACD가 더 좋은 성능을 보여주었다**. 그렇지만 MICD와 성능 차이가 그렇게 크지는 않았다.

MICD 논문에서도 이미 **Dynamic** $\alpha$를 적용했기에, 저자들은 **Confidence**를 이용하여 계산한 MICD의 $\alpha$보다 **Entropy**를 이용하여 계산한 ACD의 $\alpha$가 더 우월하다는 것을 보여주어야 했다. 따라서 저자들은 **Context Noisiness**와 **Adaptive Weight** $\alpha$ 사이의 상관관계를 분석하였다. 구체적으로는 Gold Standard Context와 Noisy Context의 두 가지 군을 두고 Classification 문제를 푼다고 생각하고 $\alpha$의 threshold에 따른 **AUROC**를 계산하였다. 결과는 다음과 같다.

![image](https://github.com/user-attachments/assets/54cc3f9d-3fef-4d9c-a975-394d1dd17de5){: .align-center}

위 결과를 통해 ACD의 $\alpha$가 **Context Noisiness**와 **Adaptive Weight** 사이의 관계를 더 잘 반영한다는 것을 보여주었다.

![image](https://github.com/user-attachments/assets/e4e039d2-81b0-4349-a37d-0f483b6fea2b){: .align-center}

한편, Fixed $\alpha$를 사용하는 경우의 Ablation을 진행하였는데 어떠한 경우에도 **Adaptive CD를 사용하는 것이 더 좋은 성능**을 보여주었다.

<br>

# 💡 Summary

본 글에서는 Retrieval과 같이 **새로운 Context가 주어진 상황**에서 어떻게 Contrastive Decoding을 활용할 수 있는지에 대해 소개하였다. 각 논문의 내용들을 간략히 요약하면 다음과 같다.

* [[24' NAACL] Enhancing Contextual Understanding in Large Language Models through Contrastive Decoding](https://arxiv.org/abs/2405.02750)
  * Non-parametric knowledge만 중요시했던 CAD와는 달리 parametric knowledge와 non-parametric knowledge가 모두 중요하다고 주장하며 Multi-Input Contrastive Decoding을 제안함
  * Dynamic $\alpha$를 사용하여 Parametric Knowledge와 Non-parametric Knowledge의 confidence에 따라 Contrastive Decoding의 가중치를 조절함
* [[24'] Adaptive Contrastive Decoding in Retrieval-Augmented Generation for Handling Noisy Contexts](https://arxiv.org/abs/2408.01084)
  * Context가 Noisy한 경우에 Context가 최종 답변에 혼란을 줄 수 있다는 가정 하에 혼란을 주는 경우 가중치를 줄이는 Adaptive Contrastive Decoding을 제안함

<br>

# 📃 Reference

* [[24' NAACL] Enhancing Contextual Understanding in Large Language Models through Contrastive Decoding](https://arxiv.org/abs/2405.02750)
* [[24'] Adaptive Contrastive Decoding in Retrieval-Augmented Generation for Handling Noisy Contexts](https://arxiv.org/abs/2408.01084)

<br>