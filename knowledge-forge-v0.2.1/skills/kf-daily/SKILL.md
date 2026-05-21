---
name: kf-daily
description: >
  This skill should be used when the user says "데일리 실행", "daily",
  "오늘 파이프라인 돌려", "전체 실행", "수집부터 콘텐츠까지",
  or when the full daily pipeline needs to run (collect → link → briefing → content).
  Orchestrates the complete daily intelligence pipeline in sequence.
version: 0.1.0
---

# KF-Daily — 일일 파이프라인 오케스트레이터

수집 → 연결 → 브리핑 → SNS 콘텐츠 제작까지 전체 파이프라인을 순차 실행한다.

## 실행 순서

### Step 1: 수집 (kf-collect)

8개 카테고리에서 최신 AI 정보를 수집한다.
완료 조건: `archive/YYYY-MM-DD/entries/` 에 엔트리 파일 생성 + `collect-log.md` 작성.

### Step 2: 연결 (kf-link)

수집된 엔트리 간 관계를 분석하고 테마를 도출한다.
완료 조건: 엔트리 related 필드 업데이트 + `link-report.md` 작성 + 인덱스 업데이트.

### Step 3: 브리핑 (kf-briefing)

종합 브리핑 마크다운을 생성한다.
완료 조건: `briefing.md` 생성 + 품질 체크리스트 통과.

### Step 4: 콘텐츠 (kf-content)

X, 페이스북, 인스타그램 콘텐츠를 제작한다.
완료 조건: `content/x.md`, `content/facebook.md`, `content/instagram.md` 생성.

### Step 5: SNS 게시 (kf-publish) — 선택적

카드뉴스 이미지를 X, Instagram, Facebook에 게시한다.
사용자가 "올려줘", "게시해줘", "publish" 등을 요청할 때만 실행한다.
자동 스케줄 실행 시에는 건너뛴다 (브라우저 조작이 필요하므로).
완료 조건: 3개 플랫폼 게시 확인.

### Step 6: 타임라인 업데이트 (kf-timeline)

수집된 엔트리의 기업/제품 정보를 기반으로 타임라인을 자동 업데이트한다.
완료 조건: `archive/timelines/` 내 관련 파일 업데이트.

> 주간/월간/분기 리포트(kf-report)는 별도로 실행한다 (일일 파이프라인에는 포함하지 않음).

### Step 7: 완료 보고

```markdown
## Daily 파이프라인 완료: YYYY-MM-DD

| 단계 | 상태 | 결과 |
|------|------|------|
| 수집 | 완료 | N건 수집 |
| 연결 | 완료 | N개 테마, N개 연결 |
| 브리핑 | 완료 | briefing.md 생성 |
| 콘텐츠 | 완료 | 3개 플랫폼 콘텐츠 생성 |
| SNS 게시 | 완료/건너뜀 | X·Instagram·Facebook |
| 타임라인 | 완료 | N개 기업/제품 타임라인 업데이트 |

헤드라인 Top 3:
1. {제목}
2. {제목}
3. {제목}
```

## 중단 및 재개

- 각 Step은 독립적으로 실행 가능하다. 중단 시 마지막 완료 Step 이후부터 재개한다.
- 특정 Step만 재실행하려면 개별 스킬(kf-collect, kf-link 등)을 직접 호출한다.

## 스케줄 실행

매일 오전 9시(KST) 자동 실행. 스케줄 설정은 `schedule` 스킬을 통해 등록한다.
자동 실행 시에도 동일한 파이프라인 순서를 따른다.

## 핵심 규칙

- **순서 엄수**: 반드시 수집 → 연결 → 브리핑 → 콘텐츠 순서로 진행한다.
- **단계별 검증**: 각 Step 완료 후 결과물 존재 여부를 확인하고 다음으로 넘어간다.
- **실패 시 중단**: 한 Step이 실패하면 다음으로 넘어가지 않고, 실패 원인을 보고한다.
