---
title:  "[23' EMNLP] Label Words are Anchors: An Information Flow Perspective for Understanding In-Context Learning" 
excerpt: ""

categories:
  - Language
tags:
  - [Interpretability]

toc: true
toc_sticky: true
 
date: 2024-08-13
last_modified_at: 2024-08-13
---

![image](https://github.com/user-attachments/assets/f251bc36-a005-4ee4-8974-7a8ba2af5edc){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/user-attachments/assets/69b4d7cb-5a3a-4104-b169-843f2d9a7c77){: .align-center}

EMNLP 2023에서 발표된 LLM의 In-Context Learning(ICL)에 대한 논문이다. 이 논문은 **Label Words are Anchors**라는 제목으로, ICL이 어떻게 작동하는지 이해하기 위한 **Information Flow Perspective**를 제시한다. 해당 논문은 일종의 **Summary Token**에 Few-shot sample의 정보가 aggregation되고, 이러한 Summary Token이 Attention을 통해 다음 token을 예측하는 것을 발견했다. 이를 정리하여 표현하면 다음과 같다. 

1. **Shallow Layer**: Few-shot sample의 Semantic Information이 **Label Word Token**에 aggregation된다.
2. **Deep Layer**: Label Word Token이 **Reference**로 사용되어 LLM의 최종 output을 결정한다.

그리고 이를 세 가지 방법으로 응용할 수 있음을 보여준다.

1. **Anchor Re-weighting**: Label Word Token의 중요성을 조절하여 ICL의 성능을 개선한다. 일반적으로 **Anchor(=Label Word)가 중요하기에 이를 더 강조**하는 것이 성능을 개선할 수 있다.
2. **Demonstration Compression Technique**: Label Word Token만으로도 Inference가 어느 정도 가능하기에 다른 토큰을 일부 Pruning하여 **Inference 시간을 줄일 수 있다**.
3. **Error Analysis**: Anchor는 Attention에서 Key로 사용되어 Output Prediction에 기여하므로, ICL에서 성능이 좋지 않은 경우 **Key 값의 유사도가 높아** 헷갈리는 경우가 많다는 것을 분석할 수 있었다.

응용 방법들의 경우는 생략하고, 본 글에서는 **Information Flow Perspective**에 대해 집중하여 요약하고자 한다.

<br>

# 1. Hypothesis

저자들은 먼저 **saliency technique**을 이용해 critical token interaction이 어떤 것인지 밝혀내었다. 즉, 아래 식을 계산한다.

![image](https://github.com/user-attachments/assets/e68b194f-e13f-4977-bc7b-d8c6519b9f4c){: .align-center}

여기서 $\mathcal{L}$은 loss function이다. 여기서 Attention matrix의 position을 indexing하면 $I_ l (i, j)$를 얻고, 이는 $j$번째 token으로부터 $i$번째 token으로의 information flow의 **significance**를 나타낸다. 저자들은 word token $w$로부터 label word $p_ i$, label word $p_ i$로부터 target output $q$로의 flow를 계산하였다.

![image](https://github.com/user-attachments/assets/27563068-5f11-4d46-a14c-4dca397f4323){: .align-center}

이때 label word, target word는 Sentiment analysis에서의 *Negative, Positive*와 같이 최종 output을 결정하는 중요한 token이다. 결과는 다음과 같다.

![image](https://github.com/user-attachments/assets/410b8171-91f2-4993-944d-8fb1e12cb4d1){: .align-center}

$S_ {wp}$가 shallow layer에서 높고, $S_ {pq}$가 deep layer에서 높다. 나머지 $S_ {ww}$는 거의 중요하지 않다. 이 사실로부터 저자들은 **Label Words are Anchors**라는 가설을 세우게 되었다. 즉,

1. **Shallow Layer**: Few-shot sample의 Semantic Information이 **Label Word Token**에 aggregation된다.
2. **Deep Layer**: Label Word Token이 **Reference**로 사용되어 LLM의 최종 output을 결정한다.

라는 가설이다. 원문에서는 다음과 같이 표현한다.

![image](https://github.com/user-attachments/assets/9ba0998d-8b7b-476f-ba16-24883d2a3093){: .align-center}

이를 그림으로 직관적으로 표현하면 다음과 같다.

![image](https://github.com/user-attachments/assets/22fc4c98-7d89-4420-9553-8410aa647540){: .align-center}

<br>

# 2. Shallow Layers: Information Aggregation

저자들은 위 가설을 각각 증명하고자 한다. 먼저 **Shallow Layer**에 대해 살펴보자. 저자들은 **Few-shot sample의 Semantic Information이 Label Word Token에 aggregation**된다는 것을 증명하기 위해 **Attention Knockout**을 사용한다. 즉, Word $w$로부터 label word $p$로의 attention을 0으로 만들어 $w$의 정보가 $p$로 전달되지 않도록 한다. 이를 first 5 layers, last 5 layers에 대해 수행하였다.

그리고 **Label Loyalty**와 **Word Loyalty**라는 metric을 정하고 이를 측정했다. **Label Loyalty**는 Attention Knockout 후에도 output label이 그대로 유지되는 경우 1, 그렇지 않은 경우 0이다. **Word Loyalty**는 Attention Knockout 후에 output label이 top-5 word 중에 있으면 1, 그렇지 않은 경우 0이다. 결과는 다음과 같다.

![image](https://github.com/user-attachments/assets/72beb67f-8636-4721-aa53-24daff526c35){: .align-center}

명확하게 first 5 layers ablation 결과 성능이 현저히 저하되는 것을 알 수 있다. 또한 random ablation에 비해 label word ablation의 성능이 더 떨어지는 것을 확인할 수 있다. 이는 **shallow layer에서 label word가 중요**한 역할을 한다는 것을 보여준다.

<br>

# 3. Deep Layers: Information Extraction

Deep layer에서도 마찬가지로 attention knockout을 진행하였는데, 특이하게 여기서는 **AUC-ROC**를 사용하였다. 저자들에 따르면 $A(q, p)$가 높은 것과 model prediction의 probability가 높은 것은 서로 다르다고 한다. 따라서 AUC-ROC는 이러한 문제를 보완할 수 있다고 한다. 우리의 해석에는 큰 문제가 없으며, **AUC-ROC가 높을수록 label word가 중요**하다는 것을 의미한다.

![image](https://github.com/user-attachments/assets/48737f2c-9238-4198-a4c1-44c2b9d73ebd){: .align-center}

결과는 위와 같고, **deep layer에서 label word가 중요하다**는 것을 확인할 수 있다.

<br>

# 💡 Summary

해당 논문에서는 ICL에서 **Label Words are Anchors**라는 가설을 세우고 이를 증명하였다. 이를 통해 ICL의 작동 원리를 이해할 수 있었으며, 이를 통해 세 가지 응용 방법을 제시하였다. 이러한 가설은 다음과 같이 요약할 수 있다.

1. **Shallow Layer**: Few-shot sample의 Semantic Information이 **Label Word Token**에 aggregation된다.
2. **Deep Layer**: Label Word Token이 **Reference**로 사용되어 LLM의 최종 output을 결정한다.

CVPR 2024에서 발표된 LMM decoding strategy 중 하나인 **OPERA(Over-Trust Penalty and Retrospection-Allocation Strategy)**에서 본 논문을 인용하여 anchoring token의 존재에 대해 논하고 있다. OPERA에서는 이러한 anchoring token이 충분한 text 정보를 담고 있지 못하여 **hallucination**이 일어날 수 있다고 지적한다. 단순 ICL 상황이 아니더라도 문장이 끝날 때마다 이러한 anchoring token이 발생하는 경우가 많고, 이러한 token이 중요한 역할을 하기에 내부적인 작동 원리를 더 연구할 필요가 있다.

<br>

# 📃 Reference

* [[23' EMNLP] Label Words are Anchors: An Information Flow Perspective for Understanding In-Context Learning](https://arxiv.org/abs/2305.14160)

<br>