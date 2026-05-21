---
name: kf-publish
description: >
  This skill should be used when the user says "SNS에 올려줘", "게시해줘", "publish",
  "트위터 올려줘", "인스타 올려줘", "페북 올려줘", "kf-publish",
  "콘텐츠 게시해줘", "오늘 콘텐츠 올려줘", "SNS 자동 게시", "카드뉴스 올려줘",
  or when kf-content has been created and the user wants to post it to social media.
  Uses Claude in Chrome to post card news images to X, Instagram, and Facebook — no API keys needed.
  Always use this skill after kf-content or kf-daily when the user asks to publish, post, or share.
version: 0.3.0
---

# KF-Publish — SNS 카드뉴스 자동 게시

`kf-content`가 생성한 카드뉴스 스크립트(`instagram.md`)를 기반으로 1080×1080px 이미지 카드를 생성하고,
X, Instagram, Facebook 세 플랫폼 모두에 이미지로 게시한다. API 키 없이 Claude in Chrome으로 동작한다.

## 전체 흐름

```
Step 0: 이미지 배분 계획 사전 확인 (사용자 승인)
Step 1: 콘텐츠 파일 읽기
Step 2: 카드뉴스 HTML 생성 → 브라우저 탭에 주입
Step 3: html2canvas로 PNG 캡처 → 저장
Step 4: Instagram — N장 이미지 캐러셀 게시
Step 5: Threads — 전체 N장 1포스트 게시 (최대 10장)
Step 6: X — 4장 단위 스레드 게시 ("게시물 추가 → 모두 게시하기")
Step 7: Facebook — N장 이미지 앨범 게시
Step 8: 완료 보고
```

PNG는 한 번 생성하면 네 플랫폼이 그대로 재사용한다.
카드 수는 `instagram.md`의 슬라이드 수에 따라 동적으로 결정된다 (보통 7~9장).

**플랫폼별 이미지 최대 첨부 한도:**
| 플랫폼 | 한도 | 초과 시 |
|--------|------|---------|
| Instagram | 10장 | 10장까지만 첨부 |
| Threads | 10장 | 1포스트로 전부 가능 |
| X | 4장/트윗 | 4장 단위로 스레드 분할 |
| Facebook | 10장+ | 전부 첨부 가능 |

---

## Step 0: 이미지 배분 계획 사전 확인

게시 시작 전 다음을 사용자에게 확인한다:

```
카드 N장 기준 배분 계획:
- Instagram: N장 전체 캐러셀
- Threads: N장 전체 1포스트
- X: 트윗1(card-01~04) → 스레드(card-05~08) → 스레드(card-09~) 4장 단위
- Facebook: N장 전체

이대로 진행할까요?
```

사용자 승인 후 Step 1 진행.

---

## Step 1: 콘텐츠 파일 읽기

날짜를 확인하고(`YYYY-MM-DD`) 네 파일을 읽는다:

- `archive/YYYY-MM-DD/content/instagram.md` — 카드뉴스 스크립트 + 캡션 + 해시태그
- `archive/YYYY-MM-DD/content/x-threads.md` — X·Threads 공용 캡션 텍스트 (게시물 1~3)
- `archive/YYYY-MM-DD/content/facebook.md` — Facebook 포스트 캡션 텍스트

---

## Step 2: 카드뉴스 HTML 생성 및 브라우저 주입

### HTML 카드 명세

`instagram.md`의 슬라이드 내용을 파싱해 7장짜리 HTML을 작성한다.

**카드 공통 요건:**
- 각 카드: `width: 1080px; height: 1080px; overflow: hidden; position: relative`
- `id="card1"` ~ `id="cardN"` (N = 슬라이드 수), `class="card"` 필수
- 전체 배경: `linear-gradient(135deg, #0d0d1a 0%, #1a0d2e 50%, #0d1a3a 100%)`
- 폰트: `-apple-system, 'Apple SD Gothic Neo', 'Noto Sans KR', sans-serif`
- 텍스트: 흰색 계열, 충분한 대비

**카드별 색상 테마 (badge/accent):**

| 위치 | 역할 | 추천 accent 색 |
|------|------|---------------|
| 첫 번째 | 표지 타이틀 | 보라 `#a78bfa` |
| 중간 (2~N-1) | 본문 (뉴스 각 항목) | 금 · 초록 · 파랑 · 빨강 · 주황 순환 |
| 마지막 | 마무리 + CTA | 초록 `#34d399` |

**첫 번째 카드 (표지) 구성:**
- 상단: 카테고리 뱃지 (예: "AI INTELLIGENCE")
- 중앙: 메인 타이틀 (대형, bold)
- 하단: 부제목 + 날짜

