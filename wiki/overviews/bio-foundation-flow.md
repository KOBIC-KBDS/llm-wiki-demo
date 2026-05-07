---
title: "생명과학 기반모델 흐름 — 7개 축으로 보는 49편"
year_range: 2017-2025
papers: 49
category: overview
tags: [overview, bio-foundation, research-flow, foundation-models]
synthesis_basis:
  - wiki/foundation-perspectives/vaswani-2017-attention-is-all-you-need.md
  - wiki/foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks.md
  - wiki/dna-rna-foundation/
  - wiki/single-cell-foundation/
  - wiki/virtual-cell/
  - wiki/spatial-foundation/
  - wiki/protein-foundation/
  - wiki/medical-foundation/
synthesis_author: "Codex"
synthesis_caveat: "이 문서는 보유 wiki/source 노트만 근거로 만든 합성 탐색 문서다. 개별 사실은 연결된 논문 노트에서 확인해야 한다."
---

# 생명과학 기반모델 흐름

이 문서는 생명과학 기반모델 관련 49편을 **7개 흐름**으로 묶은 탐색 지도다. 가중치는 운영자 지정값을 따른다: **★★★ A/B/C/G**, **★★ E/F**, **★ D**. 여기서 A는 공통 모델 문법, B는 DNA/RNA, C는 단일세포와 가상세포, D는 공간 전사체, E는 단백질, F는 의료 모델, G는 기반모델 일반론이다.

## 한 줄 요약

생명과학 기반모델의 큰 줄기는 [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]]의 Transformer에서 출발한 **공통 모델 문법**이 [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] 이후 기반모델이라는 이름으로 일반화되고, DNA/RNA·단일세포·공간 전사체·단백질·의료 영역마다 **무엇을 입력 단위로 삼고, 어떤 생물학적 맥락을 보며, 어떤 실험 과제로 검증할 것인가**를 다르게 풀어 온 역사로 읽힌다.

## 시기별 진화

```
2017 ─ 공통 모델 문법
        └ Vaswani 2017 (self-attention 기반 sequence 모델)
            ↓
2021 ─ 기반모델 개념과 초기 생명과학 적용
        ├ Bommasani 2021 (기반모델이라는 문제틀)
        ├ DNABERT / Enformer (DNA 언어모델과 조절 예측)
        └ Tangram (단일세포 상태를 조직 좌표로 배치)
            ↓
2023 ─ 분야별 기반모델 확장
        ├ Geneformer / ESM-2 / Med-PaLM
        └ HyenaDNA / CellOT / RFdiffusion
            ↓
2024 ─ 규모, 멀티모달, 평가 기준의 압력
        ├ scGPT / scFoundation / Caduceus / CONCH / Med-Gemini
        └ CellCharter / HEST-1K / iSTAR / AlphaFold 3
            ↓
2025 ─ 실제 운영과 벤치마크
        ├ Evo 2 / AlphaGenome / ESM3 / MedGemma
        ├ Tahoe-100M / ARC Virtual Cell Challenge
        └ Theodoris 2025 (단일세포 기반모델 평가 관점)
```

## 7개 축의 역할

| 축 | 가중치 | 이 축이 묻는 질문 | 대표 논문 |
|---|---|---|---|
| A. 공통 모델 문법 | ★★★ | 서로 다른 생물학적 대상을 같은 표현 학습 틀로 옮길 수 있는가 | [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] |
| B. DNA/RNA | ★★★ | 서열에서 조절 예측과 genome 설계까지 갈 수 있는가 | [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]], [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] |
| C. 단일세포와 가상세포 | ★★★ | 세포 상태 표현이 자극 반응 예측으로 이어지는가 | [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]], [[virtual-cell/bunne-2024-how-to-build-the-virtual]] |
| D. 공간 전사체 | ★ | 세포 상태를 조직 위치와 이웃 세포 관계 속에서 읽을 수 있는가 | [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]], [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]] |
| E. 단백질 | ★★ | 서열 표현이 구조 예측과 단백질 설계로 이어지는가 | [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]], [[protein-foundation/hayes-2025-simulating-500-million-years-of]] |
| F. 의료 모델 | ★★ | 생물의학 모델이 의학 질의응답, 병리 이미지, 임상 추론으로 확장되는가 | [[medical-foundation/singhal-2023-towards-expert-level-medical-question]], [[medical-foundation/lu-2024-a-visual-language-foundation-model]] |
| G. 기반모델 일반론 | ★★★ | 기반모델이라는 이름 아래 무엇을 조심하고 어떻게 평가해야 하는가 | [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]], [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]] |

