---
title: "가상세포 흐름 — 자극, 반응 예측, 검증"
year_range: 2022-2025
papers: 7
category: overview
tags: [overview, bio-foundation, virtual-cell, perturbation, AIVC]
synthesis_basis:
  - wiki/virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype.md
  - wiki/virtual-cell/bunne-2023-learning-single-cell-perturbation-responses.md
  - wiki/virtual-cell/lotfollahi-2023-mapping-single-cell-data-to.md
  - wiki/virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel.md
  - wiki/virtual-cell/bunne-2024-how-to-build-the-virtual.md
  - wiki/virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale.md
  - wiki/virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark.md
synthesis_author: "Codex"
synthesis_caveat: "이 문서는 보유 wiki/source 노트만 근거로 만든 합성 탐색 문서다. 개별 사실은 연결된 논문 노트에서 확인해야 한다."
---

# 가상세포 흐름

이 문서는 단일세포 기반모델이 **세포 상태를 표현하는 모델**에서 멈추지 않고, 실제 실험처럼 "세포에 자극을 주면 무엇이 바뀌는가"를 예측하는 방향으로 이동하는 흐름을 정리한다.

> **자극 데이터 → 반응 예측 → 재사용 가능한 세포 표현 → AIVC 검증**

핵심은 모델 이름의 나열이 아니라 역할의 이동이다. 먼저 perturbation atlas가 생기고, 그 위에서 반응 예측 모델이 만들어진다. 이후 새 데이터를 계속 붙일 수 있는 표현 체계가 필요해지고, 마지막에는 community benchmark가 분야 전체를 같은 문제로 묶는다.

## 시기별 진화

```
2022 ─ 자극 데이터와 genotype-phenotype 지도
        └ Replogle 2022 (2.5M cells, genome-scale CRISPRi Perturb-seq)
            ↓
2023 ─ 반응 예측과 atlas 재사용
        ├ CellOT (대조군 분포 → 자극 후 분포)
        └ scArches (새 데이터 → reference atlas)
            ↓
2024 ─ 미관측 perturbation 예측과 AIVC 문제정의
        ├ GEARS (실험하지 않은 단일/다중 유전자 perturbation)
        └ Bunne 2024 (Universal Representation + Virtual Instrument)
            ↓
2025 ─ 대규모 자원과 공동 벤치마크
        ├ Tahoe-100M (100M cells, 1,100 drugs, 50 cancer cell lines)
        └ ARC Virtual Cell Challenge (blind test, leaderboard, STATE baseline)
```

## 연구흐름 7단계 읽기틀

논문 요약 노트의 7개 섹션처럼, 흐름 문서도 일정한 질문 틀로 읽을 수 있다. 이 문서에서는 아래 7단계를 가상세포 흐름에 적용한다.

| 단계 | 이 흐름에서의 질문 | 관련 논문 |
|---|---|---|
| 1. 문제정의 | 가상세포가 풀어야 할 과제는 무엇인가 | [[virtual-cell/bunne-2024-how-to-build-the-virtual]] |
| 2. 데이터 | 모델이 배울 perturbation 관측값은 어디서 오는가 | [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]], [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] |
| 3. 표현 | 새 데이터셋을 기존 cell atlas와 어떻게 이어 붙이나 | [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] |
| 4. 조작 모델 | 자극 전후 cell state 변화를 어떻게 예측하나 | [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]], [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] |
| 5. 확장성 | genetic perturbation에서 chemical perturbation까지 커질 수 있나 | [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] |
| 6. 검증 | 서로 다른 모델을 같은 조건에서 비교할 수 있나 | [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] |
| 7. 다음 연구 질문 | 예측 결과가 cell line, state, assay, disease context를 넘어 일반화되나 | 아래 "다음 연구 질문" |

## 1. 자극 데이터

가상세포 흐름의 출발점은 모델 구조가 아니라 **세포를 건드린 뒤 단일세포 수준으로 읽어낸 데이터**다.

