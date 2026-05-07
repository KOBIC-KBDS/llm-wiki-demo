---
title: "scRegNet — sc-Foundation Model + GNN으로 GRN 추론 (Kommu 2024, bioRxiv)"
authors: Kommu S, Wang Y (Yizhi), Wang Y (Yue), Wang X
year: 2024
journal: bioRxiv 2024.12.16.628715
source: cui-2024-a-survey-on-the-application.md
category: foundation-perspectives
tags: [single-cell, foundation-model, GRN, GNN, scBERT, Geneformer, scFoundation, application-survey]
---

> **파일명 주의**: stem은 `cui-2024-a-survey-on-the-application`이지만, 실제 보유 PDF는 Kommu et al. 2024 scRegNet(bioRxiv 2024.12.16.628715)입니다. 이 노트는 실제 PDF를 기준으로 정리했습니다. 엄격한 stem 일치를 원하면 운영자 검토 후 `kommu-2024-...`로 rename하는 편이 맞습니다.

## 요약

scRegNet은 사전학습된 single-cell foundation model(scBERT, Geneformer, scFoundation)의 임베딩과 prior GRN 그래프에서 학습한 GNN 임베딩을 **결합**해, GRN 추론을 link prediction 문제로 푸는 supervised 방법이다. BEELINE 벤치마크 7종(인간/마우스 다양 cell type)에서 9개 baseline을 모두 능가, **Geneformer 백본**이 가장 강력(+8% AUROC, +13–17% AUPRC over best graph baseline). 본 위키에서 이 논문은 sc-FM "**활용·비교 perspective**" 자리(Cui 2024 survey 위치)에 보관되어 있으며, 실제 PDF는 Kommu 2024 scRegNet 논문이다(파일명 stem mismatch 주석은 source 프론트매터 참조).

## 핵심 주장

- **두 정보원의 통합이 강력하다**: sc-FM은 *cell-type-specific gene context*를 기억하고, GNN은 *prior network topology*를 기억한다. 둘을 합치면 어느 쪽 단독보다 잘한다 (ablation으로 입증).
- **Foundation model은 plug-and-play**: 같은 framework에 scBERT·Geneformer·scFoundation을 갈아 끼울 수 있고, 각자의 강·약점이 드러난다.
- **Scale → 적응 전략 차이**: 1M cell로 학습된 scBERT는 fine-tune이 필요하고, 95M(Geneformer)·50M(scFoundation)은 zero-shot으로 충분.
- **Link prediction 프레이밍**: GRN을 그래프로 보고 미지의 TF→target edge 예측 — 그래프 학습 진영의 표준 표현법을 생물학에 적용.
- **Hard-negative sampling이 중요**: 단순 부재 edge 대신 (TF, 비-target) 무작위 페어로 학습 — 모델이 진짜 신호와 잡음을 구분하게 한다.

## 분석 프레임워크

### 모델 아키텍처 한 장

```
scRNA-seq (N×T)                                    Prior GRN (TF→target edges)
        │                                                       │
        ▼                                                       ▼
[sc-FM (frozen): scBERT / Geneformer / scFoundation]     [GNN encoder: GCN/SAGE/GAT]
        │                                                       │
   per-cell gene embeddings                          per-gene structural embedding
        │                                                       │
   ┌─pooling─┐ (attention for scBERT, mean for others)         │
        ▼                                                       ▼
   Z_FM[t] ──────────────────────concat──────────────────── Z_GNN[t]
                                  │
                                  ▼
                     Z_joint[t] → MLP → 저차원 e_t
                                  │
                          (e_i, e_j) per gene pair
                                  │
                                  ▼
                          Cross-entropy → link prob
```

### sc-FM 비교 (이 논문의 실증 결과 기반 정리)

| 모델 | 학습 셀 수 | 아키텍처 | 본 논문 사용 | scRegNet 내 성능 |
|---|---|---|---|---|
| **scBERT** | 1M | Performer (L=6, dim 200) + gene2vec | fine-tune 필요 | 가장 약함 |
| **scFoundation** | 50M | 비대칭 encoder-decoder, 19264 gene 어휘 | zero-shot | 중간 |
| **Geneformer** | 95M | encoder-only (L=20, dim 896), rank-value 인코딩 | zero-shot | **가장 강함** |

→ Theodoris 2024가 강조한 "데이터 규모 + 적절한 입력 표현 → emergent capability"의 구체 사례.

### 결과 요약 (TFs + 500 most variable genes)

- 7/7 데이터셋에서 scRegNet(w/ Geneformer)이 최고.
- 평균 향상: AUROC +8% / AUPRC +13–17% over GENELink, GNNLink.
- mDC(가장 어려운 데이터셋)도 +3.5% AUROC.

