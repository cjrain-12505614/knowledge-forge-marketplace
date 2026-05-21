# 카테고리별 수집 소스 가이드

## 우선순위 소스 (매일 필수 확인)

### GeekNews (news.hada.io) ⭐
- **용도**: C1~C8 전 카테고리 커버
- **특징**: 한국어 큐레이션, 테크 커뮤니티 투표 기반, 빠른 속보
- **수집 방법**: WebSearch "site:news.hada.io AI" 또는 직접 접근
- **참고**: 사용자가 지정한 핵심 소스. 매일 최우선 확인.

---

## C1: AI 모델 발표

| 소스 | URL | 특징 |
|------|-----|------|
| HuggingFace Trending Papers | huggingface.co/papers/trending | 커뮤니티 투표 기반, 신규 모델 논문 빠른 노출 |
| arXiv cs.AI | arxiv.org/list/cs.AI/recent | 공식 프리프린트, 매일 업데이트 |
| Anthropic Blog | anthropic.com/news | Claude 관련 공식 발표 |
| OpenAI Blog | openai.com/news | GPT 관련 공식 발표 |
| Google DeepMind Blog | deepmind.google/discover/blog | Gemini 등 Google AI 공식 |
| Meta AI Blog | ai.meta.com/blog | LLaMA 등 Meta AI 공식 |

**검색 쿼리**: `"new AI model" OR "LLM release" OR "benchmark" site:huggingface.co OR site:arxiv.org`

## C2: 제품 업데이트

| 소스 | URL | 특징 |
|------|-----|------|
| Anthropic Changelog | docs.anthropic.com/en/docs/about-claude/models | Claude 버전·기능 변경 |
| OpenAI Changelog | help.openai.com/en/articles | ChatGPT 업데이트 |
| Google AI Updates | blog.google/technology/ai | Google AI 제품 업데이트 |
| xAI/Grok | x.ai/blog | Grok 업데이트 |
| GeekNews | news.hada.io | 한국어 큐레이션 |

**검색 쿼리**: `"Claude update" OR "ChatGPT update" OR "Gemini update" OR "Grok update" 2026`

## C3: AI 뉴스

| 소스 | URL | 특징 |
|------|-----|------|
| GeekNews | news.hada.io | 한국어, 커뮤니티 큐레이션 ⭐ |
| The Rundown AI | therundown.ai | 영문, 200만 구독자 |
| AI Trends KR | aitrends.kr | 글로벌 뉴스 한국어 자동 요약 |
| TechCrunch AI | techcrunch.com/category/artificial-intelligence | 영문, 심층 보도 |
| The Verge AI | theverge.com/ai-artificial-intelligence | 영문, 소비자 관점 |
| AI타임스 | aitimes.com | 한국어 AI 전문 미디어 |
| 인공지능신문 | aitimes.kr | 한국어 AI 전문 미디어 |

**검색 쿼리**: `"AI news today" OR "인공지능 뉴스"`, `site:news.hada.io AI`

## C4: AI 논문

| 소스 | URL | 특징 |
|------|-----|------|
| HuggingFace Trending Papers | huggingface.co/papers/trending | 커뮤니티 투표, 트렌딩 |
| arXiv cs.AI | arxiv.org/list/cs.AI/recent | 공식 프리프린트 |
| Papers With Code | paperswithcode.com | 코드 포함 논문, SOTA 트래킹 |
| Semantic Scholar | semanticscholar.org | AI 기반 논문 검색·요약 |
| Paper Digest | paperdigest.org | AI 논문 일일 하이라이트 |
| alphaXiv | alphaxiv.org | arXiv 논문 토론 플랫폼 |

**검색 쿼리**: `"machine learning" OR "large language model" site:arxiv.org recent`

## C5: 오픈소스 / GitHub 트렌딩

| 소스 | URL | 특징 |
|------|-----|------|
| GitHub Trending | github.com/trending | 전체 트렌딩 → AI 필터링 |
| HuggingFace Models | huggingface.co/models?sort=trending | 모델 트렌딩 |
| HuggingFace Spaces | huggingface.co/spaces | 데모·앱 트렌딩 |
| GeekNews | news.hada.io | 오픈소스 프로젝트 소개 |

**검색 쿼리**: `"open source AI" OR "AI GitHub" trending 2026`

## C6: 정책·규제

| 소스 | URL | 특징 |
|------|-----|------|
| AI타임스 | aitimes.com | 국내 AI 정책 보도 |
| BIG KINDS | bigkinds.or.kr | 국내 뉴스 빅데이터 검색 |
| EU AI Act Portal | artificialintelligenceact.eu | EU AI 규제 공식 |
| NIST AI | nist.gov/artificial-intelligence | 미국 AI 표준·가이드라인 |

**검색 쿼리**: `"AI regulation" OR "AI policy" OR "AI 규제" OR "AI법" 2026`

## C7: 투자·펀딩

| 소스 | URL | 특징 |
|------|-----|------|
| TechCrunch Funding | techcrunch.com/category/startups | 스타트업 펀딩 속보 |
| The Rundown AI | therundown.ai | AI 투자 소식 포함 |
| GeekNews | news.hada.io | 투자 뉴스 큐레이션 |

**검색 쿼리**: `"AI startup funding" OR "AI investment" OR "AI 투자" 2026`

## C8: 실무 툴·서비스 리뷰

| 소스 | URL | 특징 |
|------|-----|------|
| Product Hunt | producthunt.com | 신규 AI 툴 론칭 |
| There's An AI For That | theresanaiforthat.com | AI 툴 데이터베이스 |
| GeekNews | news.hada.io | 툴 리뷰·소개 |
| OpenTools.ai | opentools.ai/news | AI 툴 뉴스 일일 업데이트 |

**검색 쿼리**: `"AI tool" OR "AI dev tool" new launch review 2026`

---

## 수집 팁

1. **GeekNews 우선**: 한국어 큐레이션이 잘 되어 있어 전 카테고리의 시작점으로 활용
2. **영문 소스 병행**: The Rundown AI, TechCrunch로 글로벌 커버리지 보완
3. **공식 블로그 직접 확인**: 주요 AI 기업(Anthropic, OpenAI, Google, Meta) 블로그는 직접 확인
4. **시간대 활용**: KST 오전 9시 = 미국 서부 전일 오후. 하루치 해외 뉴스가 축적된 상태
