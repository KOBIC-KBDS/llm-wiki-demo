---
title: "Nucleotide Transformer — 다종 게놈으로 학습한 DNA foundation model 벤치마크"
authors: Dalla-Torre H, Gonzalez L, Mendoza-Revilla J, Lopez Carranza N, Grzywaczewski AH, Oteri F, Dallago C, Trop E, de Almeida BP, Sirelkhatim H, Richard G, Skwark M, Beguir K, Lopez M, Pierrot T
year: 2024
journal: "Nature Methods 22:287-297"
source: dalla-2024-nucleotide-transformer-building-and-evaluating.md
category: dna-rna-foundation
tags: [nucleotide-transformer, dna-foundation, transformer, genomics, multispecies, variant-effect, fine-tuning]
---

## 요약

**Nucleotide Transformer (NT)**는 DNA 서열을 위한 transformer 기반 foundation model 계열이다. 논문은 500M-2.5B 파라미터 NT-v1 모델을 인간 reference genome, 3,202개 인간 genome, 850종 multispecies genome으로 각각 사전학습하고, 18개 genomics 예측 task에서 BPNet, DNABERT-2, HyenaDNA, Enformer와 비교한다. 핵심 메시지는 **데이터 다양성, 특히 multispecies pretraining이 인간 genomics task에서도 강한 일반화 성능을 만든다**는 점이다. 후반부의 NT-v2는 50M-500M 모델에 rotary embedding, SwiGLU, 12 kb context 등을 적용해 250M 모델이 기존 2.5B multispecies 모델보다 높은 평균 MCC를 낸다.

## 핵심 기여

1. **DNA foundation model의 체계적 benchmark**  
   단일 논문 안에서 NT, DNABERT-2, HyenaDNA, Enformer, BPNet을 같은 18개 task와 tenfold cross-validation으로 비교한다.

2. **multispecies pretraining의 효과 제시**  
   850종 genome으로 학습한 NT-Multispecies 2.5B가 인간 reference 또는 1000 Genomes 기반 모델보다 여러 인간 genomics task에서 더 강하게 작동한다.

3. **fine-tuning operational recipe**  
   전체 파라미터의 약 0.1%만 학습하는 parameter-efficient fine-tuning으로, 가장 큰 2.5B 모델도 단일 GPU에서 15분 미만으로 downstream adaptation 가능하다고 보고한다.

4. **비지도 genomic element 인식**  
   사전학습만으로 5' UTR, CDS, intron, intergenic, 3' UTR embedding이 구분되고, attention head가 exon, enhancer, promoter 같은 요소에 집중한다.

5. **variant prioritization**  
   reference/alternate sequence embedding 차이를 이용한 zero-shot score가 eQTL, meQTL, ClinVar, HGMD variant 구분에서 AUC 0.7-0.8 수준을 보인다.

6. **NT-v2의 경량화**  
   12 kb context와 architecture update를 적용한 250M NT-v2가 평균 MCC 0.769로 논문 내 최고 benchmark 성능을 보이며, 기존 2.5B multispecies 모델보다 10배 작다.

## 방법론 및 아키텍처

| 구성 | 내용 |
|---|---|
| 사전학습 목표 | masked language modeling |
| NT-v1 context | 6 kb |
| NT-v1 scale | 500M, 2.5B |
| 학습 데이터 | human reference, 3,202 human genomes, 850 multispecies genomes |
| downstream 평가 | 18개 genomics prediction task, tenfold cross-validation |
| adaptation | probing 또는 parameter-efficient fine-tuning |
| NT-v2 update | rotary embedding, SwiGLU, bias/dropout 제거, 12 kb context |

평가는 두 갈래다. **Probing**은 여러 transformer layer embedding을 logistic regression 또는 작은 MLP에 넣어 성능을 본다. **Fine-tuning**은 task head를 붙이고 rescaling weight 중심으로 적은 파라미터만 학습한다. 논문은 thorough probing보다 parameter-efficient fine-tuning이 더 빠르고 안정적이며 성능도 좋다고 정리한다.

## 결과

- **18개 task 평균**에서 NT-Multispecies 2.5B가 DNABERT-2, HyenaDNA-1kb/32kb, Enformer, BPNet 계열보다 높은 평균 MCC를 기록한다.
- Fine-tuned NT는 BPNet baseline 18개 중 12개 task에서 surpass, 6개 task에서 match로 보고된다.
- 추가 benchmark에서 NT-Multispecies 2.5B는 DeepSEA chromatin profile AUC와 평균 약 1% 차이, SpliceAI-10k와 맞먹는 splice-site 성능, DeepSTARR와 근접한 enhancer activity 예측을 보인다.
- Zero-shot variant score는 ClinVar pathogenic variant에서 NT-Multispecies 2.5B AUC 0.80을 보고한다.
- NT-v2 500M 12 kb 모델은 splice-site task에서 top-k accuracy 96%, PR-AUC 0.98을 기록해 논문 내 비교에서 SpliceAI-10k를 넘는다.

## 해석 포인트

NT는 이 위키의 DNA/RNA 흐름에서 **DNABERT 이후, HyenaDNA/Evo 계열과 나란히 놓이는 transformer scale-up 지점**이다. Enformer가 long-range regulatory prediction을 CNN+Transformer로 풀었다면, NT는 더 표준적인 DNA language model을 크게 만들고 multispecies pretraining과 benchmark 체계를 강조한다. 다만 6-12 kb context는 Enformer/Borzoi/AlphaGenome의 장거리 조절 예측과는 목표가 다르며, 논문 자체도 100 kb 이상 장거리 dependency를 향후 과제로 둔다.

## 관련 논문

- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] :builds_on — DNA에 BERT식 masked language modeling을 먼저 적용한 선행 라인.
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] :methodologically_uses — NT benchmark의 비교 대상이며, attention 대신 Hyena operator로 긴 context를 다루는 대안.
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] :methodologically_uses — Enformer는 NT benchmark의 비교 대상이자 long-range regulation 한계를 논의할 때의 기준점.
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] :methodologically_uses — DNA sequence model에서 long-range와 reverse-complement equivariance를 다루는 후속 대안.
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] :extends — Evo 계열은 NT보다 더 넓은 생명체 범위와 genome-scale generation으로 확장한다.
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] :extends — Evo 2는 genome modeling을 모든 생명 domain으로 넓히는 다음 scale-up 흐름이다.
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] :extends — AlphaGenome은 장거리 context와 다중 molecular output을 결합하는 end-to-end genome foundation 방향이다.
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] :builds_on — NT의 기본 architecture anchor.
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] :builds_on — foundation model 개념 anchor.

## 용어집

- **NT**: Nucleotide Transformer.
- **MCC**: Matthews correlation coefficient. 이 논문 benchmark의 핵심 분류 지표.
- **Probing**: frozen embedding을 별도 classifier 입력으로 쓰는 평가 방식.
- **Parameter-efficient fine-tuning**: backbone 전체가 아니라 작은 수의 조정 파라미터만 학습하는 방식.
- **eQTL / meQTL**: 유전자 발현 또는 DNA methylation level과 연관된 variant.
- **ClinVar / HGMD**: 임상적 또는 질병 관련 variant annotation 데이터베이스.
- **Perception field**: 모델이 한 번에 볼 수 있는 서열 context 길이.

---

## 내 메모

> 이 섹션은 위키 운영자가 직접 작성하는 공간입니다. LLM은 읽기만 하고 자동 수정하지 않습니다.
