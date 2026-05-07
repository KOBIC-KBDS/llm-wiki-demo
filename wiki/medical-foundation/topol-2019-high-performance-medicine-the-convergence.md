---
title: "Topol 2019 — Deep medicine: 인간과 AI의 수렴"
authors: Eric J. Topol
year: 2019
journal: Nature Medicine 25, 44–56
source: topol-2019-high-performance-medicine-the-convergence.md
category: medical-foundation
tags: [perspective, deep-learning, radiology, pathology, dermatology, ophthalmology, AI-chasm, FDA]
---

## 요약

Eric Topol(Scripps Research)의 Nature Medicine 리뷰. **딥러닝 의료 적용의 3계층 분류**를 제시 — (1) 임상의(영상 판독), (2) 의료 시스템(워크플로우, 의료 오류 감소), (3) 환자(웨어러블·자가 모니터링). 동시에 **"AI chasm"** — in-silico AUC 0.99가 임상 효용으로 자동 번역되지 않는다는 점 — 을 명확히 정의하고, 편향·프라이버시·블랙박스·규제 병목·환자-의사 관계 변화의 5대 위험을 정리.

## 핵심 기여

- **3계층 프레임워크** — 임상의/시스템/환자라는 분류 자체가 후속 의료 AI 논문(Med-PaLM, Med-Gemini, CONCH 등)의 평가 축으로 자리잡음.
- **AI chasm 명명** — Pearse Keane과 함께 도입한 용어. 알고리즘 정확도 ≠ 임상 효용. 발표 시점(2019) 기준 prospective real-world 검증을 통과한 사례는 당뇨망막증, 손목 골절, 유방암 미세전이, 대장 폴립, 선천성 백내장 등 소수에 불과.
- **분야별 evidence 정리** — radiology, pathology, dermatology, ophthalmology, cardiology, GI, mental health 각각의 DNN vs 의사 비교를 표 1로 정리. FDA 승인 가속(2017–2018) 표 2.
- **시너지 vs 대체 논쟁 제기** — Steiner et al.의 유방암 micrometastasis 연구 인용. 병리의 + 알고리즘 조합이 단독 어느 쪽보다 정확. 제로섬 대체보다 협업 모델을 권고.
- **임상의 시간 가설** — AI가 문서화·패턴 인식 부담을 덜어주면 그 시간을 환자에게 돌려줄 수 있음. 단, 잘못 운용되면 환자-의사 접점이 더 빠르게 무너질 수 있다는 양가적 전망.

## 방법론 및 아키텍처

리뷰 논문이라 자체 실험은 없음. 종합한 핵심 자료:

| 분야 | 대표 사례 | 성능 지표 |
|---|---|---|
| Radiology | Chest X-ray pneumonia (121-layer CNN) | AUC 0.76 |
| | Google 14-finding chest X-ray | AUC 0.63–0.87 |
| | Wrist fracture DNN | sensitivity 81%→92%, 47% 오판독 감소 |
| | Brain CT 13-finding | AUC 0.73, 150× 빠름 |
| Pathology | WSI breast cancer micrometastasis | 알고리즘+병리의 > 단독 |
| | Brain tumor DNA methylation | 기존 형태학 분류 대비 큰 개선 |
| Dermatology | Esteva et al. 130k 이미지 | AUC 0.96 (carcinoma) |
| Ophthalmology | Diabetic retinopathy | AUC 0.99 (Gulshan), 87%/91% sens/spec (IDx prospective) |
| | OCT 50 retinal pathology | AUC 0.992, 알고리즘 오류 3.5% vs 8명 전문의 |
| Cardiology | Echocardiogram view classification | 92% vs 79% (boarded) |
| GI | Real-time colonoscopy polyp | 94% accuracy, 35 s |

**Box 1 (Deep learning 입문)** — 입력층–은닉층(5–1000)–출력층 구조, supervised 학습이 주류, GAN을 통한 합성 의료영상 생성으로 데이터 부족 보완.

## 결과

