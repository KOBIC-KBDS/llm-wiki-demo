---
title: "RFdiffusion — RoseTTAFold을 노이즈 제거기로 미세조정한 단백질 디자인 디퓨전"
authors: Watson JL, Juergens D, Bennett NR, Trippe BL, ..., Baek M, Baker D
year: 2023
journal: Nature 620:1089-1100
source: watson-2023-de-novo-design-of-protein.md
category: protein-foundation
tags: [RFdiffusion, de novo design, diffusion model, motif scaffolding, binder design, symmetric oligomer, ProteinMPNN]
---

## 요약

**RFdiffusion**은 **RoseTTAFold(RF)을 denoising network로 fine-tuning**하여 단백질 백본을 생성하는 **DDPM**이다. 잔기 프레임(Cα + N-Cα-C 회전)을 가우시안 노이즈와 SO(3) 브라운 운동으로 손상시킨 뒤, 이를 역으로 깨끗하게 복원하도록 학습한다. **단량체 생성, 대칭 올리고머, 모티프 scaffolding, 결합단백질 디자인** 같은 다양한 과제에 동일한 모델 + 조건만 바꿔 적용 가능. 인플루엔자 HA 결합단백질의 cryo-EM 구조가 디자인과 거의 일치하면서 원자 수준 정확도를 입증.

## 핵심 기여

- **구조 예측 네트워크를 디자인 디퓨전으로 재활용** — RF의 SE(3)-equivariant pair representation이 디퓨전 denoiser로 자연스럽게 작동.
- **Self-conditioning** — 매 step에서 이전 step의 X̂₀를 템플릿으로 다시 입력. 성능 핵심 요소.
- **MSE loss 채택** — FAPE 대신 정렬되지 않은 좌표 MSE를 사용해 timestep 간 좌표 프레임 연속성을 유지. 무조건적(unconditional) 생성에 결정적.
- **하나의 모델로 다용도** — 단량체, 대칭, 모티프 scaffolding (효소 활성부, 금속 결합), 표적 단백질 binder 디자인까지 조건만 바꿔 처리.
- **수백 건의 실험 검증** — CD 스펙트럼/SAXS/cryo-EM. HA-binder cryo-EM이 디자인 모델과 거의 일치 → 원자 수준 정확도.
- **속도** — 100-residue 디자인 ~11초 (A4000 GPU); 기존 RF Hallucination ~8.5분 대비 50배 빠름.

## 방법론 및 아키텍처

| 구성 | 내용 |
|---|---|
| 표현 | 잔기 프레임 (Cα + N-Cα-C orientation) |
| Forward 노이즈 | Cα: 3D Gaussian; orientation: SO(3) Brownian motion |
| Denoiser | **RoseTTAFold (fine-tuned)** — 사전학습 가중치에서 시작 (untrained 동일 compute보다 훨씬 우수) |
| 트릭 | **self-conditioning** (이전 step X̂₀를 템플릿 입력) |
| Loss | 좌표 frame MSE (정렬 X). FAPE 대비 timestep 간 좌표 연속성 ↑ |
| 조건 채널 | 단량체(없음), 대칭, fold/secondary structure, 모티프 좌표, 표적 단백질 |
| 서열 디자인 | **ProteinMPNN** (백본당 8 서열) |
| in silico 평가 | AF2 single-sequence 예측: pAE<5, RMSD<2 Å, motif RMSD<1 Å |
| 추론 | 보통 200 step (큰 step + early truncation으로 가속) |

```
Random noise XT → [RF denoiser + self-conditioning] × T → backbone X0 → ProteinMPNN → sequence → AF2 validate
                       (with optional conditions: symmetry / motif / target / fold)
```

## 결과

