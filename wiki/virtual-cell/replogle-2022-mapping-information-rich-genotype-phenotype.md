---
title: "Replogle Perturb-seq atlas — 2.5M cells × genome-scale CRISPRi"
authors: Replogle JM, Saunders RA, Pogson AN, ..., Norman TM*, Weissman JS*
year: 2022
journal: "Cell 185:2559–2575"
source: replogle-2022-mapping-information-rich-genotype-phenotype.md
category: virtual-cell
tags: [perturb-seq, atlas, CRISPRi, K562, RPE1, genome-scale, genotype-phenotype, training-data]
---

## 요약

K562 (~9,866 유전자)와 RPE1 (~11,258 유전자) 두 셀라인에서 **모든 발현 유전자**를 CRISPRi로 knockdown하고 단일세포 RNA-seq로 읽어낸 **2.5M cells** 규모 atlas. 31% 유전자가 유의 transcriptional phenotype을 보임. CCDC86 / C7orf26 / TMEM242 등 미특성 유전자 기능 동정, aneuploidy 드라이버 분석, 미토콘드리아 유전체의 stress-specific 조절 등 multidimensional 표현형까지 커버. **이후 거의 모든 virtual-cell perturbation 모델 (GEARS, CellOT, scGPT, scFoundation, STATE)의 학습/평가 표준 데이터**.

## 핵심 기여

1. **Genome-scale Perturb-seq** — 표적 단위로 한정되었던 Perturb-seq를 전 발현 유전체로 확장.
2. **2.5M+ 세포** — 단일세포 perturbation atlas의 첫 giga-scale (당시 기준 최대).
3. **세포 종류 일반화 인사이트**: K562 ↔ RPE1 cophenetic r = 0.37 — perturbation 효과의 cell-type dependence가 큼 → 단일 cell line만 본 모델의 OOD risk를 정량화.
4. **신규 유전자 기능 동정**:
   - **CCDC86 / ZNF236 / SPATA5L1** → ribosome biogenesis.
   - **C7orf26** → Integrator complex의 새 sub-module (INTS10–13–14–C7orf26).
   - **TMEM242** → mitochondrial respiration.
5. **복합 표현형**: aneuploidy, 미토콘드리아 유전체 stress 반응, transposable element 활성 등 single-cell이라 가능한 phenotype 분석.
6. **하류 모델의 산실**: GEARS / CellOT / scGPT / Geneformer / scFoundation / STATE 학습 또는 벤치마크.

## 방법론 및 아키텍처

| 셀라인 | 종류 | 표적 유전자 수 | 세포 수 (대략) |
|---|---|---|---|
| K562 (day 8) | CML, suspension, p53− | 9,866 | ~1.98M |
| K562 (day 6) | 동일, 시점 replicate | 9,866 | 추가 |
| RPE1 | RPE, near-euploid, p53+ | 11,258 | balance |

- **Multiplexed CRISPRi 라이브러리**: element마다 **두 개의 sgRNA**가 같은 유전자를 표적 → 효과 향상.
- 사전 growth screen으로 essential gene을 over-represent → dropout 방지.
- **Ultima Genomics** 플랫폼으로 비용 효율적 deep sequencing.
- 통계: permuted **energy distance** at principal components → 유의 phenotype 호출.
- 시각화: **minimum distortion embedding**, UMAP, CORUM/STRING annotation.

## 결과

- 9,608 표적 중 2,987개 (31.1%)가 K562에서 유의 transcriptional phenotype, control은 1.9% (FDR baseline).
- K562 day 6 vs day 8 perturbation 관계 cophenetic r = 0.82 (재현성 우수).
- K562 vs RPE1 cophenetic r = 0.37 (cell-type dependence 큼).
- CCDC86 knockdown → 28S/18S rRNA ratio 변화로 ribosome biogenesis 결함 검증.
- C7orf26은 INTS10/13/14와 직접 결합하는 새 Integrator sub-module (western + co-IP + SEC 검증).
- Aneuploidy state, 미토콘드리아 유전자 stress-specific program 등 composite phenotype 다수 발견.

## 활용 가이드

- **Virtual-cell 모델 학습용 데이터**:
  - GEARS / CellOT / scGPT / Geneformer 등 perturbation 학습/평가의 디폴트.
  - **세포 종류 의존성** 때문에 K562 학습 → RPE1 평가가 자연스러운 cross-cell-type benchmark.
- **활용 관점**:
  - 자체 cell line panel에 같은 protocol 적용 시 약 30% 유전자가 transcriptional phenotype 보일 것이라 기대.
  - rare disease / under-characterized gene의 기능을 빠르게 좁히는 도구로 활용 가능.
  - Bulk RNA-seq 대비 단일세포 single-cell이 갖는 composite phenotype (aneuploidy, mitochondrial stress) 해상도가 결정적.
- **데이터 접근**: GEO / 저자 사이트 (gwps.wi.mit.edu), CZI CELL×GENE에 미러.

## 관련 논문

- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] :supports — AIVC 데이터 요건의 핵심 사례
- [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] :provides_data_for — GEARS 학습 데이터원
- [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] :provides_data_for — CellOT 학습/평가에 활용
- [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] :supports — 유전자 perturbation Perturb-seq vs 화학 perturbation atlas의 직접 비교 대상
- [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] :provides_data_for — Virtual Cell Challenge가 후속 perturb-seq 데이터로 확장
- [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] — 같은 카테고리 (virtual cell)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 모델 — perturbation 적용
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — Geneformer in silico perturbation

## 용어집

- **Perturb-seq** — pooled CRISPR + scRNA-seq.
- **CRISPRi** — dCas9-KRAB 기반 유전자 발현 억제. Knockdown은 부분적/가역적.
- **Multiplexed sgRNA** — element 하나에 같은 유전자 표적 sgRNA 2개 → 효과 안정.
- **K562** — 만성골수성백혈병 셀라인, p53−.
- **RPE1** — 망막색소상피 셀라인, hTERT-immortalized, near-euploid, p53+.
- **Energy distance** — 두 분포 간 거리 측도. PC 공간에서 perturbation 유무 판정.
- **Cophenetic correlation** — 두 hierarchical clustering의 일치도.
- **Minimum distortion embedding (MDE)** — UMAP 대안 차원축소 (Agrawal 2021).
- **Aneuploidy** — 비정상 염색체 수; 암 세포에 빈번.
- **Integrator complex** — RNA Pol II 의존적 snRNA 처리 / transcription termination 복합체.
- **CORUM / STRING** — protein complex / 상호작용 DB.
