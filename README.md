# Bukyung Wiki

Bukyung Wiki는 논문 PDF를 직접 재배포하지 않고, 보유 논문 목록과 공개 가능한 wiki note를 정적 웹앱으로 탐색하는 LLM-assisted research wiki 데모입니다.

이 public-lite 배포본은 시연과 재사용을 위해 **Bio foundation model 흐름**만 남긴 축소판입니다. 내부 작업본의 PDF, source summary, 원문 추출물, 발표 작업 파일은 포함하지 않습니다.

## What This Is

- **정적 프론트엔드**: `index.html`과 `_data.json`만으로 검색, 카테고리 필터, overview, graph view를 볼 수 있습니다.
- **논문 리스트**: 제목, 저자, 연도, 저널, DOI, 카테고리를 확인할 수 있습니다.
- **개별 wiki note**: 논문별 한국어 정리 문서를 열어볼 수 있습니다.
- **흐름 문서**: 여러 논문을 묶어 기반모델 연구 흐름을 설명하는 overview를 볼 수 있습니다.

## Included Content

| 항목 | 수 |
|---|---:|
| PDF 원문 | 0 |
| source summary | 0 |
| wiki note | 49 |
| category | 7 |
| overview | 2 |

포함된 overview:

- `bio-foundation-flow` — 생명과학 기반모델 흐름
- `virtual-cell-flow` — 가상세포 흐름

## Public Distribution Policy

- 논문 PDF 원문은 포함하지 않습니다.
- `papers/`, `sources/`, `_bio/`, `_presentation/`, 내부 작업 로그는 포함하지 않습니다.
- 논문 확인은 DOI, 출판사 링크, PubMed/저널 페이지 등 원 출처로 이동해 확인합니다.
- 이 저장소는 논문 원문 대체물이 아니라, 논문 목록과 wiki note를 LLM-assisted retrieval용으로 구조화한 예시입니다.

## Local Preview

```powershell
python -m http.server 8791
```

그 다음 브라우저에서 엽니다.

```text
http://127.0.0.1:8791/index.html
```

## Reuse

자기 논문으로 같은 구조를 만들려면 다음 순서로 진행하면 됩니다.

1. PDF는 비공개 작업 폴더의 `papers/`에 둡니다.
2. 각 논문마다 `sources/{stem}.md`와 `wiki/{category}/{stem}.md`를 만듭니다.
3. `index.md`와 `_data.json`을 다시 생성합니다.
4. 배포 전에는 PDF와 source summary를 제외한 public-lite 폴더만 공개합니다.

## References

이 프로젝트는 아래 공개 gist의 아이디어에서 개념적 영향을 받았습니다.

- [Andrej Karpathy, `llm-wiki`](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) — LLM-readable wiki / compounding knowledge-base idea

이 저장소는 독립적인 Bukyung Wiki 데모이며, 위 reference의 공식 프로젝트가 아니고 논문 원문도 재배포하지 않습니다.
