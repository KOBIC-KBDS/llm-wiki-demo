---
title: "HEST-1k — H&E + ST 페어 1,229개 통합 데이터셋과 pathology foundation model 벤치마크"
authors: Jaume G, Doucet P, ..., Mahmood F
year: 2024
journal: NeurIPS Datasets & Benchmarks
source: jaume-2024-hest-1k-a-dataset-for.md
category: spatial-foundation
tags: [HEST-1k, dataset, benchmark, H&E, Visium, Xenium, foundation-model, CellViT, pathology]
---

## 요약

**HEST-1k**는 H&E whole-slide-image와 spatial transcriptomics를 paired로 가진 **1,229개 sample** 컬렉션이다. 153개 cohort, 26개 장기, 인간 + 마우스, 367개 cancer / 345개 healthy + 기타. 모든 sample은 (1) 통일된 pyramidal TIFF, (2) 자동 fiducial 기반 재정렬, (3) **CellViT로 76M개 핵 분할/분류**, (4) AnnData 형식 표현 매트릭스를 갖춘다. **HEST-Library**(Python)와 **HEST-Benchmark**(9개 patch-level gene expression 예측 과제)를 함께 공개하여, 11개 pathology foundation model을 직접 비교했다.

## 핵심 기여

- **규모 + 다양성** — 1,229 sample / 153 cohort / 26 장기 / 25 cancer type. 척수, 뇌, 유방, 장 등 폭넓게.
- **품질 통일** — Visium의 spot↔WSI fiducial 자동 재정렬, Xenium은 VALIS로 DAPI↔H&E fine registration. 1.15 μm/px 미만 저해상도 이미지는 제외.
- **2.1M expression-morphology 페어 + 76.4M nuclei** — 17.6M neoplastic / 21.5M stromal / 4.9M normal epithelial / 15.4M inflammatory / 76k necrotic.
- **HEST-Benchmark** — 9개 organ/cancer에서 patch(224x224 @ 20x)로부터 highly variable gene 발현을 예측. UNI / Virchow / GigaPath / CTransPath / Phikon 등 11개 인코더 평가.
- **활용 시나리오 3종** — (a) 병리 foundation model 벤치마킹, (b) expression-morphology 바이오마커 탐색, (c) 발현 guidance로 histology encoder fine-tune.

## 방법론 및 아키텍처

### 데이터 출처
- 10x Genomics, Mendeley, Spatial-Research, Zenodo, NCBI GEO, GitHub, Human Cell Atlas, BioStudies, HTAN, 내부 cohort.

### 통합 파이프라인
| 단계 | 방법 |
|---|---|
| 이미지 포맷 | OPENSLIDE 호환 pyramidal TIFF로 변환 |
| 조직 분할 | DeepLabV3 + ResNet50 fine-tune (pen mark/fiducial/artifact 학습) |
| Spot↔WSI 정렬 | Visium은 자동 fiducial detection; Xenium은 VALIS DAPI↔H&E |
| Patch 추출 | 각 spot 주변 224x224 px @ 20x |
| 핵 분할 | CellViT (PanNuke-trained); 5개 클래스 |
| Xenium 변환 | 55x55 μm pseudo-Visium spot으로 transcript pool |
| 발현 매트릭스 | scanpy AnnData (raw count, 정규화는 사용자 선택) |

### 메타데이터 표준
- 카테고리: healthy / cancer / tumor(non-cancer) / treated / genetically modified / pathological.
- Cancer 라벨: OncoTree (MSKCC). 유전자: ENSEMBL.
- 영상: 10x/20x/40x magnification, frozen vs FFPE.

### HEST-Benchmark
- 9개 task (8 cancer type, 9 organ).
- 224x224 패치에 대해 frozen encoder → linear/MLP head로 highly variable gene 발현 예측.
- 평가: gene별 Pearson correlation 평균.

## 결과

| 측면 | 수치 |
|---|---|
| 총 sample | 1,229 (153 cohort) |
| 종 | Human + Mouse |
| 장기 | 26개; Top: 척수 318, 뇌 211, 유방 125, 장 94, 피부 88 |
| Cancer | 367 sample / 25 type (OncoTree) |
| 기술별 | ST/Visium 49.0%, Visium HD 0.8%, Xenium 5.3%, 기타 |
| 발현-형태 페어 | 2.1M |
| 분할된 nuclei | 76.4M (neoplastic 17.6M / stromal 21.5M / normal epi 4.9M / inflammatory 15.4M) |

### Foundation model 벤치마크
- UNI / Virchow / GigaPath 같은 대형 pathology foundation model이 전반적으로 강세지만, 9개 task 모두에서 1위인 모델은 없음 → 다양성 있는 평가가 필요함을 입증.
- 기존 saturated 벤치마크(Gleason grading 등)에서는 보이지 않던 인코더 약점이 HEST에서 드러남.

## 활용 가이드

- **평가용**: 새 pathology foundation model을 만들면 HEST-Benchmark의 9 task로 일관 비교 가능.
- **다운스트림 학습**: H&E + ST를 contrastive하게 multimodal fine-tune (CONCH-style → expression-aware).
- **바이오마커 발굴**: nuclei feature ↔ gene expression 패턴을 cohort 가로질러 탐색.
- **사용 권장 도구**: HEST-Library(Python) + scanpy/AnnData. QUPATH로 nuclear annotation 시각화.

## 관련 논문

- [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]] :methodologically_uses — iSTAR 같은 H&E→ST super-resolution 모델을 평가하기 위한 표준 자원.
- [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] :supports — Tangram도 H&E를 옵션으로 사용; HEST의 paired data가 있으면 더 폭넓게 검증 가능.
- [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] :supports — Nicheformer는 transcriptomic-only foundation, HEST-1k는 image+transcript paired.
- [[medical-foundation/lu-2024-a-visual-language-foundation-model]] :methodologically_uses — CONCH 류 vision-language 모델이 expression-aware로 fine-tune되는 receiver.
- - [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/birk-2025-nichecompass-a-method-for-the]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/varrone-2024-cellcharter-reveals-spatial-cell-niches]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]] — 같은 카테고리 (spatial foundation)
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 패밀리 (Nicheformer가 확장)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **WSI** — Whole-Slide Image; H&E로 염색된 조직 슬라이드 전체를 디지털화한 거대 이미지.
- **Visium / Visium HD / Xenium** — 10x Genomics ST 플랫폼.
- **CellViT** — ViT 기반 nuclear instance segmentation + classification 모델.
- **PanNuke** — pan-cancer nuclei 분할 학습 데이터셋.
- **OncoTree** — MSKCC 종양 분류 온톨로지.
- **Fiducial** — Visium 슬라이드 모서리에 인쇄된 정렬 표식.
- **Pseudo-Visium** — Xenium의 transcript를 Visium 크기 spot으로 binning한 가상 데이터.
- **AnnData / scanpy** — Python 단일세포/공간 표현 표준.
- **DeepLabV3** — segmentation CNN; HEST에서 tissue/background 검출에 사용.
- **VALIS** — multi-modal slide registration 라이브러리.
- **FFPE / frozen** — Formalin-Fixed Paraffin-Embedded vs 동결 조직 처리법.
