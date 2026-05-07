---
title: "SaProt — Foldseek 3Di 토큰을 결합한 구조 인지 단백질 LM"
authors: Su J, Han C, Zhou Y, Shan J, Zhou X, Yuan F
year: 2024
journal: ICLR 2024 (bioRxiv 2023.10.01.560349)
source: su-2024-saprot-protein-language-modeling-with.md
category: protein-foundation
tags: [SaProt, structure-aware, Foldseek, 3Di, ESM, structure tokenization, protein LM, ICLR]
---

## 요약

**SaProt**는 ESM-2 백본(650M)을 그대로 쓰면서 **입력 vocabulary**만 바꾼 모델이다. 잔기마다 (1) 표준 amino acid 토큰과 (2) **Foldseek 3Di 구조 토큰**을 결합한 **SA(structure-aware) 토큰** 한 글자로 표현하고, 이걸로 BERT 식 MLM을 학습한다. 결과적으로 그래프 구조 모델(GNN)이나 contact-bias attention 같은 별도 아키텍처 없이도 **순수 시퀀스 형태로 3D 구조 정보를 주입**할 수 있고, **10개 다운스트림 task**에서 ESM-1b/1v/ESM-2를 일관되게 능가한다.

## 핵심 기여

- **SA 토큰 어휘** — 20 amino acid × 20 3Di state ≈ 441개 토큰. 잔기 한 자리에 시퀀스+구조를 함께 인코딩.
- **트랜스포머 호환성** — 구조를 그래프가 아니라 1D 토큰으로 표현 → BERT/ESM 파이프라인에 그대로 plug-in. GNN의 over-smoothing 회피.
- **대규모 예측 구조 활용** — UniRef50 + AlphaFold-DB **약 40M 예측 구조**로 훈련 (기존 GearNet ~800K 대비 50배).
- **10개 task SOTA** — clinical variant pathogenicity (ClinVar), 기능 적합도 (ProteinGym, FLIP), PPI, 위치(DeepLoc), EC, GO, thermostability, metal binding, stability.
- **설계 인사이트** — AF2 contact-bias attention을 ESM-2 BERT 학습에 그냥 붙이면 **AF2 idiosyncrasy로 overfit**. 이산 SA 토큰 방식이 이 문제를 회피한다는 ablation 제공.
- **공개성** — 추론뿐 아니라 **학습 코드까지 공개** (ESM 시리즈 대비 차별점).

## 방법론 및 아키텍처

| 구성 | 내용 |
|---|---|
| 구조 토크나이저 | **Foldseek 3Di** VQ-VAE (잔기당 20-state) |
| SA 토큰 | `Aa`, `Cc`, `Hp`, … (residue × 3Di 결합); 구조 모를 때는 `#` placeholder |
| Vocabulary 크기 | 20 × 20 + special ≈ 441 |
| Backbone | ESM-2 (650M) BERT-style transformer |
| 학습 | Masked LM (mask 15%), 토큰 정체 예측 |
| 데이터 | UniRef50 + AlphaFold-DB ≈ **40M 예측 구조** + PDB |
| 연산 | 64 × A100 80G × ~3개월 (ESM-1b 비용 수준) |

```
PDB / AF2 구조  ──(Foldseek)──→  3Di 토큰
                                   │
seq + 3Di  ──concat──>  SA token sequence  ──(ESM-2 BERT MLM)──>  예측 SA token
```

## 결과

- **ClinVar (clinical pathogenicity)**: ESM-1v 대비 zero-shot 성능 향상.
- **ProteinGym / FLIP (fitness)**: 대부분 assay에서 ESM-2 우위.
- **DeepLoc, EC, GO** 등 기능 분류: ESM-2 대비 일관된 개선.
- **Thermostability / metal binding / stability**: 시퀀스-only 베이스 대비 Spearman 상관 향상.
- **Ablation 핵심**: AF2 contact bias 직접 사용은 overfit; SA 이산 토큰은 robust.

## 활용 가이드

- **언제 적합한가**: (1) 변이 효과/적합도 zero-shot 예측 (구조가 있을 때), (2) 기능 분류 (localization, EC, GO), (3) 기존 ESM 파인튜닝 파이프라인에 구조 정보를 한 줄로 주입하고 싶을 때.
- **입력 만들기**: PDB or AF2 구조 → Foldseek로 3Di 토큰 추출 → 잔기 단위 SA 토큰 합성. 구조가 없거나 신뢰도 낮으면 `#` placeholder로 sequence-only 모드 동작.
- **체크포인트**: SaProt-650M (HuggingFace `westlake-repl/SaProt_650M_AF2`).
- **주의**: AF2 예측 구조에 의존 → 디스오더 영역의 AF2 편향이 함께 학습됨. 구조 신뢰도(pLDDT) 필터링을 권장.
- **상보적 사용**: 디자인 단계는 RFdiffusion/ESM3 → SaProt로 fitness/안정성 점수화.

## 관련 논문

- [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]] :builds_on — ESM-2 아키텍처를 그대로 사용
- [[protein-foundation/hayes-2025-simulating-500-million-years-of]] :supports — ESM3는 자체 invariant geometric VQ로 구조 토큰 (3Di 대비 풍부); 같은 "구조→이산 토큰" 철학
- [[protein-foundation/abramson-2024-accurate-structure-prediction-of-biomolecular]] :methodologically_uses — AF3/AF2가 만든 구조가 학습 입력으로 활용됨
- [[protein-foundation/watson-2023-de-novo-design-of-protein]] :methodologically_uses — RFdiffusion 디자인의 fitness 평가에 활용 가능

- [[protein-foundation/abramson-2024-accurate-structure-prediction-of-biomolecular]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/hayes-2025-simulating-500-million-years-of]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/watson-2023-de-novo-design-of-protein]] — 같은 카테고리 (protein foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/wang-2023-scientific-discovery-in-the-age]] — AI for Science 일반 framework

## 용어집

- **SA token** — Structure-Aware token; amino acid + Foldseek 3Di state 결합.
- **Foldseek** — 빠른 구조 검색 도구; 3Di 알파벳을 도입.
- **3Di** — local 3D geometry를 20-state로 이산화한 표현.
- **VQ-VAE** — Vector Quantized VAE; 연속 입력을 이산 코드로 인코딩.
- **MLM** — masked language modeling; BERT 식 학습 목표.
- **AlphaFold-DB** — AF2 예측 구조 200M+ 보관소.
- **ProteinGym / FLIP / ClinVar** — 변이 효과 / 적합도 / 임상 pathogenicity 벤치.
- **DeepLoc / EC / GO** — 위치 / 효소 분류 번호 / 기능 어노테이션.
- **Inverse folding** — 백본을 받아 서열을 생성 (ESM-IF1, ProteinMPNN).
- **Over-smoothing** — GNN이 깊어질수록 노드 표현이 비슷해져 변별력을 잃는 현상; SaProt의 "1D 토큰" 선택 동기.

---

## 내 메모

> 이 섹션은 위키 운영자(KRIBB 백부경)가 직접 작성. LLM은 이 섹션을 읽기만 하고 수정하지 않는다.
