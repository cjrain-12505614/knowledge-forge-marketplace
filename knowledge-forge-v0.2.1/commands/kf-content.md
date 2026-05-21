---
description: SNS 콘텐츠 제작 (X/페이스북/인스타)
allowed-tools: Read, Write, Edit, Grep, Glob
argument-hint: [날짜 YYYY-MM-DD (생략 시 오늘)]
---

kf-content 스킬에 따라 SNS 콘텐츠를 제작한다.

대상 날짜: $ARGUMENTS (없으면 오늘)

archive/{날짜}/briefing.md를 읽고,
3개 플랫폼(X, 페이스북, 인스타그램) 각각에 최적화된 콘텐츠를 제작하여
archive/{날짜}/content/ 에 저장한다.
