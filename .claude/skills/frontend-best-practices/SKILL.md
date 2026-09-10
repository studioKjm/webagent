---
name: frontend-best-practices
description: 시맨틱 HTML, 접근성 (a11y), 성능 최적화 등 프론트엔드 베스트 프랙티스를 가르칩니다. Use when writing HTML/CSS/JS, ensuring accessibility, or optimizing performance.
allowed-tools: Read, Grep
---

# Frontend Best Practices Skill

이 스킬은 **프로덕션 레디 프론트엔드 코드**를 작성하기 위한 베스트 프랙티스를 제공합니다.

---

## 1. 시맨틱 HTML

### 올바른 태그 사용

| 용도 | ❌ 나쁜 예 | ✅ 좋은 예 |
|-----|----------|----------|
| 내비게이션 | `<div class="nav">` | `<nav>` |
| 메인 콘텐츠 | `<div class="main">` | `<main>` |
| 섹션 구분 | `<div class="section">` | `<section>` |
| 독립 콘텐츠 | `<div class="card">` | `<article>` |
| 보조 정보 | `<div class="sidebar">` | `<aside>` |
| 헤더 영역 | `<div class="header">` | `<header>` |
| 푸터 영역 | `<div class="footer">` | `<footer>` |
| 버튼 | `<div onclick="...">` | `<button>` |
| 링크 | `<span onclick="...">` | `<a href="...">` |

---

### 시맨틱 HTML 예시

**나쁜 예**:
```html
<div class="header">
  <div class="nav">
    <div class="link">Home</div>
    <div class="link">About</div>
  </div>
</div>

<div class="main">
  <div class="article">
    <div class="title">Title</div>
    <div class="text">Content...</div>
  </div>
</div>
```

**좋은 예**:
```html
<header>
  <nav role="navigation" aria-label="Main navigation">
    <a href="#home">Home</a>
    <a href="#about">About</a>
  </nav>
</header>

<main>
  <article>
    <h1>Title</h1>
    <p>Content...</p>
  </article>
</main>
```

---

## 2. 접근성 (Accessibility - a11y)

### ARIA 레이블 사용

#### 버튼 역할 명확히

```html
<!-- ❌ 나쁜 예: 스크린 리더가 이해 못함 -->
<button class="menu-btn">
  <span>☰</span>
</button>

<!-- ✅ 좋은 예: aria-label로 명확히 -->
<button aria-label="메뉴 열기" class="menu-btn">
  <span aria-hidden="true">☰</span>
</button>
```

#### 내비게이션 구분

```html
<!-- 여러 nav가 있을 때 구분 -->
<nav aria-label="메인 네비게이션">
  <ul>
    <li><a href="#home">홈</a></li>
  </ul>
</nav>

<nav aria-label="푸터 네비게이션">
  <ul>
    <li><a href="#privacy">개인정보처리방침</a></li>
  </ul>
</nav>
```

#### 폼 접근성

```html
<!-- ✅ 좋은 예: label + aria-describedby -->
<label for="email">이메일 주소</label>
<input
  type="email"
  id="email"
  aria-required="true"
  aria-describedby="email-help"
  placeholder="example@email.com"
>
<span id="email-help" class="help-text">
  유효한 이메일 주소를 입력하세요
</span>

<!-- 에러 상태 -->
<input
  type="email"
  id="email"
  aria-required="true"
  aria-invalid="true"
  aria-describedby="email-error"
>
<span id="email-error" role="alert" class="error">
  올바른 이메일 형식이 아닙니다
</span>
```

---

### 키보드 네비게이션

#### Focus 스타일 필수

```css
/* ✅ 좋은 예: 키보드 포커스 명확히 */
*:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
  border-radius: 2px;
}

/* 마우스 클릭 시에는 아웃라인 제거 */
*:focus:not(:focus-visible) {
  outline: none;
}
```

#### Skip to Content 링크

```html
<!-- 스크린 리더 사용자를 위한 Skip Link -->
<a href="#main-content" class="skip-link">
  메인 콘텐츠로 건너뛰기
</a>

<nav>...</nav>

<main id="main-content">
  <!-- 메인 콘텐츠 -->
</main>

<style>
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  background: var(--color-primary);
  color: white;
  padding: 8px;
  z-index: 100;
}

.skip-link:focus {
  top: 0;
}
</style>
```

---

### 이미지 접근성

```html
<!-- ✅ 의미 있는 이미지: alt 필수 -->
<img src="product.jpg" alt="노트북 제품 이미지">

<!-- ✅ 장식용 이미지: alt="" (빈 문자열) -->
<img src="decoration.svg" alt="">

<!-- ✅ 복잡한 이미지: aria-describedby -->
<img
  src="chart.png"
  alt="2024년 매출 차트"
  aria-describedby="chart-description"
>
<p id="chart-description">
  1분기 100만원, 2분기 150만원, 3분기 200만원으로 증가 추세
</p>
```