## 축 간 연결 지도

| 연결 | 의미 | 읽는 법 |
|---|---|---|
| A → B/E/F | Transformer가 DNA, 단백질, 의료 데이터에 맞게 입력 단위와 학습 과제를 바꾼다 | 같은 모델 문법이 생물학적 대상에 맞춰 변형되는 흐름 |
| G → 모든 축 | 기반모델의 기회, 위험, 평가 논의가 각 분야의 검증 기준으로 되돌아간다 | "큰 모델"보다 "무엇으로 검증하는가"가 중요해지는 흐름 |
| B → C | 유전체 조절 표현과 세포 상태 표현이 자극 반응 해석에서 만난다 | 유전자 조절 변화가 세포 상태 변화로 읽히는 흐름 |
| C → 가상세포 | 단일세포 표현이 반응 예측과 AIVC 벤치마크로 운영화된다 | [[overviews/virtual-cell-flow]]에서 상세화 |
| E → B/F | 단백질 구조와 설계는 genome 설계, drug discovery, 의료 추론과 접점을 만든다 | 서열에서 기능으로 가는 또 다른 흐름 |
| F → D/G | 의료 모델과 병리 모델은 멀티모달 근거와 임상적 검증 문제를 전면화한다 | 기반모델을 실제 의사결정 맥락으로 끌고 가는 흐름 |

## A. ★★★ 공통 모델 문법

출발점은 [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]]다. 이 논문은 RNN/CNN 없이 self-attention만으로 sequence-to-sequence translation을 처리하는 Transformer를 제시했고, 이후 DNA, RNA, 유전자 발현, 세포 상태, 단백질 서열, 이미지 패치, 임상 텍스트를 모두 attention 기반 표현으로 옮겨 갈 수 있는 공통 문법이 되었다.

생명과학 쪽에서는 이 문법이 분야마다 다른 형태로 변한다. DNA에서는 [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]]와 [[dna-rna-foundation/dalla-2024-nucleotide-transformer-building-and-evaluating]]처럼 nucleotide/k-mer 서열을 언어처럼 다루고, 단일세포에서는 [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]]와 [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]]가 유전자 발현 상태를 토큰화한다. 단백질에서는 [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]]와 [[protein-foundation/hayes-2025-simulating-500-million-years-of]]가 서열·구조·기능을 하나의 표현 공간으로 묶고, 의료 영역에서는 [[medical-foundation/singhal-2023-towards-expert-level-medical-question]]와 [[medical-foundation/saab-2024-capabilities-of-gemini-models-in]]가 임상 언어와 멀티모달 추론으로 확장한다.

## B. ★★★ DNA/RNA: 서열 모델에서 조절 예측까지

DNA/RNA 흐름은 **짧은 서열 언어모델 → 긴 genome 문맥 → 조절 결과 예측 → genome 규모 생성·설계 모델**로 읽을 수 있다.

1. **초기 DNA 언어모델**  
   [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]]는 DNA k-mer를 BERT식 masked language modeling에 넣어 promoter, TFBS, splice-site classification으로 전이한다. 여기서 DNA는 아직 비교적 짧은 sequence classification 문제에 가깝다.

2. **장거리 조절 예측**  
   [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]]는 CNN+Transformer로 200 kb DNA 문맥을 보고 CAGE, ChIP, DNase/ATAC track을 예측한다. DNA 기반모델 논의에서 Enformer의 위치는 "언어모델"이라기보다 **장거리 조절 예측 모델**에 가깝다.

