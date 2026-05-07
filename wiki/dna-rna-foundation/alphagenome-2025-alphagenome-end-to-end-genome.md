---
title: "AlphaGenome — DeepMind 1 Mb / single-bp 통합 조절 변이 모델"
authors: Avsec Ž et al. (Google DeepMind)
year: 2025
journal: "bioRxiv 2025-06-25 (Shen 2025 평가 포함)"
source: alphagenome-2025-alphagenome-end-to-end-genome.md
category: dna-rna-foundation
tags: [alphagenome, deepmind, 1Mb-context, single-bp-resolution, multi-assay, regulatory-VEP, personal-expression, enformer-successor, borzoi-successor, closed-API]
---

## 요약

AlphaGenome은 **Enformer의 직계 후속(같은 1저자)** 으로, **1 Mb DNA 입력 → 단일염기 해상도 출력** 으로 chromatin accessibility, 히스톤 마크, TF 결합, CAGE, RNA-seq 커버리지, 스플라이싱을 한 네트워크에서 모두 예측한다. Enformer(200 kb × 128 bp)와 Borzoi(524 kb × 32 bp)의 패러다임을 한 모델로 흡수한 셈. Shen 2025의 독립 평가에 따르면, GTEx 953명·50조직에서 **개인별 발현 방향성 예측이 Enformer 대비 odds ratio 3.0**, head-to-head 승률 3.2 (1,374 vs 430)로 크게 개선됐다 (CUTALP 예: 폐 조직 r=−0.81→+0.82). 다만 개인 게놈 데이터로 직접 학습한 Elastic Net / Random Forest에는 여전히 못 미치며, **DeepMind가 추론 전용 API로만 공개하고 fine-tuning을 금지**한 점이 학술 활용의 제약.

## 핵심 기여

1. **1 Mb 컨텍스트 + 단일염기 해상도** — Enformer 5×, Borzoi 2× 길이. 출력은 32 bp(Borzoi)에서 1 bp로.
2. **다중 어세이 통합** — chromatin / 히스톤 / TF / CAGE / RNA-seq / splicing을 한 모델에서.
3. **개인 발현 예측 향상** — personal data 학습 없이도 Enformer 대비 거대 향상. 음의 상관(잘못된 방향)도 다수 양으로 뒤집음.
4. **비선형 sequence–expression 관계 학습** — Elastic Net으로 못 잡는 것을 잡되, Random Forest와는 다른 메커니즘 사용 (ISM 결과 차이).
5. **추론 전용 API** — fine-tuning 금지 (DeepMind 약관).

## 방법론 및 아키텍처

| 요소 | 사양 |
|---|---|
| 입력 | 1,000,000 bp (TSS 중심) |
| 출력 해상도 | **1 bp** |
| 출력 트랙 | 다중 cell-type / 다중 어세이 (chromatin, 히스톤, TF, CAGE, RNA-seq, splicing) |
| 학습 데이터 | ENCODE, GTEx, FANTOM 등 (population-averaged) |
| 사용 모드 | DeepMind 추론 API only, weights 비공개 |
| 평가 (Shen 2025) | GTEx 953인 × 50조직; phased WGS, haplotype별 입력 후 평균 |

## 결과 (Shen 2025 평가 기준)

- **Pearson r 분포 (인간×조직)**:
  - AlphaGenome: 양 2,459 / 음 971
  - Enformer: 양 1,557 / 음 1,873 (절반 이상 음)
  - AlphaGenome 중앙값 +0.07 (상위 사분위 차이 더 큼)
- **Head-to-head**:
  - AlphaGenome > Enformer: 1,374 vs 430 (**승률 3.2**)
  - AlphaGenome > Elastic Net: 218 (Elastic Net은 개인 데이터로 학습)
  - AlphaGenome > Random Forest: 63
- **방향 역전**: CUTALP −0.81 → +0.82 (폐 조직). Enformer 상위 유전자는 미미한 개선만.
- **비선형 관계**: AlphaGenome ≈ Random Forest 성능 (ABI3에서 r 0.44–0.46), 다른 ISM 패턴.

## 활용 가이드

- **언제 쓰나** — 비코딩 변이의 cell-type-특이 효과를 빠르게 점수화, 개인 게놈에서의 발현 방향 예측 (Enformer보다 신뢰).
- **주의점**:
  - **Fine-tuning 금지** (API 약관). 학술적 비교/벤치마킹만.
  - 개인 데이터 직접 학습 모델(Elastic Net, RF)에는 여전히 뒤처짐 → 정밀의료에는 비교/보완 필요.
  - 가중치·아키텍처 비공개 → 블랙박스로 다뤄야 함.
  - Enformer 시대의 personal expression 한계 (Karollus 2023, Sasse 2023, Huang 2023)는 작아졌지만 사라지진 않음.
- **Evo 2와의 페어링** — Evo 2가 inference-time guidance에 Enformer/Borzoi를 쓰듯, AlphaGenome도 scoring function 후보지만 API 제약으로 현재 어려움.

## 관련 논문

- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] :builds_on — 같은 1저자, Enformer가 직접 조상
- [[dna-rna-foundation/linder-2025-predicting-rna-seq-coverage-from]] :builds_on — Borzoi의 RNA-seq coverage 예측 능력을 흡수
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] :supports — Evo 2: 동시기 발표, 1Mb generative open-source. AlphaGenome은 supervised closed-API. 상호 보완
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] :supports — 1M 토큰 컨텍스트의 self-supervised generative 대안
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] — 같은 카테고리 (DNA/RNA foundation)
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

- **AlphaGenome** — DeepMind 2025 통합 조절 VEP 모델. 1 Mb 입력, single-bp 출력, 다중 어세이.
- **GTEx** — Genotype-Tissue Expression. 다조직 RNA-seq + WGS 인간 코호트.
- **TSS / TES** — 전사 개시 / 종결 사이트. 유전자 본체 평균을 발현으로 요약할 때 사용.
- **Personal gene expression prediction** — 개인 게놈으로 그 사람 조직의 발현을 예측. Population-averaged 학습 모델은 약함.
- **Phased WGS** — haplotype phasing이 된 전장 유전체 시퀀싱. 각 haplotype을 1Mb 입력으로 따로.
- **Elastic Net / Random Forest** — gene별 개인 데이터로 직접 학습하는 고전 ML baseline. AlphaGenome의 "위쪽 한계".
- **ISM** — in-silico mutagenesis. 한 base씩 바꿔 모델 출력 차이를 보는 변이 점수화.
- **Closed inference API** — DeepMind의 배포 형태. 점수화는 가능하지만 weight 추출이나 fine-tune은 불가.
- **CUTALP** — AlphaGenome이 Enformer의 −0.81을 +0.82로 뒤집은 사례 유전자 (폐).
