---
title: "Saab 2024 — Med-Gemini: Gemini의 의료 특화"
authors: Khaled Saab, Tao Tu, Wei-Hung Weng, ..., Alan Karthikesalingam, Vivek Natarajan
year: 2024
journal: arXiv 2404.18416 (Google DeepMind/Research)
source: saab-2024-capabilities-of-gemini-models-in.md
category: medical-foundation
tags: [Med-Gemini, Gemini, MedQA, web-search, uncertainty-guided, multimodal, long-context, ECG, EHR, surgical-video]
---

## 요약

Google의 **Med-Gemini** 패밀리 — Gemini 1.0/1.5 백본 위에 (1) **자기 학습 + 웹 검색**, (2) **불확실성 기반 inference-time 검색**, (3) **멀티모달 fine-tuning + 커스텀 인코더(ECG)**, (4) **long-context EHR/수술 영상 이해**를 결합. 14개 의학 벤치마크 중 **10개에서 SOTA**, MedQA(USMLE) **91.1%**로 GPT-4 MedPrompt(90.2%)와 Med-PaLM 2(86.5%) 모두 추월. 의무 기록 요약·진료 의뢰서 생성에서 인간 임상의보다 자주 선호됨.

## 핵심 기여

- **Med-Gemini 패밀리 4종**:
  - **Med-Gemini-L 1.0** (Gemini 1.0 Ultra) — text reasoning + 웹 검색
  - **Med-Gemini-M 1.0** (Gemini 1.0 Pro) — 의료 텍스트 생성(요약, 의뢰서)
  - **Med-Gemini-M 1.5** (Gemini 1.5 Pro) — 멀티모달, long-context
  - **Med-Gemini-S 1.0** (Gemini 1.0 Nano + Flamingo-style 인코더) — 12-lead ECG 신호
- **자기 학습 with 검색** — MedQA-R / MedQA-RS 합성 데이터셋 생성 (CoT with/without search), 정답 일치 CoT만 살려서 fine-tune.
- **불확실성 기반 inference-time 검색** — answer 분포의 Shannon entropy로 검색 트리거. MedQA 87.2% → **91.1%** (4 라운드).
- **MedQA relabeling** — 3명 PCP 평가로 7.4%가 부적합(누락 정보 3.8%, 라벨 오류 2.9%, 모호 0.7%) 발견. 필터 후 91.8%.
- **멀티모달 SOTA 5/7** — Slake-VQA, Path-VQA, ECG-QA, NEJM Image Challenge, MMMU-HM. GPT-4V 대비 평균 +44.5% relative.
- **Long-context** — 200k–700k 단어 EHR needle-in-a-haystack, MedVidQA, Cholec80-CVS 모두 SOTA.
- **실제 임상 작업** — AVS(after-visit summary), 의뢰서, plain-language 요약에서 임상의 평가 시 모델 응답 선호 또는 동률 비율 50% 이상.

## 방법론 및 아키텍처

| 변형 | 백본 | 추가 컴포넌트 | 주요 작업 |
|---|---|---|---|
| Med-Gemini-L 1.0 | Gemini 1.0 Ultra | 자기 학습 + 웹 검색 + uncertainty-guided search | MedQA, NEJM CPC, GeneTuring |
| Med-Gemini-M 1.0 | Gemini 1.0 Pro | medical instruction tuning | AVS, 의뢰서, PLS |
| Med-Gemini-M 1.5 | Gemini 1.5 Pro | 멀티모달 fine-tune (8 task / 6 dataset) | VQA, EHR long-context, video |
| Med-Gemini-S 1.0 | Gemini 1.0 Nano | Flamingo cross-attention ECG 인코더 | ECG-QA |

### 자기 학습 + 검색 파이프라인
1. 학습 질문에 대해 (a) CoT only, (b) CoT + 웹 검색 결과 두 reasoning path 생성
2. 정답 일치한 CoT만 살림
3. 모델을 그 데이터로 fine-tune → 다음 라운드에서 더 나은 CoT 생성
4. 수렴까지 반복

### Inference 시 uncertainty-guided 검색
1. 다중 reasoning path 샘플
2. answer choice 분포의 Shannon entropy 계산
3. threshold 초과 시: 갈등 해결용 검색 쿼리 3개 생성
4. 검색 결과를 prompt에 추가, 다시 1로

### 평가
- 14 벤치마크, 25 task. text / multimodal / long-context.
- Real-world: AVS, 의뢰서, PLS — 임상의 blind side-by-side preference.

## 결과

### Text reasoning

| Task | Med-Gemini-L 1.0 | 이전 SOTA | 방법 |
|---|---|---|---|
| MedQA (USMLE) | **91.1%** | 90.2% | GPT-4 + MedPrompt |
| MedQA (filter unanimous) | 91.8 ± 0.2% | — | — |
| NEJM CPC top-10 | **72.3%** | 59.1% | AMIE |
| NEJM CPC top-1 | **30.7%** | 29.2% | AMIE |
| GeneTuring gene name conv | **100%** | 85.0% | GPT-4 |
| GeneTuring gene location | **83.0%** | 61.0% | GPT-4 |
| GeneTuring TF regulation | **65.3%** | 62.0% | GPT-4 |

