---
title: "Borzoi — DNA→RNA-seq 커버리지를 직접 예측하는 통합 조절 모델"
authors: Linder J, Srivastava D, Yuan H, Agarwal V, Kelley DR*
year: 2025
journal: "Nature Genetics 57:949–961"
source: linder-2025-predicting-rna-seq-coverage-from.md
category: dna-rna-foundation
tags: [borzoi, RNA-seq, polyadenylation, splicing, U-net, CNN, attention, calico, variant-effect, enformer-successor]
---

## 요약

Borzoi는 Enformer를 확장해 **524 kb DNA → 32 bp 해상도 RNA-seq 커버리지**를 직접 예측하는 모델이다. CNN 트렁크 + self-attention(8층, 128 bp) + **U-net 업샘플링**으로 엑손/인트론 패턴, 대체 TSS, 대체 polyadenylation을 한 모델에서 모두 학습한다. ENCODE 866개(인간) + 279개(마우스) RNA-seq + recount3 GTEx 여러 조직 + Enformer의 모든 보조 트랙을 함께 학습. 결과적으로 **eQTL·sQTL·paQTL 모두에서 전용 모델과 경쟁**하며, 조직 특이적 cis-regulatory 모티프 지도를 attribution으로 만들 수 있다.

## 핵심 기여

1. **DNA→RNA-seq 커버리지 직접 회귀** — TSS 단일 카운트가 아닌 엑손/인트론 패턴 자체를 예측 → 동시에 transcription·splicing·APA·RNA 안정성을 모델링.
2. **524 kb × 32 bp** — Enformer(200 kb × 128 bp) 대비 입력 ~2.6배, 해상도 4배.
3. **U-net 디코더** — self-attention(128 bp)에서 conv-tower skip을 받아 32 bp까지 업샘플.
4. **변이 다층 점수화** — 발현, 스플라이싱, 폴리아데닐레이션을 한 모델에서 분리 점수화. 전용 eQTL/sQTL/paQTL 모델과 경쟁.
5. **조직 특이 TF 모티프 지도** — saliency + TF-MoDISco가 SPI1/IRF(혈액), HNF4A(간), SOX9/REST(뇌), MYOD1/MEF2D(근육) 등 알려진 조절자를 회수.

## 방법론 및 아키텍처

| 요소 | 사양 |
|---|---|
| 입력 | 524,288 bp (hg38/mm10) |
| 트렁크 | Conv + downsampling → 128 bp 해상도 |
| 본체 | Self-attention 8층, 상대 위치 인코딩 |
| 디코더 | **U-net 업샘플 (skip + 잔차 conv)** → 32 bp |
| 헤드 | 인간 ~7,611 + 마우스 ~2,608 트랙 |
| 학습 데이터 | RNA-seq 866(H)+279(M, ENCODE) + GTEx replicate(recount3) + Enformer 트랙 |
| 손실 | per-track Poisson NLL |
| 앙상블 | 4 random replicates 평균 |

## 결과

- **RNA-seq Pearson r (bin-level)** — 평균 0.74 단일 / 0.75 앙상블.
- **Gene-level Pearson** — 0.87 (mean-subtracted 0.58, 조직 특이성 회수).
- **조직 특이 fold change Spearman** — 0.52–0.75 (혈액/간/뇌/근육/식도).
- **TSS 사용 비율 Spearman** — 0.85 (실험과), 0.29–0.50 (조직 fold).
- **APA distal/proximal Spearman** — 0.81 vs GTEx, 0.23–0.41 (조직 fold).
- **변이 영향** — 전용 eQTL/sQTL/paQTL 모델과 동등 이상.

## 활용 가이드

- **언제 쓰나** — 비코딩 변이가 발현/스플라이싱/APA 중 어느 층에 작용하는지 알고 싶을 때, 조직 특이 cis-regulatory 모티프를 시퀀스만으로 prioritize할 때.
- **주의점**:
  - **조직 특이 alternative splicing PSI는 잘 못 잡음** (저자도 Discussion에서 인정). PSI 예측은 SpliceAI/Pangolin 추천.
  - 524 kb 한계 → 그 너머는 AlphaGenome(1 Mb) / Evo 2.
  - 32 bp 해상도 → single-bp 효과는 ISM으로 보강.
  - Reference 게놈 학습 → personal expression 예측은 Enformer와 동일 한계.
- **실무 팁** — 변이 점수화 시 4-replicate 앙상블이 노이즈 안정화에 도움.

## 관련 논문

- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] :extends — Enformer 직접 후속, 같은 Calico/Kelley 라인. 입력·해상도·트랙 모두 확장
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] :extends — 1 Mb single-bp 다중모달로 Borzoi 패러다임 재흡수
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] :supports — Evo 2가 Borzoi를 inference-time guidance용 scoring function으로 사용 (epigenome 디자인)
- [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]] :parallel_in_other_disease — RNA 측면(서열→구조/기능) 보완
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/brixi-2025-genome-modeling-and-design-across]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/penic-2025-rinalmo-general-purpose-rna-language]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] — 같은 카테고리 (DNA/RNA foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **RNA-seq coverage** — 엑손/인트론 위 base별 read 수. 발현·스플라이싱·3'-end 사용 정보가 모두 들어 있음.
- **U-net** — encoder–decoder CNN with skip connections. 해상도 복원에 자주 쓰임.
- **APA** — alternative polyadenylation. proximal vs distal poly(A) 사이트 선택에 따라 3' UTR 이소형이 달라짐.
- **PSI** — percent spliced in, 특정 엑손이 포함된 전사체의 비율.
- **TSS / pA site** — 전사 개시점 / 폴리아데닐레이션 사이트.
- **TF-MoDISco** — attribution 패턴을 PWM 모티프로 클러스터링.
- **eQTL / sQTL / paQTL** — 발현 / 스플라이싱 / 폴리아데닐레이션 정량형질좌위.
- **recount3** — 표준 재처리 RNA-seq 아카이브.
- **ISM** — in-silico mutagenesis, 모든 base 치환의 효과를 모델 출력 차이로 점수화.
