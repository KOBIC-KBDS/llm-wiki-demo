---
title: "GMAI — Generalist Medical AI 비전 (Moor 2023, Nature)"
authors: Moor M, Banerjee O, Shakeri Hossein Abad Z, Krumholz HM, Leskovec J, Topol EJ, Rajpurkar P
year: 2023
journal: Nature 616:259-265
source: moor-2023-foundation-models-for-generalist-medical.md
category: foundation-perspectives
tags: [foundation-model, medical-AI, GMAI, multimodal, perspective, Nature]
---

## 요약

이 perspective는 의료 AI의 새 패러다임 **GMAI(Generalist Medical AI)**를 제안한다. 현재의 의료 AI는 task별로 파편화되어 있고(미국 FDA 승인 ~500개 모델 대부분이 1–2 task 한정), 데이터 분포가 바뀌면 재학습이 필요하다. 반면 GMAI는 (1) **자연어 prompt로 task를 동적 지정**, (2) **이미지·EHR·검사값·신호·오믹스·텍스트를 임의 조합**, (3) **의료 도메인 지식을 형식적으로 표현하고 추론**하는 단일 foundation model이다. 6대 응용(grounded radiology report · 침대맡 의사결정 지원 · 수술 보조 · 노트 기록 · 환자 챗봇 · 텍스트→단백질 생성)과 검증·검증가능성·편향·프라이버시·스케일이라는 5대 도전을 명시한다.

## 핵심 주장

- **Task specific → Generalist**: "한 모델 한 질병"이 아니라 "한 모델 → 모든 medical task"로 가야 한다. 이 전환의 동력은 multimodal Transformer + self-supervision + in-context learning이다.
- **세 가지 차별 능력**:
  1. *Dynamic task specification* — 새 진단 기준이 등장해도 prompt만으로 처리.
  2. *Flexible multimodal I/O* — 사전에 정해진 모달리티 조합이 아니라 임의 조합.
  3. *Medical reasoning* — 본 적 없는 병태(aortoduodenal fistula 사례)도 해부학·생리학 지식으로 추론·설명.
- **Versatility는 양날의 칼**: 하나의 모델 결함이 모든 downstream에 전파 — 검증·감사·후속 모니터링이 종래보다 훨씬 까다롭다.
- **확장성보다 신뢰성이 병목**: 모델 크기와 함께 사회적 편향이 증가한다는 BIG-bench 결과(Srivastava 2022) 언급. GMAI 시대에는 편향 감지·수정이 1순위.
- **데이터가 곧 거버넌스**: GMAI는 다양·대규모·익명화된 multimodal 데이터를 요구. MIMIC·UK Biobank가 출발점이지만, 저개발국까지 포함하는 확장이 필수.

## 분석 프레임워크

### 기존 의료 AI vs GMAI

| 차원 | 기존 task-specific 의료 AI | **GMAI** |
|---|---|---|
| Task 추가 | 새 데이터셋 + 재학습 필요 | **자연어 prompt만으로 추가** |
| 입력 모달리티 | 사전 정의된 1–소수 | **임의 조합** |
| 출력 형식 | 고정(확률값) | **텍스트·이미지·음성 자유** |
| 도메인 지식 | 학습 데이터 통계만 | **knowledge graph + retrieval** |
| 데이터 분포 변화 | 성능 저하·재학습 | **in-context learning으로 적응** |
| 검증 부담 | 명확한 task 단위 | **off-label use까지 고려해야** |

### GMAI 6대 응용 사례 (Fig. 2)

| 응용 | 핵심 기술 요건 | 파급 효과 |
|---|---|---|
| Grounded radiology reports | vision-language + 시각적 grounding | 방사선과 워크로드 ↓ |
| Bedside decision support | EHR 시계열 + 자연어 설명 | 조기 경보 + 자기설명 |
| Augmented procedures | vision-audio-language + 해부학 KG | 수술 안전·드문 사례 대처 |
| Interactive note-taking | speech-to-text + 의학 용어 + EHR 통합 | 행정 부담 ↓ |
| Patient chatbots | 비전문가 친화 언어 + 불확실성 처리 | 의료 접근성 ↑ |
| Text-to-protein generation | 시퀀스 LM + 구조 조건화 | 신약·치료 단백질 설계 |

