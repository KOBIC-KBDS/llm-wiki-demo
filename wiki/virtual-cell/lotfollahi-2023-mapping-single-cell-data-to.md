---
title: "scArches — transfer learning으로 query 데이터를 reference atlas에 mapping"
authors: Lotfollahi M, Naghipourfar M, Luecken MD, ..., Yosef N, Misharin AV, Theis FJ*
year: 2022
journal: "Nature Biotechnology 40:121–130"
source: lotfollahi-2023-mapping-single-cell-data-to.md
category: virtual-cell
tags: [reference-mapping, transfer-learning, scArches, scVI, conditional-VAE, cell-atlas, query-to-reference]
---

## 요약

기존에 학습된 single-cell reference atlas (CVAE 기반 — scVI, trVAE, scANVI, totalVI)에 **새 query 데이터셋**을 raw data 공유 없이 mapping하는 transfer-learning framework. 새 study 라벨에 대해 **adaptor weights**만 추가/학습하고 나머지는 freeze → **10⁴~10⁵배 적은 학습 파라미터**로 full integration과 동등 성능. COVID-19 immune cell을 healthy reference에 mapping할 때 disease-specific 변이를 보존하고, totalVI 기반에서는 측정 안 된 단백질 modality까지 imputation 가능.

## 핵심 기여

1. **Architecture surgery**: pre-trained CVAE에 새로운 study input node + 작은 adaptor만 추가, 나머지는 동결. 데이터 공유 없이도 reference 확장 가능.
2. **Privacy-friendly + 분산형 atlas 구축**: raw data 공유 불가능한 의료/임상 환경에 적합.
3. **모델 agnostic wrapper**: scVI / trVAE / scANVI / totalVI 어느 base model에도 적용.
4. **Disease 보존 mapping**: COVID-19 같은 perturbation을 batch effect로 사라지게 하지 않고 별 cluster로 유지.
5. **Multi-modal imputation**: totalVI scArches는 RNA-only query에서 ADT 단백질 channel 보간.
6. **Iterative atlas building**: 여러 query를 차례차례 / 병렬로 통합.

## 방법론 및 아키텍처

| 항목 | 내용 |
|---|---|
| Base model | CVAE (scVI / trVAE / scANVI / totalVI) |
| 파라미터 변경 | 새 study label에 해당하는 input node + adaptor weights만 trainable |
| 동결 | 나머지 encoder/decoder weight 고정 |
| 평가 | scIB benchmark 10 metric (batch 4 + bio 6) |
| Datasets | 췌장 (3 study), mouse brain (250k cells), whole mouse, COVID-19 PBMC, CITE-seq |

### 핵심 절차

1. Reference의 CVAE를 학습 (multiple study labels로 batch 정규화).
2. Query 도착 → 새 study label에 input node 추가, adaptor 가중치 무작위 초기화.
3. Adaptor만 fine-tune → query 데이터를 reference latent space에 정렬.
4. Joint embedding에서 holdout cell type / disease state는 별 cluster로 유지.

## 결과

- Adaptor-only 학습 시 trainable parameter가 4~5 자릿수 줄어들면서도 full retrain 수준 성능.
- Reference 데이터의 절반만 있어도 robust (multiple study batch 조건).
- Holdout alpha-cell (췌장) 같은 미관측 cell type을 query에서 별 cluster로 보존.
- COVID-19 query → healthy PBMC reference에 mapping 시 monocyte / neutrophil 확장 같은 disease state 클러스터가 별도로 떠오름 (batch effect로 묻히지 않음).
- totalVI scArches: RNA-only query에서 ADT 단백질 마커 imputation 양호.

## 활용 가이드

- **사용 시나리오**:
  - 자체 데이터를 HCA / Tabula Sapiens / CZ CELL×GENE 기반 reference에 즉시 mapping.
  - 다기관 임상 데이터를 raw 공유 없이 통합 (privacy 제약).
  - 새 disease state cluster 발굴 (vs healthy reference).
  - 단일 modality 측정 → multi-modal reference에서 imputation.
- **사용 불가 시나리오**:
  - Reference에 해당 tissue가 없는 경우 (생성형 모델로 새 reference 만들어야 함).
  - reference base-model이 없거나 부적합한 경우.
- **활용 관점**:
  - 자체 단일세포 데이터 (자폐 / AD / 암 등)를 표준 atlas에 빠르게 매핑하여 표준 cell type 자동 annotation.
  - COVID-19 case study처럼 disease-specific cluster를 보존해 발굴하는 패러다임이 적용 가능.
  - Bunne 2024 AIVC의 "UR 위에 새 데이터 가상화" 의 prototypical 도구.

## 관련 논문

- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] :supports — UR contextualize 도구 사례
- [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] :supports — perturbation 예측은 CellOT / GEARS, mapping은 scArches
- [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] :supports — GEARS와 함께 같은 latent에서 작동 가능
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] :methodologically_uses — Perturb-seq 데이터를 healthy reference에 mapping해 perturbation 효과 시각화 가능
- [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] :methodologically_uses — 100M cell atlas의 latent에 자체 데이터 매핑 가능
- [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] :supports — Arc Virtual Cell Atlas의 query mapping 표준
- [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] — 같은 카테고리 (virtual cell)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 모델 — perturbation 적용
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — Geneformer in silico perturbation

## 용어집

- **scArches** — single-cell architecture surgery; transfer-learning reference mapping framework.
- **Architecture surgery** — pre-trained NN에 새 input node + adaptor weight를 외과적으로 추가.
- **Adaptor** — query study label에 해당하는 작은 trainable weight set.
- **CVAE (Conditional VAE)** — categorical label로 조건화된 VAE.
- **scVI** — scRNA-seq용 CVAE (Lopez 2018).
- **trVAE** — transfer-learning VAE.
- **scANVI** — cell-type label 기반 semi-supervised VAE.
- **totalVI** — RNA + ADT 단백질 multi-modal VAE (CITE-seq).
- **scIB** — single-cell integration benchmark; 10개 정량 metric.
- **CITE-seq** — RNA-seq + antibody barcode (ADT) 동시 측정.
- **Reference atlas** — 다 study, 다 donor, 다 batch가 통합된 대규모 단일세포 임베딩.
- **Query mapping** — 새 데이터를 reference 좌표계로 투영.