**중간 카드 (본문) 구성:**
- 상단: 뱃지 + 카테고리
- 중앙: 아이콘/이모지 + 뉴스 제목
- 하단: 2~3줄 설명

**마지막 카드 (마무리) 구성:**
- 핵심 메시지 (인사이트 1~2줄)
- "팔로우해서 매일 AI 인사이트 받기" 등 CTA

### 브라우저에 HTML 주입

`file://` URL은 Claude in Chrome에서 직접 열 수 없으므로, google.com에 접속 후 `document.write()`로 덮어쓴다.

```javascript
// tab_id는 tabs_create_mcp로 새 탭 생성 후 사용
// google.com navigate 완료 후 실행:
document.open();
document.write("<!DOCTYPE html>" + FULL_HTML_STRING);
document.close();
document.querySelectorAll('.card').length + ' cards'
// 반환값이 "7 cards"여야 정상
```

---

## Step 3: PNG 캡처 및 저장

### html2canvas 로드

```javascript
new Promise((resolve, reject) => {
  if (window.html2canvas) { resolve('already loaded'); return; }
  const s = document.createElement('script');
  s.src = 'https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js';
  s.onload = () => resolve('loaded');
  s.onerror = e => reject('error');
  document.head.appendChild(s);
})
```

### 7장 캡처

```javascript
// 카드 수를 동적으로 파악
const cardCount = document.querySelectorAll('.card').length;
window._cardImages = [];
window._captureStatus = 'running';
(async () => {
  for (let i = 1; i <= cardCount; i++) {
    const el = document.getElementById('card' + i);
    const canvas = await html2canvas(el, {
      width: 1080, height: 1080, scale: 1,
      useCORS: true, backgroundColor: null
    });
    window._cardImages.push(canvas.toDataURL('image/png'));
  }
  window._captureStatus = 'done';
})();
```

완료 확인: `window._captureStatus + ' — ' + window._cardImages.length + ' captured'`
→ `"done — N captured"` (N = 실제 카드 수) 확인 후 다음 진행.

### PNG 저장

`javascript_tool`은 dataURL 직접 반환 시 보안 필터에 차단된다. JS 다운로드 트리거를 사용한다.

```javascript
async function downloadCards() {
  const count = window._cardImages.length;
  for (let i = 0; i < count; i++) {
    const a = document.createElement('a');
    a.href = window._cardImages[i];
    a.download = `card${i+1}.png`;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    await new Promise(r => setTimeout(r, 800));
  }
  return `download triggered for ${count} cards`;
}
downloadCards()
```

저장 경로: Chrome 기본 다운로드 폴더
`/Users/cjrain/Library/Mobile Documents/com~apple~CloudDocs/_Download/card1.png` ~ `cardN.png`

Bash로 확인:
```bash
ls "/Users/cjrain/Library/Mobile Documents/com~apple~CloudDocs/_Download/card*.png"
```

---

## Step 4: Instagram 게시

1. `navigate` → `https://www.instagram.com`
2. `find` → "새로운 게시물" 링크 클릭 (aria-label="만들기")
3. `find` → `file input for photo upload` → `file_upload` N장 전체
4. 자르기 단계: `javascript_tool` → `document.querySelector('button[текст="다음"]').click()` 패턴으로 "다음" 반복 클릭
5. 캡션 입력 — ⚠️ contenteditable div이므로 `form_input` 불가:
   ```javascript
   const el = document.querySelector('[aria-label="문구를 입력하세요..."], [contenteditable="true"]');
   el.focus();
   document.execCommand('insertText', false, captionText);
   ```
6. 공유하기 버튼 확인: `dialog.querySelector('[role="button"]')` 중 "공유하기" 클릭
7. "게시물이 공유되었습니다" 확인

---

## Step 5: Threads 게시

Threads는 최대 10장 → **N장 전체를 1포스트에 게시**.

1. `navigate` → `https://www.threads.net`
2. "새로운 소식이 있나요?" 버튼 클릭으로 모달 오픈
3. 텍스트 입력 (x-threads.md 게시물 1~3 합본):
   ```javascript
   const el = document.querySelector('[contenteditable="true"]');
   el.focus();
   document.execCommand('insertText', false, combinedText);
   ```
4. `find` → `미디어 첨부 file input` → `file_upload` N장 전체
5. 이미지 로드 확인 후 "게시" 버튼 클릭
6. 모달 닫힘 확인 (`dialog: closed = success`)

---

## Step 6: X (Twitter) 게시

트윗당 최대 **4장**. "게시물 추가 → 모두 게시하기" 방식으로 스레드를 한 번에 구성한다.
(첫 트윗 게시 후 답글 방식보다 안정적)

