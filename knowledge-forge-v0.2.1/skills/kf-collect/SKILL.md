---
name: kf-collect
description: >
  This skill should be used when the user says "정보 수집해줘", "오늘 AI 뉴스 모아줘",
  "수집 시작", "collect", "최신 AI 소식 찾아줘", "모델 발표 뭐 있어",
  "논문 새로 나온 거 있어", "오픈소스 트렌딩 확인해줘",
  or when daily AI intelligence collection is needed.
  Collects information across 8 categories using web search and saves individual entries as markdown files.
version: 0.1.0
---

# KF-Collect — AI 정보 수집

8개 카테고리에서 최신 AI 정보를 웹 검색으로 수집하고 개별 엔트리 마크다운으로 저장한다.

## 수집 카테고리

| ID | 카테고리 | 주요 소스 |
|----|----------|-----------|
| C1 | AI 모델 발표 | HuggingFace Trending, arXiv cs.AI, 공식 블로그(Anthropic/OpenAI/Google/Meta) |
| C2 | 제품 업데이트 | 각 사 공식 블로그/Changelog, GeekNews |
| C3 | AI 뉴스 | **GeekNews(news.hada.io)** ⭐, The Rundown AI, AI Trends KR, TechCrunch, AI타임스 |
| C4 | AI 논문 | HuggingFace Papers, arXiv, Papers With Code, Semantic Scholar, Paper Digest |
| C5 | 오픈소스/GitHub | GitHub Trending, HuggingFace Models/Spaces, GeekNews |
| C6 | 정책·규제 | AI타임스, BIG KINDS, EU AI Act Portal, NIST AI |
| C7 | 투자·펀딩 | TechCrunch, The Rundown AI, GeekNews |
| C8 | 실무 툴 리뷰 | Product Hunt, There's An AI For That, OpenTools.ai, GeekNews |

> 카테고리별 상세 소스 목록과 검색 쿼리는 `references/sources.md`를 참조한다.

## 수집 절차

### 1단계: 날짜 폴더 생성

```
archive/YYYY-MM-DD/entries/
```

해당 날짜 폴더가 없으면 생성한다.

### 2단계: 카테고리별 웹 검색

각 카테고리에 대해 WebSearch 도구를 사용하여 최근 24시간 내 정보를 검색한다.

검색 전략:
- 카테고리별 2~3개 검색 쿼리를 실행
- 영문 + 국문 검색을 병행
- 검색 결과에서 중복을 제거하고 유의미한 항목만 선별

### 3단계: 엔트리 작성

수집된 각 정보를 개별 마크다운 파일로 저장한다.

파일명 규칙: `{카테고리ID}-{순번}-{슬러그}.md`
예: `C1-01-claude-4-release.md`, `C3-02-openai-codex-launch.md`

엔트리 형식은 `templates/entry.md` 템플릿을 따른다.

### 4단계: 타임라인 자동 업데이트

수집 완료 후, kf-timeline 스킬의 규칙에 따라 각 엔트리의 `company` 필드를 확인하고
해당 기업/제품 타임라인 파일에 이벤트를 자동 추가한다.

- `archive/timelines/companies/{slug}.md` — 기업 타임라인
- `archive/timelines/products/{slug}.md` — 제품 타임라인 (태그에 제품명이 포함된 경우)

### 5단계: 수집 로그 작성

`archive/YYYY-MM-DD/collect-log.md`에 수집 결과를 기록한다.

```markdown
# 수집 로그: YYYY-MM-DD

## 수집 현황
| 카테고리 | 수집 건수 | 주요 항목 |
|----------|----------|----------|

## 수집 시간
- 시작: HH:MM
- 완료: HH:MM

## 특이사항
- (검색 실패, 주목할 트렌드 등)
```

## 날짜 신선도 규칙 (Freshness Policy)

수집된 정보의 날짜 신선도를 반드시 검증한다. **과거 데이터를 오늘자 뉴스처럼 브리핑하면 안 된다.**

### 신선도 등급

| 등급 | 기준 | 브리핑 배치 |
|------|------|-------------|
| **🔴 TODAY** | 원문 게시일이 수집일 당일 또는 전일 (24시간 이내) | **헤드라인 / 카테고리 요약** (메인 브리핑) |
| **🟡 THIS_WEEK** | 원문 게시일이 수집일 기준 2~7일 이내 | **이번 주 주요 흐름** (별도 섹션) |
| **⚪ OLDER** | 원문 게시일이 7일 초과 | **수집 제외** (특별한 사유 없으면 스킵) |

### 날짜 검증 절차

1. **검색 쿼리에 날짜 제한 추가**: 검색 시 "today", "yesterday", "past 24 hours" 등 시간 한정어를 포함한다.
2. **원문 게시일 확인**: 검색 결과의 기사/논문/포스트에서 **실제 게시일(date_published)**을 반드시 확인한다.
   - 기사 본문의 날짜, byline, meta 태그 등에서 추출
   - 날짜를 확인할 수 없는 경우 `date_published: "unknown"`으로 표기하고, 신선도를 `THIS_WEEK`로 보수적 분류
3. **신선도 태깅**: 엔트리 frontmatter에 `freshness` 필드를 추가한다.
4. **OLDER 항목 처리**: 7일 초과 항목은 원칙적으로 수집하지 않는다. 단, 업계에 큰 임팩트가 있어 아직 활발히 논의 중인 건은 `THIS_WEEK`로 분류하고 "후속 보도" 또는 "지속 이슈"로 명시한다.

### 수집 로그에 신선도 현황 추가

`collect-log.md`에 다음 현황을 반드시 포함한다:

```markdown
## 신선도 현황
| 등급 | 건수 | 비율 |
|------|------|------|
| 🔴 TODAY | X건 | X% |
| 🟡 THIS_WEEK | X건 | X% |
```

## 핵심 규칙

- **출처 필수**: 모든 엔트리에 원문 URL을 반드시 포함한다.
- **사실 기반**: 수집 단계에서는 해석을 최소화하고 사실만 기록한다.
- **중복 방지**: 동일 사건의 복수 보도는 가장 신뢰할 수 있는 1~2개 출처만 남긴다.
- **시간대 인식**: KST 오전 9시 기준, 전일 저녁~당일 아침 사이의 해외 뉴스가 주 대상이다.
- **날짜 신선도 필수**: 모든 엔트리에 `freshness` 필드를 포함하고, `OLDER` 항목은 수집하지 않는다.
- **과거 뉴스 위장 금지**: `date_published`가 7일 이상 지난 정보를 오늘의 브리핑에 포함하지 않는다.
