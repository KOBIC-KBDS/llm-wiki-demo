---
title: "AlphaFold 3 — Pairformer + 좌표 디퓨전으로 통합한 생체분자 복합체 예측"
authors: Abramson J, Adler J, Dunger J, Evans R, ..., Hassabis D, Jumper JM
year: 2024
journal: Nature 630:493-500
source: abramson-2024-accurate-structure-prediction-of-biomolecular.md
category: protein-foundation
tags: [AlphaFold3, AF3, diffusion, Pairformer, biomolecular complex, protein-ligand, protein-nucleic acid, antibody-antigen]
---

## 요약

**AlphaFold 3 (AF3)** 는 AF2의 Evoformer를 가벼운 **Pairformer**로, 프레임 기반 Structure Module을 **원자 좌표에 직접 동작하는 Diffusion Module**로 교체했다. 그 결과 **단백질·핵산(DNA/RNA)·리간드·이온·잔기 변형**을 하나의 모델로 동시 예측하며, **PoseBusters 단백질-리간드, 항원-항체, 단백질-단백질 인터페이스** 모두에서 기존 전문 모델 대비 SOTA를 달성한다.

## 핵심 기여

- **단일 모델로 다중 분자종 처리** — 그동안 단백질-리간드는 Vina/Gold, 단백질-NA는 별도 모델, 항원-항체는 AF-Multimer 등 분야별 도구가 따로였다. AF3 한 모델로 통합.
- **Pairformer (4 MSA + 48 pair) 채택** — MSA representation은 초반 4블록만 사용하고 폐기, 이후는 pair-only로 정보가 흐름. AF2 대비 가볍지만 정확도는 향상.
- **좌표 디퓨전 모듈** — 회전 프레임/등변성/torsion/violation loss 모두 제거. 다중 스케일 노이즈 스케줄로 저노이즈에서 local stereochemistry, 고노이즈에서 global topology를 학습.
- **Cross-distillation으로 hallucination 완화** — AF-Multimer v2.3 예측을 학습 데이터에 섞어 비구조 영역이 길게 펼쳐지도록 유도.
- **새 신뢰도 지표 PDE** — 디퓨전 mini-rollout으로 pLDDT/PAE에 더해 distance error matrix까지 예측.

## 방법론 및 아키텍처

| 구성요소 | 내용 |
|---|---|
| 입력 표현 | 단백질·DNA·RNA 서열 + 잔기 변형 + 리간드 SMILES, 모두 토큰+원자 단위로 통합 |
| MSA 모듈 | 4 블록만; pair-weighted averaging; MSA rep은 trunk 후반부에서 사용 안 함 |
| Pairformer | 48 블록, pair + single representation 갱신 (Evoformer 후속) |
| Diffusion Module | raw 원자 좌표 입출력, ~200 denoising step, 회전 프레임 / 등변성 없음 |
| 신뢰도 헤드 | pLDDT (per-atom), PAE (pairwise), **PDE** (pairwise distance error) |
| 학습 | PDB + AlphaFold-Multimer v2.3 cross-distillation; small→large crop 커리큘럼 |
| 추론 | multi-seed로 chirality/clash 회피, 분포로부터 샘플링 |

```
Inputs (protein seq + NA seq + SMILES + mods)
  └→ MSA (4 blk, light) ─┐
                         ├→ Pairformer trunk (48 blk, pair+single)
  └→ Token / atom emb ───┘                    │
                                              ▼
                                  Diffusion Module (~200 steps)
                                              │
                                              ▼
                                3D atomic coordinates (with pLDDT/PAE/PDE)
```

## 결과

