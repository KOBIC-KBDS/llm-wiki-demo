---
title: "NicheCompass — Signaling pathway 단위로 해석되는 spatial multi-omics niche 모델"
authors: Birk S, Bonafonte-Pardas I, ..., Talavera-Lopez C, Lotfollahi M
year: 2025
journal: Nature Genetics
source: birk-2025-nichecompass-a-method-for-the.md
category: spatial-foundation
tags: [NicheCompass, niche, signaling, VGAE, GNN, multi-omics, gene-program, reference-mapping, mouse-brain-atlas]
---

## 요약

**NicheCompass**는 spatial omics 데이터를 **signaling 단위로 해석되는 niche embedding**으로 변환하는 multimodal **conditional variational graph autoencoder**다. embedding의 각 차원은 하나의 **spatial Gene Program (GP)** — cell-cell communication, transcriptional regulation, combined interaction, 또는 de novo program — 의 활성도. GP들은 CellChat / NicheNet / OmniPath / MEBOCOST 같은 데이터베이스에서 가져오고, neighborhood-side와 self-side 마스크로 ligand vs receptor/target gene을 분리해서 재구성한다. 마우스 발생, 인간 종양 미세환경, 마우스 뇌 RNA+ATAC multi-omics에 적용했고, **8.4M cell 마우스 전체 뇌 atlas**까지 cross-technology integration으로 확장했다.

## 핵심 기여

- **해석 가능한 niche embedding** — 각 latent 차원 = 하나의 GP의 활성도. niche를 발견하면 곧바로 "어떤 신호 경로가 활성화 되었는지" 알 수 있다.
- **3가지 GP 카테고리 + de novo** — (1) cell-cell communication, (2) transcriptional regulation, (3) combined interaction, (4) prior에 없는 spatially co-expressed gene/peak set을 학습하는 de novo.
- **Self vs Neighborhood 분리** — 두 개의 masked linear decoder가 (a) cell 자신의 발현(receiver/intracellular)과 (b) 이웃 평균 발현(source/intercellular)을 따로 재구성. ligand vs receptor를 자연스럽게 분리.
- **Multimodal (RNA + ATAC)** — peak이 gene body/promoter에 매핑되어 multi-omics GP로 결합.
- **Spatial reference mapping** — 사전훈련된 reference에 query cohort를 매핑 → novel niche 식별, 신호 경로 비교 (인간 유방암/폐암 적용).
- **확장성** — 8.4M cell 마우스 전체 뇌 atlas, 여러 spatial platform 통합.

## 방법론 및 아키텍처

### 입력
- Spatial omics: cell- 또는 spot-level. RNA only 또는 RNA + ATAC.
- 좌표 → spatial neighborhood graph.
- node별 covariate (sample, modality 등).

### 모델
| 구성 | 역할 |
|---|---|
| GNN encoder | 이웃 feature 통합 → cell embedding |
| Covariate embedder | batch effect 제거 |
| Latent dim = GP 활성도 | 해석 가능 (GP 라이브러리 + de novo) |
| Graph decoder (cosine) | sample-conditional adjacency 재구성 |
| Self omics decoder (linear, masked) | 자기 발현 재구성 (receptor/target) |
| Neighborhood omics decoder (linear, masked) | 이웃 발현 재구성 (ligand/source) |
| Pruning + sparsity reg | 정보적인 GP만 유지, 유전자 sparse |

### GP 분류
- **Cell-cell communication GP**: ligand (source) + receptor + downstream target (receiver).
- **Transcriptional regulation GP**: TF + target.
- **Combined interaction GP**: 두 가지 통합.
- **De novo GP**: prior에 없지만 spatially co-expressed gene/peak set을 학습.

### Downstream
- Niche identification (GP-activity embedding clustering).
- Niche 해석 = top GPs.
- Cell-cell communication inference (ligand×receptor weighted by GP activity).
- Spatial reference mapping.
- Hierarchical niche structure (dendrogram over mean GP activity).

## 결과