3. **긴 문맥을 다루는 구조 경쟁**  
   [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]]는 attention 대신 Hyena operator로 1M nt 문맥을 다루고, [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]]는 Mamba 기반 bidirectional·reverse-complement equivariant DNA model을 제시한다. 이 둘은 표준 Transformer가 긴 서열에서 비용이 커지는 문제에 대한 구조적 대응이다.

4. **Nucleotide Transformer benchmark**  
   [[dna-rna-foundation/dalla-2024-nucleotide-transformer-building-and-evaluating]]는 50M-2.5B DNA transformer를 인간 reference, 3,202 human genomes, 850 multispecies genomes로 학습하고, 18개 genomics task benchmark를 통해 multispecies pretraining과 parameter-efficient fine-tuning의 가치를 보여준다.

5. **Evo 계열과 genome generation**  
   [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]]는 prokaryote 중심 genome-scale generation으로, [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]]는 모든 생명 domain과 40B/1M context scale로 확장한다.

6. **Regulatory output 통합**  
   [[dna-rna-foundation/linder-2025-predicting-rna-seq-coverage-from]]는 DNA에서 RNA-seq coverage를 직접 예측하는 Borzoi로 Enformer 라인을 잇고, [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]]은 1 Mb context와 single-bp resolution의 통합 조절 변이 모델로 정리된다.

7. **RNA 흐름**  
   [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]]와 [[dna-rna-foundation/penic-2025-rinalmo-general-purpose-rna-language]]는 DNA가 아니라 ncRNA 서열 자체를 언어모델 대상으로 삼아 RNA 구조와 기능 예측으로 간다.

## C. ★★★ 단일세포에서 가상세포까지

단일세포 흐름은 **세포 상태 표현**과 **자극 반응 예측**이 만나 가상세포로 가는 구조다.

[[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]]는 Geneformer로 30M single-cell transcriptome에서 gene rank token representation을 학습하고, network biology와 in silico perturbation으로 연결한다. [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]]는 scGPT로 33M cell 기반 generative pretraining을 수행하며 single-cell multi-omics foundation model 방향을 연다. [[single-cell-foundation/hao-2024-large-scale-foundation-model-on]]는 scFoundation으로 50M cells, 100M parameters, read-depth-aware modeling을 강조한다.

이 흐름을 현실 과제로 묶는 것은 reference mapping과 benchmark다. [[single-cell-foundation/heimberg-2024-a-cell-atlas-foundation-model]]는 SCimilarity로 atlas search/metric learning을, [[single-cell-foundation/boiarsky-2025-sctab-benchmark-for-single-cell]]는 22.2M cell cross-tissue classification benchmark를, [[single-cell-foundation/kedzierska-2025-assessing-the-limits-of-zero]]는 zero-shot single-cell foundation model의 한계를 보여준다. [[single-cell-foundation/wen-2024-cellplm-pre-training-of-cell]]은 cell을 token, tissue를 sentence로 보는 공간 인식 방향을 제안한다.

가상세포 쪽은 perturbation data와 예측 모델이 핵심이다. [[virtual-cell/replogle-2022-mapping-information-rich-genotype-phenotype]]는 genome-scale Perturb-seq atlas를 제공하고, [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]]는 GEARS로 미관측 다유전자 perturbation을 예측한다. [[virtual-cell/bunne-2023-learning-single-cell-perturbation-responses]]는 CellOT로 neural optimal transport 기반 perturbation response를 학습한다. [[virtual-cell/tahoe-2025-tahoe-100m-a-giga-scale]]는 100M cell, 1,100 drug, 50 cancer cell line perturbation atlas로 규모를 키운다.

이들을 묶는 선언문은 [[virtual-cell/bunne-2024-how-to-build-the-virtual]]다. 여기서 AIVC는 단순히 cell state embedding이 아니라 perturbation, multi-scale representation, virtual instrument가 결합된 실험 파트너로 정의된다. [[virtual-cell/arc-2025-virtual-cell-challenge-community-benchmark]]는 이를 community benchmark로 옮기려는 시도이며, [[virtual-cell/lotfollahi-2023-mapping-single-cell-data-to]]는 scArches로 reference atlas transfer learning 기반을 제공한다.

