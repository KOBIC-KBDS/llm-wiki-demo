---
title: "Foundation Models — 용어를 만든 213쪽 Stanford CRFM 보고서"
authors: Bommasani R, Hudson DA, Adeli E, ..., Liang P (Stanford CRFM, 100+ 인)
year: 2021
journal: arXiv:2108.07258 (CRFM Technical Report)
source: bommasani-2021-on-the-opportunities-and-risks.md
category: foundation-perspectives
tags: [foundation-model, terminology, emergence, homogenization, alignment, ethics, CRFM]
---

## 요약

이 보고서는 **"foundation model"이라는 용어 자체를 만든** 글이다. 정의는 *broad data로 self-supervision을 거쳐 광범위한 downstream task에 adapt 가능한 모델*. BERT, GPT-3, CLIP이 대표 예. 이들의 두 가지 핵심 성질은 (1) **emergence** — 명시적으로 학습되지 않은 능력(예: in-context learning)이 규모에서 자발적으로 나타남, (2) **homogenization** — 거의 모든 downstream system이 소수의 foundation model 위에 얹혀짐 → 효율은 좋지만 결함도 함께 상속된다. 213쪽에 걸쳐 capabilities(언어/비전/로봇/추론/상호작용/철학) · applications(의료/법/교육) · technology(아키텍처/학습/적응/평가/안전/이론/해석) · society(공정성/오용/환경/법/경제/규모의 윤리)를 통합 조망한다.

## 핵심 주장

- **"Foundation model"은 단순한 사전학습 모델이 아니다** — 사회적/생태적 위치까지 포함하는 새 패러다임의 이름이다.
- **Emergence + Homogenization은 동전의 양면**: 강력한 leverage(한 모델이 좋아지면 모든 downstream이 좋아진다)와 단일 실패점(편향·취약점도 상속된다)을 동시에 만든다.
- **세 가지 기술적 가능 조건**:
  1. 하드웨어(최근 4년 GPU 처리량·메모리 ~10× ↑)
  2. **Transformer 아키텍처**(Vaswani 2017)의 병렬화
  3. Self-supervision으로 인터넷 규모 비라벨 데이터 활용
- **연구 ≠ 배포**: 학계는 대부분 연구 모델만 본다. 실제 사회적 영향은 비공개 배포 모델에서 일어남 — 평가·감사 격차가 사회적 위험.
- **생태계 사고법(Think ecosystem, act model)**: 데이터 생성→큐레이션→학습→적응→배포의 모든 단계가 윤리·안전 개입 지점. 모델만 보면 안 된다.
- **분야 다양성 위기**: foundation model R&D가 산업으로 집중. 학계·공공인프라(National Research Cloud, EleutherAI, BigScience 등)가 균형추 역할.

## 분석 프레임워크

| 차원 | 기존 ML | Deep Learning | **Foundation Models** |
|---|---|---|---|
| 무엇이 emerge하는가 | 어떻게(How) 푸는지 | 고수준 feature | **고차 능력**(in-context learning 등) |
| 무엇이 homogenize되는가 | 학습 알고리즘 | 모델 아키텍처 | **모델 자체**(GPT-3, BERT) |
| 시대 | 1990s~ | ~2010 | ~2018~ |

| 보고서 4부 구조 | 다루는 핵심 질문 |
|---|---|
| 2. Capabilities | 언어·비전·로봇·추론·상호작용·이해의 철학 |
| 3. Applications | **헬스케어**·법·교육 (각 도메인의 데이터·평가 요건) |
| 4. Technology | 모델·학습·적응·평가·시스템·데이터·보안·견고성·**AI 안전·해석** |
| 5. Society | 불평등·오용·환경·법·경제·**규모의 윤리** |

### 5단계 생태계 (§1.2)

```
[사람] ─→ Data Creation ─→ Data Curation ─→ Training ─→ Adaptation ─→ Deployment ─→ [사람]
                                  ↕              ↕            ↕              ↕
                            윤리·법·문서화      평가      가드레일·재학습   감사·되먹임
```

## 결론 및 가이드라인

- **이 보고서가 왜 흐름의 terminology anchor인가**: 본 위키의 후속 perspective 논문(Moor 2023 GMAI, Wang 2023 AI4Science, Theodoris 2024 network-biology, Cui/Truhn 2024 의·생물 foundation review)은 **모두 이 보고서의 정의를 출발점**으로 삼는다. 헬스케어·생물학에서 "foundation model"이라는 용어를 쓸 때마다 이 정의가 암묵적 전제.
- **읽을 때 우선순위**: §1.1(emergence/homogenization 정의), §1.2(생태계 도식), §3.1(헬스케어 — Moor 2023의 직접적 예시 출처), §5.1·5.6(공정성·규모의 윤리). 213쪽 전체를 통독할 필요는 없고 도메인별 섹션만 발췌해도 무방.
- **운영 가이드라인 함의**:
  - 본 위키처럼 foundation model을 정리할 때, 단지 BLEU/AUROC만 적지 말고 **데이터 출처·편향·재현성·접근성** 메모를 함께 남길 것. 이 보고서가 그 사유를 정당화한다.
  - "성능이 좋다"는 평가에 머무르지 말고 *어떤 인구·세포종·도메인이 과대표/과소표상되어 있는가*를 항상 점검 — Theodoris 2024가 이 권고를 단일세포 도메인에서 구체화한다.

## 관련 논문

- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] :builds_on — 보고서가 반복적으로 인용하는 아키텍처 기반(§1.1, §4.1).
- [[foundation-perspectives/moor-2023-foundation-models-for-generalist-medical]] :extends — 의료 generalist model 관점으로 확장.
- [[foundation-perspectives/wang-2023-scientific-discovery-in-the-age]] :extends — AI for Science 전체 분류 체계로 확장.
- [[foundation-perspectives/theodoris-2025-foundation-models-in-single-cell]] :extends — single-cell/network biology 벤치마킹 관점으로 확장.

## 용어집

- **Foundation model**: broad data + self-supervision + scale + adaptable. 이 보고서가 정의함.
- **Emergence(창발)**: 명시 학습되지 않은 능력이 규모에서 발생.
- **Homogenization(균질화)**: 다수 downstream system이 소수 모델 위에 의존.
- **Self-supervised learning**: 라벨 없이 데이터 자체에서 supervision 신호 추출(예: masked language modeling).
- **In-context learning**: 가중치 갱신 없이 prompt 내 예시만으로 새 task 수행.
- **Adaptation**: fine-tuning · prompting · RAG · 외부 분류기 결합 등 foundation model을 응용 시스템으로 만드는 모든 절차.
- **Ecosystem(생태계)**: 데이터 생성에서 배포까지의 전 파이프라인.
- **CRFM**: Stanford Center for Research on Foundation Models.
- **HAI**: Stanford Institute for Human-Centered AI.
