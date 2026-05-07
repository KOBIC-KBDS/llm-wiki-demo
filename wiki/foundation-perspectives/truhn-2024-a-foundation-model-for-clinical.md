---
title: "UNI — 임상 병리 H&E 영상의 범용 Foundation Model (Chen 2024, Nat Med)"
authors: Chen RJ, Ding T, Lu MY, Williamson DFK, ..., Mahmood F*
year: 2024
journal: Nature Medicine 30:850-862
source: truhn-2024-a-foundation-model-for-clinical.md
category: foundation-perspectives
tags: [computational-pathology, foundation-model, vision-transformer, DINOv2, WSI, OncoTree, clinical]
---

> **파일명 주의**: stem은 `truhn-2024-a-foundation-model-for-clinical`이지만, 실제 보유 PDF는 Chen et al. 2024 UNI(Nature Medicine, DOI 10.1038/s41591-024-02857-3)입니다. 이 노트는 실제 PDF를 기준으로 정리했습니다. 엄격한 stem 일치를 원하면 운영자 검토 후 `chen-2024-...`로 rename하는 편이 맞습니다.

## 요약

UNI는 **Mass-100K**(MGH/BWH/GTEx 출처 100,426개 H&E WSI에서 추출한 1억 개 이상 타일, 20개 장기)에 **DINOv2** 자기지도 학습으로 사전훈련된 ViT-Large 영상 인코더다. **34개 임상 병리 task**(ROI 분류·분할·검색·슬라이드 단위 분류 — 특히 108-class OncoTree 범암 분류 OT-108 포함)에서 CTransPath, REMEDIS, ImageNet ResNet-50 모든 baseline을 능가. 데이터 규모(1K → 22K → 100K WSI)와 모델 크기(ViT-B → L) 두 축 모두에서 단조적 성능 향상, 즉 **CPath에서도 scaling law가 성립**함을 입증. 새 능력으로 *resolution-agnostic 분류*와 *few-shot class prototype prompting*을 보여준다.

> **파일명 주의**: 본 위키 stem이 `truhn-2024-...`이지만, 실제 PDF 내용은 Chen et al.의 UNI(Nat Med 2024) 논문이다. CLAUDE.md 규칙대로 실제 보유 자료를 그대로 정리.

## 핵심 주장

- **CPath도 데이터 규모가 성능을 결정한다**: Mass-1K → 22K → 100K로 갈수록 OT-43/OT-108 정확도 단조 증가. ImageNet/TCGA만으로는 부족하다.
- **DINOv2 > MoCoV3** (동일 데이터 규모에서). 알고리즘 선택이 결과를 흔든다.
- **단일 인코더가 34개 task를 커버**: ROI 분류·분할·검색·슬라이드 분류·OncoTree 범암 — task별 모델을 만들 필요 없음.
- **희귀암(rare cancer) 분류 가능**: OT-108의 108개 종양 코드 중 90개가 RARECARE/SEER 정의 희귀암. WSI당 20장만 있으면 분류 가능 — 데이터 부족 임상 시나리오의 답.
- **새 능력 — resolution-agnostic 분류 & few-shot 프로토타입**: in-context learning의 영상판. 슬라이드 몇 장으로 새 분류 task 즉시 가능.
- **TCGA의 한계 노출**: 기존 CPath FM(CTransPath, REMEDIS)이 TCGA(~29K WSI, 주로 primary cancer)에 머물러 있던 한계를 Mass-100K(다양 organ + 정상 GTEx 포함)로 넘어섬.

## 분석 프레임워크

### CPath foundation model 비교

| 모델 | 사전학습 데이터 | 백본 | 알고리즘 | 본 논문 OT-43 top-5 / AUROC |
|---|---|---|---|---|
| ResNet-50 (ImageNet) | ImageNet-1K | ResNet-50 | supervised | 76.5% / 0.862 |
| CTransPath | TCGA + PAIP | Swin-T | MoCoV3 변형 | 87.5% / 0.954 |
| REMEDIS | TCGA | ResNet-152x2 | SimCLR + supervised | 87.5% / 0.956 |
| **UNI (this paper)** | **Mass-100K (100K WSI, 20 organs)** | **ViT-L** | **DINOv2** | **93.8% / 0.976** |

### Scaling laws (Mass-100K subsetting)

| 데이터 | ViT-L OT-43 top-1 변화 |
|---|---|
| Mass-1K (1K WSI, 1M 타일) | baseline |
| Mass-22K (21K WSI, 16M 타일) | +4.2% |
| Mass-100K (100K WSI, 100M 타일) | +3.7% (총 +7.9%) |

→ "더 많은 데이터 + 더 큰 모델" 공식이 자연영상에서 의료영상으로 이전됨.

### UNI 평가 task 34개 카테고리

| 평가 패러다임 | 대표 task | 데이터셋 |
|---|---|---|
| ROI 분류 | CRC tissue class, BRCA subtyping, NSCLC subtyping, prostate ISUP grading | UniToPatho, BACH, TCGA+, PANDA |
| ROI 분할 | nuclear segmentation, pan-cancer seg | SegPath |
| 검색·prototyping | image retrieval, few-shot class prototypes | 다수 |
| Slide-level (MIL) | breast metastasis detection, **OT-43**, **OT-108**, glioma IDH1, brain histomolecular | CAMELYON16, C17-WILDS, OT, TCGA+, EBRAINS |

