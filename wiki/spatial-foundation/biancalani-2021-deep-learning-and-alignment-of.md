---
title: "Tangram — sc/snRNA-seq를 공간 좌표에 정렬하는 deep learning 프레임워크"
authors: Biancalani T, Scalia G, ..., Macosko EZ, Regev A
year: 2021
journal: Nature Methods
source: biancalani-2021-deep-learning-and-alignment-of.md
category: spatial-foundation
tags: [Tangram, alignment, MERFISH, Visium, smFISH, STARmap, SHARE-seq, CCF]
---

## 요약

**Tangram**은 dissociated sc/snRNA-seq 데이터를 공간 측정치 (MERFISH / STARmap / smFISH / Visium / 조직학 이미지) 위에 "퍼즐 조각처럼" 정렬하여, **유전체 전체를 단일세포 해상도로 공간 매핑**하는 deep learning 프레임워크. 비-볼록 최적화로 KL divergence(세포 밀도) + 코사인 유사도(유전자 발현)를 결합하며, 하이퍼파라미터가 없다. 단일 P100 GPU에서 10만 세포를 수 분에 매핑한다.

## 핵심 기여

- **모든 ST 모달리티와 호환** — 공통 유전자 부분집합만 있으면 MERFISH, STARmap, smFISH, Visium, ISS 어느 것이든 정렬 가능.
- **5가지 활용 시나리오** — (1) 타겟 유전자를 transcriptome-wide로 확장, (2) 저품질 spatial 측정치 보정, (3) 세포 타입 공간 위치 추정, (4) Visium spot을 단일세포로 deconvolve, (5) SHARE-seq의 ATAC 정보를 공간 좌표로 투영.
- **조직학→해부학 모듈** — H&E/형광 이미지를 Allen Common Coordinate Framework(CCF)에 자동 정합. 사람의 랜드마크 지정 불필요.
- **하이퍼파라미터 없음** — 사용자가 튜닝할 항목이 없음. 재현성과 보급에 유리.

## 방법론 및 아키텍처

| 구성 | 내용 |
|---|---|
| 입력 | sc/snRNA-seq matrix + spatial matrix (공통 유전자 일부 필요) |
| 최적화 변수 | mapping matrix M (sc_cells × voxels), softmax over voxels |
| 손실 함수 | KL(cell density) + cosine(gene expression) + filter term (sc > voxels일 때) |
| 출력 | 확률적 매핑 (각 voxel당 sc 분포) / 결정적 매핑 (가장 likely한 cell) |
| 후처리 | sc의 모든 유전자 발현을 각 voxel에 투영 → genome-wide spatial map |
| 조직학 모듈 | CNN으로 voxel embedding → CCF region label 부여 |

### 공간 모달리티별 사용 시나리오
- **MERFISH (~250 genes, single-cell res.)** → 27k 유전자로 확장 + 저품질 유전자 보정
- **Visium (~55 μm spot)** → 단일세포 deconvolution
- **smFISH (소수 유전자)** → 다른 유전자 패턴 예측 (Allen ISH로 검증)
- **SHARE-seq (RNA+ATAC)** → ATAC 신호를 spatial 좌표로 가져와 chromatin 공간 패턴 시각화

## 결과

- **MERFISH 외삽**: 253 유전자로 학습 → leave-one-out에서 75%의 유전자가 spatial correlation > 40% 달성. Allen ISH와 정성적으로 잘 일치.
- **저품질 MERFISH 신호 교정**: Adatms2, L3mbtl4, Sema3e 등에서 raw MERFISH보다 Tangram 예측이 ISH ground truth와 더 일치.
- **마우스 1차 운동피질** (BICCN, 16만 snRNA-seq): glutamatergic vs GABAergic 6:1 비율 보존, 피질층 (L2/3, L5 IT/PT/NP, L6 IT/PT/CT, L6b) 패턴 회수.
- **공간 chromatin**: SHARE-seq RNA로 mapping → ATAC 채널 투영 → MERFISH 공간상에서 TF motif score를 단일세포 해상도로 시각화.

## 활용 가이드

| 가용 데이터 | Tangram 사용처 |
|---|---|
| Visium + 같은 조직 sc 레퍼런스 | spot-level deconvolution, 단일세포 해상도 회복 |
| MERFISH/STARmap (<1000 유전자) + sc 레퍼런스 | genome-wide expression 재구성 |
| sc + 조직학 이미지만 (ST 없음) | 조직학으로 anatomical region을 자동 부여 |
| SHARE-seq + spatial RNA | spatial multimodal (RNA+ATAC) 시각화 |

- **주의**: sc 레퍼런스에 누락된 cell type은 잘못 할당되기 쉬움. 공통 유전자 수가 너무 적으면 정렬 품질 저하.
- **PyTorch 구현**: GPU 1장으로 10만 cell 분 단위 처리.

## 관련 논문

- [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]] :supports — H&E 이미지로부터 ST를 super-resolution. Tangram은 sc reference로, iSTAR는 histology feature로 같은 "spot보다 작은 해상도" 문제를 푼다.
- [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]] :supports — STAGATE는 ST 자체로 spatial domain 식별, Tangram은 sc 정렬에 초점.
- [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] :extends — Nicheformer는 sc + spatial을 같이 pretraining; Tangram의 1대1 alignment를 일반 representation으로 확장.
- [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]] :provides_data_for — Tangram 같은 alignment/prediction 모델을 대규모 평가할 수 있는 H&E+ST 벤치마크.
- - [[spatial-foundation/birk-2025-nichecompass-a-method-for-the]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/varrone-2024-cellcharter-reveals-spatial-cell-niches]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]] — 같은 카테고리 (spatial foundation)
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 패밀리 (Nicheformer가 확장)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **MERFISH** — Multiplexed Error-Robust FISH; ~100–1000 유전자 패널, 단일세포 해상도, 이미지 기반 ST.
- **STARmap** — in situ sequencing 기반 ST.
- **smFISH** — single-molecule FISH; 소수 유전자.
- **Visium / ST** — sequencing 기반, 55 μm spot, transcriptome-wide.
- **SHARE-seq** — sc RNA + ATAC 동시 측정.
- **CCF** — Allen Institute Common Coordinate Framework, 마우스 뇌 3D 표준 좌표계.
- **Deconvolution** — spot 안에 어떤 세포 타입이 얼마나 있는지 추정.
- **Cosine similarity** — 두 벡터 사이 각도 기반 유사도.
- **KL divergence** — 두 확률분포 사이 비대칭 거리.
