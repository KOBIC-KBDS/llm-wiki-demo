---
title: "Tahoe-100M — 100M cells × 1,100 약물 × 50 암 셀라인 perturbation atlas"
authors: Zhang J, Ubas AA, de Borja R, Svensson V, ..., Goodarzi H*, Yu J*
year: 2025
journal: "bioRxiv (preprint, 2025)"
source: tahoe-2025-tahoe-100m-a-giga-scale.md
category: virtual-cell
tags: [chemical-perturbation, atlas, drug-response, cell-village, mosaic, parse-biosciences, virtual-cell-training-data]
---

## 요약

Tahoe Therapeutics가 공개한 **100M cell** 규모 단일세포 atlas. **50개 암 셀라인 spheroid (cell village)** 를 한 well에서 같이 키우고 well마다 약물 한 종을 처리, scRNA-seq 후 SNP로 셀라인 deconvolution. 결과: **379개 unique drug × 1,135 drug-dose 조합 × 17,813 cell-line × drug 조건**. 기존 Sciplex3 대비 31× 더 많은 조건, Replogle 2022 대비 29× 더 많은 cell/condition. **Virtual cell foundation model 학습용 chemical perturbation 데이터의 사실상 표준**.

## 핵심 기여

1. **세계 최대 chemical perturbation single-cell atlas** — public dataset 중 최고 규모.
2. **Mosaic "cell village" 디자인**: 50 셀라인 같이 spheroid → well-내 동일조건 → cell-line별 batch effect 원천 제거. SNP demuxlet으로 사후 분리.
3. **Drug 다양성**: 25 MOA, 325 표적 유전자, 69%가 승인 약물. 항암제 위주 (MAPK / PI3K-AKT / mTOR / CDK / proteasome / HDAC / 미세소관 / DNA repair).
4. **Context-dependent drug effect** 입증:
   - Dabrafenib (BRAF inhibitor) → BRAF-V600E line에서 강.
   - RMC-6236 (pan-RAS) → KRAS mut line에서 강.
   - Adagrasib (KRAS-G12C 특이) → KRAS-G12C에서 명확히 분리.
5. **Cell cycle 표현형**: Palbociclib G1, Dinaciclib G2/M arrest 등 phase 특이성 회복.
6. **AI 학습 목적 명시**: Arc Virtual Cell Atlas 풀의 한 축으로 STATE / Virtual Cell Challenge에 직접 투입.

## 방법론 및 아키텍처

| 항목 | 내용 |
|---|---|
| Cell count | 100.6M (minimal QC) / 95.6M (full QC) / 153M raw |
| Cell lines | 50 (47 분석에 충분) — 13 organ; TP53/KRAS/CDKN2A 등 driver mutation 다양 |
| Drugs | 379 unique / 1,100+ perturbations / 1,135 drug-dose combos |
| Conditions | 17,813 (cell-line × drug) |
| Plates | 14 × 96-well, 1,786 sublibrary, ~1.4T raw reads |
| Replicate | Plate 14 = Plate 6 biological replicate (matched pseudobulk r ≈ 0.975) |
| Platform | Parse GigaLab (combinatorial split-pool barcoding) |
| Deconvolution | SNP-based demuxlet (Kang 2018) |
| Embedding | scVI 10-D, tSNE on 200k subsample |
| Metric | LISI (batch), E-distance (effect 크기), Vision gene-set score |

## 결과

- 셀라인 정체성과 cell cycle phase가 변동의 주된 축, drug effect는 그 위에 layered.
- Sciplex3과 비슷한 dose-effect 추세 (높은 dose → 큰 E-distance).
- Replogle 2022 (genetic perturbation)와 비교 시 화학 perturbation은 본질적 effect 크기가 중간 정도.
- KRAS / BRAF / CDK / HDAC / 미세소관 inhibitor 모두 mechanism에 부합하는 transcriptional 시그너처 회복.
- Plate replicate Pearson median 0.975 → reproducibility 우수.

## 활용 가이드

- **Virtual cell 모델 학습용 chemical perturbation 데이터의 일순위**:
  - Replogle (genetic) + Tahoe (chemical) 조합이 Bunne 2024 AIVC 청사진의 manipulator VI 학습에 적합.
  - Arc STATE, scGPT-perturbation, GEARS-extension 등 후속 모델의 핵심 학습 자원.
- **Drug repurposing**: KRAS-G12C 처럼 driver mutation 특이 효과를 발견하는 데 적합.
- **활용 관점**:
  - 자체 cell line panel을 이 atlas의 임베딩 공간에 mapping (scArches 류) 하면 자동 약물 반응 예측 가능.
  - Cancer-only / 24h 단일 시점 한계 인지 — 임상 적용 전 organoid / primary 검증 필요.
- **데이터 한계 주의**: spheroid 내 paracrine signaling이 isolated drug effect를 다소 confound 가능.

## 관련 논문

- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] :builds_on — AIVC 학습용 데이터 요건의 직접 응답
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] :supports — 화학 perturbation vs 유전자 perturbation
- [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] :provides_data_for — CellOT 류 OT 모델의 chemical perturbation 학습원
- [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] :provides_data_for — GEARS chemical-perturbation 확장 후보
- [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] :provides_data_for — Arc Virtual Cell Atlas의 한 축
- [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] :methodologically_uses — scArches 류 reference mapping의 자연 확장
- [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] — 같은 카테고리 (virtual cell)
- [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] — 같은 카테고리 (virtual cell)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — sc-foundation 모델 — perturbation 적용
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — Geneformer in silico perturbation

## 용어집

- **Tahoe-100M** — Tahoe Therapeutics의 100M-cell 단일세포 약물 반응 atlas.
- **Mosaic / cell village** — 여러 셀라인을 같은 well에 같이 두고 동일 처리한 뒤 사후 분리하는 디자인.
- **SNP demultiplexing** — 셀라인 별 SNP 패턴으로 단일세포의 출신 셀라인 식별 (Kang 2018).
- **Parse GigaLab** — combinatorial split-pool barcoding 기반 대용량 scRNA-seq.
- **MOA (Mechanism of Action)** — 약물의 작용 기전 분류.
- **Sciplex3** — Srivatsan 2020 (Science). 종전의 화학 perturbation 단일세포 atlas, 본 atlas의 직접 비교 대상.
- **E-distance** — energy distance, 약물 효과 크기의 분포 거리.
- **LISI** — Local Inverse Simpson's Index, batch / metadata mixing 정량.
- **scVI** — single-cell VAE (Lopez 2018).
- **Vision** — gene-set 스코어링 도구 (DeTomaso 2019).
- **KRAS-G12C** — KRAS의 12번 codon 변이; Adagrasib / Sotorasib 표적.
