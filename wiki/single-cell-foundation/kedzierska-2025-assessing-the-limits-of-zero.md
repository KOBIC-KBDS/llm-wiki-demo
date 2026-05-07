---
title: "Zero-shot로 본 sc-foundation 한계 — Geneformer · scGPT는 단순 베이스라인에 진다"
authors: Kedzierska KZ, Crawford L, Amini AP, Lu AX*
year: 2025
journal: "Genome Biology 26:101"
source: kedzierska-2025-assessing-the-limits-of-zero.md
category: single-cell-foundation
tags: [zero-shot evaluation, Geneformer, scGPT, benchmark, batch integration, HVG, scVI, Harmony, MLM]
---

## 요약

**Microsoft Research × Oxford 팀**이 Geneformer와 scGPT를 **사전학습 가중치 그대로(zero-shot)** 다섯 인간 조직 데이터셋(Pancreas 16K, PBMC 12K, PBMC 95K, Cross-tissue Immune Atlas, Tabula Sapiens)에서 정량 평가. 결과: cell-type clustering(AvgBIO)과 batch integration 모두에서 **HVG 2,000개 선택, scVI, Harmony가 두 sc-foundation을 능가**. scGPT 변형(33M 인간 / 10.3M 혈액 / 814K 신장 / random init)을 비교한 scaling 실험에서도 **사전학습 데이터 ↑ ≠ 단조 향상** (scGPT-blood가 일부 비-혈액 데이터셋에서 scGPT-human보다 좋음). 사전학습 task 자체(gene expression / ranking 재구성)에서도 scGPT는 평균값을 예측하는 수준이고 Geneformer는 입력에 없는 유전자를 예측하기도 함. **zero-shot 평가가 sc-foundation 분야 표준이 되어야 한다**고 결론.

## 핵심 기여

- **단일세포 foundation 모델의 zero-shot 평가 표준** — 단백질 LM / vision LM의 평가 전통을 sc 분야로 가져옴.
- **5개 다양한 인간 조직 데이터셋**에서 두 모델 + 4 베이스라인(HVG, scVI, Harmony, random/mean) 정량 비교.
- **Cell-type clustering**: HVG > Harmony > scVI > scGPT > Geneformer (median AvgBIO 기준).
- **Batch integration**: Geneformer 모든 메트릭 최하위; scGPT는 사전학습에 본 데이터셋에서만 일부 우위.
- **Scaling은 단조 ×** — scGPT-blood (10.3M) ≥ scGPT-human (33M)이 비-혈액 일부에서도 발생, 더 다양한 사전학습 코퍼스가 자동으로 더 좋은 임베딩을 보장하지 않음.
- **사전학습 task 자체 실패 분석** — scGPT는 cell-embedding 없이는 평균 bin 예측, Geneformer는 ranking median Pearson 0.56 + 입력에 없는 유전자도 예측.
- **권고**: pretraining에 절대 사용되지 않는 reserved 평가 데이터셋 큐레이션 필요 (LLM의 MMLU/GPQA 전통).

## 방법론 및 아키텍처

| 축 | 내용 |
|---|---|
| 평가 모델 | Geneformer (commit 5d0082c, 2023-11), scGPT v0.1.6 (kidney 814K / blood 10.3M / human 33M / random) |
| 베이스라인 | HVG 2,000 (log-norm), scVI (target 데이터셋 학습), Harmony (target 데이터셋 학습), mean / avg ranking |
| 데이터셋 | Pancreas 16K, PBMC 12K, PBMC 95K, Cross-tissue Immune Atlas, Tabula Sapiens (일부는 사전학습과 중복) |
| Cell embedding eval | AvgBIO = mean(ASW, NMI, ARI), Louvain 20 resolutions, NMI 최대 resolution 사용 |
| Batch integration | batch silhouette + PCR, Average Batch Score |
| Pretraining task eval | scGPT MSE (GEP / GEPC), Geneformer Pearson (true vs predicted ranking), 모든 입력 unmasked |

## 결과

- **AvgBIO median across datasets**: HVG > Harmony > scVI > scGPT > Geneformer.
- ASW alone에서도 sc-foundation이 베이스라인에 진다.
- scGPT가 베이스라인을 능가한 유일한 dataset = PBMC 12K (사전학습에 없던 것).
- Geneformer는 batch integration 모든 메트릭 최하위; embedding 변동의 더 큰 비율을 batch가 설명.
- **Pretraining-task 재구성**:
  - scGPT GEP (cell-emb 없이): 평균 bin 예측 → MSE ≈ 평균 baseline.
  - scGPT GEPC (cell-emb 사용): 약간 개선, 특히 높은 입력 bin에서.
  - Geneformer: median Pearson 0.56 (best 0.95) + 입력에 없는 유전자 예측 사례 존재.
- **Variant scaling**: scGPT-blood가 비-혈액 일부에서 scGPT-human을 능가 — MLM objective 하에서 데이터 다양성의 한계 추정.

## 활용 가이드 / 임상적 의의

- **탐색적 분석(라벨 없는 상황)**에서 sc-foundation을 zero-shot으로 쓰지 말라 — HVG / scVI / Harmony가 더 안전한 출발점.
- **Fine-tune 자원이 있다면** 다시 비교 필요 — 본 논문은 fine-tune 시나리오는 검토하지 않음.
- **Foundation 모델 개발자**: 사전학습 task가 "마스킹된 유전자 / 순위" 재구성에 머무르는 한 cell embedding이 자동으로 좋아지지 않음 → contrastive, perturbation-aware 등 더 강한 objective 필요.
- **벤치마크 디자인**: pretraining에서 절대 본 적 없는 reserved 평가 셋 분리 (LLM 표준).

## 관련 논문

- [[single-cell-foundation/boiarsky-2025-sctab-benchmark-for-single-cell]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/hao-2024-large-scale-foundation-model-on]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/heimberg-2024-a-cell-atlas-foundation-model]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/wen-2024-cellplm-pre-training-of-cell]] — 같은 카테고리 (single-cell foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본 (architecture anchor)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology anchor
- [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]] — sc-foundation 벤치마킹 perspective

## 용어집

- **Zero-shot evaluation** — 사전학습 모델의 임베딩/출력을 fine-tune 없이 그대로 사용해 평가.
- **Few-shot / fine-tuning** — 작은 분류기 추가 학습 또는 일부 가중치 fine-tune.
- **AvgBIO** — ASW + NMI + ARI 평균, scGPT 논문이 도입한 cell-clustering 품질 메트릭.
- **ASW (Average Silhouette Width)** — cell-type 또는 batch 단위 클러스터 응집/분리 지표.
- **NMI / ARI** — Normalized Mutual Information / Adjusted Rand Index, 클러스터 일치 지표.
- **PCR score** — Principal Component Regression, batch label로 설명되는 변이 비율.
- **HVG (highly variable genes)** — 발현 분산이 큰 유전자 선택 (여기선 2,000개), 고전적 베이스라인.
- **scVI / Harmony** — VAE / PCA + iterative correction 기반 통합 베이스라인.
- **GEP / GEPC** — scGPT의 gene expression prediction (no / with cell embedding).
- **MLM** — masked language modeling, Geneformer와 scGPT 모두 사용; 본 논문이 한계를 지적하는 objective.