---

### 색상 대비 검증

**WCAG AA 기준**:
- 일반 텍스트 (< 18px): 4.5:1
- 큰 텍스트 (≥ 18px): 3:1
- UI 요소: 3:1

```css
/* ❌ 나쁜 예: 대비 부족 */
.text {
  color: #999999;  /* 대비 2.8:1 - 불합격 */
  background: #ffffff;
}

/* ✅ 좋은 예: 충분한 대비 */
.text {
  color: #666666;  /* 대비 5.7:1 - 합격 */
  background: #ffffff;
}
```

**검증 도구**: https://webaim.org/resources/contrastchecker/

---

## 3. 성능 최적화

### Core Web Vitals: INP (Interaction to Next Paint)

**2024년 3월부터 FID(First Input Delay)를 대체한 핵심 지표**입니다. 클릭/탭 등 상호작용 시작부터 다음 화면 페인트까지의 지연을 측정합니다.

| 등급 | INP | 의미 |
|-----|-----|------|
| 좋음 | ≤ 200ms | 즉각 반응하는 느낌 |
| 개선 필요 | 200-500ms | 체감 가능한 지연 |
| 나쁨 | > 500ms | 버벅거림 |

**개선 방법**:
- 클릭 핸들러에서 무거운 동기 작업 금지 — `requestIdleCallback` 또는 `setTimeout(fn, 0)`로 분할
- 스크롤 애니메이션은 JS 대신 CSS `animation-timeline`(scroll-driven animation) 사용 — 메인 스레드 부담 없음 ([frontend-coder.md](../../agents/frontend-coder.md) "3.5 모던 모션" 참고)
- 대량 DOM 업데이트는 `requestAnimationFrame`으로 배치
- 서드파티 스크립트(분석 도구 등)는 `defer`로 로드해 상호작용 차단 방지
- Chrome DevTools Performance 패널 또는 `web-vitals` 라이브러리로 실측

### Critical CSS 인라인

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <!-- Critical CSS: 첫 화면 렌더링에 필수 -->
  <style>
    body { margin: 0; font-family: sans-serif; }
    .hero { min-height: 100vh; display: flex; align-items: center; }
    .btn { padding: 1rem 2rem; background: #3b82f6; color: white; }
  </style>

  <!-- Non-critical CSS: 비동기 로드 -->
  <link rel="preload" href="styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
  <noscript><link rel="stylesheet" href="styles.css"></noscript>
</head>
<body>
  <!-- ... -->
</body>
</html>
```

---

### 이미지 최적화

#### Lazy Loading

```html
<!-- ✅ Above the fold: loading="eager" -->
<img
  src="hero.jpg"
  alt="Hero 이미지"
  loading="eager"
  width="1200"
  height="600"
>

<!-- ✅ Below the fold: loading="lazy" -->
<img
  src="feature.jpg"
  alt="Feature 이미지"
  loading="lazy"
  width="400"
  height="300"
>
```

#### AVIF 우선 + WebP + JPEG Fallback

**AVIF를 첫 번째 소스로 우선 사용**하세요. 동일 화질 기준 WebP보다 20-30% 작고, 2026년 기준 모든 주요 브라우저(Chrome/Firefox/Safari/Edge)가 지원합니다. `<picture>`는 나열 순서대로 지원 여부를 확인하므로 가장 최신/작은 포맷을 맨 위에 둡니다.

```html
<picture>
  <source srcset="hero.avif" type="image/avif">
  <source srcset="hero.webp" type="image/webp">
  <img src="hero.jpg" alt="Hero 이미지" loading="lazy">
</picture>
```

#### Responsive Images

```html
<img
  srcset="
    hero-400.jpg 400w,
    hero-800.jpg 800w,
    hero-1200.jpg 1200w
  "
  sizes="
    (max-width: 600px) 400px,
    (max-width: 1000px) 800px,
    1200px
  "
  src="hero-800.jpg"
  alt="Hero 이미지"
  loading="lazy"
>
```

---

### 폰트 최적화

Display 폰트는 Inter 등 기본 폰트를 피하고 distinctive한 variable font를 사용하세요(`design-system` 스킬 "0. AI 슬롭 방지 원칙", "13. Variable Font 가이드" 참고). 가변 축을 가진 variable font 하나면 여러 weight를 별도 파일 없이 커버할 수 있어 오히려 요청 수가 줄어듭니다.

```html
<head>
  <!-- Preconnect to Google Fonts -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>

  <!-- Variable font 1개로 여러 weight 커버, display=swap 기본 -->
  <link
    href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,300..900&display=swap"
    rel="stylesheet"
  >

  <!-- 히어로 타이틀처럼 즉시 보여야 하는 핵심 폰트는 preload + 레이아웃 시프트 방지용 optional -->
  <link rel="preload" href="/fonts/fraunces-var.woff2" as="font" type="font/woff2" crossorigin>
</head>

<style>
  /* Fallback font stack — 실제 폰트는 프로젝트 톤에 맞게 교체 */
  body {
    font-family: 'IBM Plex Sans', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  }

  /* 자체 호스팅 variable font의 경우 font-display 세밀 제어 가능 */
  @font-face {
    font-family: 'Fraunces';
    src: url('/fonts/fraunces-var.woff2') format('woff2-variations');
    font-weight: 100 900;
    font-display: optional; /* 레이아웃 시프트를 완전히 방지하고 싶을 때 */
  }
</style>
```

---

### JavaScript 최적화

```html
<!-- ✅ Defer non-critical scripts -->
<script src="analytics.js" defer></script>

<!-- ✅ Module scripts (자동 defer) -->
<script type="module" src="app.js"></script>

<!-- ✅ 인라인 스크립트는 최소화 -->
<script>
  // Critical initialization only
  window.APP_CONFIG = { apiUrl: '/api' };
</script>
```

---

## 4. CSS 베스트 프랙티스

### BEM 방법론 (선택)

```css
/* Block */
.card { }

/* Element */
.card__title { }
.card__content { }

/* Modifier */
.card--featured { }
.card__title--large { }
```

### CSS 변수 활용

```css
/* ❌ 나쁜 예: 하드코딩 */
.button {
  padding: 12px 24px;
  background: #3b82f6;
  border-radius: 8px;
}

/* ✅ 좋은 예: CSS 변수 */
.button {
  padding: var(--space-3) var(--space-6);
  background: var(--color-primary);
  border-radius: var(--radius-md);
}
```

### 모바일 우선 (Mobile-First)

```css
/* ✅ 좋은 예: 모바일 우선 */
.grid {
  display: grid;
  grid-template-columns: 1fr; /* 모바일 기본 */
}

@media (min-width: 640px) {
  .grid {
    grid-template-columns: repeat(2, 1fr); /* 태블릿 */
  }
}

@media (min-width: 1024px) {
  .grid {
    grid-template-columns: repeat(3, 1fr); /* 데스크톱 */
  }
}
```

---

## 5. JavaScript 베스트 프랙티스

### Intersection Observer (스크롤 애니메이션)

```javascript
// ✅ 좋은 예: Intersection Observer 사용
const observerOptions = {
  threshold: 0.1,
  rootMargin: '0px 0px -100px 0px'
};

const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('animate-in');
      observer.unobserve(entry.target); // 한 번만 실행
    }
  });
}, observerOptions);

