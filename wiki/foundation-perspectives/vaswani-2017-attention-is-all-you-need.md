---
title: "Transformer — 'Attention Is All You Need'"
authors: Vaswani A, Shazeer N, Parmar N, Uszkoreit J, Jones L, Gomez AN, Kaiser Ł, Polosukhin I
year: 2017
journal: NeurIPS 2017
source: vaswani-2017-attention-is-all-you-need.md
category: foundation-perspectives
tags: [transformer, attention, architecture, foundation-anchor, NeurIPS-2017]
---

## 요약

Transformer는 RNN·CNN을 완전히 버리고 **self-attention**만으로 시퀀스 변환을 수행하는 아키텍처다. 핵심은 (1) **scaled dot-product attention**(`softmax(QKᵀ/√dₖ)V`), (2) **multi-head attention**(h=8개 머리 병렬), (3) **sinusoidal positional encoding**(순서 정보 주입), (4) **encoder–decoder 스택**(각 N=6 layer, residual + LayerNorm). WMT'14 EN-DE에서 BLEU 28.4로 SOTA를 갱신했고, 8× P100에서 3.5일 훈련으로 기존 최고 ensemble 대비 학습비용을 1/10 수준으로 낮췄다. 이 논문은 이후 BERT, GPT, AlphaFold, ESM, Geneformer, scGPT 등 거의 모든 foundation model의 **공용 백본**이 된다.

## 핵심 주장

- **Recurrence는 필요없다.** 시퀀스의 위치 의존성은 attention만으로 모델링 가능하다.
- **장거리 의존성**: self-attention은 임의의 두 위치를 O(1) 순차 단계로 연결 — RNN의 O(n)보다 짧다. 이는 학습이 쉬워지는 이유.
- **병렬화 가능성**: RNN이 시간축으로 직렬인 반면 self-attention은 시퀀스 전체를 한 번에 행렬곱으로 처리 — GPU 활용도가 폭발적으로 증가한다.
- **Multi-head**: 단일 attention은 평균화로 정보를 뭉갠다. 여러 head가 서로 다른 표현 부분공간(syntactic vs semantic 등)을 동시에 학습한다.
- **Scaling**: dₖ가 커지면 dot product 분산이 dₖ로 커져 softmax가 saturate. 1/√dₖ 스케일링이 그래디언트를 살린다.

## 분석 프레임워크

| 차원 | RNN/LSTM | CNN (ConvS2S) | **Transformer (self-attn)** |
|---|---|---|---|
| Layer 복잡도 | O(n·d²) | O(k·n·d²) | **O(n²·d)** |
| 순차 연산 수 | O(n) | O(1) | **O(1)** |
| 최대 path 길이 | O(n) | O(logₖ n) | **O(1)** |
| 병렬화 | 약함 | 강함 | **강함** |
| 장거리 의존성 학습 | 어려움 | 보통 | **쉬움** |

→ 시퀀스 길이 n이 표현 차원 d보다 작을 때 self-attention이 가장 유리. 기계번역의 sub-word 토큰화 환경에서 정확히 이 조건이 성립.

### 아키텍처 한 장 요약

```
Input tokens → Embedding(×√dmodel) + PE
              ↓
Encoder ×6: [MultiHeadSelfAttn → Add&Norm → FFN(dff=2048) → Add&Norm]
              ↓
Decoder ×6: [MaskedMHSelfAttn → Add&Norm → MHCrossAttn(K,V from encoder) → Add&Norm → FFN → Add&Norm]
              ↓
Linear (shared with embedding) → Softmax → next-token prob
```

## 결론 및 가이드라인

- **이 논문이 왜 Bio-Foundation 흐름의 architecture anchor인가**: 본 위키의 거의 모든 후속 foundation model — DNABERT(Ji 2021), HyenaDNA(Nguyen 2023), Evo(Nguyen 2024), AlphaFold(Jumper 2021)/AlphaFold3(Abramson 2024), ESM-2(Lin 2023)/ESM-3(Hayes 2025), SaProt(Su 2024), Geneformer(Theodoris 2023), scGPT(Cui 2024), Nicheformer(Schaar 2025), Enformer(Avsec 2021), AlphaGenome(2025) — 모두 이 Transformer 블록(혹은 변형: Hyena, Mamba, Performer)을 입력 토큰화만 바꿔 쓴다.
- **읽을 때 집중할 부분**: §3.2(Attention 정의), §3.5(Positional Encoding), §4(왜 self-attention인가의 path-length 논증). §6.2 ablation은 head 수·dₖ 선택의 직관을 준다.
- **한계 인식**: O(n²) 비용은 long-context(genome, single-cell 수만 토큰)에서 큰 문제 — 본 위키의 HyenaDNA, Caduceus, Linder 2025, Evo 등은 이 한계 극복이 핵심 동기.

## 관련 논문

- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] :builds_on — Transformer + scale + 자기지도 = "foundation model"이라는 명명/패러다임화
- [[dna-rna-foundation/ji-2021-dnabert-pre-trained-bidirectional-encoder]] :extends — DNA k-mer를 Transformer 언어모델 입력으로 옮김
- [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]] :extends — 단백질 서열 표현과 구조 예측으로 확장
- [[single-cell-foundation/theodoris-2023-transfer-learning-enables-predictions-in]] :extends — 유전자 발현 rank를 Transformer 입력으로 사용
- [[single-cell-foundation/cui-2024-scgpt-toward-building-a-foundation]] :extends — single-cell multi-omics를 generative Transformer로 모델링

## 용어집

- **Attention**: 쿼리(Q)와 키(K)의 유사도로 값(V)을 가중합하는 연산.
- **Self-attention**: Q, K, V가 모두 같은 시퀀스에서 나옴 — 시퀀스 내부 의존성 학습.
- **Multi-head**: 서로 다른 학습된 투영으로 attention을 여러 번 병렬 수행.
- **Positional encoding (PE)**: 시퀀스 순서 정보를 임베딩에 더하는 sin/cos 벡터.
- **Masked attention**: decoder에서 미래 위치를 −∞로 가려 autoregressive 보장.
- **Residual + LayerNorm**: 깊은 네트워크의 그래디언트 흐름과 수렴 안정화 기본 블록.
- **BLEU**: 기계번역 평가 지표(n-gram 일치).
- **dmodel / dff**: 임베딩 차원 / FFN 내부 차원.
