---
name: kf-search
description: >
  This skill should be used when the user says "아카이브 검색", "이전에 수집한 거 찾아줘",
  "search", "키워드 검색", "Claude 관련 기사 모아줘", "지난주 뉴스 찾아줘",
  or when searching across the accumulated archive for specific topics, companies, or keywords.
  Searches the entire knowledge archive by keyword, company, date range, or category.
version: 0.1.0
---

# KF-Search — 아카이브 검색

축적된 아카이브에서 키워드, 기업, 날짜 범위, 카테고리로 검색한다.

## 검색 모드

### 키워드 검색

Grep 도구로 `archive/` 전체를 대상으로 검색한다.

```
검색어: "Claude"
범위: archive/**/*.md
```

### 기업별 검색

`archive/index/by-company.md` 인덱스를 먼저 확인하고, 해당 기업 엔트리를 모아서 보여준다.

### 날짜 범위 검색

`archive/YYYY-MM-DD/` 폴더 구조를 활용하여 특정 기간 내 엔트리를 필터링한다.

### 카테고리 검색

엔트리 파일명의 카테고리 ID(C1~C8)로 필터링한다.

## 검색 결과 형식

```markdown
## 검색 결과: "{검색어}"

총 N건 (YYYY-MM-DD ~ YYYY-MM-DD)

### 1. {엔트리 제목}
- 날짜: YYYY-MM-DD
- 카테고리: {카테고리명}
- 요약: {1~2문장}
- 파일: archive/YYYY-MM-DD/entries/{파일명}

### 2. ...
```

## 핵심 규칙

- 검색 결과는 최신순으로 정렬한다.
- 결과가 10건 이상이면 상위 10건만 보여주고 전체 건수를 안내한다.
- 연관 검색어를 함께 제안한다.
