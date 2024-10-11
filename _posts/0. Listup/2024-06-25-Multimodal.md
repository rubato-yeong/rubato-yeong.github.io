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
last_modified_at: 2024-10-11
---

# 🎩 Survey

* [[24’] A Survey on Multimodal Large Language Models](https://rubato-yeong.github.io/multimodal/survey-MLLM/)
  * Multimodal LLM 전반에 대해 정리한 글
* [[24'] Hallucination of Multimodal Large Language Models: A Survey](https://rubato-yeong.github.io/multimodal/hallu-survey/)
  * Multimodal LLM에서의 Hallucination의 Definition, Causes, Benchmark, Mitigation 등을 정리한 글

<br>

# 🔮 Architecture

## 1. Foundation Models

### LLaVA

* [[23’ NIPS] LLaVA: Visual Instruction Tuning](https://rubato-yeong.github.io/multimodal/llava/)
  * Visual Instruction Tuning을 위한 LLaVA 모델을 제시함
* [[24’ CVPR] LLaVA-1.5: Improved Baselines with Visual Instruction Tuning](https://rubato-yeong.github.io/multimodal/llava1_5/)
  * Academic task를 잘 하지 못하는 LLaVA의 단점을 data instructization으로 극복함
  * Adapter 개선, High-resolution input 사용 등의 방법을 제시함

### BLIP

* [[23' ICML] BLIP-2: Bootstrapping Language-Image Pre-training with Frozen Image Encoders and Large Language Models](https://rubato-yeong.github.io/multimodal/blip2/)
  * BLIP-2는 cost-efficient한 LMM을 만들고자 하는 시도로, (1) 이미 학습된 Image Encoder와 LLM을 사용하고, (2) 여러 Objective로 분리하여 학습을 진행하여 효율성을 높임
  * Representation Learning과 Generative Learning으로 나누어진 Two-stage 학습을 수행, 특히 Representation Learning에서는 Q-Former 구조가 도입되었으며 ITC, ITG, ITM Objective를 사용하여 학습
* [[24'] xGen-MM (BLIP-3): A Family of Open Large Multimodal Models](https://rubato-yeong.github.io/multimodal/blip3/)
  * xGen-MM은 BLIP-2의 문제점을 (1) 데이터의 크기와 다양성, (2) Architecture의 Scalability, (3) Training Objective의 단순화를 통해 해결함
  * Q-Former가 아닌 Perceiver를 사용하고, High-resolution Image의 정보를 보존하기 위해 Any-Resolution Visual Token Sampling을 사용하였으며, Interleaved Dataset을 사용하여 Multi-Image Input을 제공함

## 2. Few-shot Learning

* [[22’ NIPS] Flamingo: a Visual Language Model for Few-Shot Learning](https://rubato-yeong.github.io/multimodal/flamingo/)
  * Multimodal Few-shot learning task를 위한 Flamingo 모델을 제시함
* [[23' NIPS] KOSMOS-1: Language Is Not All You Need: Aligning Perception with Language Models](https://rubato-yeong.github.io/multimodal/kosmos-1/)
  * Multimodal in-context learning, multimodal chain-of-thought를 가능하게 하는 interleaved image-text input을 사용한 KOSMOS-1 모델을 제시함
  * IQ Test로 nonverbal in-context learning capability를 측정한 점이 특징
* [[24’] MM1: Methods, Analysis & Insights from Multimodal LLM Pre-training](https://rubato-yeong.github.io/multimodal/mm1/)
  * Apple의 논문으로, MLLM Pipeline을 분석하고, 이를 최적화한 MM1 모델을 제시함

## 3. Visual Encoder & Adapter

* [[21’ ICML] VL-T5: Unifying Vision-and-Language Tasks via Text Generation](https://rubato-yeong.github.io/multimodal/vlt5/)
  * Vision information을 text token으로 전환하는 구조의 multi-task unified framework를 제시함
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
  * LISA와 같은 LLM-aided segmentation에서의 In-context Learning의 효과를 발견함
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
* [[24'] PSALM: Pixelwise SegmentAtion with Large Multi-Modal Model](https://rubato-yeong.github.io/multimodal/psalm/)
  * Unified Segmentation을 위해 Task의 Condition을 Category, Sentence, Visual-Prior로 나누는 것을 제안하였고, 이를 동시에 학습하여 성능 개선을 이룸
  * 다만, LLM의 Text Output을 완전히 포기하고 Feature Encoder로만 사용하여 Conversation 능력을 갖추지 못함
* [[24'] F-LMM: Grounding Frozen Large Multimodal Models](https://rubato-yeong.github.io/multimodal/f-lmm/)
  * Frozen LLM의 Attention Map을 사용해 Segmentation Map을 생성하는 F-LMM 모델을 제시함
  * 기존의 Grounding-Chat Trade-off 문제를 해결하고, LLM의 Conversation 능력이 좋을수록 Grounding 능력도 좋을 수 있다는 것을 보여줌

### Few-shot Segmentation

* [[24’ CVPR] LLaFS: When Large Language Models Meet Few-Shot Segmentation](https://rubato-yeong.github.io/multimodal/llafs/)
  * 기존 Few-shot Segmentation을 LLM에서 In-context Learning 방식으로 해석함
  * Region-attribute table을 제안하여 LLM이 이미지를 region-wise attribute로 이해할 수 있도록 함
  
<br>

# 🚄 Efficient LMM

* [[24' ECCV] FastV: An Image is Worth 1/2 Tokens After Layer 2](https://rubato-yeong.github.io/multimodal/fastv/)
  * Visual Token의 Low Attention Efficiency를 발견하고 Deep Layer에서의 Visual Token은 Redundant할 것으로 예상함
  * 이를 통해 Early Layer에서 Visual Token을 제거하여 계산량을 줄이는 Training-free 방법론인 FastV를 제안하였고, 어느 정도까지의 Token Reduction 후에도 성능 하락이 거의 없음
* [[24' ECCV] FlexAttention for Efficient High-Resolution Vision-Language Models](https://rubato-yeong.github.io/multimodal/flexattention/)
  * Low-resolution Input에서의 Attention Score를 기반으로 High-resolution Token을 선택적으로 활용하는 FlexAttention을 제안함
  * 해당 방법은 Training-free가 아니며, Evaluation 과정이 과장되어 있음
* [[24'] LLaVA-PruMerge: Adaptive Token Reduction for Efficient Large Multimodal Models](https://rubato-yeong.github.io/multimodal/prumerge/)
  * CLIP의 `[CLS]` Token과의 Attention Score를 통해 LMM에 중요한 Image Token을 선택할 수 있다고 주장하나, 이 방법으로 수행한 PruMerge에는 한계가 있으며 Spatial Uniform Sampling을 추가한 PruMerge+에서만 성능이 유지됨
* [[24'] HiRED: Attention-Guided Token Dropping for Efficient Inference of High-Resolution Vision-Language Models in Resource-Constrained Environments](https://rubato-yeong.github.io/multimodal/hired/)
  * Visual Token 중 일부만 LMM Attention이 높은 것을 발견하고, 이를 CLIP의 `[CLS]` Token Attention으로 예측하는 Heuristic한 방법을 제안
  * 적은 Visual Token을 사용하여 Inference Efficiency는 향상시키면서 성능은 약간 저하됨; 일부에서는 오히려 적은 Visual Token을 사용하는 것이 성능이 높게 나타남
* [[24'] Quadratic Is Not What You Need For Multimodal Large Language Models](https://rubato-yeong.github.io/multimodal/layercut/)
  * LMM에서 Visual Token 간의 Attention을 제거하고, Visual Token을 계산하는 Layer를 줄이는 방법, 각각 AttnCut과 LayerCut을 제안하여 Quadratic Complexity를 줄이면서도 성능을 유지할 수 있음을 보임
  * Lower-level Layer에서만 MLLM이 Vision-Language 간 Modality Gap을 줄여 Interaction이 일어나고, Higher-level Layer에서는 Complex Visual Reasoning에 Visual Modality가 잘 고려되지 않고 대신 Language Modality에 의해 계산이 이루어지는 것이 효율적이라는 가설을 제시

<br>

# 🐍 Hallucination

## 1. Hallucination Evaluation

* [[18' EMNLP] CHAIR: Object Hallucination in Image Captioning](https://rubato-yeong.github.io/multimodal/hallu-survey/)
  * Object Captioning Task에서의 Object Hallucination을 측정하는 Metric을 제시함
* [[23' EMNLP] POPE: Evaluating Object Hallucination in Large Vision-Language Models](https://rubato-yeong.github.io/multimodal/hallu-survey/)
  * CHAIR가 Instruction 및 Generated Length에 의해 Variation이 심하다는 문제를 들어, Yes-or-No 문제로 Hallucination을 객관적으로 평가하는 Benchmark인 POPE를 제시함
  * 세 개의 Subset random, popular, adversarial을 제시하고, 이를 통해 LMM의 Statistic Bias가 Hallucination을 유발하는 것을 확인함
* [[23'] MME: A Comprehensive Evaluation Benchmark for Multimodal Large Language Models](https://rubato-yeong.github.io/multimodal/hallu-survey/)
  * 크게 Perception과 Cognition을 측정하는 14개의 subtask로 구성되며, 일반적인 LMM 성능 측정을 위해 고안됨
  * Object Hallucination을 측정하기 위해 Existence, Count, Position, Color subtask를 활용할 수 있음
* [[23'] AMBER: An LLM-free Multi-dimensional Benchmark for MLLMs Hallucination Evaluation](https://rubato-yeong.github.io/multimodal/hallu-survey/)
  * 오로지 Object Existence만 측정하며 단답형(Discriminative Task)으로만 구성된 POPE의 문제를 지적함
  * Existence, Attribute, Relation을 모두 평가하며 Discriminative, Generative Task를 모두 포함한 AMBER Benchmark를 제시함
* [[24' CVPR] HallusionBench: An Advanced Diagnostic Suite for Entangled Language Hallucination and Visual Illusion in Large Vision-Language Models](https://rubato-yeong.github.io/multimodal/hallusionbench/)
  * LMM의 Hallucination을 평가하기 위하여 Vision Dependent, Vision Supplement 질문을 제시하는 HallusionBench Benchmark를 제시함
  * LMM의 Hallucination을 Language Hallucination과 Visual Illusion으로 구분하고, 각 현상이 GPT-4V를 포함한 다양한 LMM에서 흔하게 발생한다는 것을 보여줌
  
## 2. Hallucination Mitigation

### Contrastive Decoding

* [[24' CVPR] VCD: Mitigating Object Hallucinations in Large Vision-Language Models through Visual Contrastive Decoding](https://rubato-yeong.github.io/multimodal/vcd/)
  * Distorted Image를 사용하여 Visual Uncertainty를 부여한 모델에서는 Language Prior와 Statistical Bias가 증폭되어 Hallucination이 발생하는 것을 확인
  * 기존 LMM과 Distorted Image를 사용한 LMM 간의 Contrastive Decoding을 수행하여 Object Hallucination을 개선
* [[24' CVPR] M3ID: Multi-Modal Hallucination Control by Visual Information Grounding](https://rubato-yeong.github.io/multimodal/m3id/)
  * Visual Input에 Output이 얼마나 의존하는지 측정하는 PDM(Visual Prompt Dependency Measure)를 만들고, 생성되는 문장이 길어짐에 따라 Image의 기여도가 떨어져 Hallucination이 증가하는 fading memory effect를 확인
  * 문장이 길어짐에 따라 Image의 기여도를 높이는 Dynamic Adjustment를 포함한 Contrastive Decoding을 수행하여 Object Hallucination을 개선
* [[24'] IBD: Alleviating Hallucinations in Large Vision-Language Models via Image-Biased Decoding](https://rubato-yeong.github.io/multimodal/ibd/)
  * Image에 대한 Attention Score를 높여 Image-biased Model을 만들고, 이를 기존 LMM과 혼합하여 Image-biased Decoding을 수행하였음
  * Image-biased Model은 Content Token의 Probability를 높이고, Functional Token의 Probability를 낮추는 효과가 있음; 또한 두 모델의 Token Probability Distribution이 유사할수록 Image-biased Model의 예측 능력이 떨어짐
  * LLM의 Factual Recall과 비슷하게 Content Word를 생성하는 것은 어렵기 때문에, Late Layer까지도 Probability Distribution이 바뀌는 것을 확인함
  * Image-based Hallucination은 Visual Content와 LLM의 Parametric Knowledge 사이의 충돌이 있는 경우 빈번하게 발생함
* [[24' ECCV] Contrastive Region Guidance: Improving Grounding in Vision-Language Models without Training](https://rubato-yeong.github.io/multimodal/multimodal-cd-1/)
  * Visual Prompt(Bounding Box 등)를 Fine-tuning 없이도 이해하도록 Masking 전후 LMM에 대해 Contrastive Decoding을 수행하여 성능을 개선함
* [[24'] Pensieve: Retrospect-then-Compare Mitigates Visual Hallucination](https://rubato-yeong.github.io/multimodal/multimodal-cd-1/)
  * LMM의 Visual Branch가 정확한 정보뿐만 아니라 Hallucinated Concept까지 지지하는 것을 발견하고, 이를 Retrieval을 활용한 Contrastive Decoding을 통해 개선함
  * Visual Branch의 Hallucination을 유도하는 Noise와 Language Branch의 Hallucination을 유도하는 Noise를 구분하여 Adaptive Parameter를 조절함
* [[24' ACL Findings] ICD: Mitigating Hallucinations in Large Vision-Language Models with Instruction Contrastive Decoding](https://rubato-yeong.github.io/multimodal/multimodal-cd-1/)
  * Instruction(Role Prefix)을 부여하면 LMM의 Language Prior 또는 Pretraining Data Bias에 취약해져 Hallucination이 심해지는 현상을 발견함
  * 기존 LLM과 추가적인 Disturbance Instruction을 준 LLM 간의 Contrastive Decoding을 수행하여 Object/Attribute Hallucination을 개선하였고, 이는 VCD보다 효과적이며 VCD와 보완적으로 사용할 수도 있음
* [[24'] HIO: Alleviating Hallucinations in Large Vision-Language Models through Hallucination-Induced Optimization](https://rubato-yeong.github.io/multimodal/hio/)
  * Contrastive Decoding이 잘 작동하는 조건을 이론적으로 분석하고, 이를 포함하여 Hallucination을 유도하는 DPO Loss를 제안함
  * 이를 통해 Hallucinated LMM을 만들고 Contrastive Decoding을 수행하여 Object Hallucination을 개선함
* [[24'] AvisC: Don't Miss the Forest for the Trees: Attentional Vision Calibration for Large Vision Language Models](https://rubato-yeong.github.io/multimodal/multimodal-cd-2/)
  * Visual Hallucination을 유발하는, 과도한 Attention을 가진 Image Token을 발견하고 이를 Blind Token이라 명명함
  * Blind Token만을 보는 Hallcinated LMM을 만들어 Contrastive Decoding을 수행하여 Object Hallucination을 개선함
* [[24'] RITUAL: Random Image Transformations as a Universal Anti-hallucination Lever in LVLMs](https://rubato-yeong.github.io/multimodal/multimodal-cd-2/)
  * Image Transformation을 통해 Augmented된 Image를 사용한 LMM과 원본 Image를 사용한 LMM의 Probability를 Ensemble하여 Object Hallucination을 개선함
* [[24'] CODE: Contrasting Self-generated Description to Combat Hallucination in Large Multi-modal Models](https://rubato-yeong.github.io/multimodal/multimodal-cd-2/)
  * Image Description을 생성한 것으로 Image를 대체하는 경우 Semantic Information이 충분하지 않아 Hallucination이 발생하는 것을 관찰하고, Self-generated Description을 사용한 LMM과 Contrastive Decoding을 수행하여 Object/Complicated Reasoning Hallucination을 개선함
  * Image Description은 Vision과 Language Reasoning이 결합된 Task에서는 더 효과적일 수 있으나, Vision Reasoning이 이루어져야 하는 시각적으로 복잡한 Task에서는 Hallucination을 유발할 수 있음을 [VDGD](https://rubato-yeong.github.io/multimodal/vdgd/) 논문과 비교하여 작성하였음
  * Contrastive Decoding 시 Token Distribution에 따라 Parameter를 조절하는 Dynamic Restriction $\alpha_ t$, Adaptive Information Constraint $\beta_ t$을 사용하여 Hallucination을 더 정밀하게 개선함
* [[24'] AGLA: Mitigating Object Hallucinations in Large Vision-Language Models with Assembly of Global and Local Attention](https://rubato-yeong.github.io/multimodal/multimodal-cd-2/)
  * Image Attention을 Prompt-independent한 Global Attention과 Prompt-dependent한 Local Attention으로 나누고, 기존 LMM이 Global Attention 값이 높아 Local Attention을 무시하고 있어 Hallucination이 발생한다는 것을 관찰함
  * Global Attention을 Masking하고, Local Attention에 집중하도록 한 LMM과 기존 LMM을 Assembly하여 Object Hallucination을 개선함

### Other Strategy

* [[24' CVPR] OPERA: Alleviating Hallucination in Multi-Modal Large Language Models via Over-Trust Penalty and Retrospection-Allocation](https://rubato-yeong.github.io/multimodal/opera/)
  * LMM의 Attention Map을 분석하여 Hallucination이 발생하는 columnar pattern과 summary token을 발견하였고, 이는 repetition과도 관련됨
  * Hallucination을 줄이기 위해 Over-Trust Logit Penalty와 Retrospection-Allocation Strategy를 제안함
* [[24' ICLR-WS] Skip \n: A Simple Method to Reduce Hallucination in Large Vision-Language Models](https://rubato-yeong.github.io/multimodal/skip-n/)
  * LMM Hallucination은 보통 `\n\n` token 이후에 발생한다는 것을 발견하고, `\n`의 생성을 줄이는 Skip \n이라는 간단한 방법을 제안하였고, 이는 기존 방법들보다 훨씬 간단하고 효과적임
  * Greedy Decoding이 Hallucination에 Robust하다는 것을 재발견함
* [[24'] VDGD: Mitigating LVLM Hallucinations in Cognitive Prompts by Bridging the Visual Perception Gap](https://rubato-yeong.github.io/multimodal/vdgd/)
  * LMM의 Visual Ability를 단순히 이미지를 설명하는 VR(Visual Recognition)과 이를 이해하고 추론하는 VP(Visual Perception)으로 나누었고, VP가 잘 되지 않는 이유를 VR과 Cognitive Skill을 동시에 활용하기 어렵다는 Visual Perception Gap으로 설명함
  * VR Hallucination을 그 원인에 따라 Language, Vision, Style, IT으로 나누어 분석하고, Language Hallucination에서 VCD가 잘 작동하는 이유를 Probability Gap으로 설명하며 Style, IT에 대한 연구가 부족하다고 지적함
  * VDGD(Visual Description Grounding Decoding)을 제안함; (1) Image Description을 생성한 뒤 다음 Inference에서 정답을 맞추도록 하고, (2) Image Description과의 KL-Divergence가 낮은 Token을 Preference로 두어 Decoding을 수행하는 방법으로 Hallucination을 줄일 수 있음

<br>

# 🕶 Mechanical Interpretability

## 1. Feed-Forward Network

* [[24' ICCV-WS] Multimodal Neurons in Pretrained Text-Only Transformers](https://rubato-yeong.github.io/multimodal/lmm-mmn/)
  * Gradient-based Attribution 계산을 통해 처음으로 Multimodal Neuron을 발견하였음; Early-to-Mid Layer에서 이러한 FFN이 발견되나, 이는 Gradient-based Approach의 Bias 때문일 수 있음
  * Image Embedding은 그대로 Text Semantic을 가지고 있는 것이 아니며, FFN의 Multimodal Neuron을 통해 이해할 수 있는 Text Semantic으로 Translation이 일어난다고 주장
* [[24' ACL Findings] Finding and Editing Multi-Modal Neurons in Pre-Trained Transformers](https://rubato-yeong.github.io/multimodal/lmm-mmn/)
  * LLaVA와 같은 LMM Setting에서 Forward Contribution을 통해 Multimodal Neuron을 찾아내고, Late Layer에서 더 많이 발견되는 것을 확인
  * 이러한 Neuron의 Sensitivity, Specificity, Causal-Effect를 확인

## 2. Inner Workings

* [[24'] Towards Interpreting Visual Information Processing in Vision-Language Models](https://rubato-yeong.github.io/multimodal/llava-interp/)
  * Visual Token Ablation을 통해 Object 정보는 해당 Token Position에 Localized되어 있음을 보임
  * Logit Lens를 통해 Visual Token이 Late Layer에서 Vocabulary에 있는 Interpretable Token Embedding으로 Mapping되는 것을 보임
  * Attention Knockout을 통해 Middle-to-Late Layer에서 Object Visual Token으로부터 Text Token으로의 Attention Flow가 일어남을 밝힘
* [[24'] Intriguing Properties of Large Language and Vision Models](https://rubato-yeong.github.io/multimodal/intriguing/)
  * Visual Token은 Localized Information을 가지고 있으며 어느 정도 Permutation-invariant함
  * Early Layer에서 Visual Information Processing이 일어나고, 이후에는 Text Interpretation이 더 중요하다고 주장함

## 3. Interpretation Framework

* [[24' ICLR-WS] A Concept-Based Explainability Framework for Large Multimodal Models](https://rubato-yeong.github.io/multimodal/lmm-nmf/)
  * Toy Dataset에 대해 공통 Concept Dictionary를 Semi-NMF 방법으로 찾아내고 이를 Logit Lens로 해석함
  * Layer Ablation을 통해 Intermediate to Late Layer에서 Multimodal Structure가 나타남을 확인함

<br>

# 💯 Evaluation and Benchmark

* [[24'] MMStar: Are We on the Right Way for Evaluating Large Vision-Language Models?](https://rubato-yeong.github.io/multimodal/mmstar/)
  * 기존 VLM Evaluation이 Dataset의 Quality와 Data Leakage 문제로 부정확하다는 것을 지적하고, 이를 해결하기 위한 MMStar Benchmark를 제시함
* [[24' ICLR] Beyond Task Performance: Evaluating and Reducing the Flaws of Large Multimodal Models with In-Context Learning](https://rubato-yeong.github.io/multimodal/x-icl/)
  * MLLM의 능력을 5가지 축(Hallucination, Abstention, Compositionality, Explanability, Instruction Following)으로 나누어 평가하고, 모든 축에서 MLLM의 성능은 충분하지 않음을 확인함
  * 일반적으로 사용되는 Multimodal ICL은 Instruction Following을 약간 개선시키지만 Hallucination은 오히려 악화시킴
  * 따라서 Multitask-ICL, Chain-of-Hindsight-ICL, Self-Correcting-ICL을 제안하고, 이러한 방법들로 Abstention, Explanability 등을 개선시킬 수 있음을 보여줌
* [[24' ICML] Fool Your (Vision and) Language Model With Embarrassingly Simple Permutations](https://rubato-yeong.github.io/multimodal/adv-permutation/)
  * MCQA(Multiple-Choice Question Answering) 상황에서 LLM과 VLLM이 Permutation에 굉장히 취약하며, 이는 Position Bias, Distractor Pattern, Symbol-Content Spurious Correlation 등의 요소로 인해 발생함

<br>

# 🎢 Further Research Topics

* [[24' CVPR] REAL: The Neglected Tails in Vision-Language Models](https://rubato-yeong.github.io/multimodal/real/)
  * VLM의 pretraining data가 long-tailed concept distribution을 보임을 확인하고, 이를 해결하기 위한 REAL-Prompt와 REAL-Linear을 제안함

<br>