document.querySelectorAll('.fade-in').forEach(el => {
  observer.observe(el);
});
```

### 이벤트 위임 (Event Delegation)

```javascript
// ❌ 나쁜 예: 개별 리스너
document.querySelectorAll('.btn').forEach(btn => {
  btn.addEventListener('click', handleClick);
});

// ✅ 좋은 예: 이벤트 위임
document.addEventListener('click', (e) => {
  if (e.target.matches('.btn')) {
    handleClick(e);
  }
});
```

---

## 6. SEO 최적화

### Meta 태그

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- SEO -->
  <title>제품명 - 간단한 설명 (60자 이내)</title>
  <meta name="description" content="제품 설명 150-160자">
  <meta name="keywords" content="키워드1, 키워드2, 키워드3">
  <link rel="canonical" href="https://example.com/">

  <!-- Open Graph (소셜 미디어) -->
  <meta property="og:type" content="website">
  <meta property="og:url" content="https://example.com/">
  <meta property="og:title" content="제품명 - 간단한 설명">
  <meta property="og:description" content="제품 설명">
  <meta property="og:image" content="https://example.com/og-image.jpg">

  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="제품명">
  <meta name="twitter:description" content="제품 설명">
  <meta name="twitter:image" content="https://example.com/twitter-image.jpg">

  <!-- Favicon -->
  <link rel="icon" type="image/png" sizes="32x32" href="/favicon-32x32.png">
  <link rel="icon" type="image/png" sizes="16x16" href="/favicon-16x16.png">
  <link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png">
</head>
</html>
```

---

## 7. 보안 (Security)

### Content Security Policy

```html
<meta http-equiv="Content-Security-Policy"
      content="default-src 'self'; script-src 'self' 'unsafe-inline' https://trusted-cdn.com;">
```

### 외부 링크 보안

```html
<!-- ✅ 외부 링크는 rel="noopener noreferrer" -->
<a href="https://external-site.com" target="_blank" rel="noopener noreferrer">
  External Link
</a>
```

---

## 8. 브라우저 호환성

### Autoprefixer 사용 (PostCSS)

