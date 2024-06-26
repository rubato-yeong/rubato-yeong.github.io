---
title:  "[23' PMLR] Med-Flamingo: a Multimodal Medical Few-shot Learner" 
excerpt: ""

categories:
  - Medical
tags:
  - [Multimodal, In-context Learning]

toc: true
toc_sticky: true
 
date: 2024-06-26
last_modified_at: 2024-06-26
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/4b3fff3a-cf55-4689-96fe-f6d81c9dcb4d){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/ea78e995-29e6-4cc5-883c-ac45dabf6e1d){: .align-center}

Medical Domain은 Natural Domain과 굉장히 다르고, 특히 Medical Vision Data를 사용하는 경우 더욱 그렇다. 따라서 흔히 Medical VLM은 좋은 성능을 내기 위해 **downstream dataset에서 fine-tuning을 하는 것이 필수적**이다. 그러나 실제로 좋은 퀄리티로 대규모의 Medical dataset을 구축하는 것은 굉장히 어렵다. 따라서 이 논문에서는 Few-shot learning이 가능한 Flamingo의 장점을 살려 **Medical Few-shot Learner인 Med-Flamingo**를 제안한다. 이러한 방법으로 Med-Flamingo는 **few-shot example만으로도** medical domain downstream task를 수행할 수 있게 되었다. 추가로, 저자들은 **Visual USMLE 데이터셋**을 구축하고 최초로 medical domain VQA에서 **human evaluation**을 진행했다. 방법론은 Flamingo과 굉장히 유사하므로, 자세한 구조 설명은 [여기](https://rubato-yeong.github.io/multimodal/flamingo/)를 참조.

<br>

# 1. Method

## 1.1. Dataset

Medical domain에서 정말 중요한 부분이 **dataset**이다. 어떤 dataset으로 학습시켰는지에 따라 domain knowledge를 이해하는 수준이 천차만별이기 때문. 저자들은 두 가지 데이터셋을 사용했다.

1. MTB(Medical TextBook): 0.8M Image + 584M Token. **Interleaved image-text data** from medical textbooks
2. PMC-OA(PubMed Central Open Access): 1.6M Pair. **Paired image-text data** from open-access medical articles

<br>

## 1.2. Pipeline

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/d2660467-3366-4ec5-ad3f-3c95ba831f40){: .align-center}

**Med-Flamingo**의 pipeline은 위와 같다. 먼저 이렇게 얻은 데이터로 (1) **Pre-training** 후, (2) **Few-shot example**을 이용해 VQA를 진행한다. 그리고 (3) **Human evaluation**을 통해 성능을 평가한다. 중요한 점은 Fine-tuning 없이 Few-shot example만으로 VQA를 수행한다는 것이다.

<br>

# 2. Experiments

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/d811e04b-2f65-4831-b49c-62115dbdb7fd){: .align-center}
<br>
![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/9ed9f515-59cf-46ab-9bf7-57cc4ca30c93){: .align-center}

저자들은 **VQA-Rad**, **PathVQA**, **Visual USMLE** 데이터셋을 사용해 실험을 진행했다. 결과적으로, Med-Flamingo는 VQA-Rad에서는 원하는 성능을 얻었지만 나머지에서는 그렇지 못했다. 저자들은 각각의 이유를 다음과 같이 설명한다. (1) PathVQA의 경우, Pathology Image를 Pre-training 과정에서 많이 학습하지 못했다. (2) Visual USMLE의 경우 문제가 너무 길고 따라서 답도 길게 도출되어서 BERT만으로는 평가가 부정확하다는 것이다. 이러한 설명에도 불구하고 Evaluation 결과는 **Few-shot Learning이 정말 효과적인 것인지**에 대한 의문을 품게 한다. 적어도 **더 많고 핵심적인 데이터셋을 사용하여 pre-training을 진행**한 논문이 있다면 이를 판단하는 데 더 도움이 될 것이다.

# 💡 Summary

**Med-Flamingo**는 Medical Domain에서 **Multimodal Few-shot learning**을 처음으로 적용한 모델이다. 그러나 결과적으로는 **성능이 좋지 않았다**. 이는 **데이터셋의 부적합성 및 부족**으로 인해 아직은 generalizability가 떨어지기 때문인 것으로 보인다. 앞으로 더 많은 데이터셋을 사용하여 실험을 진행하면 더 좋은 결과를 얻을 수 있을 것이다. 한 가지 Contribution을 더 찾자면 **Visual USMLE 데이터셋을 구축**했다는 점인데, 이 데이터셋은 쉽게 활용될 수 있기에 앞으로의 Medical VLM evaluation에서 자주 등장할 것 같다.

<br>

# 📃 Reference

* [[23' PMLR] Med-Flamingo: a Multimodal Medical Few-shot Learner](https://arxiv.org/abs/2307.15189)

<br>