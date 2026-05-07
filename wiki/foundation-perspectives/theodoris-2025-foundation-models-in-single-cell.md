---
title: "단일세포·네트워크 생물학 foundation model 벤치마킹 (Theodoris 2024 perspective)"
authors: Theodoris CV
year: 2024
journal: Quantitative Biology 12(4):335-338
source: theodoris-2025-foundation-models-in-single-cell.md
category: foundation-perspectives
tags: [foundation-model, network-biology, single-cell, benchmarking, perspective, Geneformer-author]
---

## 요약

Geneformer 저자 Theodoris가 쓴 짧지만 날카로운 perspective. 단일세포·네트워크 생물학용 foundation model이 폭증(Geneformer, scGPT, scFoundation, scBERT, GeneCompass, UCE, scMulan, Nicheformer, Evo, HyenaDNA …)하는 상황에서, **벤치마크가 모델을 어디로 끌고 가는지가 결정적**이라고 주장. 잘못된 벤치마크(셀타입 클러스터링·UMAP·실루엣 점수·문헌 메모리 회상)에 최적화하면 *과학적으로 무의미한 SOTA*만 만든다. 8가지 원칙을 제시하고, 닫힌 고리(closed-loop) 데이터 생성-평가 협업을 촉구.

## 핵심 주장

- **벤치마크는 모델 진화의 방향타**: 무엇을 잘하는 모델을 보상할지가 곧 우리가 어디로 가는지.
- **"전처리 단계" 벤치마크는 함정**: 셀타입 라벨링·배치 통합 자체는 우리가 답하고 싶은 생물학 질문이 아니다. 종착점이 아니라 디딤돌.
- **2D 시각화는 평가 도구가 아니다**: UMAP/t-SNE는 가장 대조적인 층(셀타입)을 강조해 미묘한 생물학(질병 상태, 나이)을 가린다. 평가는 고차원에서.
- **사전학습 손실 ↘ ≠ 다운스트림 성능 ↗**: masked-LM 손실은 모델링 유전자 수를 줄이면 떨어지지만 그건 생물학을 좁히는 짓.
- **Ground truth를 의심하라**: 셀타입 라벨이 prior 선형 방법에서 왔다면, 그 라벨 회상은 그 prior를 복제할 뿐이다.
- **K562·말초혈만 잘하는 모델은 의미 없다**: 데이터 가용성에 휘둘리지 말고 다양한 맥락에서 generalize하는지 평가.
- **Emergent capability를 노려라**: 대형 모델이 갑자기 잘하기 시작하는 task를 벤치마크에 포함.
- **문헌 외부 held-out + 실험적 검증**: prior knowledge 회상은 점수만 부풀린다. 진짜 신규 발견 능력 측정.
- **데이터 생성 의제**: 미래 scRNA-seq 데이터는 *AI 학습을 위해* 균형있게 만들어야 — iPSC 다양 셀타입, 시계열, 소아·미연구 시스템.

## 분석 프레임워크

### Theodoris 8원칙 정리

| # | 원칙 | 막으려는 실패 모드 | 운영 방법 |
|---|---|---|---|
| 1 | 생물학적으로 의미 있는 task | UMAP 미감 최적화 | 핵심 조절자 예측, 상태 전환 유전자 예측 |
| 2 | 고차원 평가 | 2D 붕괴로 미세 신호 손실 | 질병/나이 기준 silhouette, 셀타입 기준 X |
| 3 | 사전학습 loss와 다운스트림 분리 | masked-LM 게이밍 | 두 지표 동시 보고, 발산 인정 |
| 4 | Ground truth 출처 점검 | 선형 도구 복제 | 라벨 provenance를 평가의 일부로 |
| 5 | 일반화성 보장 | K562/PBMC 과적합 | tissue·species 교차 held-out |
| 6 | 충분히 어려운 task | 벤치마크 saturation | emergent capability 프로브 포함 |
| 7 | 다양한 task 묶음 | 단일 패러다임 과적합 | 분류 + 생성 + 생물학적 feasibility |
| 8 | 원칙 있는 HP 튜닝 + split | 불공정 비교 | 동일 trial 예산, study 단위 split |

### Theodoris가 명시적으로 우려하는 함정

