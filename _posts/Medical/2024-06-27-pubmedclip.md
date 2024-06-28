---
title:  "[21'] PubMedCLIP: Does CLIP Benefit Visual Question Answering in the Medical Domain as Much as it Does in the General Domain?" 
excerpt: ""

categories:
  - Medical
tags:
  - [Contrastive Learning, Dataset]

toc: true
toc_sticky: true
 
date: 2024-06-27
last_modified_at: 2024-06-27
---

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/1c0c7040-9f77-4a25-9104-cdea67589cb8){: .align-center}

<br>

# 🔍 Abstract

**PubMedCLIP**은 다른 CLIP 모델과 크게 다르지 않다. 이 논문의 가장 큰 contribution은 ROCO(80K) 데이터셋을 사용한 것이다. 이 데이터셋은 **PubMed**에서 얻은 다양한 Radiology Data를 포함하고 있어 기존에 X-ray에 치중되었던 데이터셋을 더 넓은 범위로 확장하는 역할을 했다. 이후 PubMed로부터 데이터를 추출하는 파이프라인이 구축되었고, 이를 통해 더 다양한 데이터셋이 만들어졌다. 이 논문은 그러한 연구들의 pioneer 역할을 했다.

<br>

# 1. Method

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/0cbf8e59-e1a1-4a39-8942-f98be7baeff3){: .align-center}

기존 Medical domain의 CLIP은 대부분 X-ray를 대상으로 한 모델이었다면, 이 논문에서 제안한 모델은 조금 더 일반화되고 다양한 **PubMed** 데이터를 대상으로 한 모델이다. Fig 1(a)의 Pre-training 과정은 기존 CLIP과 동일하다. 데이터셋은 **ROCO(Radiology Objects in COntext) (80K)**를 사용하였다. Ultrasound, X-Ray, fluoroscopy, PET scans, mammography, MRI, angiography 등 다양한 radiology data로 이루어져 있다. **Encoder, decoder는 CLIP과 동일**하게 사용하였다. 한편, Medical VQA fine-tuning 시에는 **기존의 MedVQA backbone을 그대로 사용**하여 Fig 1(b)와 같은 방식으로 fine-tuning을 진행하였다. CDAE는 일종의 Autoencoder로, reconstruction loss를 통한 self-supervised representation을 얻기 위해 사용된다.

<br>

# 2. Evaluation

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/c8a3c1a3-a569-430c-95b9-cee5567fd469){: .align-center}

문제는 성능이 그렇게 드라마틱하지 않다는 점이다. 아무래도 다른 Pre-training CLIP variant 보다는 **학습 데이터의 수가 적은 것**이 영향을 미친 것으로 보인다. 또한, **PubMed** 데이터는 **다양한 domain**을 포함하고 있기 때문에 더 많은 데이터셋이 필요할 것으로 보인다.

![image](https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/c7671939-53d3-4311-8ea8-12a23c507f0b){: .align-center}

<br>

# 💡 Summary

**PubMedCLIP**에서는 PubMed로부터 추출한 데이터를 활용하여 **Radiology Data**에 대한 **Visual Question Answering**을 수행하였다. 이를 위해 **ROCO(80K)** 데이터셋을 구축하였고, 이를 통해 **다양한 domain**에 대한 데이터셋을 확보하였다. 이는 **Medical Visual Question Answering**에 대한 연구를 활성화시키는 데 큰 역할을 했다. 그러나, **학습 데이터의 부족**으로 인해 성능은 그렇게 뛰어나지 않다.

<br>

# 📃 Reference

* [[21'] PubMedCLIP: Does CLIP Benefit Visual Question Answering in the Medical Domain as Much as it Does in the General Domain?](https://arxiv.org/abs/2112.13906)

<br>