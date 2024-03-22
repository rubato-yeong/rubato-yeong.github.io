---
title:  "[NGS] Introduction to NGS" 
excerpt: ""

categories:
  - Bioinformatics
tags:
  - [NGS]

toc: true
toc_sticky: true
 
date: 2024-03-22
last_modified_at: 2024-03-22
---

# 1. NGS란 무엇인가?

**NGS(Next Generation Sequencing)**? NGS는 **Sanger sequencing**으로 대표되는 기존의 **direct sequencing** 방법의 단점을 극복한 시퀀싱 방법이다. Sanger sequencing은 DNA를 fragmentation 및 cloning한 후, PCR 과정에서 ddNTP를 이용하여 DNA chain을 종결시키고, 이를 gel electrophoresis를 통해 분석하는 방식이었다. 이러한 방식은 복잡한 준비과정이 필요하고, 비용이 많이 드는 등 효율성이 낮았다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/9d6cca0e-6ebc-4d64-9e93-6f796c0d6dfc">
</p>

NGS는 이러한 Sanger sequencing의 단점을 극복하기 위해 개발된 방법이다. NGS는 **MPS(massively parallel sequencing)**, 혹은 **HTS(high throughput sequencing)**이라고도 불리며, 여러 개의 DNA fragment를 동시에 sequencing하는 방식이다. 이를 통해 빠르게 DNA sequencing을 수행할 수 있게 되었다. Sanger sequencing에 비해 NGS가 이점을 가지는 부분은 크게 다음 세 가지라고 볼 수 있다.

1. **No cloning**: NGS는 DNA를 cloning하지 않고도 원본 DNA에서 직접 sequencing을 수행할 수 있다.
2. **Amplification in parallel**: NGS도 Sanger sequencing처럼 amplification을 하지만, 여러 개의 DNA fragment를 동시에 amplification할 수 있다.
3. **Sequencing in parallel**: NGS는 여러 개의 DNA fragment를 동시에 sequencing할 수 있다.

그러나 Sanger sequencing의 정확도가 NGS보다 높은 편이기에 샘플의 수가 적거나 target gene의 수가 적은 경우에는 여전히 Sanger sequencing을 사용하는 경우도 있다.

<br>

# 2. NGS의 분류

NGS는 크게 **WGS(Whole Genome Sequencing), WES(Whole Exome Sequencing), Targeted sequencing** 등으로 나누어볼 수 있다. **WGS**는 전체 유전체 서열을 모두 분석하는 것이고, **WES**는 전체 유전체 중에서도 exome 영역만을 sequencing하는 것이다. **Targeted sequencing(panel sequencing)**은 관심을 가지고 있는 유전자가 있는 경우 선택한 유전자 서열만을 sequencing하는 방법이다. WGS에서 targeted sequencing으로 갈수록 볼 수 있는 유전자 서열, 즉 **coverage**가 낮아지지만 더 여러 번 서열을 읽을 수 있게 될 것이므로 **accuracy 및 depth**는 높아진다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/1bc26fe9-c771-410b-82c6-2c1318f80f6d">
</p>

<br>

# 3. NGS의 분석 방법

NGS는 그 장비에 따라 구체적인 sequencing 방법이 다르기 때문에, 필요에 따라 그 방법들을 이해할 필요가 있다. 

여기서는 회사 장비별 분석에 앞서 **SBS(Sequencing by Synthesis), CRT(Cyclic Reversible Termination), SNA(Single Nucleotide Addition)** 등의 용어들을 정리하고자 한다.

다양한 NGS platform에서는 각기 다른 시퀀싱 방법을 가지고 있는데, 크게 다음과 같이 나눠 볼 수 있다.

* **SBL(Sequencing by Ligation)**
* **SBS(Sequencing by Synthesis)**
  * **CRT(Cyclic Reversible Termination)**
  * **SNA(Single Nucleotide Addition)**
* **Single-molecule long-read sequencing**
  * **SMRT(Single Molecule Real Time) sequencing**
  * **Nanopore sequencing**
* **Synthetic long read sequencing**

