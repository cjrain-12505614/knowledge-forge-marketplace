---
name: kf-link
description: >
  This skill should be used when the user says "정보 연결해줘", "관계 분석해줘",
  "link", "수집된 거 연결", "뉴스랑 논문 연결", "트렌드 분석",
  or when collected entries need cross-referencing and relationship analysis.
  Analyzes relationships between collected entries, tags connections, and identifies emerging trends.
version: 0.1.0
---

# KF-Link — 정보 연결·분석

수집된 엔트리 간 관계를 분석하고, 태그를 연결하며, 트렌드를 식별한다.

## 연결 유형

| 유형 | 설명 | 예시 |
|------|------|------|
| 인과 | A가 B의 원인/결과 | 모델 발표 → 관련 뉴스 보도 |
| 발전 | A의 후속 버전/개선 | GPT-4o → GPT-4o mini |
| 경쟁 | 동일 영역의 대안 | Claude 4 vs GPT-5 vs Gemini 2 |
| 적용 | 기술이 실무에 반영 | 논문 기술 → 실무 툴 탑재 |
| 규제 | 기술에 대한 정책 대응 | AI 모델 → EU AI Act 적용 |
| 투자 | 기술/기업에 대한 자금 흐름 | 스타트업 펀딩 → 기술 상용화 |

## 분석 절차

### 1단계: 당일 엔트리 전체 읽기

`archive/YYYY-MM-DD/entries/` 내 모든 엔트리를 읽고 핵심 키워드를 추출한다.

### 2단계: 관계 매핑

엔트리 간 관계를 식별하고, 각 엔트리의 frontmatter `related` 필드에 연결 정보를 추가한다.

```yaml
related:
  - id: C1-01
    type: 경쟁
    note: "동일 시기 발표된 경쟁 모델"
  - id: C3-02
    type: 인과
    note: "이 발표에 대한 뉴스 보도"
```

### 3단계: 테마 클러스터링

연결된 엔트리들을 테마별로 묶는다.

```markdown
## 오늘의 테마

### 테마 1: {제목}
- 관련 엔트리: C1-01, C3-02, C7-01
- 핵심 흐름: {1~2문장 요약}
- 더존비즈온 시사점: {실무 관점 인사이트}
```

### 4단계: 인덱스 업데이트

`archive/index/` 디렉토리의 인덱스 파일을 업데이트한다.

- `by-company.md` — 기업별 엔트리 목록
- `by-topic.md` — 주제별 엔트리 목록
- `by-trend.md` — 트렌드 타임라인

### 5단계: 연결 보고서 저장

`archive/YYYY-MM-DD/link-report.md`에 분석 결과를 저장한다.

## 핵심 규칙

- **과잉 연결 방지**: 무리한 연결보다 명확한 관계만 기록한다.
- **더존비즈온 맥락**: PM 업무와 연결되는 인사이트를 반드시 포함한다.
- **누적 트렌드**: 단일 날짜뿐 아니라 최근 1주일 트렌드도 언급한다.
