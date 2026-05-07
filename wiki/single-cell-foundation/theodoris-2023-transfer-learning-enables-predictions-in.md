---
title: "Geneformer — 30M 단일세포 전이학습으로 네트워크 생물학을 예측"
authors: Theodoris CV, Xiao L, Chopra A, ..., Liu XS*, Ellinor PT*
year: 2023
journal: "Nature 618(7965):616–624"
source: theodoris-2023-transfer-learning-enables-predictions-in.md
category: single-cell-foundation
tags: [Geneformer, transfer learning, BERT, single-cell, rank value encoding, in silico perturbation, cardiomyopathy, network biology]
---

## 요약

**Geneformer**는 약 30M(QC 후 27.4M) 인간 단일세포 전사체 코퍼스(**Genecorpus-30M**)로 사전학습된 6-layer × 4-head, hidden 256 트랜스포머. 입력은 **rank value encoding** — 각 세포에서 유전자를 코퍼스 전체 nonzero median으로 정규화한 발현 순위로 정렬한 토큰열 — 이고, BERT식 **15% 마스킹 MLM** 목표로 학습된다. 사전학습만으로 attention head가 자발적으로 transcription factor와 네트워크 중심 노드에 가중치를 두는 **네트워크 위계**를 인코딩하며, 적은 fine-tuning 데이터로도 dosage sensitivity, bivalent chromatin, N1-network central factor 등 다양한 다운스트림 과제를 능가한다. **In silico deletion / treatment** 분석으로 심근병증 환자 cardiomyocyte 데이터에서 GSN, PLN을 후보 치료 표적으로 지목하고, TTN+/− iPSC cardiac microtissue에서 CRISPR KO로 수축력 회복을 검증.

## 핵심 기여

- **단일세포 BERT의 표준 정립** — ~30M 규모에서 MLM 사전학습이 전이가능한 네트워크 생물학 모델을 만든다는 점을 처음 제대로 증명.
- **Rank value encoding** — 절대 count 대신 코퍼스-정규화된 순위 사용 → housekeeping 억제, TF 부각, 배치 효과에 강건.
- **자발적 네트워크 위계 학습** — 라벨 없이 사전학습만으로 20% attention head가 TF / 중심 노드를 선택적으로 attend.
- **In silico perturbation framework** — 유전자 토큰 제거 → 임베딩 변화로 KO 효과 모사. GATA4+TBX5 협력 효과까지 회복.
- **In silico treatment for cardiomyopathy** — 환자 cardiomyocyte를 healthy state로 되돌리는 후보 표적 sweep → GSN, PLN 실험 검증.
- **TEAD4** — 신규 dosage-sensitive 심장 TF로 예측 + iPSC microtissue에서 검증.

## 방법론 및 아키텍처

| 축 | 내용 |
|---|---|
| 코퍼스 | Genecorpus-30M, 561 datasets, droplet-based, 정상세포만, QC 후 27.4M |
| 토큰화 | Rank value encoding (per-cell, corpus median 정규화), max 2048 토큰 |
| 모델 | 6-layer transformer encoder, 4-head, hidden 256 |
| 사전학습 | 15% 마스킹 MLM, self-supervised, 분산 GPU |
| Fine-tuning | dosage sensitivity, bivalent chromatin, TF range, N1-network, cardiomyopathy 분류, cell type annotation |
| In silico | gene deletion = 토큰 제거 → 임베딩 변화 측정; treatment = 다중 perturbation sweep |

## 결과

- Dosage sensitivity AUC **0.91** (10K 세포 fine-tune); Collins et al. high-confidence 신경발달 질환 유전자와 **96% 일치**, moderate-confidence 84% 일치, fetal cerebral 맥락 특이성 회복.
- Bivalent chromatin AUC **0.93 vs unmethylated**, 0.88 vs H3K4me3-only — 단 56 라벨 loci × ~15K ESC.
- N1-network central vs peripheral AUC **0.81**; 5K 세포에서도 거의 동등; 884 디스이즈 EC만으로도 베이스라인 초과.
- Cardiomyopathy 3-class (NF/HCM/DCM) **90% 정확도**.
- HCM-shifting 447 / DCM-shifting 478 유전자 추출.
- **GSN, PLN KO** → TTN+/− iPSC cardiac microtissue 수축응력 유의 회복 (실험 검증).
- **TEAD4 KO** → cardiomyocyte 수축응력 감소, 신규 dosage-sensitive TF 검증.
- 사전학습 코퍼스를 키울수록 동일 fine-tune 데이터에서 다운스트림 AUC 단조 증가 (scaling).

## 활용 가이드 / 임상적 의의

- **희소 질환 / 접근 어려운 조직** — 적은 환자 cohort만 있어도 fine-tune + in silico treatment로 후보 표적 도출 가능.
- **GWAS hit prioritization** — context-aware in silico deletion으로 어느 조직에서 dosage 효과가 큰지 평가.
- **iPSC 디스이즈 모델과의 결합** — 모델 예측 → CRISPR 검증의 closed loop.
- HuggingFace `ctheodoris/Geneformer`로 즉시 사용 가능. Fine-tune 코드와 토크나이저 함께 공개.

## 관련 논문

- [[single-cell-foundation/boiarsky-2025-sctab-benchmark-for-single-cell]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/hao-2024-large-scale-foundation-model-on]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/heimberg-2024-a-cell-atlas-foundation-model]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/kedzierska-2025-assessing-the-limits-of-zero]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/wen-2024-cellplm-pre-training-of-cell]] — 같은 카테고리 (single-cell foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본 (architecture anchor)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology anchor
- [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]] — sc-foundation 벤치마킹 perspective

## 용어집

- **Rank value encoding** — 세포 내 유전자를 코퍼스 nonzero median으로 정규화한 순위로 정렬한 토큰열.
- **Genecorpus-30M** — 사전학습 코퍼스, 27.4M 정상세포.
- **MLM (masked language modeling)** — 일부 토큰을 가리고 맞히게 학습하는 self-supervised 목표.
- **In silico deletion** — 유전자 토큰을 제거하고 임베딩 변화를 측정해 KO 효과를 모사.
- **In silico treatment** — 다수의 후보 perturbation을 sweep해 disease 임베딩을 healthy로 가장 잘 되돌리는 조합 탐색.
- **Dosage sensitivity** — 유전자 카피 수 변화가 표현형에 영향을 주는 정도.
- **Bivalent chromatin** — H3K27me3 + H3K4me3가 공존하는 발달 유전자 promoter, ESC에서 활성 대기 상태.
- **N1-dependent network** — NOTCH1 의존 endothelial 유전자 조절망, 본 논문의 네트워크 위계 벤치마크.
- **TTN+/−** — Titin truncating 변이 이형 접합, dilated cardiomyopathy 모델.
- **iPSC cardiac microtissue** — iPSC 유래 3D 심장 조직, KO 검증 플랫폼.
