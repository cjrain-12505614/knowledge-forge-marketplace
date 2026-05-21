---
name: kf-timeline
description: >
  This skill should be used when the user says "타임라인 보여줘", "기업 히스토리",
  "timeline", "OpenAI 타임라인", "Claude 변천사", "기업별 연표",
  "제품 히스토리 정리해줘", "특정 기업 추적",
  or when viewing or managing company/product timelines.
  Manages auto-cumulative timelines per company and product, updated on every collection.
version: 0.1.0
---

# KF-Timeline — 기업/제품 타임라인 관리

기업·제품별로 주요 이벤트를 시간순으로 누적 관리한다.
kf-collect 실행 시 자동으로 업데이트되며, 개별 조회도 가능하다.

## 타임라인 저장 구조

```
archive/timelines/
├── companies/
│   ├── openai.md
│   ├── anthropic.md
│   ├── google-deepmind.md
│   ├── meta-ai.md
│   └── {company-slug}.md
└── products/
    ├── chatgpt.md
    ├── claude.md
    ├── gemini.md
    └── {product-slug}.md
```

## 타임라인 파일 형식

```markdown
---
type: company  # 또는 product
name: "OpenAI"
slug: "openai"
last_updated: "2026-03-29"
total_events: 15
---

# OpenAI 타임라인

## 2026

### 2026-03-29
- **[C1-01] GPT-5 발표** — 멀티모달 네이티브, 200K 컨텍스트. (freshness: TODAY)
  → [엔트리 보기](../../2026-03-29/entries/C1-01-gpt5-release.md)

### 2026-03-25
- **[C2-03] ChatGPT Plugins v3 출시** — 외부 API 직접 호출 지원. (freshness: THIS_WEEK)
  → [엔트리 보기](../../2026-03-25/entries/C2-03-chatgpt-plugins-v3.md)

## 2026-Q1 요약
- 주요 흐름: GPT-5 발표, Sora 정식 출시, 에이전트 기능 강화
- 총 이벤트: 23건 (모델 8, 제품 9, 투자 3, 정책 3)
```

## 자동 업데이트 규칙

### kf-collect 연동

kf-collect 실행 후, 모든 수집 엔트리에 대해 다음을 수행한다:

1. **기업 식별**: 엔트리의 `company` 필드에서 관련 기업을 추출한다.
2. **파일 확인**: `archive/timelines/companies/{slug}.md`가 존재하면 업데이트, 없으면 신규 생성.
3. **이벤트 추가**: 해당 날짜 섹션에 이벤트를 시간 역순으로 추가한다.
4. **메타데이터 갱신**: `last_updated`, `total_events` 카운트를 갱신한다.

### 제품 타임라인 규칙

- 엔트리 태그에 특정 제품명이 포함된 경우, 제품 타임라인에도 추가한다.
- 주요 추적 제품: ChatGPT, Claude, Gemini, Copilot, Llama, Mistral, Stable Diffusion, Midjourney, Sora, Cursor
- 사용자가 추적 제품을 추가/제거할 수 있다.

### 슬러그 정규화

| 기업명 | 슬러그 |
|--------|--------|
| OpenAI | openai |
| Anthropic | anthropic |
| Google DeepMind | google-deepmind |
| Meta AI | meta-ai |
| Microsoft | microsoft |
| Apple | apple |
| NVIDIA | nvidia |
| Mistral AI | mistral-ai |
| Stability AI | stability-ai |
| Hugging Face | huggingface |

신규 기업은 `이름 → 소문자 → 공백을 하이픈으로` 규칙으로 슬러그를 생성한다.

## 과거 데이터 부트스트랩 (Backfill)

타임라인 파일이 없거나 처음 시작할 때, 웹 검색으로 과거 이벤트를 소급하여 채운다.

### 실행 조건

- 타임라인 파일이 존재하지 않는 경우 (신규 기업/제품 추가 시)
- 사용자가 명시적으로 "과거 데이터 가져와줘", "backfill", "초기화해줘" 등을 요청한 경우

### 부트스트랩 절차

1. **기간 설정**: 기본값은 최근 12개월. 사용자가 기간을 지정하면 그에 따른다.
   - 예: "OpenAI 2024년부터", "Anthropic 최근 6개월"

2. **웹 검색으로 주요 이벤트 수집**: 아래 검색 패턴을 사용한다.
   ```
   "{기업명} major announcements {연도}"
   "{기업명} product launches {연도}"
   "{기업명} AI model release history"
   "{기업명} funding milestones"
   ```
   - 분기 단위로 나누어 검색 (Q1~Q4) — 한 번에 너무 넓은 범위를 검색하면 누락이 생김
   - 공식 블로그, 위키피디아 타임라인, TechCrunch 등 신뢰할 수 있는 출처 우선

3. **이벤트 선별 기준**: 모든 소식이 아닌 **주요 이벤트**만 수집한다.
   - 신규 모델/제품 출시
   - 대규모 투자 유치 (억 달러 이상)
   - 주요 파트너십·인수합병
   - 서비스 출시/종료
   - CEO 교체 등 조직 변화
   - 규제 대응 (EU AI Act 대응, 소송 등)

