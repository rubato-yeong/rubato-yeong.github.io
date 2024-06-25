---
title:  "Multimodal Model Post List" 
excerpt: ""

categories:
  - Listup
tags:
  - []

toc: true
toc_sticky: true
 
date: 2024-06-25
last_modified_at: 2024-06-25
---

# 📊 Survey

* [[24’] A Survey on Multimodal Large Language Models](https://rubato-yeong.github.io/multimodal/survey-MLLM/)
  * Multimodal LLM 전반에 대해 정리한 글

<br>

# 😎 Early Works

* [[21’ ICML] VL-T5: Unifying Vision-and-Language Tasks via Text Generation](https://rubato-yeong.github.io/multimodal/vlt5/)
  * Vision information을 text token으로 전환하는 구조의 multi-task unified framework를 제시함

<br>

# 🧷 Contrastive Model

* [[21’ ICML] CLIP: Learning Transferable Visual Models From Natural Language Supervision](https://rubato-yeong.github.io/multimodal/clip/)
  * CLIP(Contrastive Language-Image Pre-training) 모델을 제시함

<br>

# ✨ Generative Model

## 1. Instruction Tuning

* [[23’ NIPS] LLaVA: Visual Instruction Tuning](https://rubato-yeong.github.io/multimodal/llava/)
  * Visual Instruction Tuning을 위한 LLaVA 모델을 제시함
* [[24’ CVPR] LLaVA-1.5: Improved Baselines with Visual Instruction Tuning](https://rubato-yeong.github.io/multimodal/llava1_5/)
  * Academic task를 잘 하지 못하는 LLaVA의 단점을 data instructization으로 극복함
  * Adapter 개선, High-resolution input 사용 등의 방법을 제시함

## 2. Few-shot Learning

* [[22’ NIPS] Flamingo: a Visual Language Model for Few-Shot Learning](https://rubato-yeong.github.io/multimodal/flamingo/)
  * Multimodal Few-shot learning task를 위한 Flamingo 모델을 제시함
* [[24’] MM1: Methods, Analysis & Insights from Multimodal LLM Pre-training](https://rubato-yeong.github.io/multimodal/mm1/)
  * Apple의 논문으로, MLLM Pipeline을 분석하고, 이를 최적화한 MM1 모델을 제시함

## 3. Visual Encoder & Adapter

* [[24’ ICLR] LLaMA-Adapter: Efficient Fine-tuning of Language Models with Zero-init Attention](https://rubato-yeong.github.io/language/llama-adapter/)
  * LLaMA에 적용 가능한 Vision-conditioned Prefix Tuning 방법을 제시함
* [[24’ CVPR] Honeybee: Locality-enhanced Projector for Multimodal LLM](https://rubato-yeong.github.io/multimodal/honeybee/)
  * Locality와 flexibility를 모두 고려한 C-abstractor를 제안함
  * MLLM instruction tuning 데이터셋 제작 및 학습 방법에 대한 최적의 방법을 제시함
* [[24’ CVPR] Eyes Wide Shut? Exploring the Visual Shortcomings of Multimodal LLMs](https://rubato-yeong.github.io/multimodal/eyes-wide-shut/)
  * Visual Encoder의 한계를 지적하며 CLIP-blind pair의 개념을 제안하고 MMVP 데이터셋을 구축함
  * 이를 극복하기 위해 CLIP과 DINOv2를 융합한 I-MoF 모델을 제시함
* [24' CVPR] VIVL: Towards Better Vision-Inspired Vision-Language Models

<br>

# 🤔 Multimodal Chain-of-Thought

* [[24’ TMLR] Multimodal Chain-of-Thought Reasoning in Language Models](https://rubato-yeong.github.io/multimodal/mmcot/)
  * Lightweight multimodal model(1B)에서 reasoning을 수행하는 two-stage framework를 제안함
* [[24'] Visual CoT: Unleashing Chain-of-Thought Reasoning in Multi-Modal Language Models](https://rubato-yeong.github.io/multimodal/viscot/)
  * Multimodal LLM에서 bounding box를 예측하는 image reasoning step을 추가한 Visual CoT 모델을 제시함

<br>

# 🎢 Further Research Topics

* [[24' CVPR] REAL: The Neglected Tails in Vision-Language Models](https://rubato-yeong.github.io/multimodal/real/)
  * VLM의 pretraining data가 long-tailed concept distribution을 보임을 확인하고, 이를 해결하기 위한 REAL-Prompt와 REAL-Linear을 제안함

<br>
