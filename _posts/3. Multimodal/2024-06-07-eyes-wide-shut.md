---
title:  "[24' CVPR] Eyes Wide Shut? Exploring the Visual Shortcomings of Multimodal LLMs" 
excerpt: ""

categories:
  - Multimodal
tags:
  - [Visual Encoder, Analysis]

toc: true
toc_sticky: true
 
date: 2024-06-07
last_modified_at: 2024-06-07
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/d1fd92fd-a068-4182-83b2-a7c28adfd01c){: .align-center}

<br>

# 🔍 Abstract

![image](https://i.namu.wiki/i/hArScPnESzmxD8vdiC8HTBQieTaMoI40BrjpULX2-Mm7TWEqRcRDZ5j6it-SRpCENtoRHHCSLqPr7Xvon7FqKAijNCphDPXnFnEQjrJ3cKg-VlZmmXzg-H7e03ofo7z6AQNiSdDowcrSFJdTbAGe9w.webp){: .align-center}

1999년 개봉한 Eyes Wide Shut이라는 영화가 있다. 이 영화는 미국의 감독인 Stanley Kubrick의 유작으로, Tom Cruise와 Nicole Kidman이 주연을 맡았다. 이 영화의 한줄 소개를 보면 다음과 같은 말이 있다.

**아이즈 와이드 셧(Eyes Wide Shut).** 질끈 감은 눈. 보고 싶어도 볼 수 없는 눈. 우리 모두의 마음 속에 있으면서 좀체 드러나지 않는 어떤 것.
{: .notice}

이 논문은 이 영화의 이름을 가져와, **Multimodal LLMs의 시각적 한계를 탐구**한다. 저자들은 VLM이 이미지를 보는 눈이라고 할 수 있는 **Vision Encoder에 문제가 있다고 지적**한다. 즉, 일반적으로 사용되는 CLIP(Contrastive Language-Image Pre-training)과 같은 모델에 한계가 있다는 것인데, **실제로는 서로 다른 두 이미지를 아주 유사한 공간에 매핑한다는 것**이다. 이를 저자들은 **CLIP-blind pairs**라고 부르며, 이들을 모아 **MMVP(Multimodal Visual Patterns)** benchmark를 구축했다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/1ad0c8de-2949-4af9-a4bf-e889809b5f34){: .align-center}

놀랍게도 GPT-4V를 포함한 거의 모든 VLM에서 이 이미지들을 잘 해석하지 못했고, **이미지의 시각적 특징을 잘 파악하지 못하는 것**으로 나타났다. 따라서 저자들은 CLIP이 아닌 다른 눈으로 **self-supervised learning**을 통해 학습한 **DINOv2** visual encoder를 CLIP과 융합하는 **Mixture of Features (MoF)**를 통해 이 문제를 일정 부분 해결하였다. 결과적으로, 이 논문을 통해 **VLM에 시각적 한계**가 있음을 보여주었고, 앞으로의 VLM 발전 방향에 있어 **visual perception의 향상이 필요함**을 제시하였다. 이 논문은 그 가치를 인정받아 **24년 CVPR Oral**, 그 중에서도 Multimodal 부문 Top 3에 선정되었다.

<br>

# 1. CLIP-blind Pairs

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/c59570a5-a053-4e49-b6da-720bb51dbe25){: .align-center}

저자들은 VLM의 가장 큰 문제가 **visual representation**과 관련되어 있다고 제시한다. 즉, 흔히 사용되는 CLIP의 visual representation에 한계가 있다는 것이다. 저자들은 대신 DINOv2와 같은 self-supervised encoder를 사용하여 visual similarity를 계산하였다. 따라서 저자들은 **CLIP-blind pairs**를 다음과 같이 조작적으로 정의하였다.

**CLIP-blind pairs**: CLIP embedding 상에서는 유사하지만 DINOv2 embedding 상에서는 다른 이미지 쌍
{: .notice}

이렇게 얻은 이미지들을 모아 **MMVP benchmark**를 구축하였으며, benchmark에 대하여 다음과 같이 Gemini, GPT-4V와 같은 closed-model과 LLaVA-1.5, InstructBLIP과 같은 open-model에서의 정확도를 평가하였다. 많은 부분에서 **모델에 관계없이 틀린 대답**을 내놓는 것을 알 수 있다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/9953dcf1-a13d-40de-86cb-41f9adc682b4){: .align-center}

이 결과를 정량적으로 사람과 비교해보면 아래와 같다. **사람은 95.7%**의 정답률을 보이는 반면, **어떠한 MLLM 모델도 50%의 정답률을 넘지 못했다**. 얼마나 사람에게는 쉬운 task이지만 MLLM에게는 어려운 task인지를 보여주는 결과이다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/5d3fe167-e49b-415f-80f6-d9aeb7dde996){: .align-center}

<br>

# 2. Systemic Failures in CLIP

저자들은 여기서 더 나아가서 **실제로 CLIP이 잘 구별하지 못하는 이미지일수록 VLM이 이미지를 잘 분간하지 못하는지 확인**하였다. 즉 정확한 **상관관계**가 있는지를 보고자 한 것이다. 이를 위해 먼저 GPT-4를 이용해 MMVP benchmark의 이미지를 다음과 같이 **9가지 카테고리**로 분류하였다.

* Orientation and Direction
* Presence of Specific Features
* State and Condition
* Quantity and Count
* Positional and Relational Context
* Color and Appearance
* Structural and Physical Characteristics
* Text
* Viewpoint and Perspective

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/632fa69f-ccd7-42f4-aad1-3b6b86eba0fd){: .align-center}

