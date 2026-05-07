---
title: "Caduceus — Mamba 기반 양방향·역상보 등변(RC equivariant) DNA 모델"
authors: Schiff Y, Kao C-H, Gokaslan A, Dao T, Gu A, Kuleshov V*
year: 2024
journal: "ICML 2024 (PMLR 235)"
source: schiff-2024-caduceus-bi-directional-equivariant-long.md
category: dna-rna-foundation
tags: [caduceus, mamba, SSM, BiMamba, reverse-complement, equivariance, MLM, variant-effect, long-range]
---

## 요약

Caduceus는 DNA 시퀀스가 가진 두 가지 고유 제약 — **양방향 컨텍스트** (조절요소가 상하류 모두에서 작용)와 **두 가닥의 역상보 동일성** (RC 동치) — 을 모델 구조 자체에 박아 넣은 첫 DNA 파운데이션 모델군이다. 기반은 **Mamba** (선택적 SSM)이며, 양방향용 BiMamba와 RC 등변(equivariant) 래퍼 MambaDNA를 새로 정의한다. 인간 게놈 MLM 사전학습 후 long-range variant effect 예측에서 자기보다 10× 큰 transformer를 이긴다.

## 핵심 기여

1. **BiMamba** — Mamba를 정방향과 역방향에 적용 후 합치되, **입출력 projection은 공유**. 메모리 거의 안 늘리고 양방향 깊이 확보.
2. **MambaDNA** — 채널을 반으로 쪼개 한쪽엔 RC를 적용하고 같은 Mamba 파라미터로 둘 다 처리한 뒤 다시 RC해서 concat. **이론적으로 RC equivariant** (Thm 3.1).
3. **Caduceus-PS** — 위 두 모듈 + RC equivariant token embedding + RC equivariant LM head. **MLM 사전학습 자체가 RC equivariant**한 첫 DNA LM.
4. **Caduceus-Ph** — BiMamba만 쓰고 RC 데이터 증강 + 추론 시 post-hoc conjoining(원본·RC 두 번 돌려 평균)으로 invariance 확보.
5. **벤치마크에서 큰 차이** — long-range VEP 태스크에서 10× 큰 비-등변 transformer를 능가; 양방향성과 RC 등변이 각각 독립적으로 MLM loss와 성능을 올림.

## 방법론 및 아키텍처

| 요소 | 사양 |
|---|---|
| 기반 연산 | Mamba (selective SSM, Gu & Dao 2023) |
| 양방향 모듈 | BiMamba (projection 공유로 파라미터 절약) |
| 등변 래퍼 | MambaDNA (채널 split + RC + 파라미터 공유 + concat) |
| 변형 1 (PS) | MambaDNA + RC equivariant embedding + RC equivariant LM head |
| 변형 2 (Ph) | BiMamba 스택, RC 데이터 증강, 추론 시 post-hoc conjoining |
| 토큰화 | 단일염기 (k-mer 거부; 입력 약간 변해도 토큰화가 크게 흔들리는 문제) |
| 사전학습 목표 | MLM (BERT 스타일 15% mask) |
| 데이터 | 인간 reference 게놈 |
| 컨텍스트 | 최대 131,072 |

## 결과

- **MLM 사전학습 손실** — Mamba > HyenaDNA (1k/32k/131k 모두). BiMamba weight tying이 추가 향상. RC equivariance가 추가 향상.
- **Genomics Benchmarks** — HyenaDNA·CNN baseline 능가 (5-fold CV).
- **Nucleotide Transformer benchmarks** — HyenaDNA, Mamba와 동등 이상.
- **Long-range VEP** (Enformer 데이터 기반) — 자신보다 ~10× 큰 transformer를 이김.

## 활용 가이드

- **언제 쓰나**: 양방향 컨텍스트 + 두 가닥 등가성이 모두 중요한 작업 (조절요소 분류, 멀리 떨어진 enhancer 효과의 변이 예측).
- **PS vs Ph**:
  - **PS**: 모델 자체로 equivariance가 보장됨. 학습 안정성과 데이터 효율 좋음. 다운스트림 invariance용으로 채널을 split-and-average.
  - **Ph**: 구조는 단순(BiMamba만), 추론에서 두 번 forward 필요.
- **주의점**:
  - 사전학습이 인간 reference만 → 종간 변이 예측은 Evo/NT 영역.
  - autoregressive 생성 불가 (MLM 전용).
  - 컨텍스트 131k → 1M 이상은 Evo 2 / HyenaDNA / AlphaGenome.

## 관련 논문

- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] :supports — Hyena vs Mamba 직접 비교; 둘 다 single-nt + 장거리. Caduceus는 양방향+RC 등변을 추가
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] :extends — 양방향이라는 점에서 BERT 계열 후예이지만 transformer가 아닌 SSM
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] :methodologically_uses — Enformer가 만든 long-range VEP 데이터셋을 평가에 사용
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] :supports — Mamba/Hyena/StripedHyena 계열의 다른 분지 (Evo는 generative + multi-genome)
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/linder-2025-predicting-rna-seq-coverage-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/penic-2025-rinalmo-general-purpose-rna-language]] — 같은 카테고리 (DNA/RNA foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **SSM (State-Space Model)** — 연속 시간 선형 미분방정식을 이산화한 시퀀스 모델. parallel scan으로 학습 효율적.
- **Mamba** — 입력 의존적 B, C, Δ를 가지는 선택적 SSM (Gu & Dao 2023). attention 없이 컨텐츠 선택 능력.
- **BiMamba** — 입출력 projection 공유로 양방향 Mamba를 효율적으로 구현.
- **MambaDNA** — BiMamba + RC equivariance를 보장하는 래퍼.
- **Reverse complement (RC)** — DNA의 상보적 가닥을 역순으로 읽은 것. 같은 정보를 담음(예: 5'-ACGT-3' ↔ 5'-ACGT-3').
- **Equivariance / Invariance** — 입력 변환 σ에 대해 f(σ(x))=σ(f(x)) (등변) / f(σ(x))=f(x) (불변). DNA에서는 σ=RC.
- **Post-hoc conjoining** — 추론 시 입력과 RC 입력을 둘 다 모델에 통과시켜 출력을 평균; RC 불변 보장.
- **MLM** — masked language modeling. 입력의 일부를 가리고 복원하는 양방향 사전학습.
- **VEP** — variant effect prediction.
