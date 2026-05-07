---
title: "scFoundation — 50M 세포 × 100M 파라미터 read-depth-aware 단일세포 파운데이션 모델"
authors: Hao M, Gong J, Zeng X, ..., Ma J*, Zhang X*, Song L*
year: 2024
journal: "Nature Methods 2024-06-06"
source: hao-2024-large-scale-foundation-model-on.md
category: single-cell-foundation
tags: [scFoundation, xTrimoGene, read-depth-aware, scaling law, asymmetric encoder-decoder, drug response, perturbation, gene module]
---

## 요약

**scFoundation (xTrimoscFoundationα)** 은 ~19,264개 단백질 코딩 유전자를 모두 다루는 **100M 파라미터** 트랜스포머로, **>50M 인간 단일세포 RNA-seq**으로 사전학습되었다. 핵심은 두 가지: (1) **xTrimoGene 비대칭 encoder-decoder** — MAE식으로 nonzero/non-mask 위치만 인코더에 넣어 ~20K 유전자를 통째로 다룰 수 있게 함, (2) **Read-Depth-Aware (RDA) 사전학습** — 각 세포의 source(S)/target(T) 총 카운트를 토큰으로 함께 입력해, 동일 세포를 서로 다른 read depth로 학습시킨다. 이 설계로 model/data scaling law를 확인했고, **fine-tune 없이도** read-depth enhancement, drug response, perturbation, annotation, gene module 등에서 SOTA 임베딩 백본 역할을 수행한다.

## 핵심 기여

- **xTrimoGene 비대칭 구조** — MAE 영감, encoder는 sparse(nonzero) 위치만 처리 → ~20K gene context 가능.
- **연속 발현 임베딩** — scalar → 학습형 d-dim 벡터(이산 binning 없이) 원본 정보 보존.
- **RDA modeling** — T, S 토큰으로 read depth 변동을 모델 내부에 흡수, 추론 시 T를 키우면 read-depth enhancement.
- **Scaling law** — 3M / 10M / 100M 비교에서 validation MSE가 power-law로 감소; scBERT, scGPT, Geneformer-급 비교에서 100M scFoundation이 우위.
- **Pre-trained embedding as a feature** — fine-tune 없이도 cell clustering, drug response (CCLE/GDSC), single-cell drug response, perturbation, annotation에서 SOTA.

## 방법론 및 아키텍처

| 축 | 내용 |
|---|---|
| 코퍼스 | >50M scRNA-seq cells (GEO, Single Cell Portal, HCA, hECA, DISCO, EMBL-EBI) |
| Gene 공간 | HGNC 19,264 protein-coding + mitochondrial |
| 임베딩 | scalar expression → 학습형 d-dim vector (binning 없음) |
| 모델 | xTrimoGene asymmetric encoder-decoder, ~100M params |
| 사전학습 | RDA modeling — T/S 토큰, hierarchical Bayesian downsampling, MSE loss at masked positions |
| 다운스트림 | clustering, drug response (bulk + sc), perturbation (GEARS-style), annotation (Zheng68K), gene module / GRN |
| 사용 방식 | 주로 비-파인튜닝 또는 light fine-tune; downstream 모델이 scFoundation 임베딩을 feature로 사용 |

## 결과

- **Validation MSE scaling law**: y = −0.014x + 0.611 (log FLOPs); 100M scFoundation이 동일 아키텍처의 scBERT/scGPT/Geneformer 등가 모델 능가.
- **Read-depth enhancement (T = 5×S)**: pancreatic islet 데이터, downsample 1–20%에서 MAE/MRE 거의 절반으로 감소.
- **Imputation/clustering**: T/S ≥ 3.5에서 MAGIC, SAVER, scImpute, scVI보다 NMI/ARI/SIL 우수, reference label과 cluster 일치 가장 좋음.
- **Zheng68K cell-type annotation**: 모델 크기 증가에 따라 향상.
- **Drug response (bulk + single-cell)**: scFoundation 임베딩을 feature로 쓰는 다운스트림 모델이 기존 baseline 능가.
- **Perturbation prediction**: GEARS-style 벤치마크에서 SOTA.
- **Gene module inference**: 유전자 임베딩으로 알려진 기능 모듈/TF-target 관계 회복.

## 활용 가이드 / 임상적 의의

- **Light fine-tune 또는 frozen feature**로도 강력 → 컴퓨팅 자원이 제한된 사용자에게 매력적.
- **Read-depth 차이가 큰 데이터셋**(예: 외부 cohort + 내부 cohort 통합 분석)에서 RDA enhancement가 사전 정규화 단계로 유용.
- **약물 반응 예측 / perturbation**: bulk CCLE/GDSC + single-cell 모두 지원, translational pipeline에 적합.
- HGNC 19,264 고정 vocab → 비-인간 또는 비-coding RNA 분석에는 재학습 필요.

## 관련 논문

- [[single-cell-foundation/boiarsky-2025-sctab-benchmark-for-single-cell]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/heimberg-2024-a-cell-atlas-foundation-model]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/kedzierska-2025-assessing-the-limits-of-zero]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/wen-2024-cellplm-pre-training-of-cell]] — 같은 카테고리 (single-cell foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본 (architecture anchor)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology anchor
- [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]] — sc-foundation 벤치마킹 perspective

## 용어집

- **xTrimoGene** — scFoundation의 비대칭 transformer 아키텍처, sparse nonzero 인코딩.
- **RDA modeling** — Read-Depth-Aware: T(target)/S(source) 총 카운트 토큰을 함께 입력해 read depth 변동을 흡수.
- **Asymmetric encoder-decoder** — MAE식, encoder는 visible 토큰만, 더 작은 decoder가 전체 복원.
- **연속 발현 임베딩** — scalar → 벡터 직접 학습, bin 사용 안 함.
- **Total counts (T, S)** — 세포 UMI 총합, conditioning token으로 사용.
- **Power-law scaling** — validation loss ∝ FLOPs^−α, LLM scaling law의 단일세포판.
- **Cell context embedding** — encoder 출력 pooled, cell-level feature.
- **Gene context embedding** — decoder 출력, gene-level downstream에 사용.
- **Read-depth enhancement** — 추론 시 T > S로 설정해 가상으로 깊이 증대된 발현 벡터 생성.
- **GEARS** — graph-기반 perturbation 효과 예측, sc-perturbation 벤치마크.