이 중에서도 흔히 거론되는 회사의 장비들을 중심으로 설명하도록 하겠다. 흔히 거론되는 회사로 다음 4가지를 정리해 보았다.

* **일루미나(Illumina)**: CRT
* **써모피셔사이언티픽(Thermo Fisher Scientific)**: SNA
* **퍼시픽바이오사이언스(Pacific Biosciences)**: SMRT
* **옥스포드나노포어(ONT, Oxford Nanopore Technologies)**: Nanopore

## 3.1. SBS(Sequencing by Synthesis)

그 중에서도 **SBS**는 가장 많이 사용되는 방법 중 하나이다. SBS는 DNA Pol에 의해 complementary base를 삽입하여 **DNA synthesis**가 이루어질 때의 변화를 측정하여 sequencing을 수행하는 방법이다. **SBS**는 **CRT**와 **SNA**로 나뉘어진다.

**CRT(Cyclic Reversible Termination)**는 DNA synthesis cycle마다 A, T, G, C 4가지 형광표지된 dNTP로 된 **reversible terminator**를 사용하여 DNA chain을 종결시킨 후 형광을 측정한다. 그 후 새로운 cycle 전에 형광과 나머지 dNTP를 제거한다. **Illumina**의 sequencing 방법이 이에 해당한다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/7b3afd53-e3db-4535-afea-da22f52f114c">
</p>

**SNA(Single Nucleotide Addition)**은 DNA synthesis를 위해 A, T, G, C 중 **하나의 염기**만 들어간다. 만약 상보적인 염기가 들어갔다면 결합에 의해 H+가 방출되며 pH 변화가 일어난다. 이후 이를 washing하고 다른 염기를 넣어준다. **Ion Torrent** sequencing 방법이 이에 해당한다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/a0f22d08-017a-4ba4-9618-2d839ce6537d">
</p>

즉 CRT는 모든 염기가 같이 들어가는 반면 SNA는 하나씩 들어가고, CRT는 결합에 의해 발생하는 형광을 측정하는 반면 SNA는 결합에 의해 발생하는 pH 변화를 측정한다. 꼭 그런 것은 아니지만 그런 정도로 알아두어도 무방할 것이다.

## 3.2. Single-molecule long-read sequencing

**Single-molecule long-read sequencing**은 **SMRT(Single Molecule Real Time) sequencing**과 **Nanopore sequencing**으로 나뉜다. 이 방법은 **long read**를 real time, 그리고 single molecule 단위로 얻을 수 있지만 정확도가 낮은 편에 속한다. **SMRT sequencing**은 DNA polymerase가 DNA template을 읽으면서 형광을 방출하는 방식이다. 일종의 SBS라고 분류되기도 한다. **Nanopore sequencing**은 DNA fragment를 nanopore에 통과시키면서 전기적인 신호를 측정하는 방식이다. 각각 **Pacific Biosciences**와 **Oxford Nanopore Technologies**의 sequencing 방법이 이에 해당한다.

<p align="center">
  <img src="https://github.com/rubato-yeong/rubato-yeong.github.io/assets/78250557/5f840ae9-e468-40ca-8f63-c766e7823937">
</p>

지금까지의 방법을 나이브하게 비교해보면 다음과 같다.

| Platform | Read length | Throughput | Reads | Time | Error |
|:--------:|:-----------:|:----------:|:-----:|:----:|:-----:|
| Illumina HiSeqX | 150 | 800Gb | 3B | 3d | 0.1%, substitution |
| Ion S5 540 | 200 | 15Gb | 70M | 2.5h | 1%, indel |
| PacBio RSII | 20Kb | 1Gb | 0.05M | 4h | 13%, indel |
| ONT MinION | 100Kb | 1.5Gb | 0.1M | 48h | 12%, indel |

<br>

# 📃 Reference

* [Next-Generation Sequencing (NGS) - Definition, Types](https://microbenotes.com/next-generation-sequencing-ngs/)
* Goodwin, S., McPherson, J. & McCombie, W. Coming of age: ten years of next-generation sequencing technologies. Nat Rev Genet 17, 333–351 (2016).

<br>