[[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]]는 K562와 RPE1에서 전 발현 유전자를 CRISPRi로 knockdown하고 single-cell RNA-seq로 읽은 2.5M cell 규모 Perturb-seq atlas다. 이 note에서 중요한 점은 Replogle atlas가 이후 GEARS, CellOT, scGPT, Geneformer, scFoundation, STATE 같은 하류 모델의 학습/평가 표준 데이터 문법이 된다는 것이다.

[[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]]는 이 데이터 축을 chemical perturbation으로 확장한다. 100M cell, 1,100 drug, 50 cancer cell line 규모의 atlas로, drug response와 cell-line context를 대규모로 제공한다. Replogle이 genetic perturbation의 기준점이라면, Tahoe-100M은 chemical perturbation을 대규모로 키운 기준점이다.

## 2. 반응 예측 모델

Perturbation data가 쌓이면 다음 질문은 "세포 상태가 어떻게 바뀌는가"다. 여기서 두 갈래의 response model이 나온다.

[[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]]의 CellOT는 perturbation 전후 세포를 paired로 볼 수 없다는 문제에서 출발한다. 같은 세포를 control 상태와 perturbed 상태로 동시에 측정할 수 없기 때문에, 실제 데이터는 control cell distribution과 perturbed cell distribution으로 주어진다. CellOT는 이 둘 사이의 optimal transport map을 학습해, perturbation이 세포 집단의 상태 공간을 어떻게 이동시키는지 모델링한다.

[[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]]의 GEARS는 한 발 더 나아가 실험하지 않은 단일/다중 유전자 perturbation 결과를 예측한다. Gene Ontology graph와 gene coexpression graph를 GNN으로 결합해, 학습 때 보지 못한 유전자 조합도 예측하려는 모델이다.

CellOT와 GEARS는 같은 문제를 푸는 중복 모델이 아니다. CellOT는 **세포 상태 분포의 이동**을, GEARS는 **실험하지 않은 perturbation 조합의 외삽**을 맡는다. 둘을 합치면 virtual experiment의 핵심 조작 도구, 즉 AIVC의 manipulator가 된다.

## 3. 재사용 가능한 세포 표현

가상세포가 실험 하나에 맞춘 predictor로 끝나지 않으려면, 새 데이터와 새 맥락을 계속 붙일 수 있어야 한다.

[[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]]의 scArches는 기존 single-cell reference atlas에 새 query dataset을 raw data 공유 없이 mapping하는 transfer-learning framework다. 의료/임상 데이터처럼 raw data 공유가 어렵거나, 여러 study가 계속 추가되는 상황에서 reference를 유지하며 새 데이터를 붙일 수 있다는 점이 핵심이다.

[[virtual-cell/bunne-2024-how-to-build-the-virtual]]에서 AIVC는 Universal Representation(UR)과 Virtual Instrument(VI)의 조합으로 정의된다. 이 관점에서 scArches는 새 데이터를 reference representation에 붙이는 방법이고, CellOT/GEARS는 그 표현 위에서 세포 상태를 조작하거나 예측하는 VI 사례다.

## 4. AIVC 검증 체계

마지막 단계는 분야 차원의 운영화다. 좋은 모델이 많아지는 것만으로는 충분하지 않고, 누가 어떤 조건에서 얼마나 잘 예측하는지 비교할 수 있어야 한다.

[[virtual-cell/bunne-2024-how-to-build-the-virtual]]는 AIVC를 단일 거대 모델이 아니라, multi-scale Universal Representation과 Virtual Instrument가 결합된 실험 파트너로 정의한다. 이 perspective는 "virtual cell을 어떻게 만들어야 하는가"라는 문제정의 역할을 한다.

[[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]]는 이 구상을 community benchmark로 바꾼다. 300,000 H1 hESC와 300 genetic perturbation 데이터셋, blind test, leaderboard, STATE baseline을 통해 virtual cell 성능을 비교 가능한 문제로 만든다.

여기서 흐름이 완성된다. 가상세포는 더 이상 "좋은 embedding"만을 뜻하지 않는다. **데이터를 만들고, perturbation을 예측하고, reference에 붙이고, benchmark에서 검증하는 운영 체계**가 된다.