### Ablation (MedQA)
- self-training 없음: 87.2% → +self-training: 90.4% → +4-round search: **91.1%**

### Multimodal & long-context
- 7 멀티모달 벤치마크 평균 GPT-4V 대비 **+44.5% relative**.
- MIMIC-III needle-in-a-haystack: heuristic 베이스라인 대비 precision/recall 개선.
- Cholec80-CVS, MedVidQA 모두 zero-shot prompting으로 SOTA.

### Real-world
- 의뢰서 생성: Med-Gemini-M 1.0이 임상의 대비 **모든 케이스에서 선호 or 동률**.
- AVS, PLS도 50% 이상에서 임상의 동급 또는 우위.

## 임상적 함의

- **MedQA 천장 도달** — 91.1% + 데이터셋 자체 품질 문제(7.4% 부적합) → MedQA 단독 평가의 한계 시사. 향후 평가는 NEJM CPC, GeneTuring, real-world task 쪽으로 무게중심 이동 필요.
- **검색 통합** — 의학 지식의 비정상성(non-stationarity) 대응을 모델 안에 내재화. 단, 일반 웹 검색이라 권위 있는 의료 출처 보장은 별도 정책 필요.
- **새 모달리티 통합** — ECG(12-lead) 같은 신호를 cross-attention 인코더로 흡수. 차후 wearable, genomics, 영양 데이터 통합의 기술적 청사진.
- **EHR long-context** — 한 환자의 100+ 노트(200k–700k 단어)를 in-context로 다루는 능력. "problem list 자동 작성" 같은 임상 워크플로우와 직접 연결.
- **closed-weight + 웹 의존성** — 의료기관 내 deployment 시 데이터 거버넌스, 검색 출처 신뢰성, 추론 비용이 함께 검토되어야 함.

## 관련 논문

- [[medical-foundation/singhal-2023-towards-expert-level-medical-question]] :extends — Med-PaLM(67.6% MedQA)을 Med-Gemini가 91.1%로 큰 폭 추월. 같은 Google 팀의 직계 후속.
- [[medical-foundation/topol-2019-high-performance-medicine-the-convergence]] :extends — Topol이 예견한 멀티모달·long-context·자가 학습 의료 AI를 본격 구현.
- [[medical-foundation/sellergren-2025-medgemma-open-medical-foundation-models]] :supports — Med-Gemini는 closed API. MedGemma는 동일한 Gemini 가족의 open weights 변형으로 연구 커뮤니티 보완.
- [[medical-foundation/lu-2024-a-visual-language-foundation-model]] :supports — CONCH는 pathology에 집중한 visual-language foundation. Med-Gemini-M 1.5가 Path-VQA에서 SOTA 갱신하며 일부 영역 중첩.
- [[medical-foundation/lu-2024-a-visual-language-foundation-model]] — 같은 카테고리 (medical foundation)
- [[medical-foundation/sellergren-2025-medgemma-open-medical-foundation-models]] — 같은 카테고리 (medical foundation)
- [[medical-foundation/singhal-2023-towards-expert-level-medical-question]] — 같은 카테고리 (medical foundation)
- [[medical-foundation/topol-2019-high-performance-medicine-the-convergence]] — 같은 카테고리 (medical foundation)
- [[foundation-perspectives/moor-2023-foundation-models-for-generalist-medical]] — GMAI perspective anchor
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **Gemini** — Google의 native multimodal transformer 디코더 LMM (1.0 Ultra/Pro/Nano, 1.5 Pro long-context).
- **Med-Gemini-L/-M/-S** — 의료 특화 변형. L=Ultra, M=Pro, S=Nano + 커스텀 인코더.
- **Self-training with search** — CoT를 with/without 검색 두 가지로 합성하고 정답 일치만 학습 데이터로 사용하는 반복 자기 학습.
- **Uncertainty-guided search** — inference 시 answer 분포 Shannon entropy로 검색 트리거.
- **MedQA-R / MedQA-RS** — 본 논문이 합성한 CoT(Reasoning) / CoT+검색(Reasoning+Search) 학습셋.
- **NEJM CPC** — NEJM clinico-pathological conference. 전문 진단 케이스 챌린지.
- **GeneTuring** — 12 모듈 600 QA의 유전체 지식 평가 벤치마크.
- **MIMIC-III** — 중환자실 EHR 공개 데이터셋. long-context 평가에 사용.
- **Cholec80-CVS** — 담낭절제술 영상 데이터셋과 Critical View of Safety 라벨.
- **MedVidQA** — medical instructional video question answering.
- **MMMU-HM** — MMMU의 Health & Medicine 부분집합.
- **MedPrompt** — Nori et al. 2023의 GPT-4 prompt engineering 레시피.
- **AMIE** — 진단 대화 특화 모델 (Tu et al. 2024b).
- **Flamingo cross-attention encoder** — frozen LLM에 cross-attention으로 시각/신호 인코더를 주입하는 패턴. 본 논문은 ECG에 적용.
