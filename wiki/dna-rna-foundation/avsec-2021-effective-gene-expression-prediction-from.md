---
title: "Enformer — 200 kb 컨텍스트로 유전자 발현을 예측하는 CNN+Transformer"
authors: Avsec Ž, Agarwal V, Visentin D, Ledsam JR, Grabska-Barwinska A, Taylor KR, Assael Y, Jumper J, Kohli P, Kelley DR*
year: 2021
journal: "Nature Methods 18:1196–1203"
source: avsec-2021-effective-gene-expression-prediction-from.md
category: dna-rna-foundation
tags: [enformer, transformer, CNN, gene-expression, CAGE, long-range, variant-effect, deepmind, calico]
---

## 요약

DeepMind와 Calico가 공동 개발한 **Enformer**는 200 kb DNA 입력을 받아 5,313개 인간 / 1,643개 마우스 유전체 트랙(CAGE, ChIP, DNase/ATAC)을 128 bp 해상도로 동시에 예측하는 모델이다. CNN 트렁크(7층) 위에 **transformer 11층**을 얹어, 기존 Basenji2의 receptive field 20 kb를 100 kb로 5배 확장했다. 이 한 가지 변화만으로 CAGE TSS 예측 Pearson r이 0.81→0.85로 올랐고 (실험 상한 0.94까지의 격차의 약 1/3 회복), CRISPRi 검증된 enhancer–유전자 연결까지 라벨 없이 prioritize한다.

## 핵심 기여

1. **장거리 어텐션 도입** — dilated conv 대신 self-attention으로 원거리 enhancer 정보를 직접 통합. 100 kb 안에 들어오는 high-confidence enhancer–gene pair 비율이 47% → 84%.
2. **DNA 전용 상대 위치 인코딩** — exponential / central-mask / gamma 세 가지 basis function을 결합. NLP의 표준 위치 인코딩보다 분명한 성능 향상.
3. **단일 모델·다중 트랙 멀티태스크** — 인간/마우스 두 헤드, 5천+개 트랙을 동시에 예측 → 전이학습 가능한 강한 sequence representation.
4. **Variant effect 점수 향상** — Basenji2/ExPecto 대비 eQTL fine-mapping과 saturation mutagenesis MPRA 모두에서 더 정확한 in-silico mutagenesis 점수.
5. **Enhancer 라벨 없는 enhancer 발견** — gradient×input과 attention 기반 contribution score가 ABC score(실험 데이터 사용)와 비슷하거나 능가.

## 방법론 및 아키텍처

| 요소 | 사양 |
|---|---|
| 입력 | 196,608 bp (≈200 kb) 한 시퀀스, one-hot |
| 트렁크 | Conv 7층 + pooling → 128 bp bin |
| 본체 | Transformer 11층 (multi-head attention, 상대 위치 basis) |
| 출력 | 중앙 896 bin (~114 kb) × {인간 5,313 / 마우스 1,643} 트랙 |
| 손실 | Poisson log-likelihood, softplus 출력 |
| 학습 | TPU v3, 인간+마우스 reference 게놈, chromosome split |
| Receptive field | ~100 kb (Basenji2의 5배) |

## 결과

- **CAGE Pearson r 0.85** vs Basenji2 0.81 — 모든 트랙 종류에서 일관된 향상 (paired Wilcoxon p<10⁻³⁸).
- **CRISPRi enhancer 우선순위**(Gasperini, Fulco) — Enformer contribution score가 ABC score 대비 동등 이상 성능. ABC는 H3K27ac+HiC 실험 데이터를 입력으로 받지만 Enformer는 시퀀스만 본다.
- **TAD 경계 인식** — attention map이 TAD 경계에 비대칭적으로 집중 (반대편으로 차단됨), p≈1×10⁻¹³³.
- **Variant effects** — eQTL과 saturation MPRA에서 모두 SOTA.

## 활용 가이드

- **언제 쓰나**: 인간/마우스 reference에서 비코딩 변이의 발현 영향 우선순위, distal enhancer–gene linking.
- **주의점**: population-averaged 학습이라 **개인 유전체 예측은 약함** (Huang 2023, Sasse 2023이 보고). 100 kb 너머의 ultra-distal 조절은 못 봄.
- **후속 모델 고려**: 더 긴 컨텍스트와 RNA-seq 직접 예측이 필요하면 Borzoi(524 kb), 1 Mb·single-bp 다중모달이 필요하면 AlphaGenome.

## 관련 논문

- [[dna-rna-foundation/linder-2025-predicting-rna-seq-coverage-from]] :extends — 같은 Kelley 라인이 RNA-seq 직접 예측 + 524 kb로 확장 (Borzoi)
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] :extends — 1 Mb 컨텍스트, single-bp 해상도, 다중모달로 일반화
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] :supports — Enformer의 transformer 대신 Hyena로 1M bp 컨텍스트
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] :supports — Mamba 기반 RC-equivariant 양방향 모델
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/linder-2025-predicting-rna-seq-coverage-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/penic-2025-rinalmo-general-purpose-rna-language]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] — 같은 카테고리 (DNA/RNA foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **CAGE** — 5′ cap을 잡아 시퀀싱하는 발현 정량 어세이; 프로모터/TSS 활성의 표지로 자주 사용.
- **TSS** — transcription start site, 전사 개시 위치.
- **Receptive field** — 출력 한 bin이 입력에서 영향받을 수 있는 최대 거리.
- **상대 위치 인코딩(RPE)** — 토큰 쌍의 상대 거리에 따라 attention bias를 주는 방식. Enformer는 DNA 전용 basis function을 도입.
- **CRISPRi** — dCas9-KRAB로 후보 enhancer를 끄고 표적 유전자 발현 변화를 관찰하는 검증 방법.
- **ABC score** — Activity-by-Contact, H3K27ac × Hi-C 기반 enhancer–gene 연결 점수.
- **TAD** — topologically associating domain, 염색질 컴팩션 단위.
- **eQTL** — 인근 유전자 발현량과 연관된 SNP.
- **Saturation MPRA** — 한 시퀀스의 모든 단일 염기 치환에 대한 reporter 활성을 동시에 측정하는 어세이.
