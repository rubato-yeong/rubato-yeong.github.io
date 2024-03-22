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


# 4. NGS의 장비

## 3.1. Illumina

**Illumina**는 NGS 시장에서 가장 많은 점유율을 가지고 있는 회사 중 하나이다. 출시된 장비로는 iSeq, MiniSeq, MiSeq, NextSeq, HiSeq, NovaSeq 등이 있으며 열거된 순서에 따라 출력(output)이 높아져 대량의 데이터를 생산할 수 있다. 실제로는 장비에 따라 여러 장단점을 따져야 한다고 한다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/1a41e425-5cd9-4e67-82f3-16eff62d2f09">
</p>

실제 과정을 살펴보면 다음과 같다.

1. **Library preparation**: DNA를 **fragmentation**하고, **adapter ligation**을 통해 **library**를 만든다.
2. **Cluster amplification**: Library를 **flow cell**에 loading한 후, **bridge amplification**을 통해 **cluster**를 만든다.
3. **Sequencing**: **fluorescently labeled nucleotides**를 사용하여 **sequencing**을 수행한다.
4. **Data analysis**: 얻은 데이터를 바탕으로 분석을 수행한다.

Illumina sequencing에 대한 자세한 과정은 다음에 더 깊이 다루도록 하겠다.