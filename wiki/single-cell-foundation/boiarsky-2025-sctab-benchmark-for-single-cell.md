---
title: "scTab — 22.2M 세포 cross-tissue cell type 분류 벤치마크 + TabNet 모델"
authors: Fischer F, Fischer DS, Mukhin R, Isaev A, Biederstedt E, Villani A-C, Theis FJ*
year: 2025
journal: "Nature Communications 2024 (10.1038/s41467-024-51059-5)"
source: boiarsky-2025-sctab-benchmark-for-single-cell.md
category: single-cell-foundation
tags: [scTab, TabNet, benchmark, cell type annotation, CELLxGENE, scaling, data augmentation, ontology-aware F1]
---

## 요약

**scTab**은 CELLxGENE census 2023-05-15을 기반으로 22.2M 세포 / 5,052 donors / 164 cell types / 56 tissues로 큐레이션된 **cross-tissue cell type 분류 벤치마크**와, 그에 최적화된 **TabNet 변형 모델**을 함께 제공한다. **Feature attention**으로 관측마다 ~1% 유전자만 사용하는 sparse 분류기이고, ontology-aware macro-F1 평가, donor-difference 데이터 증강, 깊은 앙상블 기반 불확실성을 포함한다. **scTab macro-F1 0.8295** vs 잘 튜닝된 linear baseline 0.7848 / XGBoost 0.8127 / **scGPT zero-shot 0.7301 / 미세조정 scGPT 0.749 / CIForm 0.766** — cross-tissue 분류에서는 비선형 특화 모델이 현재 sc-foundation 모델을 능가함을 명료하게 보였다. 모델/데이터 모두에서 scaling을 입증.

## 핵심 기여

- **재현 가능한 cross-tissue 분류 벤치마크** — 1차 데이터만, 10x만, ontology subtype, ≥5K cells × ≥30 donors 필터로 정제. donor 단위 70/15/15 split. Nvidia Merlin 기반 빠른 dataloader 동봉.
- **scTab 모델** — TabNet의 단일세포 적응판; per-observation feature-attention mask로 ~1% 유전자에만 의존, deep ensemble 불확실성.
- **Ontology-aware macro-F1** — 더 세분화된(자식 노드) 정답 예측에 페널티 부과 안 함. annotation granularity 차이 보정.
- **선형 < 비선형 (대규모에서)** — 잘 튜닝된 linear가 plateau에 빨리 도달하는 반면 scTab은 데이터·모델 모두 scaling.
- **Foundation 모델 reality check** — zero-shot scGPT (0.7301), fine-tuned scGPT (0.749), CIForm (0.766) 모두 scTab(0.8295)에 미달.
- **Donor-difference 증강** — 같은 cell type의 donor 간 centroid 차이 벡터를 사전 계산해 학습 시 add/subtract → test loss 0.797 → 0.659, macro-F1 0.7755 → 0.7841 (significant).

## 방법론 및 아키텍처

| 축 | 내용 |
|---|---|
| 데이터셋 | CELLxGENE 2023-05-15 LTS, 22.2M cells / 5,052 donors / 164 cell types / 56 tissues / 19,331 protein-coding genes |
| Split | donor-based 70/15/15 (15.2M / 3.5M / 3.45M) |
| 정규화 | size factor → 10K counts/cell, log1p |
| 모델 | TabNet 적응 (feature transformer + attention transformer), feature mask sparse (~1% 유전자) |
| 학습 | cross-entropy, ontology-corrected, Nvidia Merlin dataloader, A100 |
| 평가 | macro-F1 (ontology-aware), deep ensemble uncertainty |
| 증강 | per-cell-type donor centroid difference → sparsify ([−0.25, 0.25] → 0) → clamp [−1.5, 1.5] → k-means 50 cluster → 큰 cluster만 sample |
| 비교군 | CellTypist, optimized linear, MLP, XGBoost, scGPT (zero-shot + fine-tuned), CIForm |

## 결과

| 모델 | Macro-F1 |
|---|---|
| CellTypist (1.5M subsample) | 0.7304 ± 0.0015 |
| Optimized linear (full data) | 0.7848 ± 0.0001 |
| MLP (+ aug) | 0.7971 ± 0.0012 |
| XGBoost | 0.8127 ± 0.0005 |
| scGPT zero-shot (logistic on emb) | 0.7301 ± 0.0035 |
| scGPT fine-tuned | 0.749 |
| CIForm | 0.766 |
| **scTab (+ aug)** | **0.8295 ± 0.0007** |

- 모델 크기 1.7M → 16.2M params: macro-F1 0.7864 → 0.8323.
- Donor 기반 subsampling이 cell 기반 subsampling보다 훨씬 강한 scaling — batch diversity가 핵심.
- T cell, B cell 등 immune lineage subtype 구분에서 비선형 우위가 가장 큼 (Cell Ontology가 잘 발달한 영역).
- 31-coarse cell type으로 매핑하면 macro-F1 0.897.
- non-10x 프로토콜에서 절반 정도는 ~0.4 macro-F1, STRT-seq / SMART-seq2 / BD Rhapsody는 매우 낮음.
- Feature attention top-25 유전자에 cell-type 생물학적으로 타당한 마커 (NRXN1/SYT1 → brain, FCGR3A/HLA-DRA → macrophage 등) 출현.

## 활용 가이드 / 임상적 의의

- **HCA / atlas 구축 보조**: 표준 vocabulary로 자동 라벨 제안 → 연구자가 다듬는 워크플로 (Cell Annotation Platform, https://celltype.info/).
- **Foundation 모델 평가 표준** 확립 — 새로운 sc-foundation 모델은 본 벤치마크 + 잘 튜닝된 baseline까지 같이 비교해야 공정.
- **데이터 증강 전략**(donor-difference)은 다른 모델에도 결합 가능.
- 단, scTab은 supervised, 단일 task — multi-task pretrained backbone이 아니라는 점에서 "foundation model"보다는 **강한 task-specific deep model + 표준 벤치마크**로 보는 게 정확.

## 관련 논문

- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/hao-2024-large-scale-foundation-model-on]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/heimberg-2024-a-cell-atlas-foundation-model]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/kedzierska-2025-assessing-the-limits-of-zero]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/wen-2024-cellplm-pre-training-of-cell]] — 같은 카테고리 (single-cell foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본 (architecture anchor)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology anchor
- [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]] — sc-foundation 벤치마킹 perspective

## 용어집

- **TabNet** — sequential feature-attention 기반 tabular data 딥러닝 모델.
- **Feature attention mask** — 관측마다 어떤 입력 feature가 사용되는지 결정하는 sparse mask.
- **Cell Ontology (CL)** — 계층적 cell type 사전.
- **Ontology-corrected macro-F1** — author 라벨보다 더 세분화된 정답 예측은 페널티 없이 처리하는 macro-F1.
- **Donor-based split** — train/val/test에서 같은 donor가 두 split에 등장하지 않게 분할.
- **Donor-difference 증강** — 같은 cell type의 donor centroid 차이 벡터를 더하거나 빼서 입력 perturbation 생성.
- **Deep ensemble** — 다른 init의 여러 모델 학습 후 disagreement → 경험적 불확실성.
- **CELLxGENE census** — CZI의 버전 관리되는 단일세포 corpus.
- **Zero-shot evaluation** — 사전학습 임베딩 위에 작은 분류기만 학습 (foundation backbone fine-tune 없음).
- **Macro-F1** — class별 F1을 균등 평균; 희소 cell type 성능을 강조.
