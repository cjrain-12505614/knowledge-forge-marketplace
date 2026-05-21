---
name: kf-status
description: >
  This skill should be used when the user says "아카이브 현황", "status",
  "지금까지 몇 건 수집했어", "통계 보여줘", "아카이브 대시보드",
  or when an overview of the archive's cumulative status is needed.
  Shows archive statistics including total entries, category distribution, and collection history.
version: 0.1.0
---

# KF-Status — 아카이브 현황 대시보드

아카이브의 누적 현황을 한눈에 보여준다.

## 대시보드 항목

### 1. 전체 통계

```
총 수집일: N일
총 엔트리: N건
총 브리핑: N건
총 SNS 콘텐츠: N세트
```

### 2. 카테고리별 분포

`archive/` 내 모든 엔트리를 파일명의 카테고리 ID로 분류하여 집계한다.

```
| 카테고리 | 건수 | 비율 |
|----------|------|------|
| C1 모델 발표 | N | N% |
| C2 제품 업데이트 | N | N% |
| ... | | |
```

### 3. 최근 7일 수집 이력

```
| 날짜 | 수집 건수 | 브리핑 | SNS |
|------|----------|--------|-----|
| YYYY-MM-DD | N | O/X | O/X |
```

### 4. 자주 등장하는 기업/키워드 Top 10

`archive/index/by-company.md`와 엔트리 태그에서 빈도 집계.

## 생성 절차

1. `archive/` 하위 날짜 폴더를 스캔한다.
2. 각 날짜의 `entries/` 파일 수를 집계한다.
3. `briefing.md`, `content/` 존재 여부를 확인한다.
4. 카테고리별·기업별 빈도를 계산한다.
5. 대시보드를 출력한다.

## 핵심 규칙

- 스캔 기반으로 실제 파일에서 집계한다 (캐시 사용 안 함).
- 누락된 날짜가 있으면 "미수집"으로 표시한다.
