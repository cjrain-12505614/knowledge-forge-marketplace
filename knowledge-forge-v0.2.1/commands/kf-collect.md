---
description: AI 정보 수집 실행
allowed-tools: Read, Write, Edit, Grep, Glob, WebSearch, WebFetch
argument-hint: [날짜 YYYY-MM-DD (생략 시 오늘)]
---

kf-collect 스킬에 따라 AI 정보를 수집한다.

날짜 인자가 주어지면 해당 날짜로, 없으면 오늘 날짜로 수집한다.
대상 날짜: $ARGUMENTS (없으면 오늘)

references/sources.md의 소스 가이드를 참조하여 카테고리별 웹 검색을 실행하고,
개별 엔트리를 archive/{날짜}/entries/ 에 저장한다.
수집 완료 후 collect-log.md를 작성한다.
