---
title: "CellOT — neural optimal transport로 단일세포 perturbation 반응 학습"
authors: Bunne C, Stark SG, Gut G, ..., Pelkmans L*, Krause A*, Rätsch G*
year: 2023
journal: "Nature Methods 20:1759–1768"
source: bunne-2023-learning-single-cell-perturbation-responses.md
category: virtual-cell
tags: [perturbation, optimal-transport, ICNN, single-cell, drug-response, neural-OT, manipulator-VI]
---

## 요약

세포는 측정 시 파괴되어 control과 perturbed가 **unpaired distribution**으로 관측된다. CellOT는 이 두 분포 사이를 잇는 **optimal transport map** 을 input-convex neural network (ICNN) 로 학습한다. 결과로 **mean뿐 아니라 higher-order moment**까지 매칭, 환자/종/시간을 가로지르는 일반화 성능을 보임. 4i 단백질 이미징과 scRNA-seq 모두에서 scGEN/cAE/PopAlign 대비 일관된 우위.

## 핵심 기여

1. **단일세포 perturbation 예측 = optimal transport** 라는 모델링 패러다임 제시.
2. **Brenier 정리 + ICNN**: OT map을 직접 학습 대신 convex potential의 gradient ∇gₖ로 안정적으로 회복.
3. **MMD 기준 평가**: 평균 매칭만 보던 종전 평가를 분포 전체 매칭으로 격상.
4. **OT cost = drug severity** 라는 해석: apoptosis inducer / proteasome inhibitor 가 가장 큰 transport cost를 발생.
5. **외삽 generalization**: 한 환자에서 학습 → 다른 환자/다른 종/다른 시점 예측.

## 방법론 및 아키텍처

| 항목 | 내용 |
|---|---|
| 입력 | unpaired control / perturbed 단일세포 분포 (4i protein imaging 또는 scRNA-seq AE latent) |
| 학습 단위 | perturbation k 별로 **개별 ICNN-pair** (f, g) |
| 추론 | T_k = ∇g_k 적용해 새로운 unpaired 샘플로부터 perturbed state 예측 |
| 손실 | OT dual minimax (Makkuva 2020 기반) + cycle consistency |
| 평가 | MMD, marginal r², UMAP overlap |

### 학습 데이터셋

- **Melanoma 4i** (M130219, M130429) — 34 drugs × multiplexed 단백질 이미징.
- **scRNA-seq cancer cell line** — 9 drugs.
- **Lupus PBMC** — IFN-β + holdout 환자.
- **Glioblastoma** — panobinostat + holdout 환자.
- **Cross-species LPS** — mouse / pig / rabbit / rat innate immune.
- **Hematopoiesis** (Weinreb 2020 lineage tracing).

## 결과

- 4i 34개 drug 모두에서 MMD 기준 baseline 압도. 자가/Apop/Proteasome inhibitor 등 강한 약효 정확 포착.
- scGEN/cAE는 mean만 맞추고 subpopulation을 놓치는 반면, CellOT는 cell-cycle phase / drug-resistant subpopulation 등 heterogeneity 보존.
- Holdout 환자 (lupus, glioblastoma)에서도 성능 거의 그대로 → 임상 응용 가능성 시사.
- 종-간 LPS 반응도 transferable.
- Hematopoietic 발달 trajectory를 단일세포 단위로 재구성 (Schiebinger WOT의 parametric 신경망 후속작 격).

## 활용 가이드

- **사용 시나리오**: 같은 cell line / patient cohort 내에서 알려진 perturbation의 효과를 새로운 control population에 적용하고 싶을 때.
- **사용 불가 시나리오**: 학습 시 보지 못한 perturbation 자체의 효과 — 이때는 GEARS / CPA 류로 가야 함.
- **데이터 요구사항**: control과 perturbed가 양쪽 모두 있어야 함 (대조군 없는 실험에는 부적합).
- **계산 비용**: ICNN minimax 학습이 VAE보다 무겁고 perturbation 수만큼 모델이 늘어남. 수십~수백 perturbation까지가 현실적.
- **활용 관점**: 같은 환자/같은 cell line 안에서 patient-specific drug response를 예측하는 데 적합. 약물 severity ranking은 약물 재배치 (drug repurposing) 의 정량 지표가 될 수 있음.

## 관련 논문

- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] :supports — AIVC manipulator VI의 구체 사례
- [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] :supports — GEARS는 unseen perturbation, CellOT는 seen perturbation의 unseen sample
- [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] :supports — CPA/scArches 계열의 atlas mapping 흐름과 보완
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] :provides_data_for — 학습 데이터원
- [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] :provides_data_for — chemical perturbation 대규모 학습 데이터
- [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] — 같은 카테고리 (virtual cell)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 모델 — perturbation 적용
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — Geneformer in silico perturbation

## 용어집

- **Optimal Transport (OT)** — 한 분포를 다른 분포로 최소 비용으로 옮기는 수학적 framework.
- **Brenier 정리** — 두 절적분포 간 최적 map은 어떤 convex 함수의 gradient.
- **ICNN (Input-Convex Neural Network)** — 입력에 대해 항상 convex 한 신경망 (non-negative weights + convex activation).
- **Dual potential** — OT 문제의 라그랑지 쌍 (f, g). g의 gradient가 transport map.
- **MMD (Maximum Mean Discrepancy)** — 커널 기반 분포 거리. 0이면 모든 모멘트 일치.
- **Unpaired distribution** — 측정 파괴 때문에 control과 perturbed의 같은-세포 매칭이 없음.
- **scGEN** — VAE latent space 선형 shift 기반 perturbation 예측 baseline.
- **4i imaging** — iterative indirect immunofluorescence imaging; 한 세포에서 수십 개 단백질 마커 측정.
- **Drug severity** — OT cost. 약물이 세포 상태를 얼마나 크게 바꿨는지의 정량 지표.
