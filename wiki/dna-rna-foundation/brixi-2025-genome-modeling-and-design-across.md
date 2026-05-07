---
title: "Evo 2 — 모든 생명 도메인 게놈 위 40B/1M 파운데이션 모델 + 추론 시간 scaling"
authors: Brixi G, Durrant MG, Ku J, Poli M, ..., Hsu PD*, Hie BL*
year: 2025
journal: "bioRxiv 2025-02-18 (Science 2025 동시 발표)"
source: brixi-2025-genome-modeling-and-design-across.md
category: dna-rna-foundation
tags: [evo2, StripedHyena2, multi-hybrid, all-domains-of-life, 40B, 1M-context, sparse-autoencoder, generative-epigenomics, inference-time-scaling, arc-institute]
---

## 요약

Evo 2는 Arc Institute / NVIDIA / Stanford 컨소시엄이 만든 **40B 파라미터, 1 M 토큰 컨텍스트, 단일염기 해상도**의 완전 오픈소스 게놈 파운데이션 모델이다. **모든 생명 도메인** (박테리아·고세균·진핵·박테리오파지) 9.3T 토큰을 새 아키텍처 **StripedHyena 2** (Hyena-SE/MR/LI + rotary attention 다중 하이브리드)로 학습. zero-shot ClinVar **비코딩**·indel·splice 변이 SOTA, BRCA1 임상 변이 분류 SOTA, 그리고 **희소 자동인코더(SAE)**로 prophage·exon/intron·TFBS·α-helix 등 해석 가능한 잠재 특징 추출. **Enformer+Borzoi를 inference-time scoring으로 써서 mouse 게놈 위에 모스부호로 chromatin accessibility peak를 새기는** 첫 생물학적 inference-time scaling 사례까지 보여준다.

## 핵심 기여

1. **모든 도메인 + 1 M context** — Evo 1(prokaryote 131 k, 7B)에서 eukaryote 포함 9.3T 토큰, 1M context, 40B로 점프.
2. **StripedHyena 2 (multi-hybrid)** — Hyena-SE / Hyena-MR / Hyena-LI 세 종류 input-dependent conv + rotary attention. StripedHyena 1 대비 16k에서 1.3×, 1M에서 3× 빠름. DNA 손실도 Pareto 우세.
3. **임상 VEP SOTA (비코딩·indel·splice)** — ClinVar 비코딩 SNV/non-SNV, SpliceVarDB 엑소닉/인트로닉, BRCA1·BRCA2 saturation mutagenesis 통합. AlphaMissense는 코딩 SNV에서만 앞섬.
4. **SAE 해석성** — Batch-TopK SAE를 layer 26에 학습, prophage/CRISPR spacer/ORF/intergenic/tRNA/rRNA/α-helix/β-sheet/엑손-인트론 경계/TFBS-유사 모티프/frameshift 심각성 등 다양한 잠재 특징을 발견. 학습에 안 쓴 mammoth 게놈에도 일반화.
5. **게놈 스케일 생성** — 미토콘드리아(올바른 CDS/tRNA/rRNA 수), 600 kb 미니멀 박테리아(70% ORF가 Pfam hit, Evo 1은 18%), 330 kb 효모 III번 염색체.
6. **생물학 최초 inference-time scaling law** — Enformer+Borzoi DNase 앙상블을 scoring function으로 beam search → 모스부호 "LO", "ARC", "EVO2"를 mouse chrX 영역의 chromatin accessibility로 새김. compute–품질 log-linear 관계.
7. **완전 오픈소스** — 모델 가중치(1B/7B/40B), 학습 코드, 추론 코드, OpenGenome2 데이터 모두 공개. 자연어/비전 통틀어 가장 큰 fully-open 모델 중 하나.

## 방법론 및 아키텍처

| 요소 | 사양 |
|---|---|
| 아키텍처 | **StripedHyena 2 (multi-hybrid)** = Hyena-SE(필터 7) + Hyena-MR(필터 128) + Hyena-LI(MLP 암시) + RoPE attention |
| 파라미터 | 7B / 40B (+ 1B base) |
| 토큰화 | byte-level 단일염기 |
| 컨텍스트 | 8k → 1M (multi-stage 확장) |
| 데이터 | OpenGenome2: 9.3T 토큰 (박테리아·고세균·진핵·파지) |
| 학습 토큰 | 40B 모델 9.3T, 7B 모델 2.4T |
| 학습 비용 | 40B ≈ 2.25 × 10²⁴ FLOPS |
| 안전 제외 | eukaryote 숙주 바이러스 |
| 해석 | layer 26에 Batch-TopK SAE, 1B 토큰 |

## 결과

- **변이 효과 zero-shot**:
  - 코딩 ClinVar SNV — AlphaMissense, ESM-1b, GPN-MSA에 4-5위. **하지만**:
  - **비코딩 SNV/non-SNV ClinVar — Evo 2 최고**.
  - **인델 / non-SNV 코딩 — Evo 2 최고** (다른 모델은 점수 못 매김).
  - **SpliceVarDB 엑소닉+인트로닉 — Evo 2 최고**.
  - **BRCA1+BRCA2 (코딩+비코딩 통합) — Evo 2 최고**.
