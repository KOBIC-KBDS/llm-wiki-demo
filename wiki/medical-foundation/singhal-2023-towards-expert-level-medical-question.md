---
title: "Singhal 2023 — Med-PaLM: LLM이 임상 지식을 인코딩한다"
authors: Karan Singhal, Shekoofeh Azizi, Tao Tu, ..., Alan Karthikesalingam, Vivek Natarajan
year: 2023
journal: Nature 620, 172–180
source: singhal-2023-towards-expert-level-medical-question.md
category: medical-foundation
tags: [Med-PaLM, MultiMedQA, MedQA, USMLE, instruction-prompt-tuning, PaLM, LLM, clinical-QA]
---

## 요약

Google Research/DeepMind의 Med-PaLM 1세대 논문. **MultiMedQA(7개 의학 QA 데이터셋, 새 HealthSearchQA 포함)**를 도입하고, **Flan-PaLM 540B**가 MedQA(USMLE)에서 67.6%로 이전 SOTA를 17.3%p 추월. 여기에 **instruction prompt tuning** (clinician-curated soft prompt)을 적용한 **Med-PaLM**이 소비자 의학 질의의 인간 평가 7대 축에서 임상의에 근접 — 과학적 합의 일치율 92.6% (vs 임상의 92.9%), 잠재적 해 5.9% (vs 5.7%), 편향 0.8% (vs 1.4%).

## 핵심 기여

- **MultiMedQA 벤치마크** — MedQA(USMLE), MedMCQA(인도 의대 입시), PubMedQA, MMLU clinical topics, LiveQA, MedicationQA + 신규 **HealthSearchQA**(검색 자주 되는 소비자 질문 3,173건). LLM 의학 지식 평가의 사실상 표준이 됨.
- **인간 평가 7축 루브릭** — 과학적 합의 일치 / incorrect content / missing content / harm severity (AHRQ) / harm likelihood / bias / helpfulness. BLEU 같은 자동 지표를 넘어선 임상 평가 프레임워크.
- **Flan-PaLM 540B의 SOTA** — MedQA 67.6%, MedMCQA 57.6%, PubMedQA 79.0%, MMLU professional medicine 83.8%. 이전 BioGPT/PubMedGPT/Galactica를 모두 추월.
- **Instruction prompt tuning** — soft prompt만 학습하는 PEFT 기법으로 Med-PaLM 생성. consumer-question 평가에서 임상의 수준에 근접.
- **Selective prediction** — self-consistency 분포 entropy를 confidence proxy로 사용. 45% deferral 시 MedQA 67.6% → 82.5%.

## 방법론 및 아키텍처

| 요소 | 내용 |
|---|---|
| Backbone | **PaLM 540B** (Chowdhery et al.) dense decoder transformer |
| Instruction tuning | Flan-PaLM (Chung et al., domain-agnostic instruction tuning) |
| Sizes | 8B / 62B / 540B 모두 평가 |
| Prompting | Few-shot 5-shot + CoT + self-consistency (n=11, 셀렉티브에서 n=41) |
| Med-PaLM 정렬 | Instruction prompt tuning — 학습 가능한 soft prompt 토큰만 갱신 |
| 임상 큐레이션 | 4명 임상의(US/UK; 내과·소아·외과·1차진료) — 예제 작성 |
| 인간 평가 | 9명 임상의(US/UK/인도) + 5명 일반인(인도). 140개 long-form 질문. Bootstrap 1000회 95% CI |
| Selective prediction | self-consistency 답안 분포의 Shannon entropy로 deferral 판단 |

## 결과

### 객관식 (Flan-PaLM 540B)

| Dataset | Flan-PaLM | 이전 SOTA | Δ |
|---|---|---|---|
| MedQA (USMLE, 4-opt) | **67.6%** | 50.3% (PubMedGPT) | +17.3 |
| MedMCQA | **57.6%** | 52.9% (Galactica) | +4.7 |
| PubMedQA | **79.0%** | 78.2% (BioGPT) | +0.8 |
| MMLU prof. medicine | **83.8%** | — | new SOTA |
| MMLU clinical knowledge | **80.4%** | — | new SOTA |

### 인간 평가 (Med-PaLM vs Flan-PaLM vs 임상의)