### Mouse organogenesis (seqFISH, 3 embryo)
- 모든 embryo 일관된 niche 계층: CNS (midbrain/forebrain/floor plate/hindbrain/spinal cord), 등쪽/배쪽 gut, 순환계, 근육계.
- 신호 경로 해석:
  - **Ventral gut**: Spint1-St14 (HAI-1/matriptase, 장상피 barrier).
  - **Dorsal gut**: Cthrc1-Fzd3 (notochord, Wnt PCP).
  - **Hindbrain**: Fgf3-Fgfr1 (compartment boundary).
  - **Floor plate**: Calca + Shh (midbrain-hindbrain glutamatergic).
  - **Midbrain**: Fgf17-Fgfr2.
  - **Forebrain**: Dkk1.
- Embryo 2 leave-one-out에서도 niche/program 정확 매핑.

### 인간 종양 미세환경 (유방암/폐암)
- Donor-specific 종양 niche 검출.
- Reference에 query 매핑 → novel niche 발굴.

### 마우스 뇌 multi-omics (RNA+ATAC)
- 다중 모달 GP로 niche 특성화.

### Whole mouse brain atlas (8.4M cell)
- 다중 spatial platform 통합 → scalability + cross-technology 입증.

### 벤치마크
- niche recovery / GP inference / batch removal 모두에서 기존 방법 능가.

## 활용 가이드

- **CellCharter와의 차이**: CellCharter = niche 발견에 강함, 해석은 cell-type enrichment 위주. NicheCompass = niche 해석을 **신호 경로 단위로** 직접 제공.
- **Nicheformer와의 차이**: Nicheformer = transformer-based generic representation. NicheCompass = graph autoencoder + interpretable GP. embedding을 fine-tune 하기보다 그 자체 차원이 의미를 가짐.
- **언제 쓰면 좋은가**:
  - "어떤 ligand-receptor 축이 niche를 정의하는가" 가설 검증.
  - 종양 cohort에 reference mapping해서 신호 axis 차이 비교.
  - Multi-modal (RNA + ATAC) spatial data 통합.
- **사전 작업**:
  - Prior GP 라이브러리 선택/커스터마이즈 (CellChat / NicheNet / OmniPath / MEBOCOST).
  - 다중 sample이면 covariate column 정확히 지정.

## 관련 논문

- [[spatial-foundation/varrone-2024-cellcharter-reveals-spatial-cell-niches]] :supports — CellCharter는 niche 발견에 빠르고 가벼움; NicheCompass는 그 위에 신호 해석을 더함.
- [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]] :builds_on — STAGATE의 GAE 아이디어를 GP-interpretable VGAE로 확장.
- [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] :supports — Nicheformer는 generic transformer representation, NicheCompass는 interpretable graph model. Theis 연구그룹의 spatial foundation 흐름 안에서 상보적이다.
- [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] :builds_on — Tangram이 sc → spatial 정렬 후 NicheCompass로 신호 해석 가능.
- [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]] :provides_data_for — H&E + ST paired data로 NicheCompass + image fusion 시도 가능.
- - [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/varrone-2024-cellcharter-reveals-spatial-cell-niches]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]] — 같은 카테고리 (spatial foundation)
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 패밀리 (Nicheformer가 확장)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **Niche** — spatially co-localized cellular community.
- **Gene Program (GP)** — 라벨된 gene/peak 집합. 여기서는 latent 차원.
- **Cell-cell communication GP** — ligand + receptor + target gene.
- **Transcriptional regulation GP** — TF + target gene.
- **Combined interaction GP** — 위 둘의 통합.
- **De novo GP** — prior에 없는 학습된 program.
- **VGAE** — Variational Graph Autoencoder.
- **GNN** — Graph Neural Network.
- **seqFISH** — sequential FISH; image-based ST.
- **CellChat / NicheNet / OmniPath / MEBOCOST** — 신호경로 데이터베이스 (prior).
- **Spatial reference mapping** — 사전훈련 latent space에 query cohort를 매핑.
- **Floor plate** — 발생 신경관 복측 중앙선; Shh source.
- **PCP** — Planar Cell Polarity (Wnt 비-canonical 축).
- **HAI-1 / matriptase** — Spint1-St14 단백 쌍 (장 barrier 조절).
