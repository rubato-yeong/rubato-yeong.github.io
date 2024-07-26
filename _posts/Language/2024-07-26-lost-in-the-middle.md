---
title:  "[24' TACL] Lost in the Middle: How Language Models Use Long Contexts" 
excerpt: ""

categories:
  - Language
tags:
  - [Hallucination]

toc: true
toc_sticky: true
 
date: 2024-07-26
last_modified_at: 2024-07-26
---

![image](https://github.com/user-attachments/assets/ccf8986e-fcf9-49ee-a51b-68f14be7d0e2){: .align-center}

<br>

# 🔍 Abstract

NLP 분야 탑 저널 중 하나인 TACL(Transactions of the Association for Computational Linguistics)에 나온 Stanford 논문이다. Google DeepMind에서 발표한 논문인 [[24' ICML-WS] Transformers need glasses! Information over-squashing in language tasks](https://rubato-yeong.github.io/language/transformers-need-glasses/)에서 소개한 **U-shape effect**에 대한 내용이 포함된 연구이기에 간단히 Skimming 해 보았다.

![image](https://github.com/user-attachments/assets/51dd4c19-0de4-4a22-a3de-676474cb6cde){: .align-center}

핵심 내용은 long-context를 LLM이 사용할 때, **초반부와 후반부의 정보에만 집중하고 중앙 부분에서는 정보를 잘 가져오지 못한다**는 것이다. 논문 제목인 "Lost in the Middle"이 이러한 현상을 잘 표현하고 있으며, 정확도 그래프가 U-shape를 그리기에 이를 **U-shape effect**라고 부른다.

<br>

# 1. Experimental Setup

## 1.1. Dataset

![image](https://github.com/user-attachments/assets/17282e3e-a177-4e47-907a-9bb8a2a0b05c){: .align-center}

저자들은 가장 간단한 Retrieval Task를 가정하기 위해 위와 같이 가장 간단한 **key-value retrieval task**를 세팅하였다.

![image](https://github.com/user-attachments/assets/c73f514e-8570-4547-b937-422072febabc){: .align-center}

또한, 현실 세계에서의 Retrieval Task를 가정하기 위해 **multi-document question answering task**를 세팅하였다.

<br>

## 1.2. Model

모델의 경우 당시 상황에서 가용할 수 있는 Open Model과 Closed Model을 모두 사용하였다. 따라서 실험 결과에서 Model이 부족하지 않고, 충분히 robust한 결과를 보여줄 것으로 기대된다.

<br>

# 2. Main Results

![image](https://github.com/user-attachments/assets/66eace45-d701-4d7e-a9b3-8a92a6aa2419){: .align-center}
<br>
![image](https://github.com/user-attachments/assets/7fef514f-a9d4-4184-a9fc-7cf456e097c4){: .align-center}

결과는 위와 같다. **U-shape effect**가 발생하는 것을 확인할 수 있었으며, 이는 **long-context를 사용할 때 중앙 부분에서 정보를 잘 가져오지 못한다**는 것을 의미한다. 그리고 context가 길면 길수록 이러한 U-shape가 잘 나타나며, 이는 long-context에서 제대로 바라보지 못하는 middle segment가 길어지기 때문으로 해석할 수 있다.

<br>

# 3. Ablation Results

## 3.1. Effect of Query-Aware Contextualization

저자들은 이것이 다른 세팅에 의해 변화하는지 확인하기 위해 **query-aware contextualization**과 **instruction fine-tuning** 상황을 확인하였다. 먼저 **query-aware contextualization**의 경우, 일반적인 QA의 경우 질문을 context 이후에 놓는 것이 일반적이지만 이렇게 되면 **질문(query)을 인지하지 못한 채 context를 decoding**하게 된다. 따라서 이러한 문제를 해결하기 위해 query를 context의 앞에 놓는 **query-aware contextualization**을 수행하여 보았다.

![image](https://github.com/user-attachments/assets/a8dca341-cf5c-4aec-a040-25750bf1eea8){: .align-center}

그 결과, 간단한 **key-value retrieval task**에서는 U-shape가 없어지고 거의 100%의 정확도를 보여주었다고 한다. 그러나 Figure 9에서 알 수 있듯이 **multi-document question answering task**에서는 U-shape가 여전히 존재하였다. 이는 real-world setting에서 **query-aware contextualization**이 큰 도움을 주지 못한다는 것을 시사한다. 이 두 task 간의 결과 차이를 명확히 설명하는 것은 굉장히 어려워서 논문에서도 따로 언급하지는 않았다. 다만, real-world setting에서는 **semantic information을 추출하는 과정**이 추가로 필요한데 이 과정의 차이가 아닐까 추측해본다.

<br>

## 3.2. Effect of Instruction Fine-Tuning

![image](https://github.com/user-attachments/assets/53ce37b7-cda1-4c4a-b9cd-9e1a9b0fd6f7){: .align-center}

한편, 저자들은 이것이 IFT(Instruction Fine-Tuning)에 의해 해결되는지 확인하기 위해 Retrieval Dataset을 제작해 훈련시켰다. 물론 성능이 향상되었으나, **U-shape effect**는 여전히 존재하였다. 이는 U-shape effect가 **long-context를 사용할 때 발생하는 본질적인 문제**라는 것을 보여준다.

<br>

# 💡 Summary

지금까지의 내용을 요약하면 다음과 같다.

* LLM은 long-context를 사용할 때, **초반부와 후반부의 정보에만 집중하고 중앙 부분에서는 정보를 잘 가져오지 못한다**. 이를 **U-shape effect**라고 부른다.
* 이는 여러 Ablation Study에도 불구하고 변하지 않았기에, **Transformer 구조의 본질적 문제**로 보인다.
* Long-context를 LLM이 온전히 활용하지 못하므로 **Reranking이나, List Truncation**과 같은 방법을 통해 이를 보완할 필요가 있다.

<br>

# 📃 Reference

* [[24' TACL] Lost in the Middle: How Language Models Use Long Contexts](https://arxiv.org/pdf/2307.03172)

<br>