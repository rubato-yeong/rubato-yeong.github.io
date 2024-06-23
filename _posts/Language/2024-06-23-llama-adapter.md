---
title:  "[24' ICLR] LLaMA-Adapter: Efficient Fine-tuning of Language Models with Zero-init Attention" 
excerpt: ""

categories:
  - Language
tags:
  - [PEFT, Prefix Tuning]

toc: true
toc_sticky: true
 
date: 2024-06-23
last_modified_at: 2024-06-23
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/0d4cdbb6-bba1-41af-9501-1d22d5ecb857){: .align-center}

<br>

# 🔍 Abstract

LLaMA 7B model을 그대로 full fine-tuning하는 것은 비효율적이다. 이 논문에서는 일종의 **prefix tuning**으로 볼 수 있는 PEFT 방법론을 제안한다. 이때 learnable parameter는 **1.2M** 정도로, 이는 7B model의 0.02%에 해당한다. 그리고 A100 8개로 **1시간 이내**에 학습이 가능하다. 이러한 방법으로 **LLaMA-Adapter**는 기존 LLaMA fine-tuning model인 Alpaca나, LLaMA LoRA fine-tuning model인 Alpaca-LoRA과 **비슷한 성능을 보여주면서도 더 빠른 학습**이 가능했다.

<br>

# 1. Architecture

## 1.1. LLaMA-Adapter

![image](https://github.com/OpenGVLab/LLaMA-Adapter/raw/main/docs/pipeline.png){: .align-center}

**LLaMA-Adapter**의 Main Feature는 크게 2가지로 나눠볼 수 있다. (1) **Variants of Prefix Tuning**, (2) **Zero-initialized Attention**이다.

먼저 **Prefix Tuning** 부분을 보면, Prefix Tuning에서 backpropagation의 효율을 높이기 위해 topmost $L$ layer에만 learnable prefix를 추가한 모습이다. 이러한 방법으로 backpropagation을 모델 끝까지 다시 할 필요성을 제거했다.

또한 **Zero-initialized Attention** 부분을 보면, 초기 학습에서 prefix의 영향력을 0으로 만들기 위해 zero-gating을 시행하여 안정적으로 학습을 진행할 수 있도록 했다. 식으로 보면 다음과 같다.

먼저 Queries, Keys, Values를 다음과 같이 나타낼 수 있다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/509c2022-7994-4b20-a9f3-daa738bbfcbd){: .align-center}

이때 $P_ l$은 prefix, $T_ l$은 text token embedding, $t_ l$은 예측해야 할 직전 layer의 token embedding이다. Attention score는 다음과 같이 계산된다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/81f7b5db-513d-4438-bbee-8d81fdf1d381){: .align-center}

이를 prefix 부분($\leq K$)과 그 이후 부분($\leq K+M+1$)으로 나누면 다음과 같다. 

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/e3a4080c-03cb-4d2b-83ba-14ebba8ed823){: .align-center}

이때 앞쪽의 $S_ l ^ K$는 prefix가 $t_ l$을 생성하는데 미치는 정보들을 나타내며, 초기에 이 값들이 random initialization됨에 따라 학습이 불안정할 수 있다. 따라서 **learnable gating factor** $g_ l$을 softmax layer 이후에 추가하고, 이를 초기에 0으로 설정하여 **prefix의 영향력을 0으로 만들어 학습을 안정화**시킨다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/4d02bc62-bf30-40d4-a989-bc985c560000){: .align-center}

<br>

## 1.2. Multimodal LLaMA-Adapter

![image](https://github.com/OpenGVLab/LLaMA-Adapter/raw/main/docs/multimodal.png){: .align-center}

이러한 방법을 **multimodal setting**에도 적용할 수 있다. CLIP과 같은 visual encoder로부터 얻은 multi-scale global feature를 $\lbrace I_m \rbrace _ {m=1} ^M$이라 하고, 이로부터 vision feature를 다음과 같이 추출할 수 있다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/c23ac86e-9c40-4054-9aeb-9e2241747a0a){: .align-center}

그리고 이를 prefix에 더해주어 vision feature를 seamless하게 prefix 부분에 합칠 수 있다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/3ad7060f-b3e1-4c03-9c8a-c2ad36319453){: .align-center}

<br>

# 2. Experiments

# 2.1. Instruction-following Evaluation

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/0114509e-b197-4d9f-825e-ee68817cd977){: .align-center}

먼저 저자들은 **instruction-following evaluation**을 통해 LLaMA-Adapter의 성능을 확인했다. 이때 **LLaMA-Adapter**는 **Alpaca**나 **Alpaca-LoRA**에 비해 더 잘 학습되었으며, 더 효율적으로 학습되었다. LLaMA에 있어서 이 방법은 더 **효과적이고 효율적**이라고 할 수 있다.

<br>

## 2.2. Multi-modal Evaluation

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/6e7c1a94-2ace-4d48-aeee-5662f4c56b19){: .align-center}
<br>
![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/7ff72dc8-c0ba-4511-9fd4-93d70d774e01){: .align-center}

굉장히 간단한 방법을 사용하였음에도 불구하고, **LLaMA-Adapter**는 multimodal setting을 잘 처리하는 모습을 보여주고 있다.

<br>

## 2.3. Ablation Study

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/d7e16f74-2be7-40c6-affe-cacd3ba99943){: .align-center}

저자는 추가로 **fine-tuning하는 layer의 수가 많아지면 성능이 크게 향상**된다는 것과 **zero-initialized attention이 성능에 굉장히 중요**하다는 것을 강조했다. 그 외에 다른 세팅에서도 zero-initialization을 실험하여 이 방법의 효과를 증명했다.

<br>

# 💡 Summary

**LLaMA-Adapter**는 **LLaMA**를 효율적으로 학습시키기 위한 방법론으로, **prefix tuning**과 **zero-initialized attention**을 사용한다. 이를 통해 **Alpaca**나 **Alpaca-LoRA**와 비슷한 성능을 보여주면서도 더 빠르게 학습이 가능했다. 또한 **multimodal setting**에서도 잘 작동하는 것을 확인할 수 있다. 특히, **zero-initialized attention**이 성능에 큰 영향을 미치는 것을 확인할 수 있었고, 유사한 구현 상황에서 활용해봐야 할 중요한 부분이라고 생각된다.

<br>

# 📃 Reference

* <a href="https://arxiv.org/abs/2303.16199" target="_blank">[24' ICLR] LLaMA-Adapter: Efficient Fine-tuning of Language Models with Zero-init Attention</a>

<br>