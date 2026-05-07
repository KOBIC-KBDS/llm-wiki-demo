---
title: "ESM3 — 서열·구조·기능을 동시에 토큰화한 멀티모달 생성 단백질 LM"
authors: Hayes T, Rao R, Akin H, Sofroniew NJ, ..., Rives A
year: 2025
journal: Science 387:850-858
source: hayes-2025-simulating-500-million-years-of.md
category: protein-foundation
tags: [ESM3, multimodal, masked LM, generative, structure tokenization, esmGFP, alignment, DPO, EvolutionaryScale]
---

## 요약

**ESM3**는 단백질의 **서열·3D 구조·기능**을 모두 **이산 토큰 시퀀스**로 표현하고 하나의 양방향 트랜스포머(최대 **98B 파라미터**)에 통합한 **생성형 멀티모달 LM**이다. 가변 마스킹 비율의 generative MLM 손실로 학습되어, 어느 modality에서든 일부만 주어도 나머지를 채우는 임의 순서 생성이 가능하다. 핵심 데모는 **esmGFP** — 알려진 GFP와 **58% 서열 identity**만 공유하면서 형광을 유지하는 신규 단백질로, 저자들이 "약 5억 년 진화 시뮬레이션과 동등"하다고 평가한다.

## 핵심 기여

- **구조의 디스크리트 토큰화** — Invariant geometric attention 기반 VQ autoencoder로 잔기당 1개 구조 토큰. 디코더 복원 RMSD < 0.5 Å (CAMEO).
- **하나의 멀티모달 vocabulary** — sequence, structure token, SS8, SASA, InterPro/GO 키워드를 같은 입력 트랙으로 임베딩 + 합산.
- **가변-마스크율 generative MLM** — 임의 비율 마스크에서 학습되어 토큰 임의 순서로 샘플링 가능. 어떤 modality 조합으로도 prompt 가능.
- **스케일** — 1.4B / 7B / **98B** (216 transformer 블록), 학습 토큰 ~771B, 단백질 27.8억 + 구조 2.36억 + 기능 어노테이션 5.39억.
- **Programmable design** — 원자 수준 motif (Ca²⁺, 세로토닌, protease inhibitor, Mcl-1 inhibitor 결합 부위)를 fold/function 키워드와 합성한 prompt 처리. PDB·ESMAtlas·AF-DB 어디에도 매칭 없는 새로운 scaffold도 생성.
- **Alignment(DPO)이 latent capability를 깨움** — Pass@128 26.8% → 65.5% (98B), 작은 모델은 9.5%→18.8%로 향상 폭이 작음 → 큰 모델일수록 alignment 보상 효과 큼.
- **esmGFP** — 58% identity의 새 형광 단백질을 wet-lab 검증.

## 방법론 및 아키텍처

| 트랙 | 토큰화 방법 |
|---|---|
| 서열 | 표준 amino acid |
| 구조 | invariant geometric attention 인코더 + VQ → residue당 discrete token (디코더는 원자 좌표 복원) |
| 2차구조 | SS8 (8-class) |
| 표면노출 | SASA bin |
| 기능 | InterPro keyword + GO term (residue-level annotation 포함) |

```
[seq tokens] [struct tokens] [SS8] [SASA] [function]  ── embed + sum ──→ Bidirectional Transformer (216 blk)
                                                                     │
                                                                MLP heads → per-track token logits
First block: geometric attention (직접 backbone 좌표 conditioning)
```

- **목적함수**: generative MLM, 노이즈 스케줄로 마스크 비율을 매 배치마다 변동.
- **학습 데이터**: 자연 단백질 2.78B, 구조 2.36억(실험+ESMFold/AF2 예측), 기능 어노테이션 5.39억; inverse folding으로 합성 서열도 생성.
- **Alignment**: 동일 prompt에 대해 다중 생성 → ESMFold로 평가(cRMSD, pTM) → 우열 페어 → **DPO 식 preference loss**로 fine-tune.

## 결과