### GMAI 청사진과의 연결 (Moor 2023)

| GMAI 능력 (Moor 2023) | UNI에서의 구현 정도 |
|---|---|
| Dynamic task specification | 부분적 — few-shot 프로토타입(자연어 prompt 아님) |
| Multimodal I/O | 영상 단일(텍스트 결합은 후속 CONCH/Lu 2024 등) |
| Medical reasoning | 약 — 진단 결정에 추론보다 패턴 인식 |
| Validation across tasks | ✅ 34 task 입증 |

→ UNI는 GMAI 청사진의 **시각 인코더 backbone** 위치. 텍스트·EHR·multimodal 결합은 본 위키의 [[lu-2024-a-visual-language-foundation-model]], [[saab-2024-capabilities-of-gemini-models-in]], [[sellergren-2025-medgemma-open-medical-foundation-models]]가 분담.

## 결론 및 가이드라인

- **이 논문이 흐름에서 차지하는 위치 (clinical-pathology vertical)**: Bommasani 2021(framework) → Moor 2023(GMAI 청사진) → **UNI(병리 영상 vertical 구체화)**. Truhn 등이 그렸을 임상 병리 FM 청사진의 구체적 첫 사례.
- **읽을 때 우선순위**: Fig. 1(개요), §"Pretraining scaling laws in CPath"(데이터/모델 크기 영향), Fig. 2(34 task 비교), 후반부 OT-43/OT-108 결과. Methods 부분은 DINOv2 알고리즘 따라가기 좋음.
- **운영 가이드라인**:
  - 본 위키의 다른 의료 FM(CONCH, MedGemma, Med-Gemini, HEST-1K)을 정리할 때, *데이터 규모 / 모달리티 / DINOv2-like vs CLIP-like 알고리즘 / 평가 task 수* 4축으로 비교 정리하면 흐름이 잡힌다.
  - 새 CPath 모델이 발표되면 OT-43/OT-108 같은 다중 클래스/희귀암 task를 평가에 포함했는지 점검 — UNI 이전엔 이런 평가가 거의 없었다.
  - "Mass-100K 같은 in-house 임상 슬라이드 + 다양 organ + 정상 조직"을 사전학습 표준으로 보는 시각이 강해질 것.

## 관련 논문

- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] :builds_on — foundation model 정의의 의료 영상 적용.
- [[foundation-perspectives/moor-2023-foundation-models-for-generalist-medical]] :extends — GMAI 청사진의 vision-encoder 구성요소.
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] :methodologically_uses — ViT는 Transformer를 영상 패치에 적용한 것.
- [[medical-foundation/lu-2024-a-visual-language-foundation-model]] :parallel_in_other_disease — CONCH가 병리 vision-language 축을 보완.
- [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]] :provides_data_for — H&E + spatial transcriptomics paired dataset과 benchmark 기반.
- [[medical-foundation/sellergren-2025-medgemma-open-medical-foundation-models]] :extends — open medical foundation model 흐름과 연결.
- [[medical-foundation/saab-2024-capabilities-of-gemini-models-in]] :extends — 의료 generalist/multimodal reasoning 흐름과 연결.

## 용어집

- **CPath**: computational pathology (계산 병리학).
- **WSI**: whole-slide image (병리 슬라이드 전체 디지털 스캔, 기가픽셀).
- **H&E**: hematoxylin & eosin 염색 — 표준 조직 염색.
- **ViT**: Vision Transformer — 영상 패치를 토큰으로 한 Transformer.
- **DINOv2**: 자기지도 학습 알고리즘(masked-image modeling + self-distillation).
- **MIL / ABMIL**: Multiple Instance Learning (가방 단위 라벨로 instance 학습) / attention-based 변형.
- **OncoTree**: 계층적 암 분류 체계(43/108 코드).
- **TCGA**: The Cancer Genome Atlas; ~29K WSI, 32 cancer.
- **CAMELYON16 / C17-WILDS / PANDA / BACH / BRACS / EBRAINS / SegPath**: 병리 벤치마크 데이터셋.
- **Few-shot prototype**: 소수 라벨 예시의 평균 feature를 클래스 프로토타입으로 사용하는 분류.
- **Linear probe / k-NN**: 인코더 freeze 상태에서 feature 품질만 측정하는 평가법.
- **RARECARE / SEER**: 희귀암 정의 사용 기관(EU / NCI).

---

## 위키 운영자 메모

> 이 stem은 본래 Truhn 2024의 임상 병리 foundation model review 자리로 예정되었으나, 현재 papers/ 폴더의 PDF는 Chen et al. 2024 UNI 논문(Nat Med, DOI 10.1038/s41591-024-02857-3)이다. 위 문서는 실제 보유한 PDF의 내용 그대로다. Truhn review가 추후 입수되면 stem `chen-2024-towards-a-general-purpose-foundation`로 재명명하고 별도 source/wiki 분리 권장.
