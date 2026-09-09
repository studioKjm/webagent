---
name: design-system
description: 일관된 시각적 디자인 시스템을 구축합니다. 색상 팔레트, 타이포그래피 스케일, 여백 규칙, 그림자 시스템을 제공합니다. Use when defining visual styles, creating design tokens, or ensuring visual consistency.
allowed-tools: Read, Glob
---

# Design System Skill

이 스킬은 **일관되고 확장 가능한 디자인 시스템**을 구축하는 방법을 제공합니다.

> **SSOT 안내**: 색상/타이포그래피/여백/그림자 등 기본 디자인 토큰 정의의 단일 진실 원천(SSOT)은 이 스킬입니다. `ui-stylist` 에이전트는 이 토큰 구조를 그대로 사용하고, 타겟 오디언스별 색상 전략·레퍼런스 URL 분석 등 자신만의 판단 로직만 추가합니다. 톤/개성/모션 등 "AI스럽지 않은" 창의적 방향은 내장 **frontend-design 스킬**(Skill 도구로 `frontend-design` 호출)을 함께 사용하세요.

---

## 0. AI 슬롭(AI Slop) 방지 원칙

아래는 2026년 기준 AI가 생성한 UI에서 가장 흔하게 나타나 "AI가 만든 티"로 인식되는 패턴입니다. **매 프로젝트마다 이 목록을 점검**하고 의도적으로 피하세요.

### 절대 금지
- **폰트**: Inter, Roboto, Open Sans, Lato, Arial, 시스템 기본 폰트를 Display 용도로 그대로 사용 금지 (Body에서 가독성 목적의 제한적 사용은 허용)
- **색상**: 흰 배경 위 purple→blue 그라디언트(Tailwind indigo-500 계열 남용)를 기본값으로 사용 금지
- **레이아웃**: 아이콘+제목+2줄 설명으로 구성된 카드 3개를 손대지 않은 형태로 반복 배치 금지
- **모션**: 이유 없는 hover bounce, 산발적으로 흩뿌려진 마이크로인터랙션 금지

### 대신 이렇게
- **지배색 + 포인트 컬러** 조합이 균등 분배된 소심한 팔레트보다 낫다 — 하나의 지배적 색상에 날카로운 액센트 컬러 1-2개
- **폰트 페어링**은 개성 있는 Display 폰트 + 정제된 Body 폰트 조합으로. weight 대비를 극단적으로(100 vs 900), 크기 차이도 크게
- **모션**은 산발적 효과보다 "한 번의 잘 조율된 페이지 로드 애니메이션"(staggered reveal)에 집중
- **배경**은 단색 대신 그라디언트 메시, 노이즈/그레인 텍스처, 기하학적 패턴 등으로 분위기와 깊이 부여
- 매 프로젝트마다 **다른 폰트·다른 톤**을 선택할 것 — 특정 조합(예: 매번 Space Grotesk)으로 수렴하지 말 것

---

## 1. 색상 시스템

### OKLCH 색상 스케일 (50-950)

**색상 정의는 HEX 대신 OKLCH를 기본으로 사용합니다.** OKLCH는 지각적으로 균일한 색공간이라 같은 명도(L) 차이가 색상(Hue)에 관계없이 동일하게 "느껴지고", 다크모드에서 색을 반전/조정할 때 HEX처럼 채도가 왜곡되지 않습니다. 또한 sRGB보다 넓은 P3 색역을 활용해 더 선명한 색 표현이 가능합니다. (Baseline: Safari 15.4+/Chrome 111+/Firefox 113+ — 2023-2024년부터 사실상 전 브라우저 지원)

```css
:root {
  /* Primary Colors — oklch(L% C H) 형식. H(색상)만 바꾸면 전체 스케일이 자동으로 일관되게 생성됨 */
  --color-primary-50:  oklch(97% 0.015 250);
  --color-primary-100: oklch(94% 0.035 250);
  --color-primary-200: oklch(88% 0.07  250);
  --color-primary-300: oklch(80% 0.11  250);
  --color-primary-400: oklch(70% 0.15  250);
  --color-primary-500: oklch(60% 0.19  250);  /* ← 메인 색상 */
  --color-primary-600: oklch(52% 0.19  250);
  --color-primary-700: oklch(44% 0.17  250);
  --color-primary-800: oklch(36% 0.14  250);
  --color-primary-900: oklch(28% 0.10  250);
  --color-primary-950: oklch(20% 0.06  250);
}
```

