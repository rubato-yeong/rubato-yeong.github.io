---
title:  "[24' ICML-WS] Transformers need glasses! Information over-squashing in language tasks" 
excerpt: ""

categories:
  - Language
tags:
  - [Interpretability, Hallucination]

toc: true
toc_sticky: true
 
date: 2024-07-26
last_modified_at: 2024-07-26
---

![image](https://github.com/user-attachments/assets/725f0ca1-f82c-43d9-91d4-9ac40f222b99){: .align-center}

<br>

# 🔍 Abstract

2024년 ICML Workshop 중 Workshop on Theoretical Foundations of Foundation Models (TF2M)에 나온 논문이다. Google DeepMind에서 낸 논문이라 주목하여 읽게 되었다. 아무래도 이론적인 내용이 주를 이루는 논문이라 엄밀한 정도의 이해는 생략하고, 해당 논문에서 분석한 개념들 위주로 요약하고자 한다.

![image](https://github.com/user-attachments/assets/096b4c5e-2f1a-4ba5-b164-d8e913fe4f35){: .align-center}

저자들은 **Representational Collapse**와 **Over-squashing**이라는 개념을 LLM에서 처음으로 제안한다.

* **Representational Collapse**란 LLM이 입력 데이터의 정보를 충분히 표현하지 못하는 것을 의미한다. 특히 final token의 representation이 input이 길어지면 길어질수록 하나에 수렴하게 되며, 따라서 **LLM은 나중에 나오는 정보를 충분히 이해하기 어렵다**. 이는 어느 정도 fp16과 같은 low-precision floating point로 인해 발생하는 문제이다.
* **Over-squashing**은 Transformer 구조의 한계로 인해 Input의 Early Token에만 LLM이 집중하게 되는 현상을 의미한다. Early Token은 Token Prediction에 기여할 수 있는 경로가 Late Token보다 많고, 따라서 **LLM은 Input의 앞쪽에만 집중**하게 된다.

<br>

# 1. Motivating Examples

저자들은 위의 두 개념을 추론하기 위해 아주 간단한 Task인 **Copying, Counting**을 예시로 들었다. 각각에 대해 살펴보자.

<br>

## 1.1. Copying

![image](https://github.com/user-attachments/assets/fc9e843a-31c2-4718-8e94-09b8e0657bbf){: .align-center}

저자들은 Google의 LLM인 Gemini에게 `1...10`의 last element 또는 `01...1`의 first element를 copy하여 출력하도록 했다. 어떠한 경우에도 정답은 0이어야 한다. 그러나, input의 길이가 길어지면 길어질수록 **last element를 예측하는 task에서는 0이 아닌 1을 예측**하는 현상이 발생했다. 반면, **first element를 예측하는 task에서는 0을 잘 예측**하는 것을 확인할 수 있었다. 반면 앞에 “*Hint* It’s not necessarily a 1, check carefully” 와 같은 **Hint를 제시한 경우**, 또는 0과 1을 **번갈아 제시한 경우**에는 0을 잘 예측하는 것을 확인할 수 있었다. 저자들은 이러한 문제가 **Representational Collapse**로 인해 발생하며, 0이 앞에 있는 경우나 Hint를 제시하거나 번갈아 제시하는 경우에는 **Over-squashing**으로 인해 그러한 문제가 발생하지 않는다고 주장한다.

<br>

## 1.2. Counting

![image](https://github.com/user-attachments/assets/1ebd765e-d200-43e5-910d-f2a088abf0b4){: .align-center}

Counting은 위 그림을 통해 쉽게 이해할 수 있다. **숫자가 커지고, Input이 길어짐에 따라 LLM은 수를 잘 세지 못하게 된다**. 내용을 생략하였으나, 저자들은 Positional Encoding, Causal Attention Mechanism 없이 Transformer는 Counting이 불가능하다는 것을 수학적으로 증명하였다. 이 또한 **Representational Collapse**에 의한 문제로, 점점 `1`에 대한 **Representation이 유사**해지면서 LLM은 이를 숫자로 세야 한다는 것을 잘 인지하지 못하게 된다. 대신 **LLM은 직접 수를 세기보다는 대충 많이 등장하는 숫자를 대답**하는 방식을 사용한다. 아래 그림을 통해 LLM이 100을 넘어가면 숫자를 세는 것을 "포기"하고 답으로 `100`을 말하는 것을 확인할 수 있다.

![image](https://github.com/user-attachments/assets/c31b10c9-9795-47d8-ba04-8dba9d03ab68){: .align-center}

<br>

# 2. Representational Collapse

**Representational Collapse**는 LLM이 입력 데이터의 정보를 충분히 표현하지 못하는 것을 의미한다. 특히 final token의 representation이 input이 길어지면 길어질수록 하나에 수렴하게 되며, 따라서 **LLM은 나중에 나오는 정보를 충분히 이해하기 어렵다**. 이를 수학적으로는 다음과 같이 이야기할 수 있다.

![image](https://github.com/user-attachments/assets/b9650f88-1a76-48f4-bffe-b1a3cee40da2){: .align-center}

실제로 Copying, Counting 문제에서 1이 하나 추가되었을 때의 Representation의 차이는 길이가 길어짐에 따라 **Positional Encoding의 차이가 줄어들면서 0에 가까워진다**. 따라서 LLM은 이러한 차이를 잘 인지하지 못하게 된다. 저자들은 아주 간단히 3개의 Token마다 Comma `,`를 넣어 representation distance를 늘리는 방식으로 이를 해결할 수 있었다.

![image](https://github.com/user-attachments/assets/e2cabb1a-f053-49cf-96cf-f39daaba7f7f){: .align-center}

<br>

# 3. Over-squashing

**Over-squashing**을 수학적으로는 다음과 같이 나타낼 수 있다.

![image](https://github.com/user-attachments/assets/ac17689b-df9d-4f9b-9c47-adba8d50e99e){: .align-center}

이 식으로부터 final token의 **sensitivity**를 알 수 있다. 즉, Final Token $\mathbf{y}_ n$의 Input Token $\mathbf{v}_ i ^ {(0)}$에 대한 sensitivity를 위 식으로 이해할 수 있으며, 결국 $i$가 작을수록, 즉 input sequence 초기에 위치할수록 sensitivity가 높아진다. 이는 **LLM이 Input의 Early Token에만 집중**하고 해당 정보가 더 잘 보존되는 현상을 설명한다. 극단적인 상황에서, Layer의 수 $L \to \infty$로 가면 $\mathbf{y}_ n$는 $\mathbf{v}_ 1 ^ {(0)}$에만 의존하게 된다.

![image](https://github.com/user-attachments/assets/ea765680-ad52-45e0-b2ec-e0b1fbe5f52d){: .align-center}

이러한 **over-squashing**은 LLM에서 관찰된 **U-shape effect**를 설명하는 데 도움을 준다. **U-shape effect**란 LLM이 retrieval task를 수행하는 경우 input의 앞쪽과 뒤쪽에 해당 정보가 있는 경우 더 잘 수행하는 현상을 의미한다. 앞쪽의 경우 **over-squashing**에 의해 정보가 더 잘 보존되기 때문에, 그리고 뒤쪽의 경우 학습 당시 attention mechanism이 주변 정보를 더 잘 받아들이는 **recency bias**로 인해 더 잘 수행하는 것으로 설명할 수 있다. 

<br>

# 💡 Summary

지금까지의 내용을 요약하면 다음과 같다.

* 저자들은 Copying, Counting과 같은 간단한 문제일지라도 Sequence가 길어지면 LLM이 Task를 잘 수행하지 못한다는 것을 발견하고, 이를 **Representational Collapse**와 **Over-squashing**이라는 개념으로 설명하였다.
* **Representational Collapse**는 input sequence가 길어질수록 final token의 representation이 하나로 수렴하게 되어 나중에 나오는 정보를 충분히 이해하기 어렵게 된다는 것이고, **Over-squashing**는 input sequence의 앞쪽 부분만 충분한 정보를 보존하여 final token에 전달할 수 있고 중간 부분은 정보가 소실되는 현상이다.

<br>

# 📃 Reference

* [[24' ICML-WS] Transformers need glasses! Information over-squashing in language tasks](https://arxiv.org/abs/2406.04267)

<br>