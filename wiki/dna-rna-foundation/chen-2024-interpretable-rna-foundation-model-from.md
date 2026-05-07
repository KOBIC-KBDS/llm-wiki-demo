---
title: "RNA-FM — RNAcentral 2,300만 ncRNA로 학습한 첫 RNA 파운데이션 모델"
authors: Chen J, Hu Z, Sun S*, Tan Q, ..., King I, Li Y*
year: 2024
journal: "Nature Methods / NAR (CUHK + Fudan + MIT/Harvard)"
source: chen-2024-interpretable-rna-foundation-model-from.md
category: dna-rna-foundation
tags: [RNA-FM, RNA-foundation-model, BERT, ncRNA, RNAcentral, secondary-structure, 3D-closeness, MRL, RBP, SARS-CoV-2, interpretability]
---

## 요약

RNA-FM은 **RNA 도메인의 첫 파운데이션 모델**로, 12층 BERT 인코더(hidden 640)를 RNAcentral의 **23 M ncRNA**로 MLM 사전학습한다. 라벨 없이 학습된 임베딩이 ncRNA를 구조·기능별로 분류(RNA Atlas)하고, lncRNA / SARS-CoV-2 변이의 진화 trajectory까지 복원한다. 다운스트림에서 secondary structure, 3D closeness, distance map, RBP 결합, 5′ UTR mean ribosome loading 모두 SOTA. 단일 시퀀스만 입력하므로 MSA 생성 단계를 생략할 수 있다.

## 핵심 기여

1. **RNA용 BERT 사전학습** — 23 M ncRNA, MLM, 12층/hidden 640 → L×640 임베딩.
2. **One-for-all 임베딩** — 같은 백본이 SS / 3D / RBP / MRL을 모두 개선.
3. **RNA Atlas (해석성)** — UMAP에서 ncRNA 클래스가 길이가 아닌 구조/기능 기준으로 깔끔히 분리.
4. **암묵적 진화 신호** — VIA trajectory가 lncRNA의 종간 진화 순서, SARS-CoV-2의 Alpha→Omicron 21K→21L 흐름을 자연스럽게 복원.
5. **MSA 없이 SOTA** — secondary structure F1: ArchiveII600 0.941 (UFold 0.905), bpRNA TS0 0.704 (UFold 0.654). 3D closeness long-range Top-L precision 0.66 (전이학습) vs RNAcontact 100-ensemble 0.33.
6. **mRNA UTR로의 일반화** — ncRNA로만 학습됐는데 5′ UTR MRL 예측까지 향상.

## 방법론 및 아키텍처

| 요소 | 사양 |
|---|---|
| 백본 | 12층 BERT 인코더, hidden 640 (~100 M params) |
| 토큰화 | 단일염기 (A,U,C,G,N + 특수) |
| 사전학습 목표 | MLM (15% mask) |
| 데이터 | RNAcentral 23 M ncRNA (CD-HIT 클러스터링) |
| 출력 | per-RNA L × 640 임베딩 |
| 다운스트림 헤드 | ResNet32 (구조), U-Net (거리), CNN (MRL/RBP) |
| 활용 | frozen embedding 또는 fine-tune |

## 결과

- **2D 구조** — ArchiveII600 F1 **0.941**, bpRNA TS0 F1 **0.704**. SPOT-RNA, UFold, MXfold2, CONTRAfold 등 12개 SOTA를 거의 모든 메트릭에서 능가.
- **3D closeness (RNAcontact TE80, long-range Top-L)** — 단일 ResNet32 + RNA-FM = 0.53 → +전이학습 = **0.66** vs RNAcontact 100-ensemble 0.33.
- **Distance regression** — RNA-FM+Seq+Cov: MSE 0.0319, PMCC **0.8313**. SS+Cov+Seq보다 좋음.
- **3D 재구성** — 3dRNA로 PDB test에서 RMSD ~7.91 Å (UFold 25.70, GT SS 13.96).
- **SARS-CoV-2** — 5′/3′ UTR, regulatory 영역 SS 정확 예측. 게놈 단위 임베딩 trajectory가 FastME 계통수와 일치.
- **RBP–RNA (HeLa, 17 RBPs)** — RNA-FM+Seq 평균 AUPRC 0.824 ≈ icSHAPE 실험값(RealSS+Seq) 0.833.
- **5′ UTR MRL** — Random7600 R² 0.876 vs Seq 0.860; Seq+SS+3DS+RNA-FM 결합 시 0.882.

## 활용 가이드

- **언제 쓰나** — ncRNA 구조/기능 예측, MSA가 시간/비용 부담일 때, RNA-protein binding 신호가 필요한데 in-vivo 구조 데이터(icSHAPE)가 없을 때.
- **주의점**:
  - 기능 태스크(UTR/RBP)에서의 향상은 구조 태스크보다 작음 — 학습 분포(ncRNA)와의 gap.
  - 단일 시퀀스 입력 한계 — homology가 강한 영역에선 MSA 모델(RNA-MSM)이 유리할 수 있음.
  - 100 M 규모 → 더 큰 RiNALMo(650 M, 36 M seqs) / Uni-RNA(400 M)가 후속 SOTA. 다만 RNA-FM은 **공개**.
  - 생성 불가 (인코더만). 디자인은 Evo / Evo 2 영역.

## 관련 논문

- [[dna-rna-foundation/penic-2025-rinalmo-general-purpose-rna-language]] :extends — RiNALMo가 직접 후속 (650 M, 36 M, modern training tricks). RNA-FM을 거의 모든 다운스트림에서 능가
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] :parallel_in_other_disease — DNA 도메인 자매. 둘 다 BERT 12층, single-organism MLM 사전학습
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] :supports — Evo가 ncRNA fitness DMS에서 RNA-FM 류를 능가
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] :supports — Evo 2의 lncRNA essentiality 평가에서 RNA-FM과 비교
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/linder-2025-predicting-rna-seq-coverage-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/penic-2025-rinalmo-general-purpose-rna-language]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] — 같은 카테고리 (DNA/RNA foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **ncRNA** — non-coding RNA. rRNA, tRNA, snoRNA, miRNA, snRNA, lncRNA, piRNA 등 포함.
- **RNAcentral** — ncRNA 통합 데이터베이스. RNA-FM 사전학습 데이터.
- **MLM** — masked language modeling. BERT 스타일 양방향 사전학습.
- **L × 640 embedding** — 길이 L RNA의 위치별 640차원 벡터 출력.
- **Secondary structure** — RNA가 hydrogen bond로 접힐 때의 base-pair 그래프. L×L 행렬로 표현.
- **3D closeness** — 두 base가 3차 거리 임계(8 Å) 이내인지의 이진 라벨.
- **Distance map** — pairwise 최소 원자거리의 연속 행렬. 3D closeness보다 정보 풍부.
- **icSHAPE** — in vivo 2'-OH acylation으로 측정한 실험적 RNA 구조 유연성 점수.
- **MRL** — mean ribosome loading. 리보솜 점유도, 번역 효율의 프록시.
- **RBP / CLIP-seq** — RNA-binding protein / RBP-RNA 결합 매핑 시퀀싱 어세이.
- **VIA / FastME** — trajectory inference 도구 / 계통수 작성 도구.
