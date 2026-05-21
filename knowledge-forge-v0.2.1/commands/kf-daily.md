---
description: 일일 파이프라인 전체 실행 (수집→연결→브리핑→콘텐츠)
allowed-tools: Read, Write, Edit, Grep, Glob, Bash, WebSearch, WebFetch, Agent
---

오늘 날짜(YYYY-MM-DD) 기준으로 kf-daily 파이프라인을 실행한다.

순서:
1. kf-collect 스킬에 따라 8개 카테고리 정보를 수집한다.
2. kf-link 스킬에 따라 수집된 엔트리 간 관계를 분석한다.
3. kf-briefing 스킬에 따라 일일 종합 브리핑을 생성한다.
4. kf-content 스킬에 따라 X, 페이스북, 인스타그램 콘텐츠를 제작한다.

각 단계 완료 후 결과물 존재를 확인하고 다음 단계로 넘어간다.
실패 시 중단하고 원인을 보고한다.
완료 후 파이프라인 요약 보고를 출력한다.
