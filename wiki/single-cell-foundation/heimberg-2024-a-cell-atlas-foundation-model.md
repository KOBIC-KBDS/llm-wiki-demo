---
title: "SCimilarity — 23.4M 세포 Atlas 검색용 metric-learning 파운데이션 모델"
authors: Heimberg G, Kuo T, DePianto DJ, ..., Kaminker J*, Vander Heiden JA*, Regev A*
year: 2024
journal: "Nature 2024-11-20"
source: heimberg-2024-a-cell-atlas-foundation-model.md
category: single-cell-foundation
tags: [SCimilarity, metric learning, triplet loss, cell search, Cell Ontology, fibrosis, in vitro model, retrieval]
---

## 요약

**SCimilarity**는 facial-recognition의 **triplet loss**를 단일세포 임베딩에 도입한 metric-learning 파운데이션 모델. Cell Ontology 라벨이 있는 56 study × 7.89M 세포에서 5천만 개 triplet을 sampling 해 학습하며, 손실은 (1−β)·L_MSE(A, Â) + β·L_triplet(A,P,N) (β = 0.001)로 query 민감도와 cross-study integration을 균형. 학습된 임베딩으로 **23.4M 세포, 412 study, 184 tissue, 132 disease**에 걸친 검색 가능한 reference atlas를 구축, 10K nearest cells를 **0.05초**에 반환한다. 사례연구로 interstitial lung disease(ILD)의 macrophage/fibroblast subset를 query → 다른 fibrotic disease와 in vitro 3D hydrogel 모델까지 일치하는 cell state를 회수하고 실험으로 검증.

## 핵심 기여

- **세포용 metric-learning** — gene-token MLM 대신 FaceNet 식 triplet loss로 cell type 의미를 임베딩 거리에 주입.
- **Ontology-aware triplet sampling** — ancestor-descendant ambiguity (예: T cell vs CD4+ T cell)는 학습에서 제외 → 수동 라벨 평탄화 없이 확장.
- **Triplet + reconstruction 결합** — β로 query/integration 트레이드오프 조절.
- **23.4M 세포 검색 인덱스** — 인체 전체 규모 atlas에서 sub-second nearest-neighbor 검색 첫 사례.
- **Outlier / confidence scoring** — 임베딩별 신뢰도, OOD 셀 자동 표시.
- **생물학적 발견** — ILD macrophage query → 다른 fibrosis 질환과 in vitro 3D hydrogel 시스템에서 동일 cell state 회수, 실험 재현.

## 방법론 및 아키텍처

| 축 | 내용 |
|---|---|
| 학습 데이터 | 56 study (46 sc + 10 sn), 7.89M 세포, 203 Cell Ontology terms |
| 평가/검색 atlas | 23.38M 세포, 412 study, 5,142 tissue samples, 184 tissue + 132 disease ontology |
| Triplet 샘플링 | 5천만, anchor/positive 서로 다른 study, hard mining, ancestor-descendant 제외 |
| 모델 | FFN 기반 encoder + reconstruction decoder, 저차원 임베딩 출력 |
| 손실 | L = (1−β)·L_MSE(A, Â) + β·L_triplet(A,P,N), β = 0.001, margin α = 0.05 |
| 인덱스 | 23.4M 세포 사전 임베딩, 10K NN ≈ 0.05 s |
| 데이터 범위 | 학습은 10x Chromium 한정, 다른 플랫폼은 OOD 평가 |

## 결과

- **Query–retrieval Spearman ρ = 0.77** vs scFoundation 0.54, scGPT 0.59.
- Integration: cell-type ASW, study NMI/ARI에서 Harmony, scVI, scanorama, scArches와 동등 또는 우위 — fine-tuning 없이.
- B cell / Treg negative control: 임의로 섞지 않음 (scanorama, scVI는 섞임).
- **ILD case**:
  - Macrophage query → NAFLD, scleroderma, sepsis 등 다른 fibrotic disease와 일치하는 macrophage state 회수.
  - Fibroblast query → cross-organ fibrotic-fibroblast signature 회수.
  - Top in vitro 매칭 = 3D hydrogel 모델 → 실험으로 disease cell state 재현 검증.
- 10x로만 학습했는데 CEL-Seq2, Drop-Seq, Seq-well, SMART-Seq2, InDrops에서도 사용 가능 (Seq-well, SMART-Seq2 정밀도 약간 하락).

## 활용 가이드 / 임상적 의의

- **Cell state 검색** — 미정 cell state를 다른 cohort/조직/in vitro 모델에서 빠르게 매칭 → 가설 생성.
- **In vitro 모델 검증** — 어떤 in vitro 시스템이 환자 세포 상태를 가장 잘 재현하는지 객관적 매칭.
- **신약 표적 cohort 정렬** — 다른 fibrotic 질환 간 macrophage/fibroblast cross-talk 발견.
- 종양 / iPSC 유래 / cell line은 학습에서 제외 — 해당 분석엔 별도 모델 필요.

## 관련 논문

- [[single-cell-foundation/boiarsky-2025-sctab-benchmark-for-single-cell]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/hao-2024-large-scale-foundation-model-on]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/kedzierska-2025-assessing-the-limits-of-zero]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/wen-2024-cellplm-pre-training-of-cell]] — 같은 카테고리 (single-cell foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본 (architecture anchor)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology anchor
- [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]] — sc-foundation 벤치마킹 perspective

## 용어집

- **Metric learning** — 임베딩 거리 자체가 의미적 유사도가 되도록 학습.
- **Triplet loss** — anchor–positive는 가깝게, anchor–negative는 멀게, margin α 이상.
- **Hard triplet mining** — 현재 가장 헷갈리는 negative만 골라 학습 (정보 밀도 ↑).
- **Cell Ontology (CL)** — 계층적 cell type 사전.
- **Ancestor-descendant ambiguity** — 한 라벨이 다른 라벨의 부모인 경우의 모호성, 학습에서 제외.
- **SCimilarity score** — embedding distance 함수, query–reference 쌍의 유사도.
- **Reference atlas** — 23.4M 사전 임베딩된 세포 검색 인덱스.
- **Confidence / outlier score** — 임베딩이 학습 분포에 얼마나 부합하는지 정량화.
- **ASW (Average Silhouette Width)** — 클러스터 응집도 메트릭.
- **Cross-tissue retrieval** — 다른 조직에서 동일/유사 cell state 검색.