분할 기준:
- 트윗1: card-01~04 + x-threads.md 게시물 1 텍스트
- 트윗2: card-05~08 + 게시물 2 텍스트
- 트윗3: card-09~ + 게시물 3 텍스트

### 구성 절차

1. `navigate` → `https://x.com/home`
2. 첫 트윗 텍스트 입력:
   ```javascript
   const el = document.querySelector('[data-testid="tweetTextarea_0"]');
   el.focus();
   document.execCommand('insertText', false, tweet1);
   ```
3. `find` → `사진 동영상 추가 file input` → `file_upload` card-01~04
4. `[aria-label="게시물 추가"]` 클릭 → 두 번째 textarea 활성화
5. 두 번째 텍스트 입력 (`tweetTextarea_` 마지막 인덱스 사용)
6. `find` → 두 번째 file input → `file_upload` card-05~08
7. "게시물 추가" 반복 → 마지막 카드 묶음 + 텍스트
8. "모두 게시하기" 버튼 클릭 → 홈으로 복귀 확인

---

## Step 7: Facebook 게시

⚠️ Facebook 알림 모달이 "게시물 만들기" 클릭을 가로채는 경우가 있다. 진입 전 처리:
```javascript
// 알림 모달이 열려있으면 Escape로 닫기
document.dispatchEvent(new KeyboardEvent('keydown', {key: 'Escape', bubbles: true}));
```

1. `navigate` → `https://www.facebook.com`
2. `javascript_tool` → 알림 모달 확인 및 닫기
3. "무슨 생각을 하고 계신가요?" 버튼 클릭
4. 텍스트 입력 (contenteditable):
   ```javascript
   const dialogs = Array.from(document.querySelectorAll('[role="dialog"]'));
   const composeDialog = dialogs.find(d => d.querySelector('[contenteditable]'));
   const el = composeDialog.querySelector('[contenteditable="true"]');
   el.focus();
   document.execCommand('insertText', false, facebookText);
   ```
5. "게시물에 추가" 클릭 → 사진/동영상 file input 탐색
6. `find` → `사진 동영상 추가 file input` (compose dialog 내 두 번째 input)
7. `file_upload` → N장 전체
8. "다음" 클릭 (Facebook 미디어 편집 단계 통과)
9. "게시" 버튼: `Array.from(document.querySelectorAll('[role="button"]')).filter(b => b.textContent.trim() === '게시').pop().click()`
10. compose dialog 닫힘 확인

---

## Step 8: 완료 보고

```markdown
## kf-publish 완료: YYYY-MM-DD

| 플랫폼 | 형식 | 이미지 | 상태 |
|--------|------|--------|------|
| Instagram | 캐러셀 | N장 | ✅ 게시됨 |
| Threads | 단일 포스트 | N장 전체 | ✅ 게시됨 |
| X | 스레드 (4장 단위) | N장 분할 | ✅ 게시됨 |
| Facebook | 이미지 앨범 | N장 | ✅ 게시됨 |
```

---

## 오류 대응

| 상황 | 대응 |
|------|------|
| 로그인 안 된 상태 | 사용자에게 로그인 요청 후 재시도 |
| X 카운터 음수 | 문장 단위로 줄여 재입력 |
| 카드 PNG 없음 | Step 2~3 재실행 |
| html2canvas 로드 실패 | 탭 새로고침 후 재시도 |
| Instagram 해시태그 드롭다운 | 이미지 영역 클릭 (Escape 금지) |
| 태그 팝업 | X 버튼으로 닫기 |
| "게시물 삭제" 다이얼로그 | "취소" 클릭 후 재시도 |
| Facebook 알림 모달 간섭 | Escape 후 재시도, 또는 알림 아이콘 클릭으로 먼저 닫기 |
| Facebook 이미지 업로드 실패 | 파일 경로 재확인 후 file_upload 재시도 |
| contenteditable에 form_input 실패 | `document.execCommand('insertText', false, text)` 사용 |
| Threads 게시 후 모달 미닫힘 | 5초 대기 후 `[role="dialog"]` 존재 여부 재확인 |

## 핵심 규칙

- **게시 전 반드시 사용자 확인** — 공개 게시는 되돌릴 수 없다.
- **PNG 재사용**: 카드 생성(Step 2~3)은 1회만. 이후 모든 플랫폼이 같은 파일을 쓴다.
- **탭 관리**: 카드 HTML 탭(google.com origin)은 PNG 저장 확인 전까지 닫지 않는다.
- **플랫폼 실패 독립성**: 한 플랫폼 실패 시 보고 후 다음 플랫폼으로 계속 진행한다.
- **콘텐츠 날짜**: 항상 오늘 날짜(`YYYY-MM-DD`)의 content 폴더를 읽는다.