- **BRCA1 supervised on Evo 2 embeddings** — AUROC 0.94 / AUPRC 0.84 (AlphaMissense보다 높음).
- **DMS** — 단백질 ProGen2/ESM-1b 동등, **ncRNA SOTA**.
- **mRNA decay** — Evo 2 likelihood가 decay rate와 negative correlation (40B만, 7B/NT 모두 무상관).
- **엑손 분류 (8개 종 holdout)** — AUROC 0.82–0.99, NT/Evo 1 능가.
- **lncRNA essentiality** — 5개 세포주에서 NT 능가.
- **600 kb 박테리아 생성** — 70% ORF Pfam hit (Evo 1은 18%).
- **Inference-time scaling** — beam width 증가 → AUROC log-linear 향상, 0.9까지 도달.
- **모스부호 chromatin accessibility 디자인** — "LO/ARC/EVO2" 메시지를 mouse chrX:52,051,929–52,123,468에 인코딩 성공.

## 활용 가이드

- **언제 쓰나**:
  - **비코딩 변이 / indel / splice variant**의 임상 해석 → **Evo 2가 현재 가장 적합** (zero-shot).
  - lncRNA / ncRNA fitness 예측.
  - 합성 게놈 / 미토콘드리아 / 작은 박테리아 게놈 디자인.
  - 추론 시 다른 sequence-to-function 모델 (Enformer/Borzoi/AlphaFold)을 가이드로 써서 게놈/에피게놈 디자인.
- **주의점**:
  - **코딩 SNV는 AlphaMissense가 여전히 좋음**. 두 모델 같이 쓰는 게 합리적.
  - eukaryote-host 바이러스는 의도적 약점 (안전 조치).
  - 인구 집단 단위 변이는 학습 안 됨 → CADD/AlphaMissense 등과 결합 권장.
  - 40B 추론은 다중 GPU 필요. 간단한 사용은 7B 권장.
  - 생성된 eukaryotic 게놈은 자연 대비 tRNA/gene 밀도 낮음.
- **재현/접근**: 모델·데이터 모두 오픈. 웹 도구 `arcinstitute.org/tools/evo`.

## 관련 논문

- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] :builds_on — Evo 1 (prokaryote, 7B, 131k)
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] :builds_on — Hyena 계보의 시작
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] :supports — DeepMind의 1Mb single-bp 다중모달 모델. 닫힌 API + supervised regression. Evo 2와 상보적
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] :methodologically_uses — Evo 2의 inference-time guidance에 Enformer가 scoring function으로 사용됨
- [[dna-rna-foundation/linder-2025-predicting-rna-seq-coverage-from]] :methodologically_uses — Borzoi도 같이 사용됨
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] :supports — Mamba 기반 인코더 vs Evo 2의 StripedHyena 2 디코더
- [[dna-rna-foundation/alphagenome-2025-alphagenome-end-to-end-genome]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/chen-2024-interpretable-rna-foundation-model-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/linder-2025-predicting-rna-seq-coverage-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2023-hyenadna-long-range-genomic-sequence]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/penic-2025-rinalmo-general-purpose-rna-language]] — 같은 카테고리 (DNA/RNA foundation)
- [[dna-rna-foundation/schiff-2024-caduceus-bi-directional-equivariant-long]] — 같은 카테고리 (DNA/RNA foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **StripedHyena 2** — Hyena-SE/MR/LI 세 종류 conv 연산자와 rotary attention을 줄무늬로 배치한 multi-hybrid 아키텍처.
- **Multi-hybrid** — 서로 다른 연산자 종류를 한 모델에 줄무늬로 결합하는 새 패러다임.
- **Hyena-SE / MR / LI** — short-explicit(필터 7) / medium-regularized(필터 128) / long-implicit(MLP) 입력-의존 컨볼루션.
- **OpenGenome2** — Evo 2 학습 데이터셋. 모든 생명 도메인 9.3T 토큰. eukaryote 숙주 바이러스 제외.
- **Sparse Autoencoder (SAE)** — 모델 hidden activation에 wide auto-encoder + 희소성 페널티를 학습해 해석 가능한 잠재 특징을 추출.
- **Batch-TopK SAE** — 배치 단위 top-K 활성만 유지하는 SAE 변형 (Bussmann 2024).
- **Contrastive feature search** — 특정 생물학 개념(예: prophage)이 있는 시퀀스에 enrich된 SAE feature를 찾는 절차.
- **Inference-time scaling** — 추론 시 더 많은 compute를 써서 출력 품질을 향상 (beam search 등). 학습 시간 scaling과는 별개.
- **Generative epigenomics** — DNA를 생성해 그 예측된 chromatin accessibility / 후성유전 상태가 원하는 패턴을 따르게 하는 것.
- **BRCA1 saturation mutagenesis (Findlay 2018)** — BRCA1의 모든 단일 변이의 기능 결과를 측정한 골드스탠더드 데이터셋.
- **VEP** — variant effect prediction.
