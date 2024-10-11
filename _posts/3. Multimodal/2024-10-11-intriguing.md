---
title:  "[24'] Intriguing Properties of Large Language and Vision Models"
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

![image](https://github.com/user-attachments/assets/080b4e2a-9ef3-45d9-ab27-7658c1f4092e){: .align-center}

<br>

# 🔍 Abstract

2024년 10월에 발표된 논문으로, MLLM이 어떻게 작동하는지에 대한 다양한 실험을 수행하였다. 이를 통해 알게 된 내용을 정리하면 다음과 같다.

* Visual Token은 **Localized Visual Information**을 가지고 있으며 어느 정도 **Permutation-invariant**한 성질을 가지고 있다. 이는 CLIP과 같은 Visual Encoder에 의해 학습된 Visual Token이 **Localized Semantic Information**을 가지고 있음을 의미한다.
* Fine-tuning에 의해 Visual Encoder의 기능을 MLLM이 **완전히 Transfer받지 못하는 경향**이 있다.
* **Early Layer**에서 **Visual Information Processing**이 일어나고, 이후에는 **Text Interpretation**이 더 중요하다.

본 리뷰에서는 이 중 몇 가지 핵심적인 실험들을 다루고자 한다.

<br>

# 1. Property of Visual Token

![image](https://github.com/user-attachments/assets/dd1e49e2-8ed1-4306-b815-c903d752928e){: .align-center}

첫째로 저자들은 Visual Token이 **Localized Visual Information**을 가지고 있음을 보이기 위하여 특정 위치의 Visual Token을 제거하고 성능이 얼마나 저하되는지를 측정하였다. 만약 Global Information을 가지고 있다면 특정 위치의 Visual Token을 제거하더라도 성능이 크게 떨어지지 않을 것이다. 그러나, 실제로는 특정 위치의 Visual Token을 제거하면 성능이 크게 떨어졌다. 이는 Visual Token이 **Localized Semantic Information**을 가지고 있다는 것을 의미한다.

![image](https://github.com/user-attachments/assets/1d64cb91-1875-4a21-812a-cbd24de3f72b){: .align-center}

둘째로 저자들은 Visual Token이 **Permutation-invariant**한 성질을 가지고 있는지를 측정하였다. 이를 위해 Visual Token을 무작위로 섞은 뒤, 성능이 얼마나 떨어지는지를 측정하였다. 저자들은 위 결과를 통해 Visual Token이 어느 정도 **Permutation-invariant**한 성질을 가지고 있다는 것을 보였다. 다만, MathVista, ChartQA와 같이 Mathematical Reasoning이 필요한 Task에서는 이러한 성질이 적용되지 않을 수 있다. 즉, 적어도 Natural Domain에서는 Visual Token이 **Permutation-invariant**한 성질을 가지고 있다고 생각할 수 있다. Image 자체를 섞은 것이 아닌, CLIP과 같은 Visual Encoder를 통과한 Visual Token을 섞었음에 주의하자.

<br>

# 2. Importance of Modality and Layer

저자들은 **Importance Score**를 정의하여 해당 Token이 얼마나 중요한지를 측정하였다. 정의는 다음과 같다.

![image](https://github.com/user-attachments/assets/fc70febf-a347-41ec-8116-d47dc19f26dc){: .align-center}

이는 *sharpness of minima*를 응용한 것으로, perturbation이 일어났을 때 성능이 많이 떨어질수록 Importance Score가 높은, 중요한 Token이라고 볼 수 있다. 저자들은 Image의 Importance $\mathcal{I}_ I$와 Text의 Importance $\mathcal{I}_ T$를 측정하였다. 아래 그림은 그 중 $\mathcal{I}_ I$와 $\mathcal{I}_ I / \mathcal{I}_ T$를 나타낸 것이다.

![image](https://github.com/user-attachments/assets/c8cab1b4-813b-4554-a3ad-de59397fe235){: .align-center}

이를 통해 Early Layer에서는 Image Processing이 중요하며, 이후에는 Text Interpretation이 더 중요해진다는 것을 알 수 있다. 다만, **Importance Score**라는 Metric을 저자들이 처음으로 제안한 것이기 때문에, 이것이 정말 의미가 있는지에 대한 논의가 필요하다.

<br>

# 💡 Summary

해당 논문의 내용을 간단히 요약하면 다음과 같다.

* Visual Token은 Localized Information을 가지고 있으며 어느 정도 Permutation-invariant함
* Early Layer에서 Visual Information Processing이 일어나고, 이후에는 Text Interpretation이 더 중요하다고 주장함


<br>

# 📃 Reference

* [[24'] Intriguing Properties of Large Language and Vision Models](https://arxiv.org/abs/2410.04751)
  
<br>