**구형 브라우저 폴백이 필요한 경우**만 아래처럼 HEX를 먼저 선언하고 OKLCH로 덮어씁니다(속성이 두 번 선언되면 지원하는 브라우저가 마지막 값을 사용):

```css
:root {
  --color-primary-500: #2f6feb; /* fallback */
  --color-primary-500: oklch(60% 0.19 250); /* 지원 브라우저에서 덮어씀 */
}
```

### 명도(L) 스텝 사용 규칙

| 스텝 (L값) | 용도 | 예시 |
|---------|------|------|
| **50-100** (L 94-97%) | 밝은 배경 | 카드 배경, Hover 배경 |
| **300-400** (L 70-80%) | 보조 색상 | 아이콘, 보조 버튼 |
| **500-600** (L 52-60%) | 메인 색상 | Primary 버튼, 링크 |
| **700-900** (L 28-44%) | 다크 모드 | 다크 배경, 강조 텍스트 |

---

## 2. 타이포그래피 시스템

### Type Scale: 1.250 (Major Third)

```css
:root {
  /* Font Sizes */
  --text-xs: 0.75rem;      /* 12px */
  --text-sm: 0.875rem;     /* 14px */
  --text-base: 1rem;       /* 16px */
  --text-lg: 1.125rem;     /* 18px */
  --text-xl: 1.25rem;      /* 20px */
  --text-2xl: 1.5rem;      /* 24px */
  --text-3xl: 1.875rem;    /* 30px */
  --text-4xl: 2.25rem;     /* 36px */
  --text-5xl: 3rem;        /* 48px */
  --text-6xl: 3.75rem;     /* 60px */
  --text-7xl: 4.5rem;      /* 72px */

  /* Font Weights */
  --font-normal: 400;
  --font-medium: 500;
  --font-semibold: 600;
  --font-bold: 700;

  /* Line Heights */
  --leading-none: 1;
  --leading-tight: 1.25;
  --leading-snug: 1.375;
  --leading-normal: 1.5;
  --leading-relaxed: 1.625;
  --leading-loose: 2;
}
```

### 반응형 타이포그래피 (clamp)

```css
:root {
  /* 모바일 → 데스크톱 자동 조정 */
  --text-hero: clamp(2.5rem, 5vw, 4.5rem);       /* 40px-72px */
  --text-section: clamp(2rem, 4vw, 3rem);        /* 32px-48px */
  --text-feature: clamp(1.25rem, 2vw, 1.5rem);   /* 20px-24px */
}
```

---

## 3. 여백 시스템 (8px 기반)

```css
:root {
  /* Spacing Scale */
  --space-0: 0;
  --space-1: 0.25rem;     /* 4px */
  --space-2: 0.5rem;      /* 8px */
  --space-3: 0.75rem;     /* 12px */
  --space-4: 1rem;        /* 16px */
  --space-5: 1.25rem;     /* 20px */
  --space-6: 1.5rem;      /* 24px */
  --space-8: 2rem;        /* 32px */
  --space-10: 2.5rem;     /* 40px */
  --space-12: 3rem;       /* 48px */
  --space-16: 4rem;       /* 64px */
  --space-20: 5rem;       /* 80px */
  --space-24: 6rem;       /* 96px */
  --space-32: 8rem;       /* 128px */

  /* Semantic Spacing */
  --section-padding-mobile: 4rem 1.5rem;
  --section-padding-desktop: 8rem 3rem;
  --card-padding: 2rem;
  --container-padding: 0 1.5rem;
}
```

### 여백 사용 가이드

| 용도 | 크기 | 변수 |
|-----|------|------|
| 요소 간 최소 간격 | 4px | `--space-1` |
| 버튼 내부 패딩 | 12-16px | `--space-3` ~ `--space-4` |
| 카드 내부 패딩 | 32px | `--space-8` |
| 섹션 간 간격 (모바일) | 64px | `--space-16` |
| 섹션 간 간격 (데스크톱) | 128px | `--space-32` |

---

## 4. 그림자 시스템