그리고 이를 기준으로 각 카테고리에 15개의 text-image pair가 포함되도록 데이터셋을 구축하였고, 저자들은 이를 **MMVP-VLM benchmark**라고 이름 붙였다. 저자들은 이들에서 CLIP model의 성능과 VLM model의 성능의 상관관계를 분석하였다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/a96a4382-b15b-479e-b4e1-14655434676a){: .align-center}

명시적으로 CLIP을 사용하는 open-source model에서 그 연관성이 더 크게 나타났고, **CLIP이 잘 구별하지 못하는 이미지일수록 VLM이 이미지를 잘 분간하지 못하는 경향**이 있다는 것을 알 수 있다. 이는 **VLM이 CLIP의 시각적 한계를 따라가는 경향**이 있음을 보여준다. 저자들은 혹시 CLIP 모델이 발전함에 따라 이러한 문제가 해결되었을까 싶어 여러 CLIP 모델에 대해서도 실험을 진행했다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/5d3c659b-2c3a-496d-9d91-78a50eed1221){: .align-center}

이 결과를 통해 알 수 있는 점은 다음과 같다. 일반적으로 **scaling law**에 의해 모델 또는 학습 데이터 수가 커질수록 성능이 향상되어야 하는데, **MMVP-VLM benchmark에서는 그렇지 않았다**. 또한, CLIP의 성능을 평가할 때 보통 ImageNet-1k zero-shot accuracy로 평가를 진행하는데, 이 또한 MMVP-VLM benchmark 결과와 잘 대응되지 않았다. 즉, **ImageNet-1k로 평가하는 것이 정확하지 않다는 것**이다. 지금까지의 내용을 정리하면 다음과 같다.

* **CLIP이 잘 구별하지 못하는 이미지는 VLM이 잘 구별하지 못한다**.
* 고전적인 CLIP을 사용하면 **모델이 커지거나, 학습 데이터가 많아도 VLM의 성능이 향상되지 않는다**. 고전적인 CLIP의 평가 방식인 **ImageNet-1k도 정확한 평가 방식이 아니다**.

<br>

# 3. Mixture of Features (MoF)

저자들은 image에 대해 self-supervised learning을 진행한 **DINOv2**와 같은 모델이 visual perception에 더 유리하다고 생각하고, CLIP과 DINOv2를 융합하는 **Mixture of Features (MoF)**를 제안한다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/76d9a453-5453-4488-bac1-8302d58f9fe4){: .align-center}

두 가지 방법이 있는데, 하나는 Encoder Feature를 단순히 더하는 **Additive MoF**이고, 다른 하나는 Encoder Feature를 사이사이에 끼워넣는 **Interleaved MoF**이다. **Additive MoF**에서는 각 encoder의 비율을 조절하며 다음과 같은 결과를 얻었다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/4211cbb9-8d85-437f-8a52-c6a9e716ef3e){: .align-center}

이를 통해 얻을 수 있는 결론은 다음과 같다.

1. DINOv2 비율이 늘어날수록, MLLM의 **instruction-following 성능이 하락**한다.
2. DINOv2 비율이 늘어날수록, MLLM의 **visual grounding 능력이 향상**된다. 그러나 DINOv2 비율이 너무 늘어나면 instruction-following 성능의 하락으로 인해 전체 성능이 하락한다.
3. 따라서 **Additive MoF**에서는 visual grounding과 instruction-following 사이의 **trade-off**가 존재한다.

저자들은 대신 **Interleaved MoF**를 사용하여 이 문제를 해결하였다. 두 encoder feature를 사이사이에 끼워넣었는데, 이렇게 진행한 이유는 이들의 **original spatial order**를 보존하기 위해서이다. 결과는 다음과 같고, **instruction-following 성능 하락 없이 visual grounding 능력이 향상**되었다. 이는 다른 SSL(Self-Supervised Learning) 모델인 **MAE, MoCoV3** 등을 사용했을 때도 마찬가지였다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/7506c631-b53d-46f0-bd04-2fe97852122a){: .align-center}

이 방식을 통해 저자들은 **scaling**만이 CLIP과 같은 visual encoder의 성능을 향상시키는 것이 아니며, **다른 방식의 접근**이 필요하다는 것을 보여주었다.

<br>

# 💡 Summary

이 논문은 **Multimodal LLMs의 시각적 한계**를 탐구하였다. 저자들은 **CLIP-blind pairs**를 통해 **VLM이 CLIP의 시각적 한계를 따라가는 경향**이 있음을 보여주었다. 저자들은 이 문제를 해결하기 위해 **DINOv2와 CLIP을 융합하는 Mixture of Features (MoF)**를 제안하였고, **Interleaved MoF**를 사용하여 instruction-following 성능 하락 없이 visual grounding 능력을 향상시킬 수 있었다. 이를 통해 저자들은 **visual encoder에 대한 개선**이 앞으로의 VLM 발전에 있어 중요한 방향이 될 것임을 제시하였다. 예전부터 CLIP의 한계에 대한 지적은 있었지만, 이처럼 체계적으로 분석하고 해결 방안을 설득력 있게 제시한 논문은 이게 처음이라고 생각한다. 앞으로 visual encoder에 대한 연구가 많아질 것으로 기대된다.

<br>

# 📃 Reference

* <a href="https://arxiv.org/abs/2401.06209" target="_blank">[24' CVPR] Eyes Wide Shut? Exploring the Visual Shortcomings of Multimodal LLMs</a>

<br>