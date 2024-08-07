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
last_modified_at: 2024-07-01
---

# 🎩 Survey

* [[24’] A Survey on Multimodal Large Language Models](https://rubato-yeong.github.io/multimodal/survey-MLLM/)
  * Multimodal LLM 전반에 대해 정리한 글

<br>

# ⏱ Early Works

* [[21’ ICML] VL-T5: Unifying Vision-and-Language Tasks via Text Generation](https://rubato-yeong.github.io/multimodal/vlt5/)
  * Vision information을 text token으로 전환하는 구조의 multi-task unified framework를 제시함

<br>

# 🧷 Contrastive Model

## 1. Vision-Language Alignment

* [[21’ ICML] CLIP: Learning Transferable Visual Models From Natural Language Supervision](https://rubato-yeong.github.io/multimodal/clip/)
  * CLIP(Contrastive Language-Image Pre-training) 모델을 제시함

## 2. Segmentation

* [[22' ECCV] MaskCLIP: Extract Free Dense Labels from CLIP](https://rubato-yeong.github.io/multimodal/maskclip/)
  * MaskCLIP은 학습 없이 CLIP Last Layer Feature Map으로부터 Dense Label을 추출하고, 이를 다듬는 (1) Key Smoothing과 (2) Prompt Denoising 기법을 제시함
  * MaskCLIP+는 Pseudo Labeling과 Self-training을 통해 MaskCLIP의 성능을 더 개선시킴
* [[22' ECCV] Zsseg: A Simple Baseline for Open-Vocabulary Semantic Segmentation with Pre-trained Vision-Language Model](https://rubato-yeong.github.io/multimodal/zegclip/)
  * CLIP은 Image 전체에 대한 정보를 추출하는 반면 Segmentation Task는 Pixel 단위로 한다는 점을 지적하며 Mask Proposal Model을 사용하여 Region Proposal을 제시하고, 이를 CLIP을 이용하여 Zero-shot Classification을 수행하는 Two-stage framework를 제시함
* [[22' CVPR] ZegFormer: Decoupling Zero-Shot Semantic Segmentation](https://rubato-yeong.github.io/multimodal/zegclip/)
  * 기존의 Pixel-level Segmentation은 Seen object와 Unseen object의 의미를 잘 통합하지 못한다는 문제점을 지적함
  * ZS3(Zero-Shot Semantic Segmentation)을 (1) Class-agnostic Grouping과 (2) Segment-level Zero-shot Classification 문제로 분리하여 해결하는 방법을 제시함
* [[23' CVPR] ZegCLIP: Towards Adapting CLIP for Zero-shot Semantic Segmentation](https://rubato-yeong.github.io/multimodal/zegclip/)
  * 기존 CLIP 기반 two-stage framework가 CLIP의 zero-shot ability를 온전히 활용하지 못한다는 문제점을 지적하고, 이를 해결하기 위해 CLIP의 zero-shot prediction을 pixel level로 확장하는 ZegCLIP 모델을 제시함
  * 직관적인 one-stage framework에 DPT(Deep Prompt Tuning), Non-mutually Exclusive Loss (NEL), Relationship Descriptor (RD)라는 세 가지 기법을 적용하여 성능을 two-stage framework 수준보다 높게 끌어올림
* [[24' ICML] Cascade-CLIP: Cascaded Vision-Language Embeddings Alignment for Zero-Shot Semantic Segmentation](https://rubato-yeong.github.io/multimodal/cascade-clip/)
  * CLIP의 Intermediate Layer가 Rich Object Detail을 포함하고 있음을 확인하고, 단순 Multi-level Feature를 합치는 것은 오히려 성능을 저하시킨다고 주장함
  * 이를 해결하기 위해 Cascaded Vision-Language Embedding Alignment과 Neighborhood Gaussian Aggregation을 제안함

<br>

# 🔮 Generative Model

## 1. Instruction Tuning

* [[23’ NIPS] LLaVA: Visual Instruction Tuning](https://rubato-yeong.github.io/multimodal/llava/)
  * Visual Instruction Tuning을 위한 LLaVA 모델을 제시함
* [[24’ CVPR] LLaVA-1.5: Improved Baselines with Visual Instruction Tuning](https://rubato-yeong.github.io/multimodal/llava1_5/)
  * Academic task를 잘 하지 못하는 LLaVA의 단점을 data instructization으로 극복함
  * Adapter 개선, High-resolution input 사용 등의 방법을 제시함

## 2. Few-shot Learning

* [[22’ NIPS] Flamingo: a Visual Language Model for Few-Shot Learning](https://rubato-yeong.github.io/multimodal/flamingo/)
  * Multimodal Few-shot learning task를 위한 Flamingo 모델을 제시함
* [[23' NIPS] KOSMOS-1: Language Is Not All You Need: Aligning Perception with Language Models](https://rubato-yeong.github.io/multimodal/kosmos-1/)
  * Multimodal in-context learning, multimodal chain-of-thought를 가능하게 하는 interleaved image-text input을 사용한 KOSMOS-1 모델을 제시함
  * IQ Test로 nonverbal in-context learning capability를 측정한 점이 특징
* [[24’] MM1: Methods, Analysis & Insights from Multimodal LLM Pre-training](https://rubato-yeong.github.io/multimodal/mm1/)
  * Apple의 논문으로, MLLM Pipeline을 분석하고, 이를 최적화한 MM1 모델을 제시함

## 3. Visual Encoder & Adapter

* [[24’ ICLR] LLaMA-Adapter: Efficient Fine-tuning of Language Models with Zero-init Attention](https://rubato-yeong.github.io/language/llama-adapter/)
  * LLaMA에 적용 가능한 Vision-conditioned Prefix Tuning 방법을 제시함
* [[23'] LLaMA-Adapter V2: Parameter-Efficient Visual Instruction Model](https://rubato-yeong.github.io/multimodal/llama-adapter-v2/)
  * Visual instruction data 없이 language instruction data와 image-text data를 각각 disjoint parameter에 학습시켜 visual instruction을 효율적으로 학습하는 joint training paradigm을 제시함
  * 이때 disjoint parameter 학습을 위해 visual knowledge early fusion 방법을 사용함
* [[24' ICML] Prismatic VLMs: Investigating the Design Space of Visually-Conditioned Language Models](https://rubato-yeong.github.io/multimodal/prism/)
  * VLM에 대한 거대한 분석을 진행하고, 이에 따른 Prism 모델을 제시함
  * Vision Encoder Pre-training의 성능이 저하되는 이유, CLIP + DINOv2 Ensemble의 성능이 향상되는 이유 등을 Insight로 제시함
* [[24’ CVPR] Honeybee: Locality-enhanced Projector for Multimodal LLM](https://rubato-yeong.github.io/multimodal/honeybee/)
  * Locality와 flexibility를 모두 고려한 C-abstractor를 제안함
  * MLLM instruction tuning 데이터셋 제작 및 학습 방법에 대한 최적의 방법을 제시함
* [[24’ CVPR] Eyes Wide Shut? Exploring the Visual Shortcomings of Multimodal LLMs](https://rubato-yeong.github.io/multimodal/eyes-wide-shut/)
  * Visual Encoder의 한계를 지적하며 CLIP-blind pair의 개념을 제안하고 MMVP 데이터셋을 구축함
  * 이를 극복하기 위해 CLIP과 DINOv2를 융합한 I-MoF 모델을 제시함
* [[24' CVPR] VIVL: Towards Better Vision-Inspired Vision-Language Models](https://rubato-yeong.github.io/multimodal/vivl/)
  * VLM에서 Vision-Language Interaction이 부족함을 지적하며 Visual Encoder, Adapter의 Interaction을 강화한 FPE와 DVCP Module을 제시함
* [[24'] ConvLLaVA: Hierarchical Backbones as Visual Encoder for Large Multimodal Models](https://rubato-yeong.github.io/multimodal/convllava/)
  * ViT에 비해 계산량이 적은 ConvNeXt를 Visual Encoder로 사용하여 High-resolution input을 처리하는 ConvLLaVA 모델을 제시함
  * High-resolution, Many Visual Token, Vision Encoder Training이 성능 향상에 중요하다는 것을 보여줌
* [[24'] Cambrian-1: A Fully Open, Vision-Centric Exploration of Multimodal LLMs](https://rubato-yeong.github.io/multimodal/cambrian-1/)
  * 지금까지의 MLLM Benchmark가 MLLM의 성능을 잘 평가하지 못한다고 지적하며, Vision-centric Benchmark인 CV-Bench를 제안함
  * Vision Encoder Unfreezing, Vision Encoder Ensemble을 통해 성능이 향상되는 것을 확인하고, DINOv2와 ConvNeXt의 의미를 재확인함; 더 나아가 Spatial Vision Aggregator(SVA)를 제안하여 여러 Vision Encoder의 Feature를 효율적으로 종합하는 방법을 제안
  * Data Distribution이 Instruction Tuning에 중요하다는 것을 확인하고, 이를 조정한 Cambrian-7M 데이터셋을 제작; 또한 System Prompting을 통해 Condition을 주고 Instruction Tuning을 진행하는 것이 중요하다는 것을 확인함

<br>

# 🤔 Multimodal Chain-of-Thought

* [[24’ TMLR] Multimodal Chain-of-Thought Reasoning in Language Models](https://rubato-yeong.github.io/multimodal/mmcot/)
  * Lightweight multimodal model(1B)에서 reasoning을 수행하는 two-stage framework를 제안함
* [[24' CVPR] Compositional Chain-of-Thought Prompting for Large Multimodal Models](https://rubato-yeong.github.io/multimodal/ccot/)
  * MLLM이 Attribute, Relationship을 잘 인지하지 못한다는 문제를 지적하고, 이를 해결하기 위해 SG(Scene Graph)를 생성하도록 강제하는 Compositional Chain-of-Thought(CCoT)를 제시함
  * Bounding box와 같은 구체적인 정보까지 SG에 포함시키는 것은 일반적인 MLLM Task에서 불필요하며, 여전히 작은 모델에서는 CCoT의 Hallucination 문제가 발생할 수 있음
* [[24'] Visual CoT: Unleashing Chain-of-Thought Reasoning in Multi-Modal Language Models](https://rubato-yeong.github.io/multimodal/viscot/)
  * Multimodal LLM에서 bounding box를 예측하는 image reasoning step을 추가한 Visual CoT 모델을 제시함

<br>

# 👀 LLM-Aided Visual Reasoning

## 1. Localization

* [[24' ICLR] KOSMOS-2: Grounding Multimodal Large Language Models to the World](https://rubato-yeong.github.io/multimodal/kosmos-2/)
  * Bounding box input/output을 `<loc>` token으로 처리할 수 있는 MLLM을 제시하였으나, 이는 top-left, bottom-right만을 표현한다는 한계점이 있음
* [[23’] Shikra: Unleashing Multimodal LLM’s Referential Dialogue Magic](https://rubato-yeong.github.io/multimodal/shikra/)
  * Bounding box를 단순 text로 취급하여 Input/Output으로 처리할 수 있는 간단한 아이디어를 제시함
  * Grounded Conversation, Multimodal Chain-of-Thought의 초기 아이디어로도 볼 수 있음
* [[24'] GPT4RoI: Instruction Tuning Large Language Model on Region-of-Interest](https://rubato-yeong.github.io/multimodal/gpt4roi/)
  * Region-of-Interest를 RoIAlign으로 embedding하여 MLLM에 사용하는 방법론을 제시함
  * Bounding box를 text로 취급하는 것보다 풍부한 visual information을 포함한다고 주장함
* [[24' ICLR] Ferret: Refer and Ground Anything /Anywhere at Any Granularity](https://rubato-yeong.github.io/multimodal/ferret/)
  * Arbitrary shape의 spatial input을 point-based approach를 통해 visual feature로 추출하는 spatial-aware visual sampler를 제시함
* [[24' CVPR] Osprey: Pixel Understanding with Visual Instruction Tuning](https://rubato-yeong.github.io/multimodal/osprey/)
  * Mask-based Instruction Data의 부족을 지적하고, 이를 해결하는 Osprey-724K 데이터셋을 제작함
  * Mask Input을 처리하기 위해 Mask-Aware Visual Extracter를 사용하였으며, 이는 Spatial Geometry를 반영할 수 있다는 점에서 Ferret의 방법보다 우수함
  * ConvNeXt Vision Encoder가 High-resolution Input 계산량 면에서 ViT보다 우수하다는 것을 보였으나, 성능 비교는 하지 않음
* [[24’ CVPR] PixelLLM: Pixel Aligned Language Models](https://rubato-yeong.github.io/multimodal/pixelllm/)
  * VLM의 language token과 함께 LLM이 어디를 보고 있는지에 대한 localization 정보를 제공함

## 2. Segmentation

### LLM-guided Segmentation

* [[24’ CVPR] LISA: Reasoning Segmentation via Large Language Model](https://rubato-yeong.github.io/multimodal/lisa/)
  * Reasoning segmentation task를 제안하고, embedding-as-mask paradigm을 제안해 `<SEG>` token으로 segmentation mask를 예측함
* [[23’] LISA++: An Improved Baseline for Reasoning Segmentation with Large Language Model](https://rubato-yeong.github.io/multimodal/lisa++/)
  * LISA를 추가 데이터셋을 이용해 Instance Segmentation, SiD(Segmentation in Dialogue)도 가능하도록 확장함
* [[23'] LLaVA-Grounding: Grounded Visual Chat with Large Multimodal Models](https://rubato-yeong.github.io/multimodal/llava-grounding/)
  * 기존 Visual Grounding 모델은 Conversation을 잘하지 못한다는 단점을 지적하고, 이를 해결하기 위해 Grounded Visual Instruction Data를 제작하여 훈련함
* [[24’ CVPR] PixelLM: Pixel Reasoning with Large Multimodal Model](https://rubato-yeong.github.io/multimodal/pixellm/)
  * Lightweight pixel decoder와 segmentation codebook을 이용해 LISA 구조에서 multiple target segmentation이 가능하도록 함
* [[24’ CVPR] GSVA: Generalized Segmentation via Multimodal Large Language Models](https://rubato-yeong.github.io/multimodal/gsva/)
  * LISA에서 GRES Task를 풀기 위해 여러 개의 `<SEG>` token과 `<REJ>` token을 사용할 것을 제안함
* [[24’ CVPR] GLaMM: Pixel Grounding Large Multimodal Model](https://rubato-yeong.github.io/multimodal/glamm/)
  * GCG(Grounded Conversation Generation) Task를 제안하고, Visual Prompt를 Input으로 이용할 수 있는 확장된 LISA 모델을 제안함
  * SA-1B dataset을 재가공하여 GCG task에 맞는 거대 데이터셋인 GranD dataset(11M)을 제작함
* [[24' CVPR] GROUNDHOG: Grounding Large Language Models to Holistic Segmentation](https://rubato-yeong.github.io/multimodal/groundhog/)
  * 강력한 Mask Proposal Model인 Mask2Former+를 사용한 Proposal Stage와 Language-guided Grounding을 사용한 Retrieval Stage로 구성된 GROUNDHOG 모델을 제시함
  * 다양한 task를 포함한 2.5M의 M3G2 dataset을 구축하여 훈련함
* [[24' CVPR] AnyRef: Multi-modal Instruction Tuned LLMs with Fine-grained Visual Perception](https://rubato-yeong.github.io/multimodal/anyref/)
  * LISA 기반으로 BBox, Image, Audio Input을 받을 수 있도록 확장한 AnyRef 모델을 제시함
  * `<SEG>` Token Bottleneck을 지적하여 Previous Token으로부터 추가 정보를 합치는 Refocusing Mechanism을 제시하며, 이는 In-context Learning의 일종으로 간주할 수 있음
* [[24' CVPR] PerceptionGPT: Effectively Fusing Visual Perception into LLM](https://rubato-yeong.github.io/multimodal/perceptiongpt/)
  * SAM을 사용한 LISA와 달리 Bounding Box, Mask 모두 Input/Output으로 사용할 수 있는 `<vis>` Token과 단순한 Encoder-decoder만으로 이루어진 End-to-end 모델로도 충분한 성능을 보일 수 있음을 증명함
  * LISA의 embedding-as-mask paradigm이 discrete representation을 사용하는 것보다 성능이 더 좋고 학습에도 유리하며 속도도 빠르다는 것을 보여줌
  * CLIP multi-layer fusion analysis를 통해 vision-centric task에 CLIP의 intermediate layer가 중요하다는 사실을 발견함
* [[24'] LaSagnA: Language-based Segmentation Assistant for Complex Queries](https://rubato-yeong.github.io/multimodal/lasagna/)
  * Semantic Segmentation의 Negative Class를 Instructization에 활용하여 LISA 등의 모델의 성능을 높이는 Data Augmentation 방법을 제시함
* [[24'] F-LMM: Grounding Frozen Large Multimodal Models](https://rubato-yeong.github.io/multimodal/f-lmm/)
  * Frozen LLM의 Attention Map을 사용해 Segmentation Map을 생성하는 F-LMM 모델을 제시함
  * 기존의 Grounding-Chat Trade-off 문제를 해결하고, LLM의 Conversation 능력이 좋을수록 Grounding 능력도 좋을 수 있다는 것을 보여줌

### Referring Segmentation

* [[22' CVPR] LAVT: Language-Aware Vision Transformer for Referring Image Segmentation](https://rubato-yeong.github.io/multimodal/unilseg/)
  * Visual Encoder에 Language Attention을 주어 Language-Vision Interaction을 강화하여 RIS Task를 해결함
* [[23' CVPR] OVSeg: Open-Vocabulary Semantic Segmentation with Mask-adapted CLIP](https://rubato-yeong.github.io/multimodal/unilseg/)
  * CLIP과 Mask Prompt Tuning을 사용하여 Open-Vocabulary Semantic Segmentation Task를 해결함
* [[23’ CVPR] GRES: Generalized Referring Expression Segmentation](https://rubato-yeong.github.io/multimodal/gres/)
  * Multi-target, Empty-target expression에 대해 대답해야 하는 GRES Task를 제안하고 이를 해결하기 위한 ReLA 모델을 제시함
  * LISA와 같은 LLM-aided segmentation에서의 In-context Learning의 효과를 발견함
* [[24’ CVPR] UniLSeg: Universal Segmentation at Arbitrary Granularity with Language Instruction](https://rubato-yeong.github.io/multimodal/unilseg/)
  * Various granularity에서 모두 segmentation할 수 있는 universal segmentation model을 제안함

### Few-shot Segmentation

* [[24’ CVPR] LLaFS: When Large Language Models Meet Few-Shot Segmentation](https://rubato-yeong.github.io/multimodal/llafs/)
  * 기존 Few-shot Segmentation을 LLM에서 In-context Learning 방식으로 해석함
  * Region-attribute table을 제안하여 LLM이 이미지를 region-wise attribute로 이해할 수 있도록 함
  
<br>

# 🐍 Hallucination

* [[24' CVPR] OPERA: Alleviating Hallucination in Multi-Modal Large Language Models via Over-Trust Penalty and Retrospection-Allocation](https://rubato-yeong.github.io/multimodal/opera/)
  * LMM의 Attention Map을 분석하여 Hallucination이 발생하는 columnar pattern과 summary token을 발견하였고, 이는 repetition과도 관련됨
  * Hallucination을 줄이기 위해 Over-Trust Logit Penalty와 Retrospection-Allocation Strategy를 제안함
* [[24' CVPR] HallusionBench: An Advanced Diagnostic Suite for Entangled Language Hallucination and Visual Illusion in Large Vision-Language Models](https://rubato-yeong.github.io/multimodal/hallusionbench/)
  * LMM의 Hallucination을 평가하기 위하여 Vision Dependent, Vision Supplement 질문을 제시하는 HallusionBench Benchmark를 제시함
  * LMM의 Hallucination을 Language Hallucination과 Visual Illusion으로 구분하고, 각 현상이 GPT-4V를 포함한 다양한 LMM에서 흔하게 발생한다는 것을 보여줌

# 💯 Evaluation and Benchmark

* [[24'] MMStar: Are We on the Right Way for Evaluating Large Vision-Language Models?](https://rubato-yeong.github.io/multimodal/mmstar/)
  * 기존 VLM Evaluation이 Dataset의 Quality와 Data Leakage 문제로 부정확하다는 것을 지적하고, 이를 해결하기 위한 MMStar Benchmark를 제시함

<br>

# 🎢 Further Research Topics

* [[24' CVPR] REAL: The Neglected Tails in Vision-Language Models](https://rubato-yeong.github.io/multimodal/real/)
  * VLM의 pretraining data가 long-tailed concept distribution을 보임을 확인하고, 이를 해결하기 위한 REAL-Prompt와 REAL-Linear을 제안함

<br>