```css
:root {
  /* Shadows */
  --shadow-xs: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.1);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
  --shadow-xl: 0 20px 25px rgba(0, 0, 0, 0.15);
  --shadow-2xl: 0 25px 50px rgba(0, 0, 0, 0.25);
}
```

### 사용 규칙

| 그림자 | 용도 |
|--------|------|
| **xs-sm** | 카드, 입력 필드 |
| **md** | 버튼, 드롭다운 |
| **lg-xl** | 모달, 팝오버 |
| **2xl** | Hero 이미지 |

---

## 5. Border Radius

```css
:root {
  --radius-sm: 0.25rem;   /* 4px */
  --radius-md: 0.5rem;    /* 8px */
  --radius-lg: 0.75rem;   /* 12px */
  --radius-xl: 1rem;      /* 16px */
  --radius-2xl: 1.5rem;   /* 24px */
  --radius-full: 9999px;  /* Pill/Circle */
}
```

---

## 6. Transitions

```css
:root {
  --transition-fast: 150ms ease;
  --transition-base: 200ms ease;
  --transition-slow: 300ms ease;
  --transition-bounce: 300ms cubic-bezier(0.68, -0.55, 0.265, 1.55);
}
```

---

## 7. Container & Layout

```css
:root {
  --container-max: 1200px;
  --container-wide: 1400px;
  --container-narrow: 800px;

  /* Z-index Scale */
  --z-base: 1;
  --z-dropdown: 100;
  --z-sticky: 200;
  --z-modal: 1000;
  --z-toast: 2000;
}
```

---

## 8. 색상 대비 규칙 (WCAG AA)

### 최소 대비 비율

| 텍스트 크기 | 최소 대비 | 예시 |
|-----------|----------|------|
| < 18px | 4.5:1 | #171717 on #ffffff = 16:1 ✅ |
| ≥ 18px or Bold ≥ 14px | 3:1 | #737373 on #ffffff = 4.7:1 ✅ |
| UI 요소 | 3:1 | #3b82f6 on #ffffff = 4.5:1 ✅ |

**검증 도구**: https://webaim.org/resources/contrastchecker/

---

## 9. 다크 모드 지원

**전략 2단계**: (1) `color-scheme` 속성으로 브라우저 기본 UI(스크롤바, 폼 컨트롤)까지 다크모드에 맞추고, (2) `prefers-color-scheme` 미디어쿼리 + **사용자 수동 토글**(`data-theme` 속성, localStorage 저장)을 함께 지원합니다. OKLCH를 쓰면 다크모드 색상을 L값만 조정해 만들 수 있어 색조 왜곡이 없습니다.

```css
:root {
  color-scheme: light;
  --color-background: oklch(100% 0 0);
  --color-text: oklch(20% 0 0);
}

/* 시스템 다크모드 자동 대응 */
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) {
    color-scheme: dark;
    --color-background: oklch(15% 0 0);
    --color-background-alt: oklch(20% 0 0);
    --color-text: oklch(97% 0 0);
    --color-text-muted: oklch(70% 0.01 250);

    --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.5);
    --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.5);
  }
}

/* 사용자가 수동으로 다크모드 선택 시 (예: <html data-theme="dark">) */
:root[data-theme="dark"] {
  color-scheme: dark;
  --color-background: oklch(15% 0 0);
  --color-background-alt: oklch(20% 0 0);
  --color-text: oklch(97% 0 0);
  --color-text-muted: oklch(70% 0.01 250);
}
```

토글 구현 시 `frontend-coder`는 버튼 클릭에서 `localStorage`에 사용자 선택을 저장하고 `<html>`의 `data-theme` 속성을 갱신하는 짧은 스크립트를 추가합니다.

---

## 10. 완전한 디자인 토큰 템플릿

