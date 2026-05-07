---
title: "Evo — 7B 파라미터 prokaryote 게놈 파운데이션 모델, 분자–시스템–게놈 스케일 생성"
authors: Nguyen E, Poli M, Durrant MG, Kang B, ..., Hsu PD*, Hie BL*
year: 2024
journal: "Science 386, eado9336"
source: nguyen-2024-sequence-modeling-and-design-from.md
category: dna-rna-foundation
tags: [evo, StripedHyena, hyena, generative, prokaryote, CRISPR-Cas, codesign, scaling-laws, single-nucleotide, arc-institute]
---

## 요약

Evo는 Arc Institute / Stanford가 만든 **7B 파라미터, 131 k 컨텍스트, 단일염기 해상도**의 게놈 파운데이션 모델이다. **StripedHyena** (hyena 29층 + RoPE attention 3층) 위에 270만 개 prokaryote+phage 게놈, 총 3,000억 토큰을 next-token prediction으로 학습. 한 모델에서 zero-shot 단백질 fitness, ncRNA fitness, 조절 DNA 효과를 동시 예측하고, **CRISPR-Cas9 단백질–RNA 시스템과 IS200/IS605 단백질–DNA 트랜스포존을 LM이 직접 디자인하고 실험으로 검증**한 첫 사례. 1 Mb 길이 합성 DNA도 그럴듯한 게놈 구조로 생성한다.

## 핵심 기여

1. **단일염기 + 131 k context + 7B params** — 이전 DNA transformer(DNABERT, NT)의 k-mer/BPE 토큰화와 5 k 미만 컨텍스트를 한꺼번에 돌파.
2. **DNA 사전학습 scaling laws** — Transformer++, Mamba, Hyena, StripedHyena를 compute-optimal 비교; **StripedHyena Pareto 우세**.
3. **다중모달 zero-shot fitness** — 단백질(ESM/ProGen2 수준), ncRNA(RNA 전용 LM 능가), 조절 DNA를 한 모델로.
4. **단백질–RNA / 단백질–DNA codesign** — CRISPR-Cas9(단백질 + sgRNA), IS200/IS605(TnpA/TnpB + LE/RE) 시스템을 한 번에 생성, **wet-lab 활성 검증**. LM이 분자 복합체 시스템을 디자인한 첫 사례.
5. **Megabase 생성** — 1 Mb 이상 DNA를 그럴듯한 coding/ncRNA/intergenic 구조와 함께 생성.
6. **Whole-organism fitness 신호** — premature stop 변이의 likelihood 변화로 gene essentiality를 zero-shot 분류.

## 방법론 및 아키텍처

| 요소 | 사양 |
|---|---|
| 아키텍처 | **StripedHyena** = Hyena 29층 + RoPE Attention 3층 (~10% attention) |
| 파라미터 | 7B |
| 토큰화 | byte-level 단일염기 |
| 컨텍스트 | 8 k → 131 k (2단계 확장) |
| 사전학습 목표 | autoregressive next-token prediction |
| 데이터 | OpenGenome (GTDB + IMG/PR + IMG/VR) — prokaryote + phage 270만 게놈, 3,000억 토큰 |
| 안전 제외 | eukaryote 숙주 바이러스 제외 |

## 결과

- **Scaling**: StripedHyena가 모든 비교 아키텍처(Transformer++, Mamba, Hyena)를 perplexity-FLOPS Pareto에서 능가.
- **단백질 DMS**: ESM-1b / ProGen2와 동등 zero-shot fitness 예측.
- **ncRNA DMS**: RNA 전용 LM **능가**.
- **조절 DNA**: 변이가 발현 방향성을 어떻게 바꾸는지 zero-shot 예측.
- **Gene essentiality**: premature stop likelihood로 essential vs non-essential 분류.
- **CRISPR-Cas9 codesign**: Evo가 디자인한 Cas9 + sgRNA 쌍이 in vitro에서 표적 DNA 절단.
- **IS200/IS605 codesign**: Evo가 디자인한 TnpA/TnpB + LE/RE가 셀룰러 어세이에서 절단/삽입.
- **Mb-scale 생성**: 1 Mb 이상 합성 DNA가 자연스러운 coding density / 유전자 구조.

## 활용 가이드

- **언제 쓰나**: prokaryote/phage 시퀀스의 변이 영향 zero-shot 예측, 합성 생물학용 시스템(예: 새 CRISPR variant) 디자인.
- **주의점**:
  - **Evo 1은 prokaryote만** — 인트론·복잡한 eukaryote 조절 grammar는 Evo 2 (Brixi 2025) 필요.
  - eukaryote-host 바이러스 학습 제외 → 인간 변이 직접 점수화는 부적합. 인간 임상 변이는 **Evo 2** 또는 AlphaGenome.
  - 131 k context는 mammalian 유전자(Mb 단위)에 비해 작음.

## 관련 논문

- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] :builds_on — 같은 Stanford 라인. HyenaDNA의 1000× 스케일업
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] :extends — Evo 2: 7B/40B, 1M context, all domains of life, StripedHyena 2
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] :supports — Mamba+BiMamba+RC vs StripedHyena
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] :supports — Enformer는 supervised regulatory regressor, Evo는 self-supervised generative
- [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]] :supports — ncRNA fitness에서 RNA-FM류를 능가
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/linder-2025-predicting-rna-seq-coverage-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/penic-2025-rinalmo-general-purpose-rna-language]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] — 같은 카테고리 (DNA/RNA foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **StripedHyena** — hyena layer를 주축으로 하고 적은 비율의 RoPE attention을 줄무늬처럼 끼워 넣은 하이브리드 아키텍처.
- **Hyena layer** — data-controlled long convolution + gating, subquadratic 대체 attention.
- **RoPE** — rotary position embedding, 상대 위치 인코딩.
- **NTP / autoregressive** — next-token prediction 생성형 사전학습.
- **Zero-shot fitness prediction** — Δlikelihood로 변이 효과 점수화. 별도 fine-tune 없이.
- **DMS (Deep Mutational Scanning)** — 모든 단일 변이의 fitness를 대량 측정하는 실험.
- **CRISPR-Cas codesign** — Cas 단백질과 가이드 RNA를 LM이 함께 생성.
- **IS200/IS605 transposon** — TnpA/TnpB + 짧은 LE/RE recognition flank로 구성된 박테리아 이동성 요소.
- **OpenGenome** — Evo 1의 학습 데이터셋. eukaryote 숙주 바이러스 제외.
- **Scaling laws** — compute–loss 관계의 경험식. Evo가 DNA 도메인 최초 보고.
