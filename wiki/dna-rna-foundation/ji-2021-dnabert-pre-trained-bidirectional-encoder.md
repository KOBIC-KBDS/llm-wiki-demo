---
title: "DNABERT — DNA용 BERT, k-mer 토큰화로 promoter·TFBS·splice 통합 모델"
authors: Ji Y, Zhou Z, Liu H*, Davuluri RV*
year: 2021
journal: "Bioinformatics 37(15):2112–2120"
source: ji-2021-dnabert-pre-trained-bidirectional-encoder.md
category: dna-rna-foundation
tags: [DNABERT, BERT, k-mer, transformer, MLM, promoter, TFBS, splice-site, interpretability]
---

## 요약

DNABERT는 NLP의 BERT를 그대로 가져와 DNA에 적용한 첫 번째 대표 사례다. **3,4,5,6-mer 토큰화**로 인간 게놈을 self-supervised로 사전학습한 뒤, 같은 백본을 promoter / 전사인자 결합부위(TFBS) / splice site 분류로 미세조정해서 당시의 task-specific CNN·RNN 모델들을 큰 차이로 앞선다. attention map을 시각화해 motif 발견과 변이 우선순위까지 해결하는 "one model many tasks"를 보여준다.

## 핵심 기여

1. **DNA 최초의 BERT 사전학습** — 라벨 없이 인간 게놈만으로 일반화 가능한 임베딩 학습.
2. **연속 k-mer span masking** — 인접 k-mer로 답이 새는 문제를 막기 위해 단일 토큰이 아닌 **연속 k개 토큰**을 마스킹. (BERT의 결정적 수정점)
3. **단일 백본·다중 다운스트림** — DNABERT-Prom (promoter), DNABERT-TF (ENCODE 690 ChIP-seq), DNABERT-Splice (정상/비정상 splice site).
4. **DNABERT-viz** — attention head 시각화로 binding site 위치와 컨텍스트 관계를 직접 보여줌. JASPAR 모티프 1,595/1,999개 매칭.
5. **종간 전이** — 인간 게놈으로만 사전학습한 DNABERT가 마우스 ENCODE 78개 ChIP-seq에서도 SOTA. (단백질 코딩은 85%, 비코딩은 50% 유사도임에도 의미 보존)
6. **변이 우선순위** — attention 가중치가 큰 영역의 dbSNP 변이가 실제로 ClinVar/GWAS catalog에서 발견되는 비율 24.7%(TATA), 31.4%(비-TATA).

## 방법론 및 아키텍처

| 요소 | 사양 |
|---|---|
| 백본 | BERT-base (12층, hidden 768, 12 head) |
| 토큰화 | overlapping k-mer (k=3,4,5,6); k=6이 최고 |
| 어휘 크기 | 4ᵏ + 5 (특수 토큰: CLS, PAD, UNK, SEP, MASK) |
| 입력 길이 | 최대 512 (longer는 DNABERT-XL로 분할) |
| 사전학습 | MLM only (NSP 제거), **k 연속 토큰** 마스킹 15%→20% |
| 학습 데이터 | 인간 게놈, 5–510 nt 길이 sliding/sampling |
| 학습 비용 | 8× RTX 2080 Ti, ~25일 |

## 결과

- **Promoter** — DNABERT-Prom-300 vs DeePromoter: 정확도 +0.335, MCC +0.554. Prom-scan(1001 bp)와 core promoter(70 bp) 모두 SOTA.
- **TFBS (ENCODE 690)** — 평균 정확도/F1 모두 0.9 이상인 유일한 모델. DeepSEA 대비 Wilcoxon adj p ≈ 4.5×10⁻¹⁰⁰.
- **Splice site (SpliceFinder benchmark)** — adversarial set에서 정확도 0.923, F1 0.919, MCC 0.871. 인트론 ISE/ISS에 attention 집중.
- **종간 전이 (마우스 78 ChIP-seq)** — 사전학습 DNABERT > 모든 baseline + random init.

## 활용 가이드

- **사용 시나리오** — 단일 promoter / TFBS / splice site 분류, 작은 라벨 데이터로 fine-tune이 필요할 때.
- **주의점**: 입력 길이 512 토큰 (k=6 기준 약 3 kb) 한계. **장거리 enhancer는 못 봄** → Enformer/Borzoi 영역.
- **후속 모델**: DNABERT-2(BPE, multi-species), Nucleotide Transformer(2.5B params, 3,202 genomes), HyenaDNA(1 M nt single-bp).

## 관련 논문

- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] :extends — k-mer의 단일염기 분해 한계를 single-nucleotide tokenization으로 돌파
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] :supports — Mamba 기반 RC-equivariant 양방향 모델
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] :supports — 같은 해의 multi-task supervised CNN+attention(Enformer); DNABERT는 self-supervised pretraining에 초점
- [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]] :parallel_in_other_disease — DNABERT의 RNA 버전(BERT 12층, RNAcentral 23M ncRNA)
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/linder-2025-predicting-rna-seq-coverage-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/penic-2025-rinalmo-general-purpose-rna-language]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] — 같은 카테고리 (DNA/RNA foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **MLM** — masked language modeling. 입력의 일부를 가리고 모델이 복원하게 하는 self-supervised 학습법.
- **k-mer 토큰화** — DNA 시퀀스를 길이 k의 부분문자열로 표현. DNABERT는 overlapping k-mer 사용.
- **연속 span masking** — DNABERT가 도입한 k 연속 토큰 마스킹. 인접 k-mer로 답을 추론하는 leakage를 차단.
- **TFBS** — transcription factor binding site, 전사인자 결합부위.
- **ENCODE 690** — ENCODE 프로젝트의 690개 표준화 ChIP-seq peak set. TFBS 벤치마크 표준.
- **MCC** — Matthews correlation coefficient. 클래스 불균형에 강한 분류 메트릭.
- **DNABERT-viz / DNABERT-XL** — attention 시각화 모듈 / 512 초과 시퀀스 분할-결합 변형.
- **JASPAR / TOMTOM** — TF motif 데이터베이스 / 모티프 비교 도구.
- **ISE/ISS** — intronic splicing enhancer/silencer.