4. **타임라인 파일 생성**: 수집된 이벤트를 날짜 역순으로 정렬하여 파일 생성.
   - 출처 URL 반드시 포함
   - 엔트리 ID는 `HIST-{YYYYMMDD}-{순번}` 형식 사용 (일일 수집 엔트리와 구분)
   - 예: `HIST-20250115-01`

5. **부트스트랩 완료 표시**: 파일 상단 frontmatter에 기록.
   ```yaml
   bootstrapped: true
   bootstrap_date: "2026-03-29"
   bootstrap_range: "2024-01-01 ~ 2026-03-28"
   bootstrap_events: 47
   ```

### 부트스트랩 이벤트 형식

일일 수집 엔트리와 다르게, 출처 링크만 포함하고 내부 엔트리 파일은 생성하지 않는다.

```markdown
### 2025-01-15
- **[HIST-20250115-01] OpenAI o3 정식 출시** — AGI 수준 추론 능력 주장, 코딩/수학 SOTA.
  → 출처: [OpenAI Blog](https://openai.com/blog/o3) | [TechCrunch](https://techcrunch.com/...)

### 2025-01-08
- **[HIST-20250108-01] $6.6B 추가 투자 유치** — 소프트뱅크 주도, 기업가치 $157B.
  → 출처: [Bloomberg](https://bloomberg.com/...)
```

### 주요 추적 기업 기본 부트스트랩 목록

프로젝트 시작 시 아래 기업들은 최근 12개월 부트스트랩을 권장한다:

| 기업 | 슬러그 | 우선순위 |
|------|--------|----------|
| OpenAI | openai | ⭐⭐⭐ |
| Anthropic | anthropic | ⭐⭐⭐ |
| Google DeepMind | google-deepmind | ⭐⭐⭐ |
| Meta AI | meta-ai | ⭐⭐ |
| Microsoft | microsoft | ⭐⭐ |
| NVIDIA | nvidia | ⭐⭐ |
| Mistral AI | mistral-ai | ⭐ |
| Hugging Face | huggingface | ⭐ |

---

## 수동 조회

사용자가 특정 기업/제품 타임라인을 요청하면:

1. 해당 파일을 읽어 전체 또는 기간 필터링 후 표시한다.
2. 요청 예시:
   - "OpenAI 타임라인" → `archive/timelines/companies/openai.md` 전체
   - "Claude 최근 3개월" → `archive/timelines/products/claude.md`에서 최근 3개월 필터
   - "2026년 1분기 Anthropic" → 2026-Q1 구간만

## 분기 요약 자동 생성

분기가 끝나면 (3월, 6월, 9월, 12월 말) 각 타임라인 파일에 분기 요약 섹션을 추가한다:

```markdown
## YYYY-QN 요약
- 주요 흐름: {핵심 이벤트 3~5개}
- 총 이벤트: N건 (카테고리별 분포)
- 주목할 변화: {트렌드 전환점, 전략 변화 등}
```

## 날짜 기입 원칙 (핵심)

타임라인의 날짜 기준은 **원문 게시일(date_published)** 이다. 수집일(date_collected)이 아니다.

### 소급 기입 규칙

- 오늘 수집했더라도 `date_published`가 3일 전이면, 타임라인에서 3일 전 날짜 섹션에 기입한다.
- 수집 당일에 해당 날짜 폴더가 없어도 타임라인 파일에는 실제 게시일 기준으로 기입한다.
- **예시**: 2026-03-29에 수집한 SoftBank $400억 브릿지론이 `date_published: 2026-03-27`이면
  → 타임라인에서 `### 2026-03-27` 섹션에 기입 (2026-03-29 섹션이 아님)

### 소급 기입 시 섹션 삽입 방법

해당 날짜 섹션이 없으면 시간순으로 적절한 위치에 새 섹션을 삽입한다:

```markdown
## 2026

### 2026-03-29   ← 오늘 수집한 항목
- ...

### 2026-03-27   ← 소급 기입 (오늘 수집했지만 실제 게시일)
- **[C7-02] SoftBank $400억 브릿지론 확보** — OpenAI 추가 투자를 위한 브릿지론 조달.
  → [엔트리 보기](../../2026-03-29/entries/C7-02-softbank-40b-openai-bridge.md)
  _(수집일: 2026-03-29)_

### 2026-03-25   ← 이전 날짜
- ...
```

수집일과 게시일이 다를 경우 `_(수집일: YYYY-MM-DD)_` 를 한 줄 추가하여 추적 가능하게 한다.

## 핵심 규칙

- **중복 방지**: 동일 엔트리가 이미 타임라인에 있으면 추가하지 않는다 (엔트리 ID로 판별).
- **날짜 정확성**: 반드시 `date_published` 기준으로 배치한다. 수집일(date_collected)로 배치하지 않는다.
- **소급 기입 허용**: 과거 날짜의 뉴스를 나중에 수집해도 해당 날짜 섹션에 소급 기입한다.
- **수집일 표기**: 게시일과 수집일이 2일 이상 차이나면 `_(수집일: YYYY-MM-DD)_` 를 병기한다.
- **상대 경로**: 엔트리 링크는 상대 경로를 사용한다 (엔트리 파일은 수집일 폴더 기준).
- **읽기 편의**: 최신이 위로 오도록 역순 정렬한다.