```css
/* =============================================
   DESIGN TOKENS
   ============================================= */
:root {
  color-scheme: light;

  /* ===== Colors (OKLCH) ===== */
  --color-primary-50:  oklch(97% 0.015 250);
  --color-primary-500: oklch(60% 0.19  250);
  --color-primary-900: oklch(28% 0.10  250);

  --color-neutral-50:  oklch(98% 0 0);
  --color-neutral-500: oklch(55% 0 0);
  --color-neutral-900: oklch(20% 0 0);

  --color-success: oklch(65% 0.15 150);
  --color-error:   oklch(60% 0.20 25);
  --color-warning: oklch(75% 0.15 80);

  /* Semantic Colors */
  --color-background: oklch(100% 0 0);
  --color-background-alt: oklch(98% 0 0);
  --color-text: oklch(20% 0 0);
  --color-text-muted: oklch(55% 0 0);
  --color-border: oklch(90% 0 0);

  /* CTA */
  --color-cta: var(--color-primary-500);
  --color-cta-hover: var(--color-primary-600);

  /* ===== Typography ===== (예시 — 프로젝트 톤에 맞는 distinctive 폰트로 교체) */
  --font-display: 'Fraunces', Georgia, serif;
  --font-body: 'IBM Plex Sans', -apple-system, sans-serif;
  --font-mono: 'JetBrains Mono', monospace;

  --text-xs: 0.75rem;
  --text-sm: 0.875rem;
  --text-base: 1rem;
  --text-lg: 1.125rem;
  --text-xl: 1.25rem;
  --text-2xl: 1.5rem;
  --text-3xl: 1.875rem;
  --text-4xl: 2.25rem;
  --text-5xl: 3rem;
  --text-6xl: 3.75rem;

  --text-hero: clamp(2.5rem, 5vw, 4.5rem);
  --text-section: clamp(2rem, 4vw, 3rem);

  --font-normal: 400;
  --font-medium: 500;
  --font-semibold: 600;
  --font-bold: 700;

  --leading-tight: 1.25;
  --leading-normal: 1.5;
  --leading-relaxed: 1.75;

  /* ===== Spacing ===== */
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-3: 0.75rem;
  --space-4: 1rem;
  --space-6: 1.5rem;
  --space-8: 2rem;
  --space-12: 3rem;
  --space-16: 4rem;
  --space-24: 6rem;
  --space-32: 8rem;

  --section-padding-mobile: 4rem 1.5rem;
  --section-padding-desktop: 8rem 3rem;
  --card-padding: 2rem;
  --container-max: 1200px;

  /* ===== Effects ===== */
  --shadow-xs: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.1);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
  --shadow-xl: 0 20px 25px rgba(0, 0, 0, 0.15);

  --radius-sm: 0.25rem;
  --radius-md: 0.5rem;
  --radius-lg: 0.75rem;
  --radius-xl: 1rem;
  --radius-full: 9999px;

  --transition-fast: 150ms ease;
  --transition-base: 200ms ease;
  --transition-slow: 300ms ease;

  /* ===== Z-index ===== */
  --z-dropdown: 100;
  --z-sticky: 200;
  --z-modal: 1000;
}

/* ===== Dark Mode ===== (섹션 9 참고: color-scheme + 사용자 토글 병행) */
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) {
    color-scheme: dark;
    --color-background: oklch(15% 0 0);
    --color-background-alt: oklch(20% 0 0);
    --color-text: oklch(97% 0 0);
    --color-text-muted: oklch(70% 0.01 250);
    --color-border: oklch(30% 0 0);
  }
}

:root[data-theme="dark"] {
  color-scheme: dark;
  --color-background: oklch(15% 0 0);
  --color-background-alt: oklch(20% 0 0);
  --color-text: oklch(97% 0 0);
  --color-text-muted: oklch(70% 0.01 250);
  --color-border: oklch(30% 0 0);
}
```

---

## 11. 사용 예시

### 버튼 스타일

```css
.btn {
  padding: var(--space-3) var(--space-6);
  border-radius: var(--radius-md);
  font-weight: var(--font-medium);
  font-size: var(--text-base);
  transition: all var(--transition-base);
  box-shadow: var(--shadow-sm);
}

.btn-primary {
  background: var(--color-cta);
  color: white;
}

.btn-primary:hover {
  background: var(--color-cta-hover);
  box-shadow: var(--shadow-md);
  transform: translateY(-2px);
}
```

### 카드 스타일

```css
.card {
  padding: var(--card-padding);
  border-radius: var(--radius-lg);
  background: var(--color-background);
  box-shadow: var(--shadow-md);
  transition: transform var(--transition-base);
}

.card:hover {
  transform: translateY(-4px);
  box-shadow: var(--shadow-lg);
}
```

---

## 12. 체크리스트

디자인 시스템 적용 전 확인:

