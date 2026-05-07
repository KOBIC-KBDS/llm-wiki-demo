---
title: "Nicheformer — sc + spatial 통합 transformer foundation model"
authors: Schaar AC, Tejada-Lapuerta A, ..., Theis FJ
year: 2025
journal: Nature Methods
source: schaar-2025-nicheformer-a-foundation-model-for.md
category: spatial-foundation
tags: [Nicheformer, foundation-model, transformer, MERFISH, Xenium, CosMx, ISS, niche, cross-modal, gene-rank]
---

## 요약

**Nicheformer**는 dissociated single-cell + image-based spatial transcriptomics를 함께 학습한 **49M-parameter transformer foundation model**이다. 인간/마우스 73개 조직, **57M dissociated + 53M spatial cell** = SpatialCorpus-110M으로 사전훈련. **gene-rank tokenizer**(Geneformer 계열) + 기술/종/모달리티 context token이 핵심. fine-tuning 또는 frozen embedding + linear probing만으로도, MERFISH/Xenium/CosMx에서 **niche / region / cell density / composition 예측** task에서 scVI / PCA baseline 대비 일관 우위. 학습된 spatial 라벨을 **dissociated scRNA-seq에 전이**할 수 있다.

## 핵심 기여

- **SpatialCorpus-110M** — 현재까지 최대 규모 spatial+sc 통합 corpus. spatial 53.83M cell은 MERFISH/Xenium/CosMx/ISS 4개 기술 합산. 뇌가 60.46%로 다수, 폐 9.95% 다음. cancer sample 32%.
- **Cross-technology gene-rank tokenization** — 기술별 non-zero mean으로 정규화 후 발현 순위로 토큰화. 기술-bias 흡수.
- **Context tokens (modality / species / assay)** — 같은 gene이라도 dissociated vs MERFISH vs Xenium에서 다르게 해석.
- **Spatial-specific downstream tasks** — niche / region / density / composition 등 sc foundation model에는 없던 평가축 신설.
- **Spatial → dissociated transfer** — 사전훈련된 모델이 scRNA-seq에 spatial 라벨을 부여 (uncertainty 포함).
- **Modality ablation** — dissociated only로 pretrain하면 spatial annotation 성능 저하 → joint pretrain 정당화.

## 방법론 및 아키텍처

### Tokenizer
1. gene symbol → ENSEMBL → 16,981 ortholog + 151 mouse-only + 3,178 human-only = vocab 20,310.
2. 기술별 non-zero mean vector로 발현 정규화.
3. 각 cell을 발현 순위 기반 gene-token sequence로 변환.
4. modality / organism / assay context token prepend.
5. Context length 1,500.

### Model
| Hyper | 값 |
|---|---|
| Encoder layers | 12 |
| Attention heads | 16 |
| FF size | 1,024 |
| Embedding dim | 512 |
| Total params | 49.3M |
| Objective | masked-token prediction (BERT-style) |

### Downstream task 분류
| Task | 데이터 |
|---|---|
| Cell type / niche / region prediction | MERFISH mouse brain (Yao 2023), CCFv3 17 region |
| Cell density regression | Xenium lung/colon |
| Cell type composition | MERFISH/CosMx/Xenium 다중 |
| Spatial → dissociated transfer | mouse motor cortex scRNA-seq |

### 평가 모드
- **Linear probing**: frozen embedding + 1-layer head. zero-shot-like.
- **Fine-tuning**: 전체 backbone 업데이트.

## 결과

- Mouse brain MERFISH 7 niche / 17 region prediction: fine-tuned Nicheformer > linear probing Nicheformer > scVI/PCA baseline (F1-macro).
- Cross-modality 전이: motor cortex scRNA-seq에 niche/region 라벨 부여; 33개 type 중 9개를 정확히 해당 niche로 매핑. 경계 영역(L2/3 IT vs subpallium GABA)은 uncertainty 높게 표시.
- Modality ablation: dissociated only pretrain은 spatial annotation 성능 떨어짐 → joint pretraining 필수.
- Density/composition도 PCA/scVI보다 일관 우위.

## 활용 가이드

- **있는 그대로 쓰기**: spatial cell의 niche/region prediction → frozen embedding + linear head로 충분히 강력.
- **scRNA-seq 풍부화**: 보유 dissociated atlas에 spatial niche 라벨을 자동 부여 → 미세환경 정보 보강.
- **fine-tune 권장 케이스**: novel tissue/disease (예: 종양 미세환경)에서 task-specific 라벨 학습 시 fine-tune 효과 큼.
- **주의점**:
  - 1,500 token 제한 → 매우 다양한 발현을 가진 cell은 truncation.
  - Visium HD / Stereo-seq 같은 whole-transcriptome ST는 미포함 → 전 transcriptome spatial pretraining은 향후 과제.
  - Spatial graph attention이 transformer 내부에 없음 → 이웃 정보는 context token + downstream 라벨로 들어감.

## 관련 논문

- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] :builds_on — scGPT 등 sc foundation model을 spatial로 확장한 접근.
- [[single-cell-foundation/wen-2024-cellplm-pre-training-of-cell]] :supports — CellPLM도 일부 spatial 포함 (9M sc + 2M spatial). Nicheformer는 53M spatial로 훨씬 큼.
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] :builds_on — Geneformer의 gene-rank tokenizer.
- [[spatial-foundation/birk-2025-nichecompass-a-method-for-the]] :supports — NicheCompass는 graph-based spatial niche, Nicheformer는 transformer-based representation. 전자는 해석성, 후자는 일반성.
- [[spatial-foundation/varrone-2024-cellcharter-reveals-spatial-cell-niches]] :methodologically_uses — Nicheformer embedding을 CellCharter pipeline에 입력으로 쓰면 더 많은 cohort joint clustering 가능.
- [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] :builds_on — Tangram의 1대1 정렬을 사전훈련 representation으로 일반화한 흐름.
- - [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/birk-2025-nichecompass-a-method-for-the]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/varrone-2024-cellcharter-reveals-spatial-cell-niches]] — 같은 카테고리 (spatial foundation)
- [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]] — 같은 카테고리 (spatial foundation)
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 패밀리 (Nicheformer가 확장)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **Niche** — 작은 단위의 공간적 세포 공동체 (cell type 조성 + 미세환경).
- **Region** — 더 큰 해부학적 구역 (예: isocortex).
- **CCFv3** — Allen Common Coordinate Framework v3.
- **MERFISH / Xenium / CosMx / ISS** — image-based ST 4종.
- **Gene-rank tokenizer** — cell을 발현 순위로 정렬한 gene token sequence로 표현.
- **Linear probing** — frozen embedding + 단일 layer classifier로 평가.
- **Fine-tuning** — 전체 backbone 업데이트.
- **Foundation model** — 대규모 pretrain 후 다양한 downstream에 적응 가능한 모델.
- **scVI** — VAE 기반 sc embedding; baseline.
- **F1-macro** — class imbalance에 강인한 macro-averaged F1.
- **Cross-modality transfer** — spatial로 학습한 라벨을 dissociated scRNA-seq에 부여.