- **단일 서열 구조 예측**: ESM3 98B mean LDDT 0.880 > ESMFold 0.861 (CAMEO).
- **무조건 생성**: pLDDT 0.84, pTM 0.52, 평균 pairwise seq identity 0.155 — UMAP에서 자연 단백질 분포를 폭넓게 커버.
- **Out-of-distribution prompts** (인공 대칭 디자인, 보유 PDB와 TM<0.7): 평균 seq identity <20%, TM 0.48-0.52, pTM>0.85 — 새롭지만 고품질.
- **Pass@128 tertiary motif**: 1.4B/7B/98B 베이스 9.5/19.0/26.8% → 정렬 후 18.8/37.4/**65.5%**. 98B 정렬 모델은 46개 ligand motif 중 37개 해결.
- **창의적 압축**: 트립신 223→150 잔기로 줄여도 활성부 RMSD 0.73 Å.
- **esmGFP**: 58% identity의 새 형광 단백질 — "5억 년 진화" 시뮬레이션의 정량 estimate.

## 활용 가이드

- **언제 적합한가**: (1) sequence + 부분 구조 + 기능 키워드를 섞어 조건 디자인, (2) tertiary motif scaffolding, (3) 자연계와 거리가 먼 신규 변이체 탐색.
- **체크포인트 선택**: small experiments는 1.4B/7B로도 가능; 어려운 motif scaffolding은 98B + alignment가 권장.
- **에너지/비용**: 98B inference는 GPU 메모리·시간 비용 큼 → 실무에서는 7B alignment 모델이 cost-quality 균형.
- **alignment 강력 추천** — 동일 prompt-quality 평가지표로 데이터를 모아 DPO 식 fine-tune하면 큰 폭 개선.
- **검증 워크플로우**: ESM3 생성 → ESMFold/AF3 폴딩 검증 → ProteinMPNN/ESM-IF로 inverse folding → wet-lab.
- **윤리/biosecurity** — generative protein은 dual-use; 책임감 있는 release 절차 필요.

## 관련 논문

- [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]] :extends — ESM-2 → ESM3 (sequence-only → multimodal generative)
- [[protein-foundation/abramson-2024-accurate-structure-prediction-of-biomolecular]] :supports — AF3는 좌표 디퓨전, ESM3는 토큰 LM. 둘 다 멀티모달.
- [[protein-foundation/watson-2023-de-novo-design-of-protein]] :supports — RFdiffusion(좌표 디퓨전 디자인) vs ESM3(토큰 LM 디자인)
- [[protein-foundation/su-2024-saprot-protein-language-modeling-with]] :supports — SaProt도 구조 토큰(Foldseek 3Di) 사용; ESM3는 자체 VQ 인코더

- [[protein-foundation/abramson-2024-accurate-structure-prediction-of-biomolecular]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/su-2024-saprot-protein-language-modeling-with]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/watson-2023-de-novo-design-of-protein]] — 같은 카테고리 (protein foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/wang-2023-scientific-discovery-in-the-age]] — AI for Science 일반 framework

## 용어집

- **Multimodal tokenization** — 서열/구조/기능을 모두 같은 vocabulary의 이산 토큰으로 변환.
- **Invariant geometric attention** — 회전/이동 불변성을 보존하며 원자 좌표를 처리하는 attention.
- **VQ-VAE** — Vector Quantized VAE; ESM3의 구조 토크나이저가 이 계열.
- **Generative masked LM** — 마스크 비율을 가변으로 학습해 임의 순서 생성을 가능하게 만든 MLM 변형.
- **SS8 / SASA** — 8-class secondary structure / solvent accessible surface area.
- **InterPro / GO** — 단백질 도메인 DB / Gene Ontology 기능 어노테이션.
- **DPO** — Direct Preference Optimization; 우열 페어로 fine-tune.
- **Pass@128** — 128회 생성 중 1회 이상 성공한 task 비율.
- **pLDDT / pTM / cRMSD** — 예측 정확도 / template modeling score / 제약-residue RMSD.
- **esmGFP** — ESM3로 디자인한 신규 형광 단백질 (자연 GFP와 58% identity).

---

## 내 메모

> 이 섹션은 위키 운영자(KRIBB 백부경)가 직접 작성. LLM은 이 섹션을 읽기만 하고 수정하지 않는다.