- [ ] 색상 팔레트 정의 (Primary, Neutral, Semantic) — OKLCH 기반
- [ ] 타이포그래피 스케일 정의 (최소 6레벨)
- [ ] 여백 시스템 정의 (8px 기반)
- [ ] 그림자 시스템 정의
- [ ] Border Radius 정의
- [ ] Transition 정의
- [ ] 색상 대비 검증 (WCAG AA)
- [ ] 다크 모드 대응: `color-scheme` + 시스템 감지 + 사용자 토글
- [ ] CSS 변수로 모두 정의
- [ ] 배경 처리: 단색 대신 텍스처/그라디언트 모듈(섹션 14) 중 하나를 의도적으로 선택했는가

---

## 13. Variable Font 가이드

가능하면 **variable font**(하나의 파일이 weight/optical-size 등 여러 축을 가변적으로 담음)를 사용하세요. 파일 요청 수가 줄고, weight를 애니메이션할 수 있습니다.

```css
/* Google Fonts의 variable font는 자동으로 가변 축을 지원 (예: Fraunces, Recursive) */
@import url('https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,300..900&display=swap');

h1 {
  font-family: 'Fraunces', serif;
  font-variation-settings: 'opsz' 144, 'wght' 900;  /* optical size + weight 동시 제어 */
}

/* Hover/scroll에 반응해 weight를 자연스럽게 전환 (variable font만 가능한 효과) */
.logo {
  font-variation-settings: 'wght' 400;
  transition: font-variation-settings 300ms ease;
}
.logo:hover {
  font-variation-settings: 'wght' 800;
}
```

**폰트 로딩 전략**: `font-display: swap`(기본) 또는 히어로 타이틀처럼 즉시 보여야 하는 경우 `font-display: optional`로 레이아웃 시프트를 방지하세요. `<link rel="preload" as="font" crossorigin>`으로 핵심 폰트를 우선 로드합니다.

---

## 14. 배경 & 텍스처 모듈

단색 배경 대신 아래 모듈 중 프로젝트 톤에 맞는 것을 의도적으로 선택하세요(AI 슬롭 방지 원칙 참고). 매번 같은 모듈로 수렴하지 말 것.

### Glassmorphism (유리질감)
```css
.card--glass {
  background: oklch(100% 0 0 / 0.6);
  backdrop-filter: blur(16px) saturate(160%);
  -webkit-backdrop-filter: blur(16px) saturate(160%);
  border: 1px solid oklch(100% 0 0 / 0.3);
}
```
다크 배경 위 카드/네비게이션 등 "떠 있는" 느낌을 줄 때. `backdrop-filter` 미지원 브라우저는 `@supports not (backdrop-filter: blur(1px))`로 반투명 단색 폴백 제공.

### Gradient Mesh (그라디언트 메시)
```css
.hero--mesh {
  background:
    radial-gradient(at 20% 20%, oklch(75% 0.15 30 / 0.5) 0px, transparent 50%),
    radial-gradient(at 80% 0%, oklch(70% 0.18 250 / 0.5) 0px, transparent 50%),
    radial-gradient(at 40% 90%, oklch(70% 0.15 150 / 0.4) 0px, transparent 50%),
    var(--color-background);
}
```
purple→blue 단순 그라디언트 대신 여러 색의 radial-gradient를 레이어링해 은은한 깊이를 만듭니다.

### Noise / Grain 텍스처
```css
.section--grain::before {
  content: '';
  position: absolute; inset: 0;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.04'/%3E%3C/svg%3E");
  pointer-events: none;
  mix-blend-mode: overlay;
}
```
SVG `feTurbulence`로 순수 CSS/인라인 SVG만으로 그레인 텍스처를 만들어 이미지 파일 없이 질감을 더합니다.

### Neumorphism (뉴모피즘 — 제한적 사용 권장)
```css
.card--neumorphic {
  background: var(--color-background-alt);
  box-shadow:
    8px 8px 16px oklch(0% 0 0 / 0.08),
    -8px -8px 16px oklch(100% 0 0 / 0.7);
}
```
대비가 약해 접근성(WCAG) 위반 위험이 크므로, 텍스트가 없는 장식 요소나 토글/버튼처럼 크기가 큰 UI에만 제한적으로 사용하세요.

---

이 디자인 시스템을 사용하면 **일관성 있고 유지보수 쉬운 UI**를 구축할 수 있습니다!
