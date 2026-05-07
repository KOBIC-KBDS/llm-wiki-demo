---
title: "RiNALMo — 650 M 파라미터, 36 M ncRNA 학습 RNA 파운데이션 모델"
authors: Penić RJ*, Vlašić T*, Huber RG, Wan Y, Šikić M*
year: 2025
journal: "arXiv 2403.00043 (Zagreb + A*STAR/GIS/BII)"
source: penic-2025-rinalmo-general-purpose-rna-language.md
category: dna-rna-foundation
tags: [RiNALMo, RNA-foundation-model, BERT, RoPE, SwiGLU, FlashAttention2, ncRNA, secondary-structure, splice-site, MRL, inter-family-generalization]
---

## 요약

RiNALMo은 **650 M 파라미터**의 BERT-style RNA 언어 모델로, **36 M ncRNA** (RNAcentral + Rfam + nt + Ensembl)를 MLM으로 사전학습한다. RNA-FM(100 M, 23 M)을 6배 이상 키우면서 LLaMA 시대의 모던 기법(RoPE, SwiGLU, FlashAttention-2)을 도입했다. RNA secondary structure, multi-species splice site, MRL 예측 모두 SOTA. 가장 중요한 결과는 **inter-family generalization** — 사전학습/파인튜닝에서 보지 못한 RNA 패밀리에서도 잘 작동, 기존 DL 모델은 모두 무너지는 영역.

## 핵심 기여

1. **650 M, 모던 transformer** — 33층, hidden 1280, RoPE / SwiGLU / FlashAttention-2.
2. **36 M ncRNA 사전학습 코퍼스** — RNAcentral 외에 Rfam, nt, Ensembl ncRNA까지 포함해 다양성 확보.
3. **Inter-family 일반화** — 학습 시 본 적 없는 RNA family에서도 secondary structure를 잘 예측. SPOT-RNA, UFold, MXfold2, RNA-FM 전부 무너지는 영역에서 살아남음.
4. **Multi-species splice site** — DNABERT, SpliceBERT를 인간·비인간 splice site 모두에서 능가.
5. **공개** — pretrained + fine-tuned weights와 fine-tune 스크립트 release.

## 방법론 및 아키텍처

| 요소 | 사양 |
|---|---|
| 백본 | 33층 Transformer 인코더, hidden 1280 |
| 파라미터 | 650 M (현존 공개 RNA LM 최대) |
| 위치 인코딩 | **RoPE** (relative) |
| FFN | **SwiGLU** |
| Attention | **FlashAttention-2** |
| 토큰화 | 단일염기 (A, U, C, G + N + 특수) |
| 사전학습 목표 | MLM (15% mask) |
| 데이터 | 36 M ncRNA (RNAcentral + Rfam + nt + Ensembl) |
| 다운스트림 헤드 | SS: 2D ResNet over outer-concat / Splice: CLS+MLP / MRL: 1D ResNet |

## 결과

- **Intra-family SS (Singh 2019 / SPOT-RNA dataset)**:
  - RiNALMo F1 **0.75** (Pre 0.78, Rec 0.73).
  - SPOT-RNA 0.67, UFold 0.66, MXfold2 0.60, RNA-FM 0.68.
- **Inter-family SS** — 다른 모델 모두 실패하는 unseen family에서도 RiNALMo는 성능 유지 (논문의 헤드라인 결과).
- **Multi-species splice site** — DNABERT, SpliceBERT 능가.
- **MRL** — RNA-FM, CNN baseline 모두 능가.
- **t-SNE** — CLS embedding이 RNA family별로 깔끔히 분리됨 (RNA Atlas와 비슷하지만 더 fine).

## 활용 가이드

- **언제 쓰나**:
  - **새로운 RNA family** 구조 예측 — 다른 DL 모델이 못하는 영역.
  - 종 간 splice site 예측, 5′ UTR MRL 예측.
  - MSA가 없거나 시간이 없을 때 — single-sequence이라 9시간 정렬 부담 없음 (cf. RNA-MSM).
- **주의점**:
  - 인코더 전용 → 생성 불가. RNA 디자인은 Evo/Evo 2.
  - long RNA는 메모리 한계 (FlashAttention-2여도 한계 존재) → chunking 필요할 수 있음.
  - MSA가 깊고 신뢰도 높을 땐 RNA-MSM이 보완재.
  - 대안 / 보완: ncRNA fitness zero-shot은 Evo 2가 더 강력 (Brixi 2025 보고).

## 관련 논문

- [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]] :extends — RiNALMo가 RNA-FM의 6.5× 스케일업 + 모던 트랜스포머 기법
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] :supports — splice site에서 DNABERT를 능가
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] :supports — Evo 2가 zero-shot ncRNA fitness/lncRNA essentiality에서 더 강력. 단 RiNALMo는 SS 예측 등 "small-data fine-tune" 시나리오에 적합
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] :supports — Evo가 ncRNA fitness에서 RNA 전용 LM 능가 보고
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/linder-2025-predicting-rna-seq-coverage-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] — 같은 카테고리 (DNA/RNA foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **RoPE (Rotary Position Embedding)** — 복소수 회전으로 상대 위치를 임베딩. LLaMA 계열의 표준.
- **SwiGLU** — gated linear unit 활성화. 모던 LLM의 FFN에서 GELU/ReLU 대체.
- **FlashAttention-2** — IO-aware attention 커널. 긴 시퀀스에서 메모리·throughput 효율적.
- **MLM** — masked language modeling.
- **Inter-family / Intra-family** — 학습에서 본 적 없는 / 본 RNA 패밀리에 대한 평가. RiNALMo의 차별점은 inter-family 일반화.
- **Outer concatenation** — 위치별 임베딩 [eᵢ, eⱼ]를 모든 쌍에 대해 쌓아 L×L×2D 표현으로 만드는 연산. SS 예측 헤드의 입력.
- **MRL** — mean ribosome loading. 5′ UTR이 결정하는 번역 효율의 프록시.
- **MSA replacement** — 단일 시퀀스 LM 임베딩으로 multiple sequence alignment를 대신해 구조 예측기에 입력하는 전략 (ESMFold/OmegaFold식).
