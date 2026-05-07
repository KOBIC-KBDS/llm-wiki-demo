---
title: "HyenaDNA — 1 M nt 단일염기 컨텍스트, attention 없는 장거리 게놈 모델"
authors: Nguyen E, Poli M, Faizi M, Thomas AW, ..., Baccus SA*, Ré C*
year: 2023
journal: "NeurIPS 2023 (arXiv 2306.15794)"
source: nguyen-2023-hyenadna-long-range-genomic-sequence.md
category: dna-rna-foundation
tags: [HyenaDNA, hyena, long-range, single-nucleotide, foundation-model, NTP, in-context-learning, stanford]
---

## 요약

HyenaDNA는 transformer attention 대신 **Hyena operator** (implicit long-convolution + data-controlled gating)를 써서 인간 reference 게놈 위에 사전학습한 디코더-전용 게놈 파운데이션 모델이다. **단일염기 토큰화**로 1,000,000 nt 컨텍스트(이전 DNA 모델 대비 ~500배)에 도달하며, 1 M 토큰에서 transformer보다 160배 빠르다. 1.6 M 파라미터(NT-2.5B의 1/1500), 게놈 1개(NT의 1/3202)만으로 NT 벤치마크 18개 중 12개에서 SOTA.

## 핵심 기여

1. **단일염기 + 1 M 컨텍스트 동시 달성** — k-mer/BPE 없이 ATCG 그대로, full global receptive field, 모든 layer에서.
2. **Hyena operator로 attention 대체** — FFT 기반 O(L log² L), 1 M 토큰에서 ~160× 가속.
3. **시퀀스 길이 warm-up 스케줄러** — 64 → 1M까지 단계적 길이 증가; 450k에서 학습시간 40% 단축, species 분류 정확도 +7.5%p.
4. **Soft prompt / in-context 학습** — 백본 동결, 32k까지의 학습 가능한 prompt 토큰만 업데이트; full fine-tune에 근접.
5. **소형 모델로 거대 모델 능가** — 1.6M HyenaDNA가 2.5B Nucleotide Transformer를 18개 중 12개 태스크에서 능가.

## 방법론 및 아키텍처

| 요소 | 사양 |
|---|---|
| 아키텍처 | Decoder-only Hyena 블록 스택 |
| 토큰화 | 단일염기 (A,C,G,T,N + 특수) |
| 사전학습 목표 | next-token prediction (autoregressive) |
| 컨텍스트 | 1 k → 1 M tokens |
| 데이터 | 인간 reference 게놈 (GRCh38) 1개 |
| 모델 크기 | 2–8층 × hidden 128–256 (≈1.6 M params at small) |
| 메모리 트릭 | gradient checkpointing (3× 절약) |
| 학습 안정화 | sequence-length warm-up (필수, 200 k+) |
| 다운스트림 적응 | full fine-tune / soft prompt / instruction tuning |

## 결과

- **GenomicBenchmarks 8개** — 7/8 SOTA. Human Enhancers Ensembl 89.2 vs DNABERT 85.7. Mouse Enhancers 85.1 vs DNABERT 66.9. Human Nontata Promoters 96.6 vs CNN 84.6.
- **Nucleotide Transformer 18개** — 12/18 SOTA. NT-2.5B(2.5B params, 3,202 genomes)를 1.6M params, 1 genome으로 이김.
- **속도** — 1 M 토큰에서 transformer 대비 ~160× 가속.
- **Species classification at 1 M** — 다른 모델은 길이 부족으로 풀 수 없음, HyenaDNA만 길이 확장으로 해결.

## 활용 가이드

- **언제 쓰나**: 단일염기 해상도가 필요한 SNP/SNV 영향, 100 kb 이상 장거리 컨텍스트가 필요한 분류·표현학습.
- **주의점**:
  - **단방향**(causal) → bidirectional 정보가 필요하면 Caduceus(Mamba 기반).
  - 인간 reference 게놈만 학습 → 종간 진화 신호로 variant effect 예측은 약함 → Evo / Evo 2 / Nucleotide Transformer 영역.
  - RNA-seq coverage 같은 회귀 트랙은 학습 안 됨 → Enformer/Borzoi/AlphaGenome.
- **다음 모델 흐름**: HyenaDNA → Evo (StripedHyena, 7B, 131k) → Evo 2 (StripedHyena 2, 40B, 1M) → Caduceus (Mamba+BiMamba+RC).

## 관련 논문

- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] :extends — 같은 Stanford 라인의 Hyena 후속, 7B 파라미터, prokaryote 학습, generative
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] :extends — 40B, 1M context, all domains of life
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] :supports — Hyena → Mamba, BiMamba + RC equivariance
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] :extends — DNABERT의 k-mer + 512 context를 single-nt + 1M로 돌파
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] :supports — Enformer는 conv+attn 200 kb supervised regressor, HyenaDNA는 self-supervised generative LM
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/linder-2025-predicting-rna-seq-coverage-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/penic-2025-rinalmo-general-purpose-rna-language]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] — 같은 카테고리 (DNA/RNA foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **Hyena operator** — long-convolution + data-controlled gating의 결합. attention 대비 O(L log² L)로 sub-quadratic.
- **Implicit convolution** — 필터 값을 위치 인덱스를 입력받는 작은 MLP가 출력. 길이가 늘어나도 파라미터 수가 늘지 않음.
- **단일염기 토큰화** — A,C,G,T,N 한 문자 = 토큰 1개. SNP 분해 능력 보존.
- **NTP** — next-token prediction. GPT 스타일 autoregressive 사전학습.
- **Sequence-length warm-up** — 짧은 시퀀스부터 학습 후 점진적 두 배 확장하는 커리큘럼.
- **Soft prompt** — 입력 임베딩에 학습 가능한 토큰을 추가해 백본을 동결한 채 적응시키는 방식.
- **GenomicBenchmarks / NT benchmark** — DNA FM 평가용 표준 다운스트림 모음.
- **Receptive field** — 한 출력이 입력에서 영향받을 수 있는 최대 거리. HyenaDNA는 모든 layer에서 global.
