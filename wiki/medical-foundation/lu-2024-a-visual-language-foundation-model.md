---
title: "Lu 2024 — CONCH: 계산병리용 시각-언어 파운데이션 모델"
authors: Ming Y. Lu, Bowen Chen, Drew F. K. Williamson, ..., Faisal Mahmood
year: 2024
journal: Nature Medicine 30, 863–874
source: lu-2024-a-visual-language-foundation-model.md
category: medical-foundation
tags: [CONCH, computational-pathology, visual-language, foundation-model, CoCa, WSI, zero-shot, contrastive-learning, MI-Zero, ABMIL]
---

## 요약

**CONCH (CONtrastive learning from Captions for Histopathology)** — Faisal Mahmood 그룹(BWH/Harvard)이 1.17 M 인간 조직 image–caption 쌍으로 훈련한 CoCa 기반 visual-language pathology foundation model. 14개 다양한 벤치마크에서 PLIP, BiomedCLIP, OpenAICLIP을 큰 폭으로 추월. WSI 단위 zero-shot NSCLC subtyping 90.7%, RCC 90.2%. EBRAINS 30-class에서 frozen encoder + ABMIL로 68.2% (CTransPath 대비 +6.8). zero-shot이 64-shot supervised baseline을 자주 능가.

## 핵심 기여

- **117만 인간 조직 image–caption 코퍼스** — PMC Open Access(PMC-Path) + 교육용 자료(EDU)에서 자동 cleaning 파이프라인으로 1.79 M 추출 → 인간 한정 1.17 M 필터. 26개 장기계 커버, H&E 457k + IHC/special stain 713k.
- **자동 큐레이션 파이프라인 3컴포넌트** — (1) histopathology 이미지 검출기, (2) "(A) ... (B) ..." 형태 caption splitter LM, (3) 이미지-자막 matcher.
- **CoCa 기반 CONCH** — image encoder + text encoder + multimodal fusion decoder. **contrastive alignment + captioning** dual objective.
- **14 벤치마크 SOTA** — WSI subtyping (TCGA BRCA/NSCLC/RCC, DHMC LUAD), ROI 분류 (CRC100k, WSSS4LUAD, SICAP), retrieval (Source A/B/TCGA LUAD), zero-shot segmentation (SICAP, DigestPath), captioning (TCGA LUAD).
- **Frozen encoder로도 강함** — EBRAINS 30-class ABMIL: CONCH 68.2% vs CTransPath 61.4% (+6.8, p<0.01), PLIP 57.5% (+10.7), BiomedCLIP 53.8% (+14.4), OpenAICLIP 50.4% (+17.8).
- **레이블 효율성** — TCGA BRCA/NSCLC에서 CONCH zero-shot이 다른 모델들의 64-label-per-class supervised 성능을 자주 추월.

## 방법론 및 아키텍처

| 요소 | 내용 |
|---|---|
| 백본 | **CoCa** (Yu et al. 2022) |
| 구성 | Image encoder + Text encoder + Multimodal fusion decoder |
| 학습 손실 | Contrastive alignment (cosine sim) + Captioning (next-token) |
| 데이터 | 1.17 M (인간만) image–caption pairs |
| 데이터 출처 | PMC OA (PMC-Path) + 교육 자료 (EDU) |
| Cleaning | Object detector → Caption splitter → Matcher → Human-only filter |
| Zero-shot 분류 | text prompt ensemble + cosine sim argmax |
| Zero-shot WSI | **MI-Zero** — tile별 prompt score → slide aggregation |
| Few-shot supervised | frozen CONCH 피처 + ABMIL 헤드 |
| 평가 | 14 벤치마크 / 분류·retrieval·segmentation·captioning |

## 결과

### Zero-shot WSI subtyping

| Task | CONCH | PLIP | Δ |
|---|---|---|---|
| TCGA NSCLC | **90.7%** | 78.7% | +12.0 |
| TCGA RCC | **90.2%** | 80.4% | +9.8 |
| TCGA BRCA | (Fig 2c) | (보다 낮음) | wide margin |
| EBRAINS 30-class (zero-shot) | **37.1%** | ~20% | +17.0 vs BiomedCLIP |

### Frozen encoder + ABMIL (EBRAINS 30-class)

| Encoder | Balanced acc |
|---|---|
| **CONCH** | **68.2%** |
| CTransPath | 61.4% |
| PLIP | 57.5% |
| BiomedCLIP | 53.8% |
| OpenAICLIP | 50.4% |

### 추가 지표
- Cross-modal retrieval Recall@1/5/10에서 모든 dataset/방향에서 CONCH 우위.
- Zero-shot segmentation (SICAP Gleason, DigestPath) 큰 폭 SOTA.
- TCGA LUAD captioning에서 SOTA.
- Few-shot 설정에서 다른 visual-language 모델들이 CONCH의 zero-shot을 따라잡으려면 ~4배의 라벨 필요.

