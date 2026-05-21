# Knowledge Forge

AI 업계 인텔리전스 수집·분석·브리핑·콘텐츠 자동화 플러그인.

매일 8개 카테고리(모델 발표, 제품 업데이트, 뉴스, 논문, 오픈소스, 정책·규제, 투자·펀딩, 실무 툴)를 수집하고, 정보 연결·종합 브리핑·SNS 콘텐츠 제작까지 파이프라인으로 실행합니다.

## 파이프라인

```
수집 (kf-collect) → 연결 (kf-link) → 브리핑 (kf-briefing) → 콘텐츠 (kf-content)
```

매일 오전 9시(KST) 자동 실행 가능. 개별 단계 수동 실행도 지원.

## 컴포넌트

### Skills (8개)

| 스킬 | 역할 |
|------|------|
| `kf-collect` | 8개 카테고리별 웹 수집 + 엔트리 저장 |
| `kf-link` | 엔트리 간 관계 분석·태깅·테마 도출 |
| `kf-briefing` | 일일 종합 브리핑 마크다운 생성 |
| `kf-content` | SNS 콘텐츠 제작 (X/페이스북/인스타그램 차별화) |
| `kf-search` | 아카이브 전체 키워드·기업·날짜 검색 |
| `kf-status` | 아카이브 현황 대시보드 |
| `kf-session` | 세션 시작/종료 프로토콜·교훈 관리 |
| `kf-daily` | 일일 파이프라인 오케스트레이터 |

### Commands (9개)

| 커맨드 | 설명 |
|--------|------|
| `/kf-daily` | 전체 파이프라인 실행 |
| `/kf-collect [날짜]` | 정보 수집 |
| `/kf-link [날짜]` | 관계 분석 |
| `/kf-briefing [날짜]` | 브리핑 생성 |
| `/kf-content [날짜]` | SNS 콘텐츠 제작 |
| `/kf-search <검색어>` | 아카이브 검색 |
| `/kf-status` | 현황 대시보드 |
| `/kf-start-session` | 세션 시작 |
| `/kf-end-session` | 세션 종료 |

### Templates (5개)

| 템플릿 | 용도 |
|--------|------|
| `entry.md` | 개별 수집 엔트리 |
| `briefing.md` | 일일 종합 브리핑 |
| `content-x.md` | X(Twitter) 콘텐츠 |
| `content-facebook.md` | Facebook 포스트 |
| `content-instagram.md` | Instagram 카드뉴스 |

## 아카이브 폴더 구조

```
archive/
├── YYYY-MM-DD/
│   ├── entries/           # 개별 수집 엔트리
│   │   ├── C1-01-xxx.md
│   │   ├── C3-02-yyy.md
│   │   └── ...
│   ├── collect-log.md     # 수집 로그
│   ├── link-report.md     # 연결 분석 보고서
│   ├── briefing.md        # 일일 종합 브리핑
│   └── content/           # SNS 콘텐츠
│       ├── x.md
│       ├── facebook.md
│       └── instagram.md
├── index/                 # 인덱스
│   ├── by-company.md
│   ├── by-topic.md
│   └── by-trend.md
└── sessions/              # 세션 로그
    ├── session_YYYY-MM-DD_N.md
    └── lessons.md
```

## 수집 카테고리

| ID | 카테고리 | 핵심 소스 |
|----|----------|-----------|
| C1 | AI 모델 발표 | HuggingFace, arXiv, 공식 블로그 |
| C2 | 제품 업데이트 | 각 사 Changelog, GeekNews |
| C3 | AI 뉴스 | GeekNews, The Rundown AI, AI타임스 |
| C4 | AI 논문 | arXiv, Papers With Code, Semantic Scholar |
| C5 | 오픈소스/GitHub | GitHub Trending, HuggingFace |
| C6 | 정책·규제 | AI타임스, BIG KINDS, EU AI Act |
| C7 | 투자·펀딩 | TechCrunch, GeekNews |
| C8 | 실무 툴 | Product Hunt, There's An AI For That |

## 사용법

1. 플러그인 설치 후 워크스페이스에서 `/kf-daily` 실행
2. 또는 개별 단계: `/kf-collect` → `/kf-link` → `/kf-briefing` → `/kf-content`
3. 자동화: `schedule` 스킬로 매일 오전 9시 `/kf-daily` 스케줄 등록

## 저자

차민수 (cjrain.cms@gmail.com)
