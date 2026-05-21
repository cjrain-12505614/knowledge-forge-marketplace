---
name: kf-session
description: >
  This skill should be used when the user says "세션 시작", "세션 종료",
  "이어서 하자", "어디까지 했지", "세션 저장", "오늘 여기까지",
  or when session lifecycle management is needed for the knowledge archive project.
  Manages session start/end protocols, progress tracking, and lessons learned.
version: 0.1.0
---

# KF-Session — 세션 관리

지식 아카이브 프로젝트의 세션 시작/종료 프로토콜을 관리한다.

## 세션 시작 프로토콜

새 세션이 시작되면 아래 순서를 따른다.

1. **최신 세션 로그 확인**: `archive/sessions/` 에서 가장 최근 `session_YYYY-MM-DD_N.md`를 읽는다.
2. **아카이브 현황 파악**: 최근 수집 날짜, 미수집일 여부를 확인한다.
3. **교훈 확인**: `archive/sessions/lessons.md`에서 최근 교훈을 읽는다.
4. **보고**: 아래 형식으로 보고한다.

```
이전 세션: YYYY-MM-DD, {주요 수행 내용}
아카이브 현황: 마지막 수집일 YYYY-MM-DD, 총 N건
미수집일: {있으면 나열}
이번 세션 제안: {수집/브리핑/콘텐츠 등}

진행할까요?
```

## 세션 종료 프로토콜

세션 종료 시 아래를 수행한다.

1. **세션 로그 작성**: `archive/sessions/session_YYYY-MM-DD_N.md`

```markdown
# 세션 로그: YYYY-MM-DD #N

## 수행 작업
- [x] 완료된 작업
- [ ] 미완료 작업

## 수집 현황
- 날짜: YYYY-MM-DD
- 수집: N건
- 브리핑: 생성/미생성
- SNS 콘텐츠: 생성/미생성

## 특이사항
- (이슈, 트렌드 메모 등)

## 다음 세션 TODO
1. (우선순위 순)
```

2. **교훈 기록**: 새로운 교훈이 있으면 `archive/sessions/lessons.md`에 추가한다.

```markdown
### YYYY-MM-DD
- {교훈} → {규칙}
```

3. **종료 보고**: "세션 저장 완료. 다음 세션에서 [TODO]부터 진행합니다."

## 핵심 규칙

- 세션 로그의 "다음 세션 TODO"가 가장 중요한 인수인계 문서다.
- "무엇을 했는지"보다 **"다음에 무엇을 해야 하는지"**를 우선한다.
