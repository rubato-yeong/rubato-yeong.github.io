---
title:  "[NGS] Illumina Sequencing" 
excerpt: ""

categories:
  - Bioinformatics
tags:
  - [NGS]

toc: true
toc_sticky: true
 
date: 2024-03-23
last_modified_at: 2024-03-23
---

---

# 1. Illumina Sequencing

**Illumina**는 NGS 시장에서 가장 많은 점유율을 가지고 있는 회사 중 하나이다. 출시된 장비로는 iSeq, MiniSeq, MiSeq, NextSeq, HiSeq, NovaSeq 등이 있으며 열거된 순서에 따라 출력(output)이 높아져 대량의 데이터를 생산할 수 있다. 실제로는 장비에 따라 여러 장단점을 따져야 한다고 한다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/1a41e425-5cd9-4e67-82f3-16eff62d2f09">
</p>

실제 과정을 살펴보면 다음과 같다.

1. **Library preparation**: DNA를 **fragmentation**하고, **adapter ligation**을 통해 **library**를 만든다.
2. **Cluster amplification**: Library를 **flow cell**에 loading한 후, **bridge amplification**을 통해 **cluster**를 만든다.
3. **Sequencing**: **fluorescently labeled nucleotides**를 사용하여 **sequencing**을 수행한다.
4. **Data analysis**: 얻은 데이터를 바탕으로 분석을 수행한다.

이제 각 과정을 자세히 살펴보자.

<br>

# 2. Library Preparation

**Library preparation**은 NGS의 **preprocessing** 과정이라고도 하며, sample DNA 또는 RNA가 NGS 장비에서 인식하여 sequencing을 수행할 수 있도록 가공하는 과정이라고 할 수 있다. 보통 fragmentation 후 양 끝에 특정 염기서열을 가진 oligonucleotide를 붙여 library를 제작한다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/74420c0f-3717-40e1-97a7-37b356d0f2b8">
</p>

라이브러리 제작은 **fragmentation, end-repair, 3' end A-tailing, adapter ligation, size selection, library amplification**으로 나누어진다. Fragmentation은 여러 가지 방법으로 가능하지만 주로 초음파 등을 사용한다. Adapter ligation은 ligase 효소를 주로 사용한다. Amplification 이후에는 보통 부산물을 제거하는 clean-up 과정을 거친다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/9ed132d5-3865-4626-b133-72c04c49bc4c">
</p>

Illumina의 library는 양쪽 끝에 모두 index sequence, 즉 샘플 바코드(barcode)를 가지고 있다고 해서 **dual-indexed library**라고 한다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/7df27be1-8191-40e0-b3b9-b21d1dbc03ad">
</p>

Library 각 부분의 역할을 알아보자.

* **Adapter**: Flow cell에 부착될 수 있는 특정 서열 (P5, P7 Adapter)
* **Index(Barcode/tag)**: 3', 5' 끝에 부착되어 각 library를 구분하는 unique sequence
* **Sequencing Primer Binding Site**: sequencing primer가 결합할 수 있는 서열 (Rd1 SP, Rd2 SP)
* **Insert**: 관심을 가지고 있는 DNA sequence

Index의 경우 **multiplexing**에 이용된다. Multiplexing이란 **2개 이상의 sample을 동시에 sequencing**하는 것을 의미한다. 이를 통해 시간과 비용을 절약할 수 있다. 즉, 다른 샘플에 다른 index를 부여한 뒤 동일한 flow cell에서 한 번에 sequencing을 수행할 수 있다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/946f8c5d-53c3-4c0f-96ac-70d96877310a">
</p>

그러나 multiplexing을 사용하면 아래와 같이 **index hopping(index switching)**에 의해 잘못된 index가 부여되어 error가 발생하는 경우가 있으므로 주의를 요한다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/7f66a919-f79b-4d50-ab99-ddad3dc67426">
</p>

<br>

# 3. Cluster Amplification