```css
/* 작성한 CSS */
.box {
  display: flex;
  transition: transform 200ms;
}

/* Autoprefixer 결과 */
.box {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-transition: -webkit-transform 200ms;
  transition: -webkit-transform 200ms;
  transition: transform 200ms;
  transition: transform 200ms, -webkit-transform 200ms;
}
```

---

## 9. Reduced Motion 지원

```css
/* 애니메이션 선호하지 않는 사용자 */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## 10. 체크리스트

### HTML
- [ ] 시맨틱 태그 사용 (`<nav>`, `<main>`, `<article>`)
- [ ] `lang` 속성 (`<html lang="ko">`)
- [ ] Meta 태그 (description, OG)
- [ ] 이미지 alt 속성

### 접근성
- [ ] ARIA 레이블 사용
- [ ] 키보드 네비게이션 가능
- [ ] Focus 스타일 명확
- [ ] 색상 대비 4.5:1 이상

### 성능
- [ ] 이미지 lazy loading
- [ ] 이미지 포맷: AVIF 우선 → WebP → JPEG 폴백
- [ ] Critical CSS 인라인
- [ ] 폰트 최적화 (variable font, preconnect, display=swap 또는 optional)
- [ ] JavaScript defer/async
- [ ] INP 200ms 이하 — 클릭 핸들러에 무거운 동기 작업 없음, 스크롤 애니메이션은 CSS 우선

### SEO
- [ ] Title 60자 이내
- [ ] Description 150-160자
- [ ] OG 이미지 (1200x630px)
- [ ] Canonical URL

### 보안
- [ ] 외부 링크 rel="noopener"
- [ ] CSP 설정 (선택)

---

## 11. 출시 전 QA 체크리스트 (버전별 실행)

`main-orchestrator`가 3개 버전 생성을 마친 뒤, **버전마다** 아래를 점검합니다.

**⚠️ 중요한 구분**: 아래 항목 중 상당수(색상 대비, 레이아웃 붕괴, AI 슬롭 여부, 버전 간 차별화)는 **코드를 읽는 것만으로는 검증되지 않습니다.** `frontend-coder.md`의 "6.5 시각 검수"(Playwright로 실제 스크린샷을 찍어 Read로 확인)를 거치지 않은 상태에서 이 체크리스트에 체크하는 것은 자체 판단을 사실처럼 기록하는 것이므로 금지합니다. 실제로 확인하지 않은 항목은 미체크 상태로 남기고 그렇게 보고하세요.

### 도구로 실측 가능한 항목 (Playwright 스크린샷 + 콘솔 로그 기반)

**이진 통과/실패 대신 점수제를 씁니다.** `frontend-coder.md` "6.5 시각 검수"의 감점표(폴드 불일치 -20, 평범한 구도 -15, AI 슬롭 위반 1건당 -15, 레이아웃 패턴 중복 -10, 반응형 붕괴 -10, 의미 없는 모션 -5, 단색 배경 -5, 콘솔 에러 -5, 접근성 결함 -4~-8)로 100점에서 채점해 각 버전이 **80점(B) 이상**인지 확인하세요. 코드를 읽는 것만으로는 나오지 않는 점수이므로, 시각 검수를 실행하지 않은 채 이 항목에 체크하는 것은 금지입니다.

- [ ] `frontend-coder.md` "6.5 시각 검수" 실행 완료 — 데스크톱/모바일 스크린샷을 실제로 Read로 확인함
- [ ] 각 버전 점수 80점(B) 이상 (최대 3회 수정 사이클 이내) — 미달이면 재작업 필요(main-orchestrator가 판단)
- [ ] 각 버전 README에 `**시각 검수 점수**: N/100 (등급)` 형식의 줄이 정확히 존재 — 이 줄이 없으면 시각 검수를 건너뛴 것으로 간주하고 반려
- [ ] 3개 버전이 레이아웃 패턴/톤에서 실제로 구분되는지 — 스크린샷을 나란히 놓고 비교

### Lighthouse 등 별도 도구가 있을 때만 (없으면 "목표치"로만 표기, "확인됨"이라고 쓰지 말 것)
- [ ] Lighthouse Performance 90+ / Accessibility 100 / Best Practices 100 / SEO 100
- [ ] INP 200ms 이하, CLS 0.1 이하, LCP 2.5초 이하

### 코드 검토로 확인 가능한 항목
- [ ] 색상 대비 4.5:1 이상 (CSS의 실제 색상값으로 WebAIM Contrast Checker 계산 — 눈대중 아님)
- [ ] 키보드만으로 모든 인터랙션 접근 가능 (마크업의 tabindex/focus 스타일 확인)
- [ ] `prefers-reduced-motion: reduce` 대응 코드 존재

---

이 베스트 프랙티스를 따르면 **접근성, 성능, SEO 모두 우수한 랜딩페이지**를 만들 수 있습니다!
