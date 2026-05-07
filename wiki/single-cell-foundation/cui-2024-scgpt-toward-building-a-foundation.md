---
title: "scGPT — 33M 세포 generative pretraining 단일세포 multi-omics 파운데이션 모델"
authors: Cui H, Wang C, Maan H, ..., Wang B*
year: 2024
journal: "Nature Methods 21(8):1470–1480"
source: cui-2024-scgpt-toward-building-a-foundation.md
category: single-cell-foundation
tags: [scGPT, generative pretraining, transformer, single-cell multi-omics, perturbation prediction, GRN, batch integration, GEARS]
---

## 요약

**scGPT**는 CELLxGENE에서 가져온 **33M 정상 인간 세포**로 generatively pretrained 된 트랜스포머. 입력은 **gene token + binned expression value (50 bin) + condition token (modality/batch/perturbation)** 의 element-wise sum. 비순차 omics 데이터를 위한 특수 attention mask로 known/unknown 구분 후, 마스킹된 유전자를 cell embedding과 함께 반복적으로 예측하는 generative MLM을 수행. 단일 사전학습 백본이 cell type annotation, batch integration, multi-omic integration, perturbation prediction, GRN inference에 모두 fine-tune되어 SOTA를 달성했다. 사전학습 데이터를 키울수록 다운스트림이 개선되는 **scaling effect**를 입증.

## 핵심 기여

- **Generative pretraining for cells** — GPT 스타일 autoregressive-like 생성을 비순차 cell × gene 매트릭스에 적용하는 attention mask 설계.
- **3-layer 입력 임베딩** (gene + value + condition) — 모달리티/배치/perturbation을 입력 단계에서 자연스럽게 통합.
- **Pretrain-once, fine-tune-many** — 한 백본으로 5개 주요 다운스트림.
- **Tissue-specific 변형** — whole-human 33M, brain 13.2M, blood 10.3M, kidney 814K 등 — 후속 zero-shot 평가에서 비교 baseline으로 사용.
- **Scaling effect** — 사전학습 코퍼스 ↑ → 다운스트림 성능 ↑.

## 방법론 및 아키텍처

| 축 | 내용 |
|---|---|
| 코퍼스 | CELLxGENE 33M 정상세포, 51 organs, 441 studies (brain 13.2M, blood 10.3M, lung 2.1M, heart 1.8M …) |
| 입력 임베딩 | gene token + 50-bin expression + condition token (sum) |
| 모델 | 12-layer × 8-head transformer (per Kedzierska 2025 측정) |
| 사전학습 | Generative MLM with specialized attention mask, two-pass: GEP (no cell emb) → GEPC (with cell emb) |
| Fine-tune | annotation (CE), integration (ECS+DAR), perturbation (GEARS-style), GRN (gene-gene attention) |
| 평가 | hPancreas, MS, tumor-myeloid, Adamson/Replogle/Norman Perturb-seq, scIB integration suite |

## 결과

- **Cell type annotation**: hPancreas 대부분 cell type precision >0.8; MS 정확도 ~0.85; 미관측 암 종에 대한 myeloid subtype 분류에서 TOSICA, scBERT 능가.
- **Perturbation prediction**: Adamson, Replogle (1,823 single-gene), Norman (131 two-gene + 105 single-gene)에서 GEARS, CPA 대비 best Pearson-delta on DEGs; unseen gene perturbation 평가.
- **Batch integration / multi-omic**: scIB 메트릭 우수, RNA + ATAC 및 RNA + protein 동시 통합.
- **Reference mapping**: 사전학습 가중치만으로 기존 방법과 견줄 수준.
- **Scaling**: pretraining 규모 키울수록 cell type annotation, integration 등 모두 향상.

## 활용 가이드 / 임상적 의의

- **Pretrain-once, fine-tune-many**가 강력해 다양한 분석 파이프라인을 단일 백본으로 통일 가능.
- **Multi-omic 통합** 기능은 CITE-seq + scRNA, scATAC + scRNA 동시 분석에 매력적.
- 다만 zero-shot에서는 약함 (Kedzierska 2025) — 라벨 없는 탐색적 분석에선 HVG / scVI / Harmony 우선 검토 권장.
- 종양 분석에는 추가 fine-tune 필요 (사전학습이 정상세포 한정).

## 관련 논문

- [[single-cell-foundation/boiarsky-2025-sctab-benchmark-for-single-cell]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/hao-2024-large-scale-foundation-model-on]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/heimberg-2024-a-cell-atlas-foundation-model]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/kedzierska-2025-assessing-the-limits-of-zero]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/wen-2024-cellplm-pre-training-of-cell]] — 같은 카테고리 (single-cell foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본 (architecture anchor)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology anchor
- [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]] — sc-foundation 벤치마킹 perspective

## 용어집

- **Generative pretraining** — 마스킹된 토큰을 반복적으로 예측해 plausible 발현 상태를 생성하도록 학습.
- **Binned expression** — 발현값을 50 등간격 bin으로 이산화한 토큰.
- **Condition token** — modality/batch/perturbation 등을 입력 단계에서 합치는 보조 임베딩.
- **GEP / GEPC** — gene expression prediction (unmasked context) / from cell embedding.
- **Pearson-delta** — 예측 vs 실측 post-perturbation 발현 변화량의 Pearson 상관.
- **Multi-omic integration** — scRNA + scATAC + protein을 단일 latent에 통합.
- **GRN inference** — gene-gene attention 또는 gene embedding 유사도로 조절망 추정.
- **scIB** — single-cell integration benchmarking suite (Luecken et al.).
- **GEARS** — graph-기반 perturbation 효과 예측 모델.
- **Reference mapping** — 새로운 query 세포를 기존 reference atlas에 임베딩해 라벨 전이.
