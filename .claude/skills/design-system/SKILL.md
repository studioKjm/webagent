---
name: design-system
description: 일관된 시각적 디자인 시스템을 구축합니다. 색상 팔레트, 타이포그래피 스케일, 여백 규칙, 그림자 시스템을 제공합니다. Use when defining visual styles, creating design tokens, or ensuring visual consistency.
allowed-tools: Read, Glob
---

# Design System Skill

이 스킬은 **일관되고 확장 가능한 디자인 시스템**을 구축하는 방법을 제공합니다.

---

## 1. 색상 시스템

### Tailwind 스타일 색상 스케일 (50-950)

모든 색상은 10단계 스케일로 정의:

```css
:root {
  /* Primary Colors */
  --color-primary-50: #f0f9ff;
  --color-primary-100: #e0f2fe;
  --color-primary-200: #bae6fd;
  --color-primary-300: #7dd3fc;
  --color-primary-400: #38bdf8;
  --color-primary-500: #0ea5e9;  /* ← 메인 색상 */
  --color-primary-600: #0284c7;
  --color-primary-700: #0369a1;
  --color-primary-800: #075985;
  --color-primary-900: #0c4a6e;
  --color-primary-950: #082f49;
}
```

### 사용 규칙

| 색상 레벨 | 용도 | 예시 |
|---------|------|------|
| **50-100** | 밝은 배경 | 카드 배경, Hover 배경 |
| **300-400** | 보조 색상 | 아이콘, 보조 버튼 |
| **500-600** | 메인 색상 | Primary 버튼, 링크 |
| **700-900** | 다크 모드 | 다크 배경, 강조 텍스트 |

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

```css
:root {
  --color-background: #ffffff;
  --color-text: #171717;
}

@media (prefers-color-scheme: dark) {
  :root {
    --color-background: #0a0a0a;
    --color-background-alt: #171717;
    --color-text: #fafafa;
    --color-text-muted: #a3a3a3;

    /* 그림자도 조정 */
    --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.5);
    --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.5);
  }
}
```

---

## 10. 완전한 디자인 토큰 템플릿

```css
/* =============================================
   DESIGN TOKENS
   ============================================= */
:root {
  /* ===== Colors ===== */
  --color-primary-50: #f0f9ff;
  --color-primary-500: #0ea5e9;
  --color-primary-900: #0c4a6e;

  --color-neutral-50: #fafafa;
  --color-neutral-500: #737373;
  --color-neutral-900: #171717;

  --color-success: #10b981;
  --color-error: #ef4444;
  --color-warning: #f59e0b;

  /* Semantic Colors */
  --color-background: #ffffff;
  --color-background-alt: #f9fafb;
  --color-text: #171717;
  --color-text-muted: #737373;
  --color-border: #e5e5e5;

  /* CTA */
  --color-cta: var(--color-primary-500);
  --color-cta-hover: var(--color-primary-600);

  /* ===== Typography ===== */
  --font-display: 'Inter', -apple-system, sans-serif;
  --font-body: 'Inter', -apple-system, sans-serif;
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

/* ===== Dark Mode ===== */
@media (prefers-color-scheme: dark) {
  :root {
    --color-background: #0a0a0a;
    --color-background-alt: #171717;
    --color-text: #fafafa;
    --color-text-muted: #a3a3a3;
    --color-border: #404040;
  }
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

- [ ] 색상 팔레트 정의 (Primary, Neutral, Semantic)
- [ ] 타이포그래피 스케일 정의 (최소 6레벨)
- [ ] 여백 시스템 정의 (8px 기반)
- [ ] 그림자 시스템 정의
- [ ] Border Radius 정의
- [ ] Transition 정의
- [ ] 색상 대비 검증 (WCAG AA)
- [ ] 다크 모드 대응 (선택)
- [ ] CSS 변수로 모두 정의

---

이 디자인 시스템을 사용하면 **일관성 있고 유지보수 쉬운 UI**를 구축할 수 있습니다!