## 임상적 함의

- **Pathology DL의 foundation-model 단계** — Topol(2019)이 정리한 "병리는 디지털화가 늦다"는 진단을 5년 만에 visual-language foundation model로 추월. 이제는 1개 모델이 14개 task에 활용 가능.
- **희귀 질환 대응** — EBRAINS 30-class 같은 long-tail 질환은 zero-shot 37%로 여전히 임상 부족. 그러나 frozen encoder + 약한 supervision (68.2%)으로 실용 가능.
- **데이터 큐레이션 아키텍처** — PMC OA + 교과서 + 자동 splitter라는 모듈은 다른 의료 분야(radiology, dermatology)에 그대로 이식 가능한 청사진.
- **Closed model이지만 frozen 사용 가능** — Med-PaLM/Med-Gemini와 달리 CONCH는 frozen encoder를 다운스트림에 결합하기 쉬움. 한국 임상 데이터에 zero-shot부터 시도 후 약지도 학습으로 전환하는 점진적 도입 경로 제시.
- **임상 검증 부재** — prospective deployment 연구는 없음. 본 논문은 retrospective benchmark만. 한국 식약처 또는 KFDA 인증을 위해서는 자국 코호트의 prospective trial 필요.

## 관련 논문

- [[medical-foundation/topol-2019-high-performance-medicine-the-convergence]] :extends — Topol이 정리한 pathology DL 라인의 visual-language foundation model 단계.
- [[medical-foundation/saab-2024-capabilities-of-gemini-models-in]] :supports — Med-Gemini가 Path-VQA 등에서 멀티모달 SOTA를 갱신하지만, CONCH는 pathology specialist로서 14 task 폭이 더 넓음. 두 접근(generalist vs specialist)의 대비.
- [[medical-foundation/singhal-2023-towards-expert-level-medical-question]] :supports — Med-PaLM은 텍스트 임상 지식, CONCH는 시각 병리 지식. 결합 시 multimodal pathology QA 가능성.
- [[medical-foundation/sellergren-2025-medgemma-open-medical-foundation-models]] :supports — MedGemma/BioGPT는 NLP workflow, CONCH는 vision foundation. 통합 임상 시스템에서 합쳐질 두 축.
- [[medical-foundation/saab-2024-capabilities-of-gemini-models-in]] — 같은 카테고리 (medical foundation)
- [[medical-foundation/sellergren-2025-medgemma-open-medical-foundation-models]] — 같은 카테고리 (medical foundation)
- [[medical-foundation/singhal-2023-towards-expert-level-medical-question]] — 같은 카테고리 (medical foundation)
- [[medical-foundation/topol-2019-high-performance-medicine-the-convergence]] — 같은 카테고리 (medical foundation)
- [[foundation-perspectives/moor-2023-foundation-models-for-generalist-medical]] — GMAI perspective anchor
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **WSI (Whole-Slide Image)** — gigapixel 단위로 스캔된 병리 슬라이드 전체. 계산병리의 기본 입력.
- **H&E** — Hematoxylin & Eosin 염색. 병리의 기본 염색법.
- **IHC** — Immunohistochemistry. 항체 기반 단백질 특이 염색.
- **CoCa** — Contrastive Captioners (Yu et al. 2022). Contrastive + captioning 이중 목적의 비전-언어 모델 아키텍처. CONCH의 백본.
- **CLIP / ALIGN** — OpenAI / Google의 대표 contrastive 비전-언어 모델.
- **PLIP / BiomedCLIP / OpenAICLIP** — pathology / 생의학 영상용 대표 visual-language 베이스라인.
- **Zero-shot classification** — task-specific 학습 없이 prompt 임베딩과의 유사도로 분류.
- **MI-Zero** — Multiple Instance Zero-shot. tile별 점수를 슬라이드 점수로 집계하는 CONCH 그룹 방식.
- **ROI / tile** — WSI에서 잘라낸 region-of-interest. 인코더의 실제 입력 단위.
- **ABMIL** — Attention-Based Multiple-Instance Learning. 약지도 학습으로 tile feature를 슬라이드 라벨로 매핑.
- **EBRAINS** — 30-class 뇌종양 병리 벤치마크. open-set / 희귀 질환 평가.
- **TCGA** — The Cancer Genome Atlas. 대규모 범암종 병리 + 다중오믹스 코호트.
- **Recall@K** — 검색 메트릭. top-K 안에 정답이 있는 query 비율.
- **Cohen's κ / quadratic-weighted κ** — 주관적 등급 task (LUAD pattern, Gleason)에 사용되는 동의도 메트릭.
