---
title: "AI 가상 세포(AIVC) 청사진 — 다중 스케일 UR + Virtual Instrument 프레임워크"
authors: Bunne C, Roohani Y, Rosen Y, ..., Karaletsos T*, Regev A*, Lundberg E*, Leskovec J*, Quake SR*
year: 2024
journal: "Cell 187(25):7045–7063"
source: bunne-2024-how-to-build-the-virtual.md
category: virtual-cell
tags: [perspective, virtual-cell, foundation-model, AIVC, CZI, multi-scale, multi-modal, roadmap]
---

## 요약

CZI 워크숍에서 정리된 **AI Virtual Cell (AIVC)** 비전 페이퍼. 세포를 분자 / 세포 / 다세포 세 스케일에서 학습한 **Universal Representation (UR)** 와, UR을 조작하거나 해독하는 **Virtual Instrument (VI)** — *decoder* (UR → 표현형) 와 *manipulator* (UR → UR) — 의 조합으로 만들자고 제안. 단일 모델이 아니라 상호 연결된 foundation model 들의 협업 인프라이다.

## 핵심 기여

1. **AIVC 정의**: 룰 기반 whole-cell 모델(Karr 2012)의 한계를 넘어, 데이터 기반 다중 스케일 신경망 시뮬레이터로 재정의.
2. **3가지 능력 분류**:
   - (i) UR 구축 — 종/모달리티/문맥을 가로지르는 표상,
   - (ii) 예측 — 세포 행동과 동역학, 메커니즘 추정,
   - (iii) in silico 실험 — 가설 생성, 데이터 수집 가이드.
3. **2-컴포넌트 아키텍처**: UR(임베딩) + VI(신경망 도구). 누구나 UR 위에 자신의 VI를 만들고 공유할 수 있는 모듈성을 강조.
4. **CASP-스타일 오픈 협업**: 동일한 벤치마크와 평가 프레임워크가 분야의 진보를 가속한다는 신념. 이는 후일 Arc Virtual Cell Challenge로 구체화됨.

## 방법론 및 아키텍처

| 스케일 | 입력 데이터 | 추천 아키텍처 |
|---|---|---|
| 분자 (molecular) | DNA, RNA, 단백질 서열; 원자구조 | Transformer LM (DNABERT, ESM, Evo); equivariant atomic NN (AlphaFold 3, RoseTTAFold-AA) |
| 세포 (cellular) | scRNA-seq, scATAC, proteomics, fluorescence | Autoencoder (scVI), transformer (scGPT, Geneformer, UCE), vision transformer |
| 다세포 (multicellular) | spatial transcriptomics, H&E, 3D tomography | GNN, equivariant NN, ViT |

각 스케일의 UR은 위 스케일로 *aggregate / abstract* 되어 hierarchically 결합된다.

### Virtual Instrument 종류

- **Manipulator**: UR + 섭동 prompt → 새로운 UR. 구현 후보로 **conditional diffusion**, **flow matching**, **Schrödinger bridge**, **autoregressive transformer**, **optimal transport** (CellOT), **VAE shift** (CPA, SAMS-VAE).
- **Decoder**: UR → 관측 가능한 양 (cell type, 이미지, 생존, 약물 반응).

### Lab-in-the-loop

- 예측에 **uncertainty estimate** (Bayesian / 앙상블 / conformal) 를 부여,
- **active learning** 으로 다음 실험 선택,
- 가상 ↔ 실험실 반복.

## 결과

실험 결과는 없음 (Perspective). 핵심 정량 포인트:

- 단일세포 데이터가 약 6개월마다 두 배로 증가.
- Short Read Archive 14 PB — ChatGPT 학습 데이터의 1,000배 이상.
- 활용 시나리오 (Box 2): 가상 환자, 가상 organoid, 약물 스크리닝, 합성 생물학 설계.

## 활용 가이드

- **연구 시작점으로**: 자신의 모델/데이터를 "어느 UR 스케일을 만드는지", "decoder인지 manipulator인지" 로 자리매김하면 분야 안에서 위치 파악이 쉬움.
- **활용 관점**: 동일 그룹이 만든 GEARS(perturbation manipulator)와 CellOT(OT manipulator)가 이 프레임워크의 구체적 인스턴스로 등장. 자체 perturbation 데이터셋이 있다면 manipulator VI 학습 / fine-tune 후보.
- **데이터 기여**: 다중 모달, 다중 스케일, 다양한 ancestry/sex/species — 어느 한 축이라도 부족하면 일반화 한계.
- **벤치마크**: cross-modal reconstruction, OOD generalization, 새로운 생물학 발견 — 셋 다 동시에 평가.

## 관련 논문

- [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] :extends — manipulator VI의 OT 기반 구체 사례 (CellOT)
- [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] :extends — manipulator VI의 GNN + Knowledge graph 사례 (GEARS)
- [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] :extends — UR + 새 데이터 contextualize (scArches)
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] :provides_data_for — manipulator 학습용 genome-scale Perturb-seq
- [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] :provides_data_for — chemical perturbation atlas
- [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] :extends — CASP-style benchmark 실현
- [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] — 같은 카테고리 (virtual cell)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 모델 — perturbation 적용
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — Geneformer in silico perturbation

## 용어집

- **AIVC** — AI Virtual Cell. 본 페이퍼가 정의한 신경망 기반 다중 스케일 세포 시뮬레이터.
- **UR (Universal Representation)** — 한 스케일에서 학습된 임베딩 공간. 세포 / 분자 / 조직 각각.
- **VI (Virtual Instrument)** — UR 위에서 동작하는 신경망. decoder(읽기) / manipulator(변환).
- **Foundation model** — 대규모 사전학습 모델. 다양한 downstream 태스크의 출발점.
- **Lab-in-the-loop** — 예측 ↔ 실험 ↔ 재학습 반복 루프.
- **In silico experimentation** — 컴퓨터 안에서 가상의 perturbation을 적용해 결과를 예측.
- **Perturb-Seq** — pooled CRISPR + scRNA-seq. 본 프레임워크의 manipulator 학습에 핵심 데이터.
- **Optical pooled screen (OPS)** — pooled CRISPR + 이미지 표현형.
- **Active learning** — 모델 불확실성이 큰 곳에 다음 실험을 배치하여 정보 이득을 극대화.
- **Cell atlas** — 참조 단일세포 데이터셋 (HCA, Tabula Sapiens, CZ CELL×GENE).
