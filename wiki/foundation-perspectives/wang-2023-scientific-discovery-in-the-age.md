---
title: "AI for Science 분류 체계 (Wang 2023, Nature)"
authors: Wang H, Fu T, Du Y, ..., Bengio Y, Zitnik M*
year: 2023
journal: Nature 620:47-60
source: wang-2023-scientific-discovery-in-the-age.md
category: foundation-perspectives
tags: [AI4Science, foundation-model, geometric-deep-learning, self-supervised, generative, perspective, Nature]
---

## 요약

이 perspective는 "**과학 발견의 모든 단계**(데이터 수집·표현 학습·가설 생성·실험 설계·자율 발견)에서 AI가 어떻게 작동하는지"를 통합 정리한 분류 체계 논문이다. 핵심 도구는 네 가지 — **geometric deep learning**, **self-supervised foundation model**, **generative model**, **reinforcement learning** — 이며, 이들을 묶는 통합 원칙은 *과학 지식을 inductive bias로 모델에 주입*하는 것이다. 대표 성과로 AlphaFold 2(50년 묵은 단백질 접힘 문제), 분자동역학 수백만 입자 시뮬, 마그네틱 핵융합 제어, AI 일기예보, 입자물리 이상 탐지를 들고, 분포 변화·reliability·dual-use·환경비용·데이터 청지기 정신을 미해결 과제로 꼽는다.

## 핵심 주장

- **과학적 발견은 가설 공간 항해(navigation of hypothesis space)**: 신약 분자만 해도 ~10⁶⁰개 — 인간 단독 탐색 불가능. AI는 이 공간을 효율적으로 탐사하는 도구.
- **Inductive bias가 핵심**: 물리 법칙·대칭성·분자 기하 같은 도메인 지식을 모델 구조에 주입하면 데이터 효율과 외삽력이 동시에 좋아진다(예: SE(3) equivariance).
- **자기지도 사전학습 + foundation model이 과학에서도 작동**: 단백질·게놈·분자·전자구조·기상 데이터 모두 unlabeled로 풍부 → 사전학습 후 다양한 task로 전이.
- **생성 모델이 과학을 generative discipline으로 바꾼다**: 분자, 단백질, 결정 구조, 합성 EHR, 입자 충돌 사건 — 모두 sample 가능.
- **RL은 자율 실험을 가능케 한다**: 능동적 측정 선택, 자동 실험 스케줄링, 닫힌 고리 발견.
- **그러나 신뢰성·재현성·편향·dual-use가 따라온다**: 단순한 SOTA 추구가 아닌 도메인 검증·불확실성 정량화·인간-루프 통합이 필수.

## 분석 프레임워크

### 과학 발견 4단계 × AI 역할

| 단계 | AI가 하는 일 | 대표 기법 |
|---|---|---|
| 1. 데이터 수집·큐레이션 | rare-event 트리거링, denoising, super-resolution, pseudo-labeling, 합성 데이터 | autoencoder, VAE, GAN, weak supervision |
| 2. 표현 학습 | 의미 있는 latent vector | **geometric deep learning, self-supervised, language modeling, neural operators, Transformer** |
| 3. 가설 생성 | symbolic expression, 분자/단백질/결정 디자인, 역문제 해결 | symbolic regression, diffusion, normalizing flow |
| 4. 실험 설계·자율 발견 | 능동 학습으로 다음 측정 선택, RL로 실험 자동화 | active learning, Bayesian optimization, RL |

### 네 가지 ML 기본 요소 (Box 1 발췌)

1. **Geometric deep learning** — GNN, message passing, 대칭성/등가성으로 그래프·기하 데이터 처리.
2. **Self-supervised learning** — 라벨 없는 데이터에서 supervision 신호 추출 → foundation model 사전학습의 핵심.
3. **Generative models** — VAE, GAN, normalizing flow, diffusion, generative pretrained Transformer. 후보 샘플링.
4. **Reinforcement learning** — 누적 보상 최대화 정책. 능동 측정·자율 실험에 사용.

### 통합 원칙: Inductive bias as bridge