```
잘못된 벤치마크:        →  잘못된 모델:
- 셀타입 클러스터링        - K562 전문가
- UMAP 미감                 - 선형 방법 흉내
- 사전학습 loss             - 좁힌 유전자 집합
- 문헌 회상                  - 메모리 머신
```

### 좋은 벤치마크의 운영적 정의

- **Biologically meaningful**: 답이 새 가설이거나 실험으로 검증 가능한가?
- **Generalizable**: tissue·species·cohort 넘어서도 작동하는가?
- **Discriminative AND generative**: 분류만 잘하는가? 생성도 물리적으로 가능한가?
- **Hard enough**: 단순 baseline이 random인 task가 포함되었는가?
- **Fair**: 모델별 HP 튜닝 예산이 같은가? split이 study 단위인가?

## 결론 및 가이드라인

- **이 논문이 흐름에서 차지하는 위치 (subdomain perspective + benchmarking)**: Bommasani 2021·Wang 2023의 일반론을 **단일세포·네트워크 생물학으로 좁히면서 평가 방법론에 집중**한 글. Cui 2024 survey가 모델 enumeration이라면, 이 논문은 *어떻게 비교할 것인가*에 답한다.
- **읽을 때 우선순위**: 4쪽짜리니 통독 추천. 8원칙(§"Key points") + 마지막 데이터 생성 의제.
- **운영 가이드라인 함의 (본 위키 운영자에게)**:
  - 본 위키에 single-cell foundation model 비교 페이지를 만들 때, *각 모델이 셀타입 라벨링만으로 평가되었는지*를 1차 점검.
  - "SOTA on Liu 2023 benchmark" 같은 문구는 그 자체로 신뢰성 보증이 아님 — 어떤 task에서, 어떤 split으로, 어떤 ground truth로인지 함께 기록.
  - 본 위키의 [[boiarsky-2025-sctab-benchmark-for-single-cell]] (scTab), [[kedzierska-2025-assessing-the-limits-of-zero]] (zero-shot 한계) 같은 벤치마크 논문을 이 perspective의 후속 실증으로 묶어 정리.

## 관련 논문

- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] :builds_on — foundation model 일반 정의의 상위.
- [[foundation-perspectives/wang-2023-scientific-discovery-in-the-age]] :extends — Wang의 과학 일반론을 네트워크 생물학으로 좁힘.
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] :builds_on — Geneformer 본체.
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] :parallel_in_other_disease — scGPT generative single-cell foundation model.
- [[single-cell-foundation/hao-2024-large-scale-foundation-model-on]] :parallel_in_other_disease — scFoundation 대규모 single-cell 모델.
- [[single-cell-foundation/heimberg-2024-a-cell-atlas-foundation-model]] :supports — atlas search/retrieval 지향 cell representation.
- [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] :extends — single-cell과 spatial omics를 연결.
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] :parallel_in_other_disease — genome-scale foundation model 흐름.
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] :parallel_in_other_disease — long-context DNA 모델 흐름.
- [[single-cell-foundation/boiarsky-2025-sctab-benchmark-for-single-cell]] :supports — supervised scTab benchmark.
- [[single-cell-foundation/kedzierska-2025-assessing-the-limits-of-zero]] :supports — zero-shot single-cell foundation model 한계 평가.

## 용어집

- **Foundation model for network biology**: 유전자·세포 상태를 모델링하는 사전학습 모델, 다양한 다운스트림 task로 전이.
- **Network biology**: 조절·신호·대사 네트워크를 세포 상태별로 모델링.
- **Zero-shot**: fine-tuning 없이 사전학습 모델을 직접 사용.
- **Silhouette score**: 클러스터링 평가; 셀타입 대비를 과대평가하는 함정 가능.
- **Emergent capability**: 모델 규모에서 갑자기 나타나는 능력; 벤치마크가 이를 측정해야.
- **Ground truth**: 벤치마크 정답; provenance 자체가 평가의 일부.
- **Held-out from literature**: 문헌과 분리된 새 데이터로 진짜 발견 능력 측정.
- **K562**: 자주 쓰이는 immortalized 세포주; over-representation 경고 대상.
- **iPSC**: 유도만능줄기세포; 균형잡힌 학습 데이터의 후보 출처.