### 5대 도전

1. **Validation** — versatility로 인한 use case 폭발. "off-label warning" UX 필요.
2. **Verification** — multimodal 출력 → 다학제 검증 패널.
3. **Social bias** — scale ↑ → bias ↑ 가능성. 지속적 audit 필수.
4. **Privacy** — 대형 모델은 memorization·prompt injection 취약.
5. **Scale** — 학습/배포 비용·환경비용; 병원 환경엔 knowledge distillation 필요.

## 결론 및 가이드라인

- **이 논문이 흐름에서 차지하는 위치 (domain perspective)**: Bommasani 2021의 일반 foundation-model 정의를 **의료 도메인으로 좁혀 구체화한 청사진**. Wang 2023(과학 일반)과 함께 "도메인 perspective" 쌍을 이룬다.
- **읽을 때 우선순위**: Fig. 1(GMAI 파이프라인 도식) → Fig. 2(6대 응용 사례) → Challenges of GMAI 섹션. 이후 본 위키의 Med-PaLM(Singhal 2023), Med-Gemini(Saab 2024), MedGemma(Sellergren 2025), UNI(Truhn-stem PDF), 시각-언어 병리 FM(Lu 2024)을 GMAI 청사진의 부분 구현체로 매핑하면 좋다.
- **운영 가이드라인**:
  - 본 위키에 의료 foundation model을 추가할 때 GMAI 3대 능력 기준으로 자가 점검: dynamic task spec? multimodal I/O? medical knowledge representation?
  - 단일 cohort/단일 modality 모델은 GMAI라고 부르지 말 것 — Moor 정의에 부합하지 않음.
  - 평가 보고 시 BIG-bench 식 bias scaling 경고를 함께 기재.

## 관련 논문

- [[foundation-perspectives/bommasani-2021-on-the-opportunities-and-risks]] :builds_on — foundation model 일반 정의의 의료 도메인 적용.
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] :methodologically_uses — multimodal Transformer가 GMAI의 backbone.
- [[medical-foundation/singhal-2023-towards-expert-level-medical-question]] :supports — Med-PaLM이 medical QA에서 GMAI 일부 기능을 구현.
- [[medical-foundation/saab-2024-capabilities-of-gemini-models-in]] :extends — Med-Gemini가 multimodal/long-context medical reasoning으로 확장.
- [[medical-foundation/sellergren-2025-medgemma-open-medical-foundation-models]] :extends — open medical foundation model 흐름.
- [[medical-foundation/lu-2024-a-visual-language-foundation-model]] :supports — 병리 vision-language foundation model 사례.
- [[foundation-perspectives/chen-2024-towards-a-general-purpose-foundation]] :supports — UNI pathology foundation model 사례.

## 용어집

- **GMAI**: Generalist Medical AI. 본 논문이 정의한 의료 foundation model 클래스.
- **Dynamic task specification**: 자연어 prompt로 inference 시점에 task 정의.
- **Multimodal I/O**: 이미지·EHR·검사값·신호·오믹스·텍스트·음성 자유 조합.
- **Visual grounding**: 텍스트 결론에 대응하는 이미지 영역을 지시(Grad-CAM 등).
- **Knowledge graph**: 의학 개념·관계 구조화 표현.
- **Retrieval-augmented generation**: 추론 시점에 외부 문서 검색해 참조(REALM, RETRO).
- **In-context learning**: 가중치 업데이트 없이 prompt 예시만으로 새 task 학습.
- **Long tail**: 데이터에서 드문 희귀 질환 — GMAI가 추론으로 대응해야 할 영역.
- **RLHF**: Reinforcement Learning from Human Feedback; 임상의 피드백 통합.
- **MIMIC / UK Biobank**: 학습 데이터 출발점으로 자주 인용되는 대형 임상·생명 데이터셋.