## D. ★ 공간 전사체: 조직 안에서 세포 읽기

공간 전사체 흐름의 가중치는 낮지만, 단일세포 표현이 조직 좌표와 이웃 세포 관계로 확장되는 연결점이다.

초기 기준점은 [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]]의 Tangram이다. single-cell 또는 single-nucleus RNA-seq를 조직 좌표에 정렬해 "cell type/state를 어디에 놓을 것인가"를 묻는다. [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]]의 STAGATE는 graph attention auto-encoder로 spatial domain을 찾는다.

<!-- DEMO_INSERT: spatial_virtual_cell_context -->

<div class="demo-added">

<p>가상세포 흐름과 공간 전사체 흐름은 여기서 만난다. [[overviews/virtual-cell-flow]]가 세포 자극 뒤의 반응을 예측하는 흐름이라면, spatial transcriptomics는 그 반응이 <strong>실제 조직 안에서 어디에 놓이고 어떤 이웃 세포와 함께 있는지</strong>를 설명하는 흐름이다.</p>

<p>특히 [[spatial-foundation/schaar-2025-nicheformer-a-foundation-model-for]]는 single-cell과 spatial data를 함께 학습해 cell state를 niche/region 같은 공간 label로 연결하고, [[spatial-foundation/birk-2025-nichecompass-a-method-for-the]]는 이웃 세포 신호를 gene program 단위로 해석한다. [[spatial-foundation/zhang-2024-inferring-super-resolution-tissue-architecture]]와 [[spatial-foundation/jaume-2024-hest-1k-a-dataset-for]]는 H&E 조직 이미지와 ST를 연결해, perturbation response를 조직 구조와 함께 읽을 수 있는 기반을 제공한다. 따라서 공간 전사체 흐름은 가상세포의 반응 예측을 실제 조직 맥락에서 생물학적으로 의미 있게 만드는 보강축이다.</p>

</div>

## E. ★★ 단백질: 서열, 구조, 설계

단백질 흐름은 **서열 표현 → 구조 예측 → 설계·생성 → 구조 인식 언어모델**로 정리된다.

[[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]]는 ESM-2/ESMFold로 MSA 없이 단일 protein sequence에서 structure까지 가는 representation line을 만든다. [[protein-foundation/abramson-2024-accurate-structure-prediction-of-biomolecular]]는 AlphaFold 3로 protein, nucleic acid, ligand 등 biomolecular interaction을 Pairformer와 diffusion으로 통합한다. [[protein-foundation/watson-2023-de-novo-design-of-protein]]는 RFdiffusion으로 structure/function design을 diffusion 문제로 바꾼다.

생성형 protein language model은 [[protein-foundation/hayes-2025-simulating-500-million-years-of]]에서 ESM3로 강해진다. ESM3는 sequence·structure·function을 함께 token화하고, [[protein-foundation/su-2024-saprot-protein-language-modeling-with]]는 Foldseek 3Di token을 결합해 structure-aware protein LM으로 간다.

## F. ★★ 의료 모델: 질문답변, 병리, 임상 추론

의료 흐름은 기반모델이 생물학적 분자 수준을 넘어 **임상 의사결정, 의학 질의응답, 병리 이미지-언어 모델**로 확장되는 방향이다.

[[medical-foundation/topol-2019-high-performance-medicine-the-convergence]]는 AI와 human intelligence가 결합되는 high-performance medicine을 큰 의제로 놓는다. [[medical-foundation/singhal-2023-towards-expert-level-medical-question]]는 Med-PaLM으로 medical QA에서 LLM의 clinical knowledge encoding을 평가하고, [[medical-foundation/saab-2024-capabilities-of-gemini-models-in]]는 Med-Gemini로 multimodal medical reasoning과 long-context clinical tasks를 확장한다. [[medical-foundation/sellergren-2025-medgemma-open-medical-foundation-models]]는 open medical foundation model 흐름을 담당한다.