- **PoseBusters 단백질-리간드**: AF3 > Vina/Gold (p=2.27×10⁻¹³), > RoseTTAFold All-Atom (p=4.45×10⁻²⁵). 도킹 도구가 사용하는 holo 구조 정보를 AF3는 받지 않고도 이긴다.
- **항원-항체**: AF-Multimer v2.3 대비 큰 폭의 향상.
- **단백질-핵산**: NA-전문 예측기보다 정확.
- **단백질-단백질 인터페이스**: AF2 대비 LDDT 상승, large-crop fine-tuning 단계에서 가장 큰 개선.
- **CAID 2 disorder 벤치마크**: cross-distillation으로 hallucination이 줄어 무질서 영역 처리 개선.

## 활용 가이드

- **언제 AF3가 적합한가**: (1) 단백질에 리간드/이온/RNA/DNA가 같이 묶인 복합체, (2) 항원-항체 인터페이스 예측, (3) 잔기 변형(인산화, glycosylation)을 포함한 모델링.
- **운영상 주의**: 출시 시점 weight는 비공개, **AlphaFold Server**(비상업)로만 접근. 일일 job 제한, 일부 리간드 제외. (이후 정책 변경은 별도 확인 필요.)
- **샘플링 전략**: 다중 seed로 여러 sample을 얻어 chirality/clash 검사. 한 번에 하나의 정답을 기대하지 말 것 — 디퓨전은 분포를 만든다.
- **순수 단백질-only & 속도가 중요할 때**: ESMFold/AF2를 선호. AF3는 inference 비용이 더 큼.
- **De novo design 검증**: AF3의 복합체 예측은 RFdiffusion 출력의 ligand/cofactor 결합 검증에 유용.

## 관련 논문

- [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]] :supports — ESMFold (단일 서열, 빠름) vs AF3 (멀티모달, 정밀)
- [[protein-foundation/watson-2023-de-novo-design-of-protein]] :supports — RFdiffusion도 디퓨전이지만 디자인 목적; AF3는 예측 목적
- [[protein-foundation/hayes-2025-simulating-500-million-years-of]] :supports — ESM3는 토큰화된 multimodal LM, AF3는 디퓨전-기반 multimodal predictor
- [[protein-foundation/su-2024-saprot-protein-language-modeling-with]] :supports — 구조 인지 LM, AF2/AF3 예측 구조를 학습 데이터로 활용

- [[protein-foundation/hayes-2025-simulating-500-million-years-of]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/lin-2023-evolutionary-scale-prediction-of-atomic]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/su-2024-saprot-protein-language-modeling-with]] — 같은 카테고리 (protein foundation)
- [[protein-foundation/watson-2023-de-novo-design-of-protein]] — 같은 카테고리 (protein foundation)
- [[foundation-perspectives/vaswani-2017-attention-is-all-you-need]] — Transformer 백본
- [[foundation-perspectives/wang-2023-scientific-discovery-in-the-age]] — AI for Science 일반 framework

## 용어집

- **Evoformer / Pairformer** — AF2 / AF3의 trunk; pair + (MSA, single) representation을 반복 정제.
- **Structure Module** — AF2의 프레임-기반 좌표 생성기 (AF3에서는 Diffusion Module이 대체).
- **Diffusion Module** — Gaussian noise를 가했다가 점진적으로 denoise하는 생성 모델. 좌표에 직접 적용.
- **pLDDT / PAE / PDE** — per-atom 정확도 / pairwise alignment error / pairwise distance error (PDE는 AF3 신규).
- **Cross-distillation** — 더 검증된 구버전(AF-Multimer)의 출력으로 신버전을 보강 학습.
- **Mini-rollout** — 학습 중 디퓨전을 짧게 끝까지 굴려 confidence head 지도를 만드는 트릭.
- **PoseBusters** — 단백질-리간드 예측 벤치마크.
- **SMILES** — 분자식 텍스트 표기.
- **Hallucination** — 생성형 모델이 그럴듯하지만 잘못된 구조를 만들어내는 현상.
- **MSA** — 다중 서열 정렬, 공진화 신호의 표준 입력.

---

## 내 메모

> 이 섹션은 위키 운영자(KRIBB 백부경)가 직접 작성. LLM은 이 섹션을 읽기만 하고 수정하지 않는다.
