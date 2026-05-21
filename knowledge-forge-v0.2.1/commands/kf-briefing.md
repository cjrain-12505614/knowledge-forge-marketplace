---
description: 일일 종합 브리핑 생성
allowed-tools: Read, Write, Edit, Grep, Glob
argument-hint: [날짜 YYYY-MM-DD (생략 시 오늘)]
---

kf-briefing 스킬에 따라 일일 종합 브리핑을 생성한다.

대상 날짜: $ARGUMENTS (없으면 오늘)

archive/{날짜}/entries/ 의 수집 엔트리와 link-report.md를 읽고,
templates/briefing.md 형식에 맞춰 briefing.md를 생성한다.
