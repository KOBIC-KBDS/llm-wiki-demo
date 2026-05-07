---
title: "iSTAR — H&E와 ST를 결합한 super-resolution gene expression 예측"
authors: Zhang D, Schroeder A, ..., Wang L, Li M
year: 2024
journal: Nature Biotechnology 42(9):1372-1377
source: zhang-2024-inferring-super-resolution-tissue-architecture.md
category: spatial-foundation
tags: [iSTAR, super-resolution, H&E, histology, Visium, Xenium, HViT, weakly-supervised]
---

## 요약

**iSTAR** (Inferring Super-resolution Tissue ARchitecture)는 H&E 조직학 이미지에 대해 **사전훈련된 hierarchical Vision Transformer(HViT)** 와 **약지도 학습(weakly-supervised)** feed-forward MLP을 결합하여, **Visium spot 수준의 ST 데이터를 단일세포에 가까운 해상도(8×8 μm)로 super-resolution** 한다. 같은 환자의 인접 절편처럼 H&E만 있는 조직에서도 gene expression을 예측할 수 있고, 기존 SOTA였던 XFuse 대비 약 **200배 빠르다**.

## 핵심 기여

- **계층적 H&E 특징 추출** — TCGA에서 DINO로 사전훈련된 HViT로 16×16 px(국소 세포 수준) + 256×256 px(전역 조직 구조)을 동시에 인코딩. iSTAR는 frozen으로 사용 (효율).
- **약지도 super-resolution** — Visium spot의 발현량을 "내부 superpixel들의 합"으로 모델링하여, fine resolution 라벨 없이도 8x~128x 해상도 향상.
- **H&E-only out-of-sample 예측** — 한 절편은 ST + H&E로 학습, 인접 절편은 H&E만으로 예측 가능. Image registration 없이 두 이미지를 concat해서 처리.
- **속도** — Xenium-pseudo-Visium 분석 9분 vs XFuse 1969분 (~33시간). 동일 RTX 2080 Ti.
- **임상적 가치** — HER2+ 유방암에서 pathologist가 놓친 surgical margin, TLS(tertiary lymphoid structure)를 고해상도로 자동 검출.

## 방법론 및 아키텍처

### 1) Histology feature extractor (frozen HViT)
- 0.5 μm/px로 리스케일 → 16x16 px ≈ 8x8 μm² ≈ 단일세포.
- Local ViT (16x16 → 192d) → local aggregator (256x256 → 384d) → global ViT.
- DINO + TCGA pretraining (Chen 2022 HIPT 가중치 사용).
- 출력: superpixel feature (C1=192 + C2=384 + RGB=3 채널).

### 2) Super-resolution gene predictor
- 4-layer MLP (256 hidden, leaky-ReLU, ELU output → 비음수 보장).
- Loss: spot 단위 관측 발현량 = 그 spot 내 superpixel 예측의 합 (MSE).
- Top 1000 HVG + 사용자 marker 패널만 예측 (sparse 유전자는 노이즈가 커서 제외).

### 3) Tissue annotator
- MLP의 second-last layer를 embedding으로 사용 → Gaussian smoothing → k-means.
- Cell-type score: 표준화된 marker 평균; threshold 0.1.
- TLS 같은 사용자 정의 구조: marker 패널 평균 score image.

### 평가 셋업
- Xenium(서브셀룰러 ground truth)을 55 μm hexagonal spot으로 binning → pseudo-Visium.
- 평가지표: RMSE + SSIM. (PCC는 high-resolution noisy 신호에서 이상값에 민감해 부적합.)

## 결과

| 데이터셋 | 결과 |
|---|---|
| Xenium breast cancer (313 genes) | 8x~128x 모든 스케일, in-sample/out-of-sample 모두에서 거의 모든 유전자에서 XFuse 능가 |
| HER2ST (legacy ST) | 매뉴얼 어노테이션과 일치하는 segmentation; XFuse가 분리하지 못한 DCIS #2를 invasive cancer와 분리 |
| HER2ST Subject H | Pathologist가 원 논문에서 놓친 positive surgical margin을 iSTAR가 검출 |
| HER2ST Subject G | 다수 TLS를 고해상도로 검출, pathologist 검증 |
| Mouse brain Xenium pseudo-Visium | Allen Brain Atlas annotation과 일치하는 fine-grained segmentation |
| Visium 기타 (breast/colorectal/prostate/kidney cancer, mouse brain/kidney) | 일관된 high-resolution architecture 회수 |

## 활용 가이드

- **언제 쓰면 좋은가**:
  - Visium 데이터를 갖고 있고, 단일세포 해상도가 필요한 경우.
  - 같은 환자의 여러 인접 절편 중 일부만 ST이고 나머지는 H&E만 있을 때 → ST를 가상 확장.
  - 작은 면역 구조(TLS), 작은 cancer foci처럼 spot 해상도로 놓치기 쉬운 영역 검출.
- **주의점**:
  - HViT가 TCGA로 학습 → 비-종양/비-인간 tissue에서는 fine-tuning 고려.
  - 기본적으로 top 1000 HVG만 예측 (전 전사체 기본 아님).
  - Single-cell 수준 발현은 외부 cell segmentation mask 필요.

## 관련 논문

- [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] :supports — Tangram은 sc reference로, iSTAR는 H&E feature로 spot보다 작은 해상도를 회복. 데이터 가용성에 따라 선택.
- [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]] :provides_data_for — H&E + ST 페어 1,229개. iSTAR 같은 모델을 다양한 조직에서 평가할 수 있는 표준.
- [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]] :supports — STAGATE는 발현 기반 spatial domain, iSTAR는 morphology 기반 super-resolution. 결합 가능성 큼.
- - [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/birk-2025-nichecompass-a-method-for-the]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/varrone-2024-cellcharter-reveals-spatial-cell-niches]] — 같은 카테고리 (spatial foundation)
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 패밀리 (Nicheformer가 확장)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **H&E** — Hematoxylin and Eosin; 표준 조직학 염색.
- **HViT** — Hierarchical Vision Transformer; 다중 스케일 ViT.
- **DINO** — self-distillation으로 ViT를 self-supervised 학습하는 방법.
- **Visium / Xenium** — 10x Genomics ST 플랫폼. Visium = 55 μm spot, transcriptome-wide. Xenium = subcellular ~313 genes.
- **Superpixel** — spot보다 작은 예측 단위 (8–64 μm²).
- **Weakly-supervised** — 훈련 라벨이 spot 단위(coarse)인데 예측은 superpixel 단위(fine).
- **TLS** — Tertiary Lymphoid Structure; 종양 내 면역 응집체, immunotherapy 반응 마커.
- **DCIS** — Ductal Carcinoma In Situ; 초기 유방암.
- **SSIM** — Structural Similarity Index Measure; 영상 유사도 지표.
- **Pseudo-Visium** — Xenium 같은 고해상도 데이터를 Visium 형태로 binning한 합성 ST.