**Cluster amplification**은 flow cell에서 이루어진다. Flow cell은 **oligonucleotide-coated glass slide**로, library가 hybridization될 수 있도록 형성되어 있다. 줄여서 여기 있는 P5, P7과 같은 oligonucleotide를 **oligo**라고 한다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/c21039d1-4abe-4e2a-884c-2bf735b23761">
</p>

Cluster amplification은 **bridge amplification**, **bridge PCR**이라고도 하며 아래와 같은 과정을 거쳐 **동일한 DNA insert를 가진 집단**인 **cluster** 여러 개를 형성하게 된다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/f2cfa1d0-5e04-44de-adfe-e277dd7dc82e">
</p>

<br>

# 4. Sequencing

**Sequencing**은 SBS(Sequencing by Synthesis) 방식으로 이루어진다. SBS는 **fluorescently labeled nucleotides**를 사용하여 sequencing을 수행하는 방식이다. 아래와 같은 과정을 거치며, 이를 통해 forward, reverse direction으로 index(barcode) sequencing 및 insert sequencing이 이루어진다. 이때 읽은 염기서열을 **read**라고 하고, **read length**는 sequencing 과정에서 읽은 염기서열의 길이를 의미하며 이는 **cycle의 수**와 같다. 보통 최대 100~200bp 정도이다. 헷갈리는 개념으로 **read number**가 있는데 이는 **cluster의 수**와 동일한 개념으로 생각하면 된다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/0dfb4d04-6392-4c31-a501-1b7239b72009">
</p>

이를 전체적으로 보면 다음과 같다. **Flow cell**에는 1~8개의 **lane**이 있고, 각 lane에는 다른 sample이 loading될 수 있다. 각 lane은 **tile**로 나누어져 있고, 각 tile은 **cluster**로 나누어져 있으며, 각 cluster의 형광 발현을 보고 sequencing을 진행한다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/389c5e84-ef4c-4235-88b6-7c14ded94578">
</p>

Illumina sequencing의 큰 특징 중 하나는 **paired end sequencing(PE)**이다. 즉, 두 번에 나누어 insert의 양 끝을 모두 sequencing하는 방식인데, 이를 사용하면 **indel mutation, repeats** 등을 더 잘 탐지할 수 있다. Paired read 사이의 거리는 알려져 있다. 이를 **insert size**라고 하며, 이 값은 library preparation 과정 중 size selection 과정에서 결정되었다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/696c6381-c259-4083-9cea-5c14b09c5436">
</p>

이 sequencing 과정을 분석하여 FASTQ 파일을 얻게 되는데, 이 과정을 **base calling**이라고 한다. **FASTQ** 파일은 DNA **fragment의 염기서열 정보와 quality 정보**를 담고 있는 파일이다. 이를 바탕으로 다음 단계인 **data analysis**를 수행하게 되는데, 이 과정은 사실상 NGS 분석의 핵심 영역으로 다음에 더 깊이 다루도록 하겠다.

<br>

# 5. Summary

지금까지의 과정을 영상으로 확인할 수 있다.

<iframe width="1261" height="719" src="https://www.youtube.com/embed/fCd6B5HRaZ8" title="Overview of Illumina Sequencing by Synthesis Workflow" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

<br>

# 📃 Reference

* [NGS(Next Generation Sequencing) 기반 유전자 검사의 이해(1)](https://www.biotimes.co.kr/news/articleView.html?idxno=4602)
* [How short inserts affect sequencing performance](https://support.illumina.com/ko-kr/bulletins/2020/12/how-short-inserts-affect-sequencing-performance.html)
* [An introduction to Next-Generation Sequencing Technology](https://www.illumina.com/content/dam/illumina-marketing/documents/products/illumina_sequencing_introduction.pdf)
* [GA Pipeline Module 2: Cluster Generation](https://www.broadinstitute.org/files/shared/illuminavids/clusterGenSlides.pdf)
* [GA Pipeline Module 3: Sequencing](https://www.broadinstitute.org/files/shared/illuminavids/sequencingSlides.pdf)
* [Illumina Sequencing](https://joshuapenalba.com/illumina-sequencing/)

<br>