---
title: "Arc Virtual Cell Challenge — AI 가상세포의 CASP, 그리고 STATE baseline"
authors: Lin F (reporting); Roohani Y (Arc Institute commentary lead); Hsu PD, Konermann S
year: 2025
journal: "GEN Edge 7(1):442–446"
source: arc-2025-virtual-cell-challenge-community-benchmark.md
category: virtual-cell
tags: [benchmark, competition, CASP-analog, virtual-cell-challenge, STATE, perturbation-prediction, OOD, arc-institute]
---

## 요약

Arc Institute가 출범한 **Virtual Cell Challenge**는 가상세포 분야의 CASP에 해당하는 오픈 벤치마크 대회. 새로 제작한 **300,000 H1 hESC × 300 genetic perturbation** 데이터셋으로, 모델이 **새로운 cell context에 일반화** 하는 능력을 평가한다. 평가 지표는 (1) DE gene 예측 정확도, (2) perturbation 효과 구별력, (3) expression count 오차. Arc 자체 모델 **STATE** (ST + SE 모듈)이 baseline. 상금 $100K + DGX Cloud credits, Nvidia / 10x Genomics / Ultima Genomics 후원. Bunne 2024 AIVC 비전을 실제 community 인프라로 구현하는 첫 시도.

## 핵심 기여

1. **AIVC 비전의 운영화** — Bunne 2024 *Cell* perspective를 표준화된 community benchmark로 변환.
2. **OOD generalization을 핵심 지표로**: 단순 fit이 아닌 **새 cell context** 외삽 능력 평가.
3. **Purpose-built dataset**: 기존 데이터는 노이즈/재현성 문제로 모델 평가가 흐려졌다는 진단 → Arc가 직접 고품질 perturbation 데이터 생산.
4. **STATE baseline 공개**: ST (100M cells × 70 contexts 학습한 set-transformer) + SE (167M cells observational embedding) 결합.
5. **다양한 학습 데이터 풀**: Arc Virtual Cell Atlas + scBaseCount + Tahoe-100M + X-Atlas/Orion → 5억 cell 이상.
6. **CASP-style 운영**: 단계별 fine-tune / validation / blind test, 라이브 leaderboard.

## 방법론 및 아키텍처

### Challenge

| 항목 | 내용 |
|---|---|
| Dataset | 300k H1 hESC × 300 genetic perturbation (Arc 신규 생성) |
| Eligible 학습 데이터 | Arc Virtual Cell Atlas, scBaseCount, Tahoe-100M, X-Atlas/Orion 등 5억+ cells |
| 평가 지표 | (1) DE gene prediction, (2) perturbation 효과 구별력, (3) expression count error |
| Phase | fine-tune → validation (leaderboard) → blind test (10월 말) |
| 상금 | $100K / $50K / $25K + NVIDIA DGX Cloud credits |
| 발표 | 12월 |

### STATE 모델 (Arc baseline)

| 모듈 | 역할 | 학습 데이터 |
|---|---|---|
| **State Transition (ST)** | bi-directional transformer가 **cell set** 단위로 perturbation 예측 (per-cell이 아님 → 분포 가정 없이 heterogeneity 캡처) | 100M perturbed cells × 70 contexts |
| **State Embedding (SE)** | gene expression의 robust representation (technical noise 제거, biological signal 보존) | 167M observational cells |

조합: SE 임베딩을 ST가 받아 perturbation 적용 후 새 SE 출력.

Arc 보고: 기존 모델 대비 perturbation 효과 구별 +50% 이상, DE gene 정확도 2× 이상.

## 결과

- 본 commentary 자체에는 정량 결과가 없고, 챌린지 출범 + STATE preprint 결과 인용 위주.
- 인용된 학계/산업계 코멘트:
  - **Emma Lundberg** — 표준 벤치마크 부재가 가상세포 분야의 핵심 병목, 이 챌린지가 community alignment 가속.
  - **Theofanis Karaletsos (CZI)** — open competition이 진보의 강력한 메커니즘.
  - **Patrick Hsu (Arc)** — CASP의 25년 궤적이 AlphaFold 같은 돌파를 만들었듯, 같은 접근으로 가상세포 가속.
  - **Fabian Theis** — 데이터 규모가 충분히 커진 지금 OOD 평가가 결정적.
  - **Ci Chu (Xaira)** — 분야 발전은 data-bound; 그래서 X-Atlas/Orion 공개.

## 활용 가이드

- **연구자 입장**:
  - 본인 모델이 STATE / scGPT / GEARS / CellOT 류와 head-to-head로 비교 가능.
  - blind test set 공개 (10월 말) 전까지 leaderboard로 위치 파악 가능.
  - 학습 데이터 풀(5억+ cells)을 제한 없이 사용 가능.
- **활용 관점**:
  - 자체 perturbation 실험을 STATE 학습 corpus나 challenge 데이터의 표현 공간에 mapping → 즉시 기준점 확보.
  - 한국인 cell line / patient-derived organoid 데이터를 추후 challenge OOD 케이스로 제안할 수 있음.
- **위험**: leaderboard 과적합 (Goodhart's law). 실제 임상 / wet-lab 가치는 OOD 데이터로 별도 검증 필요.

## 관련 논문

- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] :extends — AIVC 비전을 community 벤치마크로 구체화
- [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] :supports — Roohani 본인이 lead. GEARS는 본 챌린지의 기존 baseline 중 하나.
- [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] :supports — CellOT는 manipulator VI 부류로 비교 대상
- [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] :supports — scArches류 mapping은 baseline 표상 공간 후보
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] :provides_data_for — Perturb-seq의 표준 데이터 형식 계승
- [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] :provides_data_for — eligible 학습 데이터 풀의 한 축
- [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] — 같은 카테고리 (virtual cell)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 모델 — perturbation 적용
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — Geneformer in silico perturbation

## 용어집

- **Virtual Cell Challenge** — Arc Institute의 가상세포 모델 community 벤치마크.
- **CASP** — Critical Assessment of protein Structure Prediction. AlphaFold를 촉발한 단백질 구조 예측 격년 벤치마크.
- **STATE** — Arc의 가상세포 모델. ST + SE 두 모듈.
- **State Transition (ST)** — set 단위 bi-directional transformer perturbation predictor.
- **State Embedding (SE)** — 167M cells로 학습한 observational gene expression embedding.
- **H1 hESC** — H1 human embryonic stem cell. 챌린지 데이터셋의 cell type.
- **OOD (Out-of-distribution)** — 학습 분포 밖 데이터. 본 챌린지의 핵심 평가 축.
- **DE gene** — differentially expressed gene; control 대비 발현 변화 유의 유전자.
- **Arc Virtual Cell Atlas** — Arc가 큐레이션한 가상세포 학습 데이터 풀.
- **scBaseCount** — 공개 scRNA-seq 통합 corpus.
- **X-Atlas/Orion** — Xaira Therapeutics가 공개한 대규모 Perturb-seq.
- **CellFlow** — Theis 연구그룹의 flow matching perturbation 시뮬레이터.
- **scGenePT / TranscriptFormer** — CZI 가상세포 foundation model.