## 각 논문의 역할

| 논문 | 이 흐름에서의 역할 | 왜 필요한가 |
|---|---|---|
| [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] | genetic perturbation data | 모델이 배울 genotype-phenotype 대응을 제공 |
| [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] | chemical perturbation data | drug response와 cell-line context를 대규모로 제공 |
| [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] | response model | perturbation 전후 cell distribution 이동을 예측 |
| [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] | extrapolation model | 실험하지 않은 유전자 조합을 in silico로 탐색 |
| [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] | reusable representation | 새 데이터를 기존 atlas에 붙여 재사용 가능하게 만듦 |
| [[virtual-cell/bunne-2024-how-to-build-the-virtual]] | AIVC blueprint | UR + VI라는 전체 architecture를 제시 |
| [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] | benchmark operation | blind test와 leaderboard로 분야 차원의 진전을 측정 |

## 관계 지도

| 연결 | 관계 | 의미 |
|---|---|---|
| [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] → [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] | data → response model | Perturb-seq류 데이터가 state 이동 예측의 기반 |
| [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] → [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] | data → extrapolation | genetic perturbation atlas가 GEARS류 외삽 모델의 문제공간을 제공 |
| [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] ↔ [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] | complementary manipulator | CellOT는 state 이동, GEARS는 unseen perturbation 조합에 강점 |
| [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] → [[virtual-cell/bunne-2024-how-to-build-the-virtual]] | representation → AIVC | reference mapping이 Universal Representation 운영의 기반 |
| [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] → [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] | scale-up resource → benchmark | 대규모 perturbation atlas가 challenge 학습 데이터 풀의 한 축 |
| [[virtual-cell/bunne-2024-how-to-build-the-virtual]] → [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] | blueprint → operation | AIVC 구상이 community challenge와 평가 체계로 이동 |

## 다른 생명과학 기반모델 흐름과 만나는 지점

- **단일세포 기반모델 → 가상세포**: [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]], [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]], [[single-cell-foundation/hao-2024-large-scale-foundation-model-on]]는 cell state representation의 기반을 제공하고, 가상세포 흐름은 그 표현을 perturbation response prediction으로 밀어붙인다.
- **평가 문화 → 가상세포**: [[single-cell-foundation/boiarsky-2025-sctab-benchmark-for-single-cell]]와 [[single-cell-foundation/kedzierska-2025-assessing-the-limits-of-zero]]가 single-cell foundation model의 평가 문제를 드러낸다면, [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]]는 perturbation prediction을 community benchmark로 운영하려는 쪽이다.
- **기반모델 일반론 → 가상세포**: [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]]의 single-cell foundation model 반성은 AIVC가 단순 embedding보다 task-grounded benchmark를 필요로 한다는 점과 맞물린다.

## 다음 연구 질문

현재 문서는 의도적으로 **세포 자극과 반응 예측** 중심으로 정리되어 있다. 이 관점에서 자연스러운 다음 질문은 다음과 같다.

> 이 perturbation response가 cell line, cell state, assay, disease context를 넘어 얼마나 일반화되는가?

시연에서는 이 질문을 더 밀어붙여, 보유 wiki의 다른 category 논문을 연결해 virtual cell 흐름을 보강할 수 있다.

<!-- DEMO_INSERT: virtual_cell_tissue_context -->

<div class="demo-added">

<h2>5. 조직 맥락: 세포 반응이 실제 조직에서 갖는 의미</h2>

<p>가상세포 흐름은 지금까지 <strong>세포에 자극을 주면 어떤 molecular state로 바뀌는가</strong>를 잘 다뤘다. 하지만 실제 생물학에서는 같은 세포라도 조직 안의 위치, 주변 이웃 세포, 미세환경, 조직 구조에 따라 반응의 의미가 달라질 수 있다. 그래서 다음 단계의 질문은 단순히 "세포가 변했는가"가 아니라 <strong>그 변화가 어떤 조직 자리에서, 어떤 이웃 관계 속에서 일어났는가</strong>가 된다.</p>