```
domain knowledge (laws of physics, symmetries, geometry)
        ↓ (encoded as)
inductive bias in model architecture
        ↓
↓ data efficiency, ↓ extrapolation error, ↑ scientific plausibility of outputs
```

## 결론 및 가이드라인

- **이 논문이 흐름에서 차지하는 위치 (domain perspective)**: Bommasani 2021의 일반 framework를 **자연과학 영역 전체로 확장**한 청사진. Moor 2023이 의료를 좁게 내려간다면, Wang 2023은 과학을 가로로 넓혀 정리한다.
- **읽을 때 우선순위**: Fig. 1(과학 발견 단계 도식), Box 1(용어집), §"Learning meaningful representations"(이 위키의 protein·genome·single-cell foundation model을 모두 이 절의 카테고리로 분류 가능). 후반부 challenge 섹션(misuse, fairness, environment)은 governance 논의에 유용.
- **운영 가이드라인 함의**:
  - 본 위키의 생물학·게놈·단백질·single-cell foundation model을 정리할 때, 이 논문의 4기법(geometric / self-supervised / generative / RL) 중 **어디에 속하는지**를 명시하면 cross-paper 비교가 쉬워진다.
  - 새 논문이 "foundation model"을 표방한다면, *자기지도 사전학습 + 광범위 적응성 + emergent capability* 세 조건을 점검(Bommasani 2021 정의를 빌려서). Wang 2023은 그 적용 범위를 자연과학 일반으로 정당화.
  - SOTA 외에 *분포 변화 평가, 불확실성, 도메인 외삽력*을 함께 보고하는 것이 이 논문의 가이드라인.

## 관련 논문

- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] :builds_on — foundation model 정의를 자연과학 도메인에 확장.
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] :methodologically_uses — Transformer가 과학 sequence 모델의 표준.
- [[foundation-perspectives/moor-2023-foundation-models-for-generalist-medical]] :parallel_in_other_disease — Moor가 의료 도메인 narrowdown, Wang이 과학 일반 cross-domain.
- [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]] :extends — Wang의 일반론을 네트워크 생물학 벤치마킹으로 좁힘.
- [[protein-foundation/abramson-2024-accurate-structure-prediction-of-biomolecular]] :supports — 단백질·핵산·ligand 상호작용을 통합한 과학 AI 사례.
- [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]] :supports — 대규모 단백질 언어모델과 구조 예측 사례.
- [[dna-rna-foundation/avsec-2021-effective-gene-expression-prediction-from]] :supports — 긴 DNA 문맥에서 조절 결과를 예측하는 사례.
- [[dna-rna-foundation/nguyen-2024-sequence-modeling-and-design-from]] :supports — genome-scale sequence modeling과 design 사례.

## 용어집

- **AI4Science**: 과학 발견 전 단계에 AI를 통합하는 패러다임.
- **Geometric deep learning**: 그래프·다양체 등 기하 구조 데이터 위 ML.
- **Equivariance / Invariance**: 입력 변환에 대해 출력이 같이 변하거나(eq) 변하지 않는(inv) 성질.
- **Inductive bias**: 도메인 지식을 모델 구조에 주입한 사전 가정.
- **Inverse problem**: 관측에서 원인을 역산하는 문제(보통 비유일·불안정).
- **Self-supervised learning**: 라벨 없는 데이터에서 supervision 신호를 자동 생성.
- **Foundation model**: 대규모 자기지도 사전학습 + 다양한 task에 적응 가능한 모델.
- **Generative model**: 데이터 분포를 학습해 새 샘플을 생성(VAE/GAN/diffusion/GPT).
- **Reinforcement learning**: 환경 상호작용으로 보상 최대화 정책 학습.
- **Active learning**: 가장 정보 많은 다음 데이터·실험을 선택.
- **Symbolic regression**: 데이터에 맞는 수식을 직접 탐색.
- **Neural operator**: 함수 공간 사이 매핑을 학습; discretization invariant.
- **Distribution shift**: 학습/배포 분포 차이 — 과학 응용에서 흔한 실패 모드.
- **Surrogate model**: 비싼 시뮬/실험을 대신하는 경량 ML 근사.