| 축 | Flan-PaLM | Med-PaLM | 임상의 |
|---|---|---|---|
| 과학적 합의 일치 | 61.9% | **92.6%** | 92.9% |
| Inappropriate content (낮을수록 좋음) | 16.1% | 18.7% | 1.4% |
| Missing content (낮을수록 좋음) | 47.6% | **15.3%** | 11.1% |
| 잠재적 해 (있음) | 29.7% | **5.9%** | 5.7% |
| 편향 (있음) | 7.9% | **0.8%** | 1.4% |
| 일반인 helpfulness | 60.6% | **80.3%** | 91.1% |
| 정확한 retrieval (임상의 평가) | 76.3% | 95.4% | 97.8% |

### Selective prediction

- Self-consistency 41개 decode → entropy → deferral fraction 0.45에서 정확도 67.6% → **82.5%**.
- LLM이 의학 도메인에서도 자기 confidence를 어느 정도 표현 가능함을 보여줌.

## 임상적 함의

- **USMLE pass-mark 60% 돌파** — Flan-PaLM 540B가 처음으로 USMLE 합격선을 안정적으로 넘김. "LLM이 의학 시험 합격 수준의 지식을 인코딩한다"는 명제 입증.
- **Soft-prompt 기반 의료 정렬** — 모델 가중치를 건드리지 않는 PEFT가 clinical alignment에 효과적. 임상 도입 시 컴플라이언스 측면에서도 유리.
- **Length-correctness tradeoff** — Med-PaLM은 missing content는 줄였으나 inappropriate content는 약간 증가. 더 긴 답변이 더 안전하지는 않음.
- **소비자 질의의 가치** — HealthSearchQA가 임상 시험 질문보다 임상 의의를 정량화하기 어렵지만, 실제 환자 행동에 영향을 미치는 곳이 여기.
- **단일 평가자 한계 + English-only** — 실제 deployment 전에 다국어, 다양한 인구통계, 다중 평가자 라운드 필수.

## 관련 논문

- [[medical-foundation/topol-2019-high-performance-medicine-the-convergence]] :extends — Topol이 제시한 "임상의의 시간 회복" 비전의 LLM 단계.
- [[medical-foundation/saab-2024-capabilities-of-gemini-models-in]] :extends — 같은 Google 팀의 Med-Gemini가 MedQA 91.1%로 본 논문의 67.6%를 큰 폭 추월. uncertainty-guided web search를 도입.
- [[medical-foundation/sellergren-2025-medgemma-open-medical-foundation-models]] :extends — Med-PaLM이 closed-weight였던 한계를 MedGemma 오픈 모델 라인이 보완.
- [[medical-foundation/lu-2024-a-visual-language-foundation-model]] :supports — CONCH는 같은 시기의 visual-language pathology foundation model. text-only Med-PaLM과 보완 관계.
- [[medical-foundation/lu-2024-a-visual-language-foundation-model]] — 같은 카테고리 (medical foundation)
- [[medical-foundation/saab-2024-capabilities-of-gemini-models-in]] — 같은 카테고리 (medical foundation)
- [[medical-foundation/sellergren-2025-medgemma-open-medical-foundation-models]] — 같은 카테고리 (medical foundation)
- [[medical-foundation/topol-2019-high-performance-medicine-the-convergence]] — 같은 카테고리 (medical foundation)
- [[foundation-perspectives/moor-2023-foundation-models-for-generalist-medical]] — GMAI perspective anchor
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **MultiMedQA** — 본 논문에서 묶은 7개 의학 QA 데이터셋 벤치마크.
- **MedQA / USMLE** — 미국 의사면허시험 스타일 multiple-choice. 60% 이상이면 합격선.
- **MedMCQA** — 인도 의대 입학 시험 multiple-choice.
- **PubMedQA** — PubMed 초록 기반 yes/no/maybe.
- **MMLU clinical topics** — 임상 지식·해부학·전문 의학 등 MMLU 부분집합.
- **HealthSearchQA** — 본 논문이 처음 공개한 3,173개 소비자 의학 질문.
- **PaLM / Flan-PaLM** — Google 540B 디코더 transformer / instruction-tuned 버전.
- **Instruction prompt tuning** — 모델 가중치 freeze, learnable soft prompt token만 학습하는 PEFT.
- **Chain-of-Thought (CoT)** — 모델에게 추론 단계를 먼저 쓰도록 유도하는 프롬프트 기법.
- **Self-consistency** — 여러 CoT decode를 샘플하고 다수결로 답을 정함. 본 논문은 동시에 confidence proxy로도 사용.
- **AHRQ harm scale** — 미국 Agency for Healthcare Research and Quality의 환자 안전 이벤트 심각도 척도.
- **Scientific-consensus alignment** — 답변이 현재 임상·과학적 합의에 부합하는가를 묻는 평가 축.
