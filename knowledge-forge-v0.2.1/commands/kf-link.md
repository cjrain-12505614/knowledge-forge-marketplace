---
description: 수집된 정보 간 관계 분석·연결
allowed-tools: Read, Write, Edit, Grep, Glob
argument-hint: [날짜 YYYY-MM-DD (생략 시 오늘)]
---

kf-link 스킬에 따라 수집된 엔트리 간 관계를 분석한다.

대상 날짜: $ARGUMENTS (없으면 오늘)

archive/{날짜}/entries/ 의 모든 엔트리를 읽고,
관계 매핑 → 테마 클러스터링 → 인덱스 업데이트 → link-report.md 작성을 수행한다.
