---
title:  "[24'] Towards Interpreting Visual Information Processing in Vision-Language Models"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Interpretability]

toc: true
toc_sticky: true
 
date: 2024-10-11
last_modified_at: 2024-10-11
---

![image](https://github.com/user-attachments/assets/9d2dc382-79a9-43e4-b4d9-0b79864c602e){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/user-attachments/assets/2c82db57-f4e3-4640-b65d-73e6d820e819){: .align-center}

해당 논문은 2024년 10월에 발표되었으며, LVLM이 Visual Information을 어떻게 처리하는지에 대하여 Mechanistic Interpretability 방법론을 사용하여 해석한다. 크게 3가지의 실험을 수행하는데, 각각의 내용과 결론을 요약하면 다음과 같다.

* Visual Token Ablation을 통해 Object 정보는 해당 Token Position에 **Localized**되어 있음을 보였다.
* Logit Lens를 통해 Visual Token이 Late Layer에서 **Vocabulary**에 있는 Interpretable Token Embedding으로 **Mapping**되는 것을 보였다.
* Attention Knockout을 통해 **Middle-to-Late Layer**에서 Object Visual Token으로부터 Text Token으로의 Attention Flow가 일어남을 밝혔다.

<br>

# 1. Experiment 1: Visual Token Ablation

Visual Transformer에서의 최근 연구 두 가지는 상충되는 가설을 제시했다.

1. **Object-Centric Localization**: [Joseph & Nanda (2024)](https://www.alignmentforum.org/posts/kobJymvvcvhbjWFKe/laying-the-foundations-for-vision-and-multimodal-mechanistic)에 따르면, ViT의 Late Layer는 Object Class Embedding을 가지고 있다. 이는 Object 정보가 **Localized**되어 있음을 의미한다. 물론 이 연구는 ImageNet에서만 적용되며, CLIP과는 다를 수 있다.
2. **Global Information in Register Token**: [Darcet et al. (2023)](https://arxiv.org/abs/2309.16588)에 따르면 High Norm을 가진 **Global Visual Token**이 있다. High Norm으로 인해 LVLM은 이러한 Register Token에만 의존할 가능성이 있다.

저자들은 이러한 Hypothesis를 검증하기 위해 **Visual Token Ablation** 실험을 수행했다. 실험 과정은 아래 그림과 같다.

![image](https://github.com/user-attachments/assets/c914bf68-3021-49b6-959d-2f113549611e){: .align-center}

이를 통해 여러 방면에서 Ablation의 효과를 평가하겠다는 것이다. Ablation 시에는 Embedding 값을 ImageNet Validation Split의 Visual Token의 **평균 Embedding으로 대체**하여 Ablation을 수행했다.

![image](https://github.com/user-attachments/assets/591589fe-560f-4dfb-bec2-d45e0e960129){: .align-center}

결과는 위와 같다. 대략 40개 정도의 Token을 Ablation했을 때, Random 또는 Gradient-based Ablation보다 실제 Local Object Token을 Ablation했을 때 성능이 더 떨어졌다. 이는 Object 정보가 해당 Token에 **Localized**되어 있음을 보여준다. Buffer는 Object Mask 주변의 Token을 더 Ablation한 것을 의미한다.

<br>

# 2. Experiment 2: Logit Lens

![image](https://github.com/user-attachments/assets/9e34a6ff-59ce-4af1-947b-e2a5cc81c8c1){: .align-center}

저자들은 LLM에서와 같이 Late Layer에서 Visual Token에 **Logit Lens**를 적용하여 보았다. 놀랍게도 **Interpretable Token Embedding**으로 Mapping되는 것을 확인할 수 있었다. 더욱 놀라운 것은 단순 Object 뿐만 아니라 Pattern, Texture, Background 등 다양한 정보가 Mapping되어 있다는 것이다. 몇 가지 추가로 주목할 만한 부분은 다음과 같다.

* Fig 3(c): 월, 달 정보의 경우 **중국어 또는 한국어로 Mapping**되는 특이한 경우가 있다.
* Fig 3(d): Background에 Counting을 의미하는 Token이 존재하는데, 저자들은 이를 Global Feature로 해석하였다. 이들은 Ablation을 통해 제거하여도 다른 위치에 계속 나타났다고 한다. 따라서, 저자들은 이러한 Global Feature가 ViT로부터 온 것이 아닌 Language Model이 생성한 **Artifact**라고 본다.

저자들은 이를 통해 **coarse segmentation map**을 얻을 수 있음을 보였다. 이는 여러 downstream task에서 유용할 수 있다. 예를 들어, 특정 object로의 attention을 높여 성능 향상을 꾀할 수 있다.

<br>

# 3. Experiment 3: Attention Knockout

저자들은 early layers (L1-10), early to middle layers (L5-14), middle layers (L11-20), middle to late layers (L15-24), 그리고 late layers (L21-31)에 대하여 **Attention Knockout** 실험을 수행했다. 이때 VQA에서의 성능 변화를 측정하였다.

![image](https://github.com/user-attachments/assets/44247757-bad0-464b-a6a4-4f5a2d910f15){: .align-center}

저자들은 이를 통하여 **Middle-to-Late Layer**에서 Object Visual Token으로부터 Text Token으로의 Attention Flow가 일어남을 밝혔다.

한편, [Basu et al. (2024)](https://arxiv.org/abs/2406.04236)에서 Visual Token의 마지막 부분에 Visual 정보를 요약한 뒤 Text에 넘겨준다는 가설은 적어도 Object Identification Task에서는 성립하지 않는 것으로 보인다.

<br>

# 💡 Summary

해당 논문의 내용을 간단히 요약하면 다음과 같다.

* Visual Token Ablation을 통해 Object 정보는 해당 Token Position에 Localized되어 있음을 보임
* Logit Lens를 통해 Visual Token이 Late Layer에서 Vocabulary에 있는 Interpretable Token Embedding으로 Mapping되는 것을 보임
* Attention Knockout을 통해 Middle-to-Late Layer에서 Object Visual Token으로부터 Text Token으로의 Attention Flow가 일어남을 밝힘

<br>

# 📃 Reference

* [[24'] Towards Interpreting Visual Information Processing in Vision-Language Models](https://arxiv.org/abs/2410.07149)
  
<br>