병리 쪽은 [[medical-foundation/lu-2024-a-visual-language-foundation-model]]의 CONCH가 기준점이다. 이 note는 계산병리용 visual-language foundation model로, 조직 이미지·공간 전사체와 의료 모델 사이의 연결점이 된다. Foundation perspective category의 [[foundation-perspectives/moor-2023-foundation-models-for-generalist-medical]]와 [[foundation-perspectives/truhn-2024-a-foundation-model-for-clinical]]도 의료 generalist와 computational pathology의 개념적 기준점으로 읽힌다.

## G. ★★★ 기반모델 일반론

기반모델 일반론은 생명과학의 여러 분야가 "foundation model"이라는 말을 공유하게 만든 배경이다.

[[foundation-perspectives/vaswani-2017-attention-is-all-you-need]]는 모델 구조의 출발점이고, [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]]는 foundation model이라는 용어와 사회·기술적 위험/기회 프레임을 제공한다. [[foundation-perspectives/wang-2023-scientific-discovery-in-the-age]]는 AI for Science 전체 분류 체계를 제공한다.

의료 쪽 큰 개념은 [[foundation-perspectives/moor-2023-foundation-models-for-generalist-medical]]가 GMAI로 잡고, single-cell 쪽 reflection은 [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]]가 benchmarking 관점에서 정리한다. 단, [[foundation-perspectives/cui-2024-a-survey-on-the-application]]와 [[foundation-perspectives/truhn-2024-a-foundation-model-for-clinical]]는 stem과 실제 PDF 내용이 맞지 않는 상태다. 현재 파일 내용은 각각 Kommu 2024 scRegNet과 Chen 2024 UNI로 정리되어 있으므로, stem rename 또는 top-of-file warning이 필요한 manual review 대상이다.

## 개념적 핵심

> "생명과학 기반모델의 공통 질문은 큰 모델을 만들었느냐가 아니라, 생물학적 대상을 어떤 단위로 읽고, 어떤 맥락을 보게 하며, 어떤 실험·벤치마크로 되돌려 검증하느냐다."

이 관점에서 DNA/RNA, 단일세포, 단백질, 의료 모델은 서로 다른 분야 목록이 아니라 같은 질문을 서로 다른 생물학적 스케일에서 푼 흐름으로 읽힌다.

## 읽는 순서

1. **공통 문법**: [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] → [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]]
2. **DNA/RNA 핵심축**: [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] → [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] → [[dna-rna-foundation/dalla-2024-nucleotide-transformer-building-and-evaluating]] → [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] → [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] → [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]]
3. **단일세포에서 가상세포로**: [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] → [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] → [[single-cell-foundation/hao-2024-large-scale-foundation-model-on]] → [[virtual-cell/roohani-2024-predicting-transcriptional-outcomes-of-novel]] → [[virtual-cell/bunne-2024-how-to-build-the-virtual]]
4. **공간 전사체 연결**: [[spatial-foundation/biancalani-2021-deep-learning-and-alignment-of]] → [[spatial-foundation/dong-2022-deciphering-spatial-domains-from-spatially]]
5. **단백질 흐름**: [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]] → [[protein-foundation/abramson-2024-accurate-structure-prediction-of-biomolecular]] → [[protein-foundation/watson-2023-de-novo-design-of-protein]] → [[protein-foundation/hayes-2025-simulating-500-million-years-of]]
6. **의료 모델 흐름**: [[medical-foundation/topol-2019-high-performance-medicine-the-convergence]] → [[medical-foundation/singhal-2023-towards-expert-level-medical-question]] → [[foundation-perspectives/moor-2023-foundation-models-for-generalist-medical]] → [[medical-foundation/saab-2024-capabilities-of-gemini-models-in]] → [[medical-foundation/lu-2024-a-visual-language-foundation-model]]

## 출처 파일

이 overview는 `wiki/dna-rna-foundation/`, `wiki/single-cell-foundation/`, `wiki/virtual-cell/`, `wiki/spatial-foundation/`, `wiki/protein-foundation/`, `wiki/medical-foundation/`, `wiki/foundation-perspectives/`의 보유 wiki note와 대응 `sources/` 파일만을 근거로 작성했다.
