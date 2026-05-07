---
title: "STAGATE — Adaptive Graph Attention Auto-Encoder로 spatial domain 식별"
authors: Dong K, Zhang S
year: 2022
journal: Nature Communications 13:1739
source: dong-2022-deciphering-spatial-domains-from-spatially.md
category: spatial-foundation
tags: [STAGATE, GAT, GAE, spatial-domain, Visium, Slide-seq, Stereo-seq, denoising, 3D]
---

## 요약

**STAGATE**는 spatial transcriptomics에서 spatial domain을 식별하기 위한 **adaptive Graph Attention Auto-Encoder**다. 좌표로 만든 Spatial Neighbor Network(SNN)과 유전자 발현을 함께 인코딩하며, 인코더-디코더 가운데 **graph attention layer**가 이웃 spot 사이 edge weight를 적응적으로 학습한다. 저해상도(Visium)에서는 사전 클러스터링으로 SNN을 prune하는 **cell-type-aware 모듈**이 작은 도메인 경계를 살린다. Visium / Slide-seq(V2) / Stereo-seq / STARmap 모두에서 SOTA, 발현 denoising, 그리고 3D 도메인 재구성까지 가능.

## 핵심 기여

- **Adaptive attention** — pre-defined similarity 대신 spot 사이 유사도를 학습. 도메인 경계에서 이질성을 잘 잡음.
- **Cell-type-aware SNN (옵션)** — Visium처럼 spot당 여러 세포가 섞이는 경우, Louvain pre-cluster 결과로 SNN을 prune → 작은 영역(CA1, dentate gyrus, cortical sub-layer 등)이 분리됨.
- **다중 플랫폼 일반성** — Visium, Slide-seq/V2, Stereo-seq, STARmap 모두에서 best ARI; 평가지표(NMI/HS) 일관 우위.
- **Denoising / imputation** — 디코더 출력을 사용. Allen ISH ground truth와 일치하는 layer marker 패턴 회수.
- **3D 재구성** — 인접 절편 사이 edge 추가한 3D SNN으로 batch effect 완화 + 3D 도메인 추출.

## 방법론 및 아키텍처

### 1) SNN 구축
- Aij=1 ↔ Euclidean(i,j) < r.
- Visium: 6-NN. 다른 플랫폼: spot당 6-15 이웃 평균.

### 2) Cell-type-aware SNN (선택)
- PCA → Louvain (resolution 0.2) pre-cluster.
- 다른 pre-cluster에 속한 인접 spot 사이 edge 제거.
- → Visium 같은 저해상도에서 heterogeneous tissue 경계 향상.

### 3) Graph Attention Auto-Encoder
- 인코더 가운데 graph attention layer가 attention coefficient α_ij를 학습.
- 디코더는 대칭 구조; loss = 입력/출력 발현 MSE (HVG 3,000개).
- Latent dim, layer 수에 robust.

### 4) Downstream
- mclust / Louvain on embedding.
- UMAP + PAGA trajectory.
- 디코더 출력 → 발현 denoising / imputation.

### 5) 3D 확장
- ICP + 수동 fine-tuning으로 절편 정합.
- 3D SNN = section 내 2D SNN ∪ section 간 spatial edge.
- batch effect 줄이고 3D 도메인 추출.

## 결과

### Visium DLPFC (12 sections, 6 layers + WM)
| Method | ARI (151676) |
|---|---|
| SCANPY | 0.29 |
| UTAG | 0.36 |
| SEDR | 0.42 |
| SpaGCN | 0.44 |
| BayesSpace | 0.44 |
| **STAGATE** | **0.60** |

- UMAP에서 L1→L6→WM 깨끗한 trajectory; PAGA에서도 선형 순서.

### Mouse hippocampus (Slide-seqV2, 10 μm)
- Cord-like Ammon's horn (CA1sp/CA2sp/CA3sp) + arrow-like dentate gyrus (DG-sg) 회수.
- Marker: Itpka, Bcl11b (CA1) / Amigo2, Pcp4 (CA2) / Lrrtm4 (DG).

### Mouse olfactory bulb (Stereo-seq + Slide-seqV2)
- RMS / GCL / IPL / MCL / EPL / GL / ONL laminar 구조 회수.
- AOB와 AOBgr 분리; 좁은 MCL도 검출 (Gabra1 검증).

### Whole coronal brain (Visium)
- Cell-type-aware 모듈 켜야 hippocampus CA1/CA3/dentate gyrus + cortical sub-layer 분리.

### Denoising
- 6 layer marker (B3GALT2, ATP2B4 등) → STAGATE 출력이 Allen ISH와 일치.

### 3D Hippocampus
- 7 Slide-seq section 정합 후 3D SNN으로 batch 보정 + 3D 도메인.

### Scalability
- 50k spot: ~40 min, ~4 GB GPU. spot 수에 GPU 메모리 선형.

## 활용 가이드

- **Visium 단일 sample 분석**: 단일 sample / 작은 cohort에서 가장 강력 (DLPFC ARI 0.60).
- **Slide-seq/Stereo-seq**: cell-type-aware 모듈 끄고 사용 권장 (이미 단일세포 해상도).
- **다중 sample joint**: batch correction 내장 안 됨 → CellCharter가 더 적합.
- **Histology 활용**: STAGATE는 H&E 사용 안 함 → iSTAR 또는 stLearn 보완 가능.
- **3D 데이터**: 인접 절편 정합 후 3D SNN 옵션 사용.

## 관련 논문

- [[spatial-foundation/varrone-2024-cellcharter-reveals-spatial-cell-niches]] :supports — STAGATE는 단일 sample 강세, CellCharter는 다중 cohort + batch correction + asymmetric NE 강세.
- [[spatial-foundation/birk-2025-nichecompass-a-method-for-the]] :extends — NicheCompass도 GNN 기반이지만 signaling pathway interpretation까지 확장.
- [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]] :supports — iSTAR는 H&E 기반 super-resolution; STAGATE는 ST 자체로 domain 식별. 결합 가능.
- [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] :builds_on — Tangram이 sc → spatial 정렬 후 STAGATE로 domain identification 흔한 워크플로.
- - [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/birk-2025-nichecompass-a-method-for-the]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/varrone-2024-cellcharter-reveals-spatial-cell-niches]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]] — 같은 카테고리 (spatial foundation)
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 패밀리 (Nicheformer가 확장)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **GAE / GAT** — Graph Auto-Encoder / Graph Attention Network.
- **SNN** — Spatial Neighbor Network (좌표 기반 그래프).
- **DLPFC** — Dorsolateral Prefrontal Cortex; Visium 표준 벤치마크.
- **Slide-seq / Slide-seqV2** — 10 μm 해상도 sequencing 기반 ST.
- **Stereo-seq** — DNA nanoball patterned array, subcellular resolution.
- **STARmap** — image-based in situ sequencing.
- **ARI / NMI / HS** — clustering 정확도 표준 지표.
- **PAGA** — Partition-based Graph Abstraction; trajectory inference.
- **Ammon's horn** — 해마의 CA1/CA2/CA3 field.
- **Dentate gyrus (DG-sg)** — 해마 과립세포층.
- **Cell-type-aware 모듈** — pre-clustering으로 SNN을 prune하는 STAGATE 옵션.
- **Mclust / Louvain** — 임베딩 위에서 사용하는 clustering 알고리즘.
