---
title:  "[22' NIPS] Zero-shot-CoT: Large Language Models are Zero-Shot Reasoners" 
excerpt: ""

categories:
  - Language
tags:
  - [Chain-of-Thought]

toc: true
toc_sticky: true
 
date: 2024-06-19
last_modified_at: 2024-06-20
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/36f211ea-c500-46fa-94f8-b4c8832a061c){: .align-center}

<br>

# 🔍 Abstract

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/3eb35b7a-d6e1-4241-8676-83757b2539a7){: .align-center}

이 논문이 **Zero-shot-CoT**의 시작이다. 단순히 **"Let's think step by step"**이라는 문장을 정답 직전에 추가해주는 것만으로도 LLM의 성능이 크게 향상되는 것을 발견했다. 비록 task-specific하게 human annotator가 추가한 기존의 CoT 논문, 즉 **Few-shot-CoT를 뛰어넘지는 못했지만, 일반적인 Few-shot Learning보다 훨씬 강력한 성능**을 보여주었다. 이를 통해 LLM이 **Zero-shot**으로도 **Reasoning**을 수행하는 능력이 상당히 높다는 것을 확인할 수 있었다.

<br>

# 1. Zero-shot-CoT

## 1.1. Introduction

![image](https://media.licdn.com/dms/image/C4D12AQFTN5NTb7-PnQ/article-cover_image-shrink_720_1280/0/1570612670044?e=2147483647&v=beta&t=k3e5QblP-StYt7H8PD1JbSr1K5dojywaMDH_UNLLBO4){: .align-center}

사람들이 하는 task는 크게 *system-1*과 *system-2*로 나눌 수 있다. *system-1*은 자동적이고 빠르며, *system-2*는 느리고 논리적이다. LLM은 *system-1*을 잘 수행하지만, *system-2*는 그렇지 못하다. 이 논문에서는 LLM이 *system-2*를 수행하는 능력을 향상시키기 위해 **Zero-shot-CoT**을 제안했다고 주장한다. 즉, **"Let's think step by step"**이라는 문장을 정답 직전에 추가하여 논리적인 추론을 진행하도록 명령한다는 것이다. 상세한 설명은 다음과 같다.

<br>

## 1.2. Method

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/f6324a55-703e-4865-8de7-a6c32d1769c9){: .align-center}

**Zero-shot-CoT**에서는 LLM을 2회 이용하며, 이들을 각각 **Reasoning Extraction**과 **Answer Extraction**이라 하였다. 첫 번째 단계에서는 "Let's think step by step"이라는 문장을 추가하여 LLM의 Reasoning을 추출하고, 두 번째 단계에서는 이를 통해 정답을 추출한다. Few-shot-CoT에서는 이러한 과정을 human annotator가 수행했기에 Reasoning도 annotation이 필요하고, answer formatting도 하나로 통일해야 했다. 하지만 Zero-shot-CoT는 이러한 불편함이 없다는 장점이 있다.

<br>

# 2. Experiments

## 2.1. Comparison with Baselines

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/828e5fe2-84fd-4647-8b7e-84922dc19e77){: .align-center}

결과는 명확하다. 성능은 **Zero-shot < Few-shot < Zero-shot-CoT < Few-shot-CoT** 순으로 나타났다. 이를 통해 **Zero-shot-CoT가 Few-shot보다 성능이 우수**하다는 것을 확인할 수 있었고, LLM의 zero-shot reasoning ability를 확인할 수 있었다.

<br>

## 2.2. Scaling Law

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/d0ae88b1-41fd-444e-bf5f-59cabb9e53ba){: .align-center}

**Zero-shot-CoT**의 성능은 LLM의 크기에 따라 증가하며, 이는 기존 CoT 논문에서 발견되었던 것과 동일한 결과이다. 결국 **Zero-shot-CoT도 LLM의 emergent ability**라는 것이다. 다른 말로 하면, **small LLM에서 Zero-shot-CoT는 효과적이지 않다**.

<br>

## 2.3. Detailed Analysis

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/1e8c0240-9065-4cfb-9c03-c5e9a16232c2){: .align-center}

정성적인 분석이긴 하지만 저자들은 실제 정답을 맞추지 못했더라도 LLM이 생성한 **Chain-of-Thought** 자체는 **논리적으로 합리적이거나 봐줄 만한 실수를 하는 경우**가 많았다고 한다. LLM이 **실제 evaluation 결과보다 더 뛰어난 reasoning 성능을 가지고 있다**는 것이다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/f85260cc-9585-416b-923c-b5fa08ddcb9f){: .align-center}

그리고 개인적으로 궁금했던 부분. 왜 하필이면 "Let's think step by step"이라는 문장을 선택했을까? 여러 가지를 실험했는데 가장 좋았기 때문이다. 여러 Instructive Sentence가 있는데, **문장에 따라 성능이 천차만별**인 것이 특징이다.

<br>

# 💡 Summary

논문의 내용이 간단해서 요약할 내용이 많지 않다. 핵심은 **Few-shot < Zero-shot-CoT < Few-shot-CoT** 순으로 성능이 나타난다는 것이다. 추가로, 저자들은 LLM의 **zero-shot reasoning ability**가 보기보다 굉장히 뛰어나다고 주장한다. 이처럼 LLM에게 답을 맞추라고 직접적으로 명령하기보다 추론하도록 시키는 것이 얼마나 효과적인지 알게 된 이후로 이를 활용한 Prompt Engineering 등의 다른 연구들이 많이 진행되었다.

<br>

# 📃 Reference

* <a href="https://arxiv.org/abs/2205.11916" target="_blank">[22' NIPS] Large Language Models are Zero-Shot Reasoners</a>

<br>