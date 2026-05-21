---
description: 아카이브 키워드 검색
allowed-tools: Read, Grep, Glob
argument-hint: <검색어> [--from YYYY-MM-DD] [--to YYYY-MM-DD] [--category C1-C8]
---

kf-search 스킬에 따라 아카이브를 검색한다.

검색어: $ARGUMENTS

archive/ 전체를 대상으로 Grep 검색을 실행하고,
결과를 최신순으로 정렬하여 보여준다.
날짜 범위나 카테고리가 지정되면 필터링한다.