- 좁은 단일 작업(narrow task)에 한해 DNN이 인간 수준 또는 그 이상 성능을 달성.
- **AUC 분포 0.56 (급성 신경학적 CT) ~ 0.99 (당뇨망막증, 고관절 골절)**.
- Prospective 임상 검증은 소수에 불과 — 대부분 retrospective in-silico.
- **시너지 발견** — 병리의 + 알고리즘 조합이 단독 어느 쪽보다 우수.
- **망막 사진의 systemic 정보** — 성별 AUC 0.97, 심혈관 위험인자, 치매 신호까지 인코딩. 안저는 전신 건강의 창.
- **FDA 승인 가속** — 2017–2018 동안 IDx (당뇨망막증), Aidoc, Viz.ai, Arterys, Apple AFib 등 빠르게 누적.

## 임상적 함의

- **임상 효용 = AUC 아님** — 도입 결정은 RCT/prospective 결과에 근거해야 함. 한국 식약처/심평원 검토 시에도 "in-silico 정확도"만으로는 부족.
- **편향 위험** — 인종·연령·기기 다양성 부족한 학습 데이터는 deployment 시 differential performance를 일으킴 (피부암 모델의 어두운 피부톤 실패 예).
- **black box 문제** — De Fauw et al. 2018(OCT)처럼 mapping → classifier 2단계로 만들어야 임상의가 모델 판단을 검사할 수 있음.
- **규제-기술 충돌** — FDA가 모델을 lock해야 인증되므로 deep learning의 자기 학습 특성이 사라짐. 지속학습 인증 제도 정비 필요.
- **임상의-환자 관계** — AI가 절약한 시간을 어디에 쓸지가 정책·문화 문제. 경영진 KPI에 따라 "더 많은 환자 회전" 또는 "더 깊은 환자 상호작용"으로 갈릴 수 있음.

## 관련 논문

- [[medical-foundation/singhal-2023-towards-expert-level-medical-question]] :extends — Topol이 본 "임상의 시간 회복" 시나리오의 LLM 버전. Med-PaLM이 의학 시험·소비자 질의에 응답.
- [[medical-foundation/saab-2024-capabilities-of-gemini-models-in]] :extends — Med-Gemini는 멀티모달·long-context로 Topol이 예견한 "여러 영상·EHR 통합 판독"의 첫 본격 시도.
- [[medical-foundation/sellergren-2025-medgemma-open-medical-foundation-models]] :extends — open foundation model이 임상 워크플로우(triage, 문서화, 요약)에 통합되는 케이스 — Topol이 말한 시스템 계층의 후속.
- [[medical-foundation/lu-2024-a-visual-language-foundation-model]] :extends — CONCH는 Topol이 정리한 pathology DL 라인의 visual-language foundation model 단계로의 도약.
- [[medical-foundation/lu-2024-a-visual-language-foundation-model]] — 같은 카테고리 (medical foundation)
- [[medical-foundation/saab-2024-capabilities-of-gemini-models-in]] — 같은 카테고리 (medical foundation)
- [[medical-foundation/sellergren-2025-medgemma-open-medical-foundation-models]] — 같은 카테고리 (medical foundation)
- [[medical-foundation/singhal-2023-towards-expert-level-medical-question]] — 같은 카테고리 (medical foundation)
- [[foundation-perspectives/moor-2023-foundation-models-for-generalist-medical]] — GMAI perspective anchor
- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] — foundation model 정의

## 용어집

- **Deep learning** — 다층 신경망을 통한 표현 학습. CNN/RNN/GAN/RL 등 변종.
- **AUC / ROC** — Receiver operating characteristic 곡선 아래 면적; 이진 분류기 표준 지표.
- **WSI** — Whole-Slide Imaging, 병리 슬라이드 전체 디지털 스캔.
- **OCT** — Optical coherence tomography, 망막 3D 영상.
- **AI chasm** — in-silico 정확도 vs 임상 효용 사이의 격차(Topol & Keane).
- **GAN** — Generative Adversarial Network. 의료영상 합성으로 데이터 부족 보완.
- **Prospective validation** — 모델 lock 이후 새로 수집한 데이터로 검증. clinical efficacy의 최소 조건.
- **Autodidactic 모델** — 새 데이터로 스스로 갱신되는 모델. FDA의 locked-model 정책과 충돌.
- **510(k) / De Novo** — FDA가 의료기기 AI를 승인하는 두 주요 경로.
