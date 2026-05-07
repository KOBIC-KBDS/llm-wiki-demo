---
title: "GEARS — knowledge graph + GNN으로 미관측 다유전자 perturbation 예측"
authors: Roohani Y, Huang K, Leskovec J*
year: 2024
journal: "Nature Biotechnology 42:927–935"
source: roohani-2024-predicting-transcriptional-outcomes-of-novel.md
category: virtual-cell
tags: [perturbation, GEARS, GNN, knowledge-graph, GO, perturb-seq, combinatorial, manipulator-VI]
---

## 요약

GEARS는 **Gene Ontology 기반 perturbation 그래프**와 **유전자 coexpression 그래프**를 GNN으로 결합해, **단일/다중 유전자 perturbation의 단일세포 transcriptome 결과**를 예측한다. 가장 큰 차별점은 **학습 시 한번도 개별 perturbation으로 본 적 없는 유전자 조합**도 예측 가능하다는 점. Norman 2019 K562 Perturb-seq에서 5가지 genetic interaction subtype을 baseline 대비 40% 더 정밀하게, 강력한 상호작용 검출은 2배 더 잘 함.

## 핵심 기여

1. **Combinatorial extrapolation**: 한 번도 직접 측정하지 않은 유전자 쌍의 효과 예측 — 기존 CPA / scGEN으로는 불가능했던 영역.
2. **Two-graph inductive bias**: GO + coexpression. 미관측 유전자도 GO neighbor의 임베딩을 inherit.
3. **Non-additive 효과 모델링**: synergy / suppression / neomorphism / redundancy / epistasis 5종 GI subtype.
4. **Discovery 모드**: 102 유전자 → 10,302 가상 조합. 기존 클러스터 재현 + 신규 phenotype (erythroid) 제안.

## 방법론 및 아키텍처

| 항목 | 내용 |
|---|---|
| Task | unperturbed cell + perturbation set → post-perturbation expression |
| Graph 1 | 유전자 coexpression (gene–gene) |
| Graph 2 | Gene Ontology (perturbation–perturbation 유사도) |
| Architecture | gene-level GNN + perturbation-level GNN → combine → decode Δexpression |
| 학습 데이터 | Adamson 2016, Dixit 2016, Norman 2019 (102 single + 128 two-gene), Replogle 2020 |
| 평가 | seen/unseen split, top-20 affected genes MSE, GI subtype AUC |

### 핵심 아이디어

- 가깝게 연관된 유전자(같은 GO term, 같은 coexpression module)는 perturbation 시 비슷한 영향 → 미관측 유전자 임베딩은 KG 이웃에서 유도.
- GNN의 message passing으로 위 inductive bias가 자연스럽게 모델 안에 들어감.

## 결과

- 두 유전자 모두 한 번도 단독 perturbation 안 된 조합도 예측. 기존 방법은 적어도 한 유전자는 학습에 있어야 함.
- Top 영향 유전자 MSE에서 baseline 대비 40% 개선.
- 강력한 GI 검출 AUC 2× 향상.
- Norman 2019 102개 유전자 가상 조합 — 알려진 erythroid / megakaryocyte 등 클러스터 회복 + 새 phenotype.
- Fitness 직접 예측 variant: R² 0.64–0.93.

## 활용 가이드

- **사용 시나리오**:
  - 새 multi-gene perturbation 실험 우선순위 정하기 (in silico screen → 흥미로운 조합만 wet-lab).
  - synergy / suppression / neomorphism 후보 동정.
  - GO-rich 유전자 풀 안에서 작업 (예: 잘 정의된 pathway).
- **사용 불가 시나리오**:
  - GO term이 없는 신규 / poorly characterized 유전자.
  - 3개 이상 유전자 조합 (논문은 2-gene까지).
  - 시간 동역학 (kinetics).
- **데이터 요구**: Perturb-seq 형태 (perturbation label + scRNA-seq).
- **활용 관점**: 보유 cell line에서 Perturb-seq을 일부 confirm한 뒤, 나머지는 GEARS로 in silico screening → wet-lab 비용 절감 전략에 적합.

## 관련 논문

- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] :supports — AIVC manipulator VI의 대표 사례
- [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] :supports — CellOT는 unseen sample, GEARS는 unseen perturbation
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] :provides_data_for — genome-scale Perturb-seq 학습 데이터
- [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] :supports — CPA가 같은 연구그룹의 baseline
- [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] :supports — Roohani 본인이 lead한 Virtual Cell Challenge의 baseline-generation 토대
- [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] — 같은 카테고리 (virtual cell)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 모델 — perturbation 적용
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — Geneformer in silico perturbation

## 용어집

- **GEARS** — Graph-Enhanced Gene Activation and Repression Simulator.
- **Perturb-seq** — pooled CRISPR + single-cell RNA-seq.
- **CRISPRi** — Cas9의 dead 변이체로 유전자 억제.
- **Multi-gene perturbation** — 한 세포에서 두 개 이상 유전자 동시 knock-down.
- **Genetic interaction (GI)** — 두 perturbation 효과가 단순 합산이 아닌 경우. Subtypes: synergy / suppression / neomorphism / redundancy / epistasis.
- **GO (Gene Ontology)** — 유전자 기능을 BP/MF/CC로 분류한 onthology.
- **Knowledge Graph (KG)** — 생물학적 entity-relation 그래프. GNN의 inductive bias로 사용.
- **GNN (Graph Neural Network)** — 그래프 구조 위에서 message passing.
- **Coexpression graph** — 발현이 함께 변하는 유전자들을 연결한 그래프.
- **In silico knockout** — wet-lab 없이 유전자 제거 효과를 시뮬레이션.
- **Combinatorial extrapolation** — 학습 분포 밖의 조합으로 일반화.