- **무조건 단량체 생성**: 다양한 α/β/αβ topology, 600 residue까지 designable. 실험으로 6개 (300 aa) + 3개 (200 aa) 확인 — CD 스펙트럼 일치, 매우 thermostable.
- **대칭 올리고머**: C2~C6, D-symmetric, icosahedral assembly까지 cryo-EM/SAXS로 검증.
- **모티프 scaffolding**: 기존 RFjoint Inpainting이 실패하는 minimalist site에서도 작동.
- **금속 결합 단백질**: Ni²⁺, Zn²⁺ 등 실험 검증.
- **De novo binder**: 인플루엔자 HA 등 다중 표적에 nM-affinity binder. **HA-binder cryo-EM이 디자인 모델과 거의 일치** — 원자 수준 정확도의 결정적 증거.
- **속도**: A4000에서 100 aa ~11초.

## 활용 가이드

- **언제 적합한가**: (1) 새로운 fold/topology의 단량체, (2) 대칭 단백질 케이지/필라멘트, (3) 활성 부위 scaffolding (효소, 금속 결합), (4) 표적 단백질에 대한 binder 디자인.
- **워크플로우**: RFdiffusion 백본 → ProteinMPNN 서열 → AF2(또는 ESMFold) in silico 검증 → 다중 합성·실험 평가.
- **체크포인트 선택**: 공식 RFdiffusion repo (RosettaCommons/RFdiffusion). 표적 binder엔 별도 fine-tune 가중치 사용.
- **실험 통과율 팁**: AF2 success 기준(pAE<5, RMSD<2 Å)은 wet-lab 성공률과 유의미한 상관. TM-score 단독 필터보다 엄격.
- **한계 인지**: 백본만 디자인, 서열은 ProteinMPNN; 리간드/핵산 co-design은 native하게 안 됨 (RoseTTAFold All-Atom 또는 AF3 결합 필요).

## 관련 논문

- [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]] :methodologically_uses — ESMFold도 디자인 검증 용도로 호환 가능
- [[protein-foundation/abramson-2024-accurate-structure-prediction-of-biomolecular]] :supports — 둘 다 디퓨전이지만 RFdiffusion=디자인, AF3=예측
- [[protein-foundation/hayes-2025-simulating-500-million-years-of]] :supports — ESM3는 토큰화 LM으로 단백질 생성 (디퓨전 아님)
- [[protein-foundation/su-2024-saprot-protein-language-modeling-with]] :supports — 구조 인지 LM은 디자인 후보 평가에 활용 가능

- [[protein-foundation/abramson-2024-accurate-structure-prediction-of-biomolecular]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/hayes-2025-simulating-500-million-years-of]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/su-2024-saprot-protein-language-modeling-with]] — 같은 카테고리 (protein foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/wang-2023-scientific-discovery-in-the-age]] — AI for Science 일반 framework

## 용어집

- **DDPM** — Denoising Diffusion Probabilistic Model. 가우시안 노이즈를 가했다가 역으로 정제.
- **Frame representation** — 잔기당 Cα + N-Cα-C 회전 프레임. AF2/RF/RFdiffusion 표준.
- **SO(3)** — 3D 회전군 manifold; orientation 디퓨전이 일어나는 공간.
- **Self-conditioning** — 직전 step의 X̂₀를 템플릿으로 다시 입력하여 trajectory coherence 향상 (AF2 recycling과 유사).
- **MSE vs FAPE** — RFdiffusion은 좌표 MSE 사용 (timestep 간 frame 연속성 유지). FAPE는 AF2 식 정렬-불변 손실.
- **ProteinMPNN** — 백본을 받아 amino acid 서열을 디자인하는 그래프 기반 모델.
- **Motif scaffolding** — 활성/결합 부위 좌표를 고정하고 그 주위 백본을 새로 디자인.
- **Binder design** — 표적 단백질에 결합하는 신규 단백질 디자인.
- **In silico success** — AF2 예측 기반 성공 기준 (pAE<5, RMSD<2 Å, motif RMSD<1 Å).
- **TM-score / RMSD / pAE** — 구조 유사도 / 좌표 차이 / 예측 alignment error.

---

## 내 메모

> 이 섹션은 위키 운영자(KRIBB 백부경)가 직접 작성. LLM은 이 섹션을 읽기만 하고 수정하지 않는다.
