---
title: "CellPLM — 세포를 토큰으로, 조직을 문장으로 보는 spatial-aware 단일세포 LM"
authors: Wen H, Tang W, Dai X, Ding J, Jin W, Xie Y, Tang J*
year: 2024
journal: "ICLR 2024 / bioRxiv 2023.10.03.560734"
source: wen-2024-cellplm-pre-training-of-cell.md
category: single-cell-foundation
tags: [CellPLM, cell language model, spatial transcriptomics, Gaussian mixture VAE, cell-cell relations, Flowformer, batch-aware decoder]
---

## 요약

**CellPLM**은 기존 sc-foundation의 "유전자=토큰, 세포=문장" 패러다임을 뒤집어 **세포를 토큰, 조직(또는 SRT FOV)을 문장**으로 본다. 마스킹된 발현값의 분포를 같은 cell 안의 유전자뿐 아니라 **다른 cell의 unmasked 발현값**에 조건짓는 *cell language model*을 정의하고, scRNA-seq + spatially-resolved transcriptomic(SRT) atlas를 함께 사전학습한다. 추가로 **Gaussian Mixture latent prior**, **batch-aware decoder**, **Flowformer 선형 attention**을 도입해 ~10⁴ cell tokens / sentence를 다루며 기존 gene-token foundation 대비 **추론 속도 100×**를 보이고 5개 다운스트림에서 일관된 우위를 보고한다.

## 핵심 기여

- **Cell language model** — `p(X_i,j | 다른 cell의 unmasked 발현)`로 정식화, **세포 간 관계**를 명시적으로 인코딩한 첫 sc-foundation transformer.
- **scRNA-seq + SRT 동시 사전학습** — SRT의 2D 좌표를 sinusoidal positional encoding으로 입력 → cell-cell communication, lineage 단서 학습.
- **Gaussian Mixture latent prior** — isotropic Gaussian 대신 functional cell group을 표현하는 mixture VAE.
- **Bag-of-genes embedder** — gene embedding의 sparse weighted sum으로 cell embedding 초기화 (scRNA-seq의 비순차 본질 반영).
- **Flowformer (linear attention)** — ~10⁴ cell tokens 처리 가능 + **추론 속도 100× 향상**.
- **Batch-aware decoder** — 학습형 batch embedding을 decoder 입력에 추가 → latent에서 batch effect 제거 (scVI 영감).

## 방법론 및 아키텍처

| 축 | 내용 |
|---|---|
| 토큰화 | Token = 세포; sentence = tissue / SRT FOV |
| Gene embedder | E_i = Σ_j X_{i,j} · h_j (sparse weighted sum) |
| Positional encoding | SRT는 2D sinusoidal, scRNA-seq은 공유 학습 벡터 |
| Encoder | Flowformer (linear attention) L 층 |
| Latent | Gaussian Mixture VAE, L 컴포넌트, 학습형 mixture parameters |
| Decoder | z + batch embedding b → FF layer stack → 발현 재구성 |
| 사전학습 | Cell-level masked language modeling, 측정된 gene만 마스킹/loss, MSE + ELBO terms (recon + cond + Y) |
| Fine-tune | 전체 파라미터 fine-tune; 5 task (annotation, scRNA imputation, SRT imputation, perturbation, denoising) |

## 결과

- 5개 다운스트림(annotation, scRNA imputation, SRT imputation, perturbation, denoising)에서 **scBERT, scGPT, Geneformer, tGPT 및 비-pretrain 베이스라인 모두 일관 능가**.
- **추론 속도 100×** (gene-token 대비) — cell-token 변환과 Flowformer 선형 attention 결합 효과.
- GMM prior와 SRT 사전학습 ablation 둘 다 cell-type 클러스터링 메트릭 향상에 기여.
- SRT imputation에서 scRNA-only foundation이 약한 영역에서 분명한 우위.

## 활용 가이드 / 임상적 의의

- **Spatial transcriptomic + scRNA-seq 혼합 분석**(Visium / MERFISH / Xenium)에 자연스럽게 적용 가능.
- **Tissue 단위 inter-cellular 관계** 모델링이 핵심 — niche, immune-stromal cross-talk, tumor microenvironment 같은 응용에서 강점.
- **Inference 속도** 우위 → 큰 cohort에 즉시 적용 가능, fine-tuning 비용도 낮음.
- 단, SRT 데이터 부재 시 cell-token의 위치 grounding 효과는 일부 손실; SRT 코퍼스 자체가 아직 작은 점이 한계.

## 관련 논문

- [[single-cell-foundation/boiarsky-2025-sctab-benchmark-for-single-cell]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/hao-2024-large-scale-foundation-model-on]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/heimberg-2024-a-cell-atlas-foundation-model]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/kedzierska-2025-assessing-the-limits-of-zero]] — 같은 카테고리 (single-cell foundation)
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] — 같은 카테고리 (single-cell foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본 (architecture anchor)
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model terminology anchor
- [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]] — sc-foundation 벤치마킹 perspective

## 용어집

- **Cell language model** — p(X_i,j | 다른 cell의 unmasked 발현)으로 정식화한 사전학습; intercellular 관계 모델링.
- **Gene language model** — 기존 sc-foundation의 gene-token 식 정식화.
- **SRT (spatially-resolved transcriptomics)** — 2D 위치 정보를 동반한 단일세포 RNA 측정 기술.
- **FOV (field of view)** — SRT 한 image patch, 본 모델에선 sentence 단위.
- **Bag-of-genes embedder** — gene 임베딩의 sparse 가중합으로 cell 임베딩 생성.
- **Flowformer** — 선형 복잡도 transformer, ~10⁴ tokens 가능.
- **Gaussian Mixture VAE** — latent prior가 mixture of Gaussians (cluster-aware).
- **Denoising variational lower bound** — masked input의 reconstruction을 위한 ELBO 변형.
- **Batch-aware decoder** — decoder에 batch embedding을 주입해 latent를 batch-effect-free로 유지.
- **2D sinusoidal PE** — 2차원 좌표축 각각에 sinusoidal positional encoding 적용.