<p>이 약점을 보완하는 축이 spatial transcriptomics다. [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]]는 dissociated single-cell과 spatial transcriptomics를 함께 학습해, cell state를 niche, region, density, composition 같은 공간 task로 연결한다. 이 논문은 가상세포가 예측한 반응을 조직 안의 위치와 이웃 구성이라는 언어로 다시 해석하게 해준다.</p>

<p>[[spatial-foundation/birk-2025-nichecompass-a-method-for-the]]는 한 걸음 더 나아가 niche를 <strong>signaling pathway 단위</strong>로 해석한다. 이 관점에서는 perturbation response가 단일 세포 내부 변화에 그치지 않고, ligand-receptor, transcriptional regulation, neighborhood-side signal처럼 이웃 세포와 주고받는 신호 변화로 읽힌다. 가상세포의 response model이 "세포 상태 이동"을 말한다면, NicheCompass는 그 이동이 어떤 공간 신호 프로그램과 연결되는지 설명하는 보조축이 된다.</p>

<p>이미지와 조직 구조 쪽에서는 [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]]와 [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]]가 중요하다. iSTAR는 H&E 조직 이미지와 ST를 결합해 spot보다 높은 해상도의 gene expression을 추정하고, HEST-1K는 H&E + ST paired sample과 pathology foundation model benchmark를 제공한다. 즉, perturbation response를 단일세포 expression 변화로만 보지 않고, 실제 조직 이미지와 발현 지도를 함께 놓고 해석할 수 있는 기반을 만든다.</p>

<p>따라서 가상세포 흐름의 보강된 구조는 다음처럼 읽을 수 있다.</p>

<blockquote><p><strong>자극 데이터 → 반응 예측 → 재사용 가능한 세포 표현 → AIVC 검증 → 조직 맥락에서 해석</strong></p></blockquote>

<p>이 마지막 단계는 가상세포가 실험실 plate 안의 평균적 세포 반응을 넘어, 실제 조직 안에서 생물학적 의미를 갖는 반응을 구분하기 위한 단계다.</p>

</div>

## 읽는 순서

1. [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] — perturbation data의 출발점
2. [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]] — response model: state 이동 예측
3. [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] — response model: unseen perturbation 조합 예측
4. [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]] · [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] — reusable representation과 scale-up
5. [[virtual-cell/bunne-2024-how-to-build-the-virtual]] · [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] — AIVC blueprint와 benchmark 운영화
6. [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] · [[spatial-foundation/birk-2025-nichecompass-a-method-for-the]] · [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]] · [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]] — 조직 위치, 이웃 세포, 조직 이미지로 response의 biological relevance 보강

## 개념적 핵심

> "가상세포는 세포를 예쁘게 embedding하는 모델이 아니라, perturbation을 가상으로 해보고 wet-lab 의사결정으로 되돌려주는 운영 체계다."

## 출처 파일

이 overview는 `wiki/virtual-cell/`의 보유 wiki note와 대응 `sources/` 파일만을 근거로 작성했다. 개별 claim은 위 `synthesis_basis`의 paper note로 돌아가 확인해야 한다.

## 관련 흐름

- [[overviews/bio-foundation-flow]] :extends — 생명과학 기반모델 7개 흐름 중 가상세포 축을 상세화.
- [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]] :provides_data_for — perturbation 예측 모델이 학습할 표준 데이터 기반.
- [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]] :provides_data_for — chemical perturbation scale-up resource.
- [[virtual-cell/bunne-2024-how-to-build-the-virtual]] :builds_on — AIVC 문제정의의 기준점.
- [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]] :supports — virtual cell community benchmark.
- [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]] :supports — virtual cell response를 niche/region/density/composition 맥락으로 해석.
- [[spatial-foundation/birk-2025-nichecompass-a-method-for-the]] :supports — 이웃 세포 신호와 gene program 단위로 tissue context를 설명.
- [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]] :supports — H&E와 ST를 결합해 조직 구조와 gene expression을 함께 읽음.
- [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]] :provides_data_for — H&E + ST paired dataset과 pathology benchmark 기반을 제공.
