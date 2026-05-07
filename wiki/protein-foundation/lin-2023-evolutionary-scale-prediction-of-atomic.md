---
title: "ESM-2 / ESMFold — 단일 서열에서 원자 수준 단백질 구조까지"
authors: Lin Z, Akin H, Rao R, Hie B, ..., Rives A
year: 2023
journal: Science 379:1123-1130
source: lin-2023-evolutionary-scale-prediction-of-atomic.md
category: protein-foundation
tags: [ESM-2, ESMFold, protein language model, single-sequence structure prediction, metagenomic atlas, MSA-free]
---

## 요약

**ESM-2**는 8M~**15B 파라미터**까지 스케일 업한 단백질 서열 트랜스포머이고, 그 위에 폴딩 트렁크 + 구조 모듈을 얹은 것이 **ESMFold**다. 핵심 결과 한 줄: **MSA 없이 단일 서열만으로 AlphaFold2에 근접한 원자 수준 구조 예측**이 가능하며, 이를 활용해 **MGnify90의 6.17억 메타지놈 단백질**을 폴딩한 ESM Metagenomic Atlas (2.25억 high-confidence 구조)를 공개했다.

## 핵심 기여

- **스케일에 따른 구조 emergence** — 8M→15B로 키울수록 퍼플렉시티가 10.45→6.37로 떨어지고, **비지도 contact prediction (P@L)** 이 단조 증가. 15B에서는 일부 어려운 타깃(T1056)이 비로소 풀린다.
- **MSA 제거** — AlphaFold2가 의존하던 MSA 검색 (>10분/서열) 단계를 통째로 빼버림. 단일 서열만 입력.
- **속도 60× 이상** — forward pass 자체가 60배 빠르고, MSA 검색까지 합치면 1~2 자릿수 가속.
- **ESM Metagenomic Atlas** — 6.17억 단백질을 2주 안에 폴딩(2,000 GPU 클러스터). high-confidence 2.25억 중 76.8%가 UniRef90과 90% 이상 서열 차이, 12.6%는 어떤 PDB 구조와도 매칭되지 않음.
- **MLM만으로 구조가 emerge** — 트렁크는 단순 마스크드 LM 손실로만 학습되었는데도 contact, 2차/3차 구조, 원자 좌표가 표현 공간 안에서 차례로 자라남.

## 방법론 및 아키텍처

| 구성 | 내용 |
|---|---|
| ESM-2 트렁크 | Bidirectional transformer (BERT-like) + RoPE; 6개 크기: 8M / 35M / 150M / 650M / 3B / 15B |
| 학습 데이터 | UniRef50 클러스터 (~43M) 균등 샘플 + UniRef90 멤버, MLM (mask 15%) |
| ESMFold 폴딩 트렁크 | 48개 폴딩 블록 (sequence rep + pair rep) + recycling |
| 구조 모듈 | AF2 스타일 IPA, 8 블록, 백본 프레임 + 사이드체인 torsion |
| 입력 | **단일 서열만** (MSA, 템플릿 없음) |
| 출력 | 원자 좌표, per-residue pLDDT, pTM, PAE |
| 벤치마크 | CASP14, CAMEO; 메타지놈 폴딩은 MGnify90 |

```
Sequence  →  ESM-2 (15B)  →  Folding Trunk (48 blocks)  →  Structure Module  →  3D coords + pLDDT
                                       ↑ recycling ↩
```

## 결과

- **CASP14 + CAMEO TM-score**: ESMFold가 AlphaFold2에 근접; 일부 타깃(T1057)에서는 0.98 vs 0.97로 더 높음.
- **추론 속도**: 384-residue 단백질이 V100 1장에서 ~14초.
- **Atlas**: 2.25억 high-confidence 구조 중 12.6%는 PDB에 매칭 없음 → 자연계가 보유한 미지의 구조 공간 가시화.
- **Single-sequence 한계**: 깊은 MSA가 있는 타깃(보존된 패밀리)에서는 AF2가 여전히 강함.

## 활용 가이드

- **언제 ESMFold가 유리한가**: (1) MSA가 얕거나 부재할 때 (orphan, 메타지놈, 디자인 단백질), (2) 수백만~수억 단백질을 빠르게 훑어야 할 때, (3) 단백질 디자인 in silico 검증 (RFdiffusion 출력 평가에 표준).
- **언제 AlphaFold2/3이 더 나은가**: 깊은 MSA가 있고 정밀도가 우선; 복합체 예측은 AF-Multimer/AF3로.
- **체크포인트 선택**: 실용적으론 **650M / 3B** 가 cost-quality 균형. 15B는 GPU 메모리 부담이 크다.
- **다운스트림 임베딩**: ESM-2 표현은 protein function prediction, mutation effect, contact prediction 헤드의 입력으로 광범위하게 사용됨 (SaProt 등이 백본으로 채택).

## 관련 논문

- [[protein-foundation/abramson-2024-accurate-structure-prediction-of-biomolecular]] :extends — AlphaFold 3가 멀티모달 복합체로 확장 (단, 단백질-only 단일서열 속도는 ESMFold가 우위)
- [[protein-foundation/hayes-2025-simulating-500-million-years-of]] :extends — 같은 ESM 라인의 ESM3, 생성형 멀티모달로 진화
- [[protein-foundation/su-2024-saprot-protein-language-modeling-with]] :builds_on — ESM-2 백본 + Foldseek 3Di 토큰을 결합 (구조 인지 LM)
- [[protein-foundation/watson-2023-de-novo-design-of-protein]] :methodologically_uses — RFdiffusion이 ESMFold를 in silico 검증 도구로 활용

- [[protein-foundation/abramson-2024-accurate-structure-prediction-of-biomolecular]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/hayes-2025-simulating-500-million-years-of]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/su-2024-saprot-protein-language-modeling-with]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/watson-2023-de-novo-design-of-protein]] — 같은 카테고리 (protein foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/wang-2023-scientific-discovery-in-the-age]] — AI for Science 일반 framework

## 용어집

- **ESM** — Evolutionary Scale Modeling, FAIR/Meta의 단백질 LM 시리즈.
- **MSA** — multiple sequence alignment, 진화 동족 서열 정렬. 공진화(co-evolution) 신호의 표준 입력.
- **MLM** — masked language modeling, BERT 식 자기지도학습 목표.
- **Contact map** — 잔기 쌍의 3D 인접도 행렬 (Cβ-Cβ ≤ 8 Å).
- **P@L** — precision at L; 길이 L 단백질의 상위 L 예측 contact 정밀도.
- **TM-score** — 0~1, ≥0.5 ≈ 같은 fold.
- **pLDDT** — 예측 local 정확도 (0~100), 신뢰도 지표.
- **MGnify90** — EBI 메타지놈 단백질 90% identity 클러스터.
- **Folding trunk** — LM 임베딩을 pair representation으로 정제하는 트랜스포머 블록 군.
- **IPA** — invariant point attention, AF2가 도입한 백본 프레임 갱신 모듈.

---

## 내 메모

> 이 섹션은 위키 운영자(KRIBB 백부경)가 직접 작성. LLM은 이 섹션을 읽기만 하고 수정하지 않는다.
