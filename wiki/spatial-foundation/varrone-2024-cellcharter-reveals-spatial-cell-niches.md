---
title: "CellCharter — 확장 가능한 spatial cell niche 식별·특성화·비교 프레임워크"
authors: Varrone M, Tavernari D, Santamaria-Martinez A, Walsh LA, Ciriello G
year: 2024
journal: Nature Genetics
source: varrone-2024-cellcharter-reveals-spatial-cell-niches.md
category: spatial-foundation
tags: [CellCharter, niche, GMM, VAE, IMC, CODEX, MERFISH, Visium, lung-cancer, batch-correction]
---

## 요약

**CellCharter**는 imaging 기반(CODEX, IMC, MERFISH) + sequencing 기반(Visium, Slide-seq) ST 데이터를 모두 처리할 수 있는 **scalable spatial niche** 프레임워크다. 핵심은 (1) 모달리티별 VAE로 dimensionality reduction + batch correction, (2) **l-step neighborhood feature concatenation**(GNN 없이 이웃 정보 인코딩), (3) **GMM clustering with stability-based k selection**(Fowlkes-Mallows index로 자동 선택), (4) cluster를 cell-type enrichment, **asymmetric neighborhood enrichment**, shape descriptor (curl/elongation/linearity/purity)로 분석. 폐암 cohort에서 **hypoxia + 호중구 + cell migration**으로 특징지어지는 prognostic 종양 niche를 발견했다.

## 핵심 기여

- **기술 무관(technology-agnostic)** — 단백질(CODEX/IMC), 전사체(MERFISH/Visium/Slide-seq), 다중오믹스 모두 동일 파이프라인.
- **확장성** — DLPFC 벤치마크에서 STAGATE, SEDR, BayesSpace 등 7종 대비 메모리 최저, 속도 2위(UTAG 다음).
- **자동 cluster 수 선택** — k별 10회 GMM 반복 → FMI(k-1, k, k+1)이 함께 안정적인 k를 stable solution으로 선택.
- **비대칭 이웃 인접도** — "A는 B 근처에 있지만 B는 A 근처에 있지 않다" 같은 방향성을 분석 (단순 permutation보다 빠름).
- **모양(shape) 정량화** — curl, elongation, linearity, purity → linear / round / circular / irregular 분류.
- **Batch correction 내장** — 다중 sample joint clustering이 donor가 아니라 organ 구조로 수렴하게 함.
- **폐암 임상 발견** — hypoxia(CA9, GLUT1) + 종양관련 호중구 + migration signature로 정의되는 종양 niche, proliferative tumor와 spatially 분리, 독립 cohort에서 poor prognosis와 연관.

## 방법론 및 아키텍처

### 입력
- Cell × feature 매트릭스 (mRNA / protein / multi-omics).
- 2D 좌표.
- (선택) sample/batch ID.

### 파이프라인
| 단계 | 내용 |
|---|---|
| 1) Dim reduction + batch correction | VAE (modality-specific: scVI / totalVI 등) |
| 2) Spatial network | 인접 cell/spot 사이 edge |
| 3) l-neighborhood aggregation | cell A의 feature + i-step neighbor 평균 (i=1..l, default l=3) concat |
| 4) GMM clustering | 후보 k별 10회 반복; FMI 기반 stability로 k 자동 선택 |
| 5) Downstream | cluster proportion / cell-type enrichment / symmetric & asymmetric NE / shape descriptor |

### Shape descriptor
- 경계, 면적, 둘레, bounding box 계산.
- **Curl** = 얼마나 휘었는지, **Elongation** = major/minor axis 비, **Linearity** = 선형 경로 근사도, **Purity** = boundary 안 cell 중 같은 cluster 비율.

## 결과

### DLPFC Visium 벤치마크 (12 sections, 7 layers)
| 측면 | 결과 |
|---|---|
| Memory | 7개 method 중 최저 |
| 속도 | 2위 (UTAG 다음); STAGATE 대비 빠름 |
| Joint clustering ARI | best (mean + best 모두 우위) |
| Batch correction | donor-effect 제거 후 anatomy로 수렴 |
| Sequencing depth robustness | 10% subsample에서도 성능 유지 |
| 자동 k 선택 | k=9 (true 7과 근접; 12-sample 파일럿과 42-sample 확장 cohort 모두 동일) |

### 다른 데이터
- **Mouse spleen CODEX**: B follicle / PALS / marginal zone / red pulp 분리.
- **Mouse brain spatial multi-omics (RNA + ATAC)**: 두 모달리티 일관 niche 구조.
- **Lung cancer (IMC + Visium 다중 cohort)**: hypoxia + neutrophil + migration niche → poor OS와 독립적 검증.

## 활용 가이드

- **언제 쓰면 좋은가**:
  - 다양한 spatial 모달리티(CODEX/IMC/MERFISH/Visium)를 통합 분석할 때.
  - 큰 cohort (수십~수백 sample, 수백만 cell)를 한 번에 joint clustering 해야 할 때.
  - "A 주위에 어떤 type이 모이는가"의 directional 관계가 중요한 종양 미세환경 분석.
- **STAGATE와 비교**:
  - STAGATE: 단일 sample / 작은 cohort에서 ARI 더 좋음, GAT로 edge weight 학습.
  - CellCharter: 다중 sample joint, batch correction, 자동 k, 더 빠르고 가벼움.
- **NicheCompass와 비교**:
  - CellCharter는 niche를 발견·비교, NicheCompass는 niche를 **signaling pathway 단위**로 해석.

## 관련 논문

- [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]] :supports — STAGATE는 GAT 기반, CellCharter는 GMM + l-neighborhood. 단일 sample은 STAGATE, 다중 cohort는 CellCharter.
- [[spatial-foundation/birk-2025-nichecompass-a-method-for-the]] :extends — NicheCompass는 niche 식별을 넘어 signaling-program 해석으로 확장.
- [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] :supports — Nicheformer는 transformer pretraining으로 cell embedding을 일반화. 사전훈련 representation을 CellCharter pipeline에 입력으로 쓸 수도 있음.
- [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] :builds_on — Tangram이 sc 정보를 spatial로 가져온 후 CellCharter로 niche 분석 가능.
- - [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/birk-2025-nichecompass-a-method-for-the]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]] — 같은 카테고리 (spatial foundation)
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 패밀리 (Nicheformer가 확장)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **Niche** — 공간적으로 모인 세포 공동체; 조직 기능 단위.
- **CODEX / IMC** — multiplex 단백질 imaging (수십~수백 마커).
- **MERFISH / Visium / Slide-seq** — spatial transcriptomics 플랫폼.
- **VAE** — Variational Autoencoder.
- **GMM** — Gaussian Mixture Model.
- **FMI** — Fowlkes-Mallows Index; pairwise clustering 유사도.
- **ARI** — Adjusted Rand Index; clustering 정확도 표준 지표.
- **Asymmetric Neighborhood Enrichment** — 방향성 있는 인접 관계 (A 주위에 B vs B 주위에 A).
- **Shape descriptor** — curl / elongation / linearity / purity.
- **DLPFC** — Dorsolateral Prefrontal Cortex; Visium 벤치마크 데이터.
- **PALS** — Periarteriolar Lymphoid Sheath; 비장의 T-zone niche.
