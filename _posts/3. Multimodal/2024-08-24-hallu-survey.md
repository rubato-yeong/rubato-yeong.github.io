---
title:  "[24'] Hallucination of Multimodal Large Language Models: A Survey"
excerpt: ""

categories:
  - Multimodal
tags:
  - [Hallucination, Summary]

toc: true
toc_sticky: true
 
date: 2024-08-24
last_modified_at: 2024-08-24
---

![image](https://github.com/user-attachments/assets/878af0b2-b8a5-4f8e-9220-ea72004b1e38){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/showlab/Awesome-MLLM-Hallucination/raw/main/assets/tax.png){: .align-center}

Multimodal Model에서의 Hallucination은 어떤 의미인가? 저자들은 최근 각광받고 있는 **LMM(Large Language Models)**에 대한 Hallucination을 조사하였다. 이 글에서는 해당 Survey의 내용을 간략히 정리해보며 **Multimodal Hallucination**의 개념을 다시 이해해보고자 한다.

<br>

# 1. Definitions

## 1.1. LLM vs. LMM Hallucination

**Hallucination**은 **LLM**이 대답할 때 **Ground Truth**와 다른 결과를 내놓는 현상을 의미한다. 먼저 LLM에서는 Hallucination을 다음과 같이 두 가지로 나눌 수 있다.

1. **Factuality Hallucination**: LLM이 **Real-world Fact**와 다른 결과를 내놓는 경우
2. **Faithfulness Hallucination**: LLM이 **User Instruction이가 Provided Context**와 다른 결과를 내놓는 경우

한편, **LMM**에서는 Hallucination을 다음과 같이 정의하는 경우가 많다.

📌 **Cross-modal Inconsistency**: **Text Response**와 **Visual Content** 간의 불일치
{: .text-center}
{: .notice}

즉, LLM Hallucination의 연구는 그대로 LMM Hallucination으로 Transfer되는 것이 아니다. 대신, Multimodal Interaction을 고려한 새로운 연구 분야가 필요하다.

<br>

## 1.2. Classification

Multimodal Hallucination은 다양한 방법으로 나눌 수 있다. 그러나 지금까지의 연구는 대개 **Object Hallucination**에만 집중해왔고, 이에 기반하면 Object Hallucination은 다음과 같이 세 가지 정도로 나눠볼 수 있다.

1. **Object Category**: **Nonexistent Object, Incorrect Object**를 생성하는 경우, 이때 category는 *person, cloud, bench*와 같은 것들이 될 수 있다.
2. **Object Attribute**: **Incorrect Attribute**를 생성하는 경우, 이때 Attribute는 *color, shape, material, content, count, action* 등 다양한 것들이 될 수 있다.
3. **Object Relation**: **Incorrect Relation**을 생성하는 경우, 이때 Relation은 대개 **Spatial Relation**을 의미한다. 예를 들면 *above, below, around* 등이 될 수 있다.

다른 논문들에서는 **Object Counting, Object Event**와 같은 일부 특성들은 다른 카테고리로 분류되기도 하나, 저자들은 이를 **Attribute**로 간주하였다. 이제 세 가지 분류를 아래 예시를 통하여 이해해보자.

![image](https://github.com/user-attachments/assets/437e7801-1bb0-4339-867a-09e4b790cbb9){: .align-center}

<br>

# 2. Hallucination Causes

저자들은 Hallucination이 발생하는 원인을 다음과 같이 **Data, Model, Training, Inference** 측면으로 나누어 정리하였다.

1. **Data**: 크게 **Quantity**와 **Quality**, **Statistic Bias**로 나눌 수 있다.
   1. **Quantity**: 학습 데이터의 부족으로 인해 Hallucination이 발생할 수 있다.
   2. **Quality**:
      1. **Noisy Data**: Automated Generation에 의해 잘못된 데이터를 생성하는 경우가 많다.
      2. **Lack of Diversity**: Instruction Tuning Data는 대개 Positive Instruction으로 구성되어 있고, Negative Instruction 및 Reject은 학습시키지 않아 **Yes Bias**가 발생하기도 한다.
      3. **Detailed Description**: 일반적으로 LMM 학습 데이터에서는 Object Position, Attribute, Non-salient Object 등에 대한 **정보가 부족**하다. 반대로, LMM의 Perception Limit을 넘어갈 정도로 **세부적인 정보를 학습**시키는 경우, Hallucination이 발생할 수 있다.
   3. **Statistic Bias**: **Frequently appeared Object** 및 **Object Co-occurrence**로 인한 Bias가 발생할 수 있으며, POPE와 같은 Benchmark에서 이러한 Bias를 확인할 수 있다.
2. **Model**: Model의 세 구조, 즉 **Vision Model**, **LLM**, **Alignment Interface**에서의 문제로 나눌 수 있다.
   1. **Vision Model**: Vision Model은 필연적으로 **Image Information Loss**를 겪는다. 따라서 Image에 대한 모든 정보를 LLM에 제공하지 못한다.
   2. **LLM**: LLM은 Vision Model보다 크고 강하기 때문에, **Language Prior**에 의해 Visual Information을 무시할 수 있다.
   3. **Alignment Interface**: 아직 Visual, Textual Input 간의 **Alignment**이 완벽하지 않다.
3. **Training**: 크게 **Loss Design**, **Alignment Fine-tuning** 문제로 나눠볼 수 있다.
   1. **Loss Design**: LMM은 LLM과 동일하게 Next Token Prediction Loss로 훈련한다. 이러한 Autoregressive Loss는 **Visual Content를 학습하는 데 적합하지 않을** 수 있다. Visual Content는 굉장히 복잡한 Spatial Structure를 갖고 있기 때문이다.
   2. **Alignment Fine-tuning**: LMM은 LLM과 달리 일반적으로 **RLHF를 수행하지 않는다**. 이러한 문제로 Hallucination이 발생할 수 있다.
4. **Inference**: Sequence Length가 길어짐에 따라 Image에는 Attention이 덜하고, **Previous Output Token에만 집중**하게 되면서 Visual Information을 놓칠 수 있다.

<br>

# 3. Hallucination Metrics & Benchmarks

Hallucination Metrics 또는 Benchmarks로 주로 사용되는 것은 **CHAIR, POPE, MME, AMBER** 등이 있다. 이러한 Benchmark는 다양한 Hallucination 현상을 측정하고, 이를 개선하는 데 초점이 맞춰져 있다. 여기서는 흔히 Hallucination 논문에서 많이 측정하는 위 4가지에 대해 간략히 소개한다.

<br>

## 3.1. CHAIR [18' EMNLP]

**CHAIR(Caption Hallucination Assessment with Image Relevance)**는 Image Captioning에서의 Hallucination을 측정하는 Metric으로, Image Hallucination에서 거의 최초로 고안된 방법이기에 그 의미가 있다. 또한 이후에 나올 Metric과는 달리 **Open-ended Question**을 사용하기에, 더욱 **정확한 Hallucination 측정**이 가능하다.

![image](https://github.com/user-attachments/assets/327af114-472b-4c9b-bb29-0bbdbd7e5138){: .align-center}

CHAIR에는 두 가지 Metric이 있다. **Per-instance** $\text{CHAIR}_ i$와 **Per-sentence** $\text{CHAIR}_ s$로 나누어진다. Per-instance는 Image에서 Caption에 달린 모든 Instance 중 Hallucination이 발생한 비율을 의미하며, Per-sentence는 Image에 대한 Caption Sentence 전체에서 Hallucination이 발생한 비율을 의미한다.

![image](https://github.com/user-attachments/assets/a9ceebe2-e7c4-4f7b-a210-39e4f1442914){: .align-center}

이때 데이터셋은 **MSCOCO**에 한정되어 있으며, 생성된 답변은 미리 정의된 **Synonyms**를 사용하여 **MSCOCO Object**로 분류된다.

그러나 CHAIR는 기존에 **Image Captioning Model**에서 개발된 것이기에, **LMM**에 이를 그대로 적용하기에는 한계가 있다는 주장도 있다. 일반적으로 LMM에서는 **Instruction Design**과 **Generated Length**에 따라 CHAIR Metric이 달라질 수 있기 때문이다.

<br>

## 3.2. POPE [23' EMNLP]

![image](https://github.com/user-attachments/assets/6d76e3d9-a91e-4374-b198-85f6fe44ed2d){: .align-center}

**POPE(Polling-based Object Probing Evaluation)**은 이러한 문제를 해결하기 위하여 제안되었다. 즉, Hallucination 문제를 *Yes-or-No* **Binary Classification**으로 단순화하여 **Instruction 및 Generated Length에 무관**하게 Hallucination을 측정할 수 있도록 하였다.

![image](https://github.com/user-attachments/assets/3d9a6905-30c2-4109-9cbb-e7f2ba5b4ffb){: .align-center}

POPE의 핵심은 Benchmark Design 시 세 개의 subset **random, popular, adversarial**을 만들었다는 점이다. **Popular**의 경우 가장 많이 등장하는 Object를 포함하고 있으며, **Adversarial**의 경우에는 co-occurrence가 높은 Object를 포함하고 있다. 이를 통하여 LMM에서 발생하는 **Statistic Bias**를 확인할 수 있었다.

<br>

## 3.3. MME [23' ArXiv]

![image](https://github.com/BradyFU/Awesome-Multimodal-Large-Language-Models/raw/Evaluation/images/dataset.png){: .align-center}

**MME(MLLM Evaluation Benchmark)**은 LMM의 Vision-centric Performance를 측정하기 위한 General한 Benchmark로 제안되었으며, 크게 **Perception**과 **Cognition**을 측정하는 14개의 subtask로 구성되어 있다. MME에서는 Object Hallucination을 측정하기 위해 활용할 수 있는 *Existence, Count, Position, Color* Question이 있다.

<br>

## 3.4. AMBER [23' ArXiv]

![image](https://github.com/junyangwang0410/AMBER/raw/master/README_File/intro.jpg?v=1&type=image){: .align-center}

**AMBER**는 **POPE**의 문제점을 지적하였다. POPE는 오로지 **Object Existence만 측정**하였고, 따라서 다른 다양한 Hallucination을 측정할 수 없었다. 또한, Discriminative Task, 즉 **Binary Classification은 평가가 굉장히 제한적**이라는 문제가 있다. 따라서 AMBER에서는 **Existence, Attribute, Relation**을 모두 평가하고 **Discriminative Task** 뿐만 아니라 **Generative Task**도 함께 사용하여 Hallucination을 측정하였다.

<br>

## 3.5. Others

그 외에 주목할 만한 Benchmark로는 다음과 같은 것들이 있다.

* [MMHal-Bench](https://arxiv.org/abs/2309.14525): 8개의 Category로 각각 12개의 자세한 질문을 만들어 Hallucination을 측정한다. 이때 **GPT-4 Evaluation**을 사용한다. 해당 논문의 핵심 내용은 **Fact-RLHF**를 시행하여 Hallucination을 줄이는 방법을 제안하는 것이다.
* [GAVIE](https://arxiv.org/abs/2306.14565): GPT4-Assisted Vision Instruction Evaulation의 약자로, 여기서는 Instruction을 잘 따르는지 여부인 **Relevancy**와 Hallucination을 일으키지 않는지 여부인 **Accuracy**를 측정한다. 해당 논문의 핵심 내용은 **Negative Instruction을 추가한 IFT**를 수행하여 Hallucination을 줄이는 방법을 제안하는 것이다.

<br>

## 3.6. Summary

지금까지의 Benchmark를 표로 정리하면 다음과 같다.

| Benchmark | Venue | Source | Size | Task | Hallucination Type |
|:---------:|:-----:|:------:|:----:|:----:|:------------------:|
| CHAIR | EMNLP 18' | MSCOCO | 5,000 | Generative | Category |
| POPE | EMNLP 23' | MSCOCO | 3,000 | Discriminative | Category |
| MME | ArXiv 23' | MSCOCO | 1,457 | Discriminative | Category, Attribute |
| MMHal-Bench | ArXiv 23' | Open-Images | 96 | Generative | Category, Etc |
| GAVIE | ICLR 24' | Visual Genome | 1,000 | Generative | Various |
| AMBER | ArXiv 23' | Web | 15,202 | Discriminative + Generative | Category, Attribute, Relation |

<br>

# 4. Hallucination Mitigation

![image](https://github.com/user-attachments/assets/539b93ca-2e69-4c61-aa61-7a43a6cf1387){: .align-center}

Hallucination을 해결하기 위한 방법도 원인의 분류와 같이 **Data, Model, Training, Inference** 측면으로 나눌 수 있다. 자세한 방법들은 위 그림을 참고하자. 일부 방법들은  [리스트업](https://rubato-yeong.github.io/listup/Multimodal/#2-hallucination-mitigation) 링크에 따로 정리하여 두었다.

<br>

# 📃 Reference

* [[24'] Hallucination of Multimodal Large Language Models: A Survey](https://arxiv.org/pdf/2404.18930)
* [[18' EMNLP] CHAIR: Object Hallucination in Image Captioning](https://arxiv.org/abs/1809.02156)
* [[23' EMNLP] POPE: Evaluating Object Hallucination in Large Vision-Language Models](https://arxiv.org/abs/2305.10355)
* [[23'] MME: A Comprehensive Evaluation Benchmark for Multimodal Large Language Models](https://arxiv.org/abs/2306.13394)
* [[23'] AMBER: An LLM-free Multi-dimensional Benchmark for MLLMs Hallucination Evaluation](https://arxiv.org/abs/2311.07397)

<br>