### Ablation

| 제거 | 영향 |
|---|---|
| GNN 인코더 | 큰 폭 감소 — 그래프 위상 필요 |
| sc-FM 임베딩 | 큰 폭 감소 — cell-context 필요 |
| Attention pooling | 명확 감소 (scBERT 변종에서) |
| GNN 종류(GCN/SAGE/GAT) | 거의 동일 |

## 결론 및 가이드라인

- **이 논문의 본 위키 위치 (application/comparison perspective)**: Theodoris 2024(벤치마킹 원칙) 옆자리, Cui 2024 survey 슬롯에 보관. *"sc-FM을 실제 task에 끼워 넣으면 어떤 성능 차이가 나는가"*에 대한 현장 답안.
- **Theodoris 2024 8원칙으로 검증**:
  - 생물학적으로 의미 있는 task ✅ (GRN edge 예측은 단순 클러스터링 아님)
  - 고차원 평가 ✅ (AUROC/AUPRC, UMAP 아님)
  - 사전학습 loss vs 다운스트림 분리 — Geneformer/scFoundation은 zero-shot
  - Ground truth 출처 — BEELINE 라벨도 결국 ChIP/ENCODE 등에 의존, 주의 필요
  - 일반화성 — 7 cell type, 인간+마우스, 그러나 disease state 변이는 미포함
  - 도전 난이도 — mDC 같은 어려운 데이터셋 포함 ✅
  - HP 튜닝 공정성 — 동일 split, 50 trial, Optuna ✅
  - 실험적 검증 — 본 논문은 wet-lab 검증 없음 ✗
- **운영 가이드라인**:
  - 본 위키에 sc-FM을 비교할 때, 단독 임베딩의 SOTA가 아니라 *graph-aware downstream task*에서의 비교를 함께 보면 더 신뢰성 있음.
  - "Geneformer가 zero-shot으로 scFoundation을 능가"한다는 결과는 **데이터 규모 × 입력 인코딩**이 임베딩 품질에 결정적임을 시사.

## 관련 논문

- [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]] :supports — Theodoris의 벤치마킹 원칙을 GRN task에서 부분 만족.
- [[foundation-perspectives/wang-2023-scientific-discovery-in-the-age]] :methodologically_uses — geometric deep learning + self-supervised FM의 결합 사례.
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] :builds_on — Geneformer 본체.
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] :parallel_in_other_disease — scGPT generative single-cell foundation model.
- [[single-cell-foundation/hao-2024-large-scale-foundation-model-on]] :parallel_in_other_disease — scFoundation 대규모 single-cell 모델.
- [[single-cell-foundation/boiarsky-2025-sctab-benchmark-for-single-cell]] :supports — supervised single-cell benchmark.
- [[single-cell-foundation/kedzierska-2025-assessing-the-limits-of-zero]] :supports — zero-shot 한계 평가.

## 용어집

- **GRN**: gene regulatory network. 노드=유전자, edge=TF→target 조절.
- **TF**: transcription factor (전사인자).
- **sc-FM**: 단일세포 foundation model (sc-foundation model).
- **BEELINE**: scRNA-seq용 GRN 추론 벤치마크.
- **GNN/GCN/GraphSAGE/GAT**: 그래프 신경망 4종.
- **Link prediction**: 두 노드 간 edge 존재 여부 예측 task.
- **Zero-shot / fine-tuning**: 사전학습 모델 그대로 사용 / 추가 학습.
- **Attention pooling**: 학습된 attention 가중치로 cell들의 임베딩 합성.
- **Hard-negative sampling**: 자명한 부재 edge 대신 어려운 무작위 비-target을 negative로 사용.
- **Performer / xTrimoGene**: 효율적 attention 변종 (scBERT / scFoundation 사용).
- **Rank-value encoding**: 셀 안에서 유전자의 상대 발현 순위를 토큰으로 사용 (Geneformer).
- **AUROC / AUPRC**: 분류 성능 지표; 클래스 불균형에서는 AUPRC가 더 정보량 많음.
- **ENCODE / ChIP-Atlas / ESCAPE**: TF-binding 공개 DB; ground-truth GRN edge 출처.

---

## 위키 운영자 메모

> 파일명 `cui-2024-a-survey-on-the-application` 은 본래 Cui et al. survey 논문 자리로 예정되었으나, 현재 papers/ 폴더의 PDF는 Kommu et al. 2024 (scRegNet, bioRxiv 2024.12.16.628715)이다. 위 문서는 실제 보유한 PDF 내용 그대로를 기록한 것이며, 진짜 Cui survey가 추후 입수되면 stem `kommu-2024-gene-regulatory-network-inference`로 재명명하고 별도 source/wiki 분리 권장.
