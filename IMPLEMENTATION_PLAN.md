# 랜딩페이지 제작 자동화 에이전트 구현 계획

> **작성일**: 2025-12-29
> **기반 문서**: PRD.md
> **Claude Code 버전**: Sonnet 4.5

---

## 📋 목차

1. [프로젝트 구조 개요](#1-프로젝트-구조-개요)
2. [필수 다운로드 및 설치 목록](#2-필수-다운로드-및-설치-목록)
3. [서브에이전트 구현 계획](#3-서브에이전트-구현-계획)
4. [스킬 구현 계획](#4-스킬-구현-계획)
5. [MCP 서버 설정](#5-mcp-서버-설정)
6. [단계별 구현 로드맵](#6-단계별-구현-로드맵)
7. [테스트 시나리오](#7-테스트-시나리오)
8. [확장 계획](#8-확장-계획)

---

## 1. 프로젝트 구조 개요

### 1.1 최종 디렉토리 구조

```
/Users/jimin/pageagent/webagent/
├── .claude/
│   ├── agents/                              # 서브에이전트 디렉토리
│   │   ├── main-orchestrator.md             # 메인 오케스트레이터
│   │   ├── layout-designer.md               # 레이아웃 설계자
│   │   ├── ui-stylist.md                    # UI 스타일 전문가
│   │   ├── copywriter.md                    # 카피라이팅 전문가
│   │   └── frontend-coder.md                # 프론트엔드 코더
│   ├── skills/                              # 스킬 디렉토리
│   │   ├── landing-page-patterns/           # 랜딩페이지 패턴 스킬
│   │   │   ├── SKILL.md
│   │   │   ├── conversion-patterns.md
│   │   │   ├── layout-templates.md
│   │   │   └── examples/
│   │   │       ├── saas-minimal.html
│   │   │       ├── developer-dark.html
│   │   │       └── conversion-focused.html
│   │   ├── design-system/                   # 디자인 시스템 스킬
│   │   │   ├── SKILL.md
│   │   │   ├── color-schemes.md
│   │   │   ├── typography.md
│   │   │   └── spacing-system.md
│   │   └── frontend-best-practices/         # 프론트엔드 베스트 프랙티스
│   │       ├── SKILL.md
│   │       ├── semantic-html.md
│   │       ├── accessibility.md
│   │       └── performance.md
│   └── .mcp.json                            # MCP 서버 설정
├── CLAUDE.md                                # 프로젝트 컨텍스트
├── PRD.md                                   # 제품 요구사항 문서
├── IMPLEMENTATION_PLAN.md                   # 이 문서
├── output/                                  # 생성된 랜딩페이지 출력
│   ├── version-1/
│   ├── version-2/
│   └── version-3/
├── templates/                               # 재사용 가능한 템플릿
│   ├── hero-sections/
│   ├── feature-sections/
│   └── cta-sections/
└── package.json                             # 의존성 관리
```

---

## 2. 필수 다운로드 및 설치 목록

### 2.1 ✅ 이미 사용 가능한 것들 (설치 불필요)

Claude Code에 기본 탑재되어 있어 **별도 다운로드 불필요**:

- ✅ `frontend-design` 플러그인 (이미 사용 가능)
- ✅ 기본 도구들: Read, Write, Edit, Glob, Grep, Bash
- ✅ Task 도구 (서브에이전트 실행용)

### 2.2 🔧 직접 설치해야 할 MCP 서버

다음 명령어를 **순서대로** 실행하세요:

#### A. Web Tools MCP (디자인 레퍼런스 분석용)
```bash
claude mcp add --transport stdio web-tools -- npx -y @modelcontextprotocol/server-puppeteer
```
**용도**: Mobbin, 디자인 레퍼런스 URL 스크린샷 및 DOM 분석

#### B. GitHub MCP (버전 관리용 - 선택사항)
```bash
claude mcp add --transport http github https://api.githubcopilot.com/mcp/
```
**용도**: 생성된 랜딩페이지 자동 커밋 및 배포

#### C. Google Drive MCP (디자인 에셋 관리용 - 선택사항)
```bash
claude mcp add --transport http gdrive -- npx -y @modelcontextprotocol/server-gdrive
```
**용도**: 디자인 레퍼런스 이미지 저장 및 불러오기

### 2.3 📦 직접 생성해야 할 것들

다음 항목들은 **이 계획서를 기반으로 직접 생성**해야 합니다:

- [ ] 5개의 서브에이전트 (`.claude/agents/*.md`)
- [ ] 3개의 스킬 (`.claude/skills/*/SKILL.md`)
- [ ] CLAUDE.md 프로젝트 컨텍스트
- [ ] 템플릿 예시 파일들 (HTML/CSS)

### 2.4 ⚠️ 공식 템플릿 다운로드 (참고용)

Claude Code 공식 GitHub에서 **참고용 예시**를 확인할 수 있습니다:

```bash
# 1. Claude Code 공식 예제 클론 (참고용)
git clone https://github.com/anthropics/claude-code-examples.git
cd claude-code-examples

# 2. 서브에이전트 예시 확인
cat examples/sub-agents/code-reviewer.md
cat examples/sub-agents/debugger.md

# 3. 스킬 예시 확인
cat examples/skills/api-design/SKILL.md
```

**주의**: 이 예제들은 **참고만** 하고, 랜딩페이지 목적에 맞게 **직접 커스터마이징**해야 합니다.

---

## 3. 서브에이전트 구현 계획

PRD.md 섹션 6에 명시된 5개 에이전트 구현 방법

### 3.1 Main Orchestrator Agent

**파일 경로**: `.claude/agents/main-orchestrator.md`

```markdown
---
name: main-orchestrator
description: 랜딩페이지 제작 프로세스를 총괄하는 메인 오케스트레이터. 사용자 요구사항을 분석하고 다른 에이전트들을 조율하여 2-3개의 랜딩페이지 버전을 생성합니다.
tools: Read, Write, Glob, Grep, Bash, Task, AskUserQuestion
model: sonnet
permissionMode: default
skills: landing-page-patterns
---

# Main Orchestrator Agent

당신은 랜딩페이지 제작 프로젝트의 총 책임자입니다. 사용자 요구사항을 정제하고, 적절한 서브 에이전트들을 조율하며, 최종 결과물을 통합하는 역할을 담당합니다.

## 핵심 책임

### 1. 요구사항 분석
- 사용자 입력을 받아 명확한 랜딩페이지 목표 설정
- 디자인 레퍼런스 URL이 제공된 경우 분석
- 타겟 오디언스, 브랜드 톤, 전환 목표 파악

### 2. 작업 분배
다음 서브에이전트들을 순차적으로 호출:
1. **Layout Designer**: 섹션 구조 설계
2. **UI Stylist**: 디자인 토큰 및 스타일 정의
3. **Copywriter**: 헤드라인, 기능 설명, CTA 문구 작성
4. **Frontend Coder**: 최종 HTML/CSS/JS 코드 생성

### 3. 버전 생성 전략
- 동일 요구사항 기반 **2-3개 디자인 버전** 생성
- 각 버전은 다른 스타일 접근법 사용:
  - 버전 1: 미니멀 & 클린
  - 버전 2: 대담한 타이포그래피 & 컬러
  - 버전 3: 다크모드 & 현대적

### 4. 결과물 통합
- 각 에이전트 결과물을 `output/version-{n}/` 디렉토리에 저장
- 사용자에게 3가지 버전 미리보기 제공
- 선택된 버전 추가 수정 지원

## 실행 흐름

```
사용자 입력 접수
    ↓
요구사항 정제 (AskUserQuestion 활용)
    ↓
디자인 레퍼런스 분석 (URL 제공 시)
    ↓
[병렬 실행] Layout Designer + UI Stylist
    ↓
Copywriter 실행
    ↓
Frontend Coder 실행 (버전 1)
    ↓
스타일 변경 후 Frontend Coder 재실행 (버전 2, 3)
    ↓
결과물 저장 및 사용자 피드백 수집
```

## 사용 예시

사용자 입력:
> "SaaS 제품용 미니멀한 랜딩페이지가 필요해요. 타겟은 개발자입니다."

당신의 행동:
1. AskUserQuestion으로 추가 정보 확인:
   - 주요 기능 3가지는?
   - 선호하는 색상?
   - 디자인 레퍼런스 있나요?
2. Layout Designer 호출
3. UI Stylist 호출 (개발자 타겟 → 다크 모드 우선)
4. Copywriter 호출 (기술적 톤)
5. Frontend Coder 호출 (3회, 각각 다른 스타일)
6. `output/version-{1,2,3}/index.html` 생성
```

**생성 방법**:
```bash
# 대화형 인터페이스 사용
/agents

# 또는 파일 직접 생성
mkdir -p .claude/agents
# 위 내용을 .claude/agents/main-orchestrator.md에 저장
```

---

### 3.2 Layout Designer Agent

**파일 경로**: `.claude/agents/layout-designer.md`

```markdown
---
name: layout-designer
description: 랜딩페이지 섹션 구조와 레이아웃을 설계하는 전문가. Hero, Features, CTA, Footer 섹션의 배치와 위계를 결정합니다.
tools: Read, Glob, Grep
model: sonnet
permissionMode: default
skills: landing-page-patterns
---

# Layout Designer Agent

당신은 랜딩페이지 레이아웃 설계 전문가입니다. 전환율을 최적화하는 섹션 구조를 설계합니다.

## 핵심 책임

### 1. 섹션 구조 결정
표준 랜딩페이지 섹션:
- **Hero Section**: 헤드라인 + 서브헤드라인 + CTA + 히어로 이미지
- **Social Proof**: 고객 로고 또는 숫자 (신뢰 구축)
- **Problem Section**: 타겟의 고민 정의
- **Solution Section**: 제품/서비스가 해결하는 방법
- **Features Section**: 주요 기능 3-6개 (아이콘 + 설명)
- **Testimonials**: 고객 후기 2-3개
- **Pricing** (선택): 간단한 가격표
- **Final CTA**: 강력한 재유도
- **Footer**: 링크, 소셜 미디어, 법적 정보

### 2. 레이아웃 패턴 선택

#### 패턴 A: 단일 컬럼 (모바일 최적화)
```
[Hero - Full Width]
[Social Proof - Logos]
[Feature 1 - Full Width]
[Feature 2 - Full Width]
[Feature 3 - Full Width]
[CTA - Full Width]
```

#### 패턴 B: 번갈아 나타나는 2 컬럼
```
[Hero - 왼쪽 텍스트 / 오른쪽 이미지]
[Feature 1 - 왼쪽 이미지 / 오른쪽 텍스트]
[Feature 2 - 왼쪽 텍스트 / 오른쪽 이미지]
[CTA - 중앙 정렬]
```

#### 패턴 C: 그리드 기반 (현대적)
```
[Hero - 중앙 정렬]
[Features - 3 컬럼 그리드]
[Testimonials - 2 컬럼 그리드]
[CTA - Full Width]
```

### 3. 반응형 브레이크포인트

| 디바이스 | 너비 | 컬럼 수 |
|---------|------|---------|
| Mobile | < 640px | 1 |
| Tablet | 640-1024px | 2 |
| Desktop | > 1024px | 3 |

### 4. 출력 형식

JSON 형식으로 레이아웃 구조 출력:

```json
{
  "layout_type": "alternating-two-column",
  "sections": [
    {
      "name": "hero",
      "position": 1,
      "layout": "left-text-right-image",
      "height": "100vh",
      "elements": ["headline", "subheadline", "cta-button", "hero-image"]
    },
    {
      "name": "social-proof",
      "position": 2,
      "layout": "centered-logos",
      "elements": ["customer-logos"]
    },
    {
      "name": "features",
      "position": 3,
      "layout": "three-column-grid",
      "elements": ["feature-card-1", "feature-card-2", "feature-card-3"]
    }
  ],
  "responsive_strategy": "mobile-first",
  "max_width": "1200px"
}
```

## 사용 예시

입력: "개발자 타겟, 기술 중심 SaaS"

출력:
- 레이아웃: 번갈아 나타나는 2 컬럼
- Hero: 코드 스니펫 이미지 + 기술적 헤드라인
- Features: 3 컬럼 그리드 (API 문서, SDK, CLI)
- CTA: GitHub 스타 + "Start Building" 버튼
```

---

### 3.3 UI Stylist Agent

**파일 경로**: `.claude/agents/ui-stylist.md`

```markdown
---
name: ui-stylist
description: 색상, 타이포그래피, 여백, 디자인 토큰을 정의하는 UI 스타일 전문가. 디자인 레퍼런스를 분석하여 일관된 디자인 시스템을 생성합니다.
tools: Read, Glob, Grep, WebFetch
model: sonnet
permissionMode: default
skills: design-system
---

# UI Stylist Agent

당신은 시각적으로 일관되고 현대적인 디자인 시스템을 만드는 전문가입니다.

## 핵심 책임

### 1. 색상 팔레트 정의

#### 색상 시스템 구조
```css
:root {
  /* Primary Colors */
  --color-primary-50: #...;
  --color-primary-100: #...;
  --color-primary-500: #...;  /* 메인 브랜드 색상 */
  --color-primary-900: #...;

  /* Neutral Colors */
  --color-neutral-50: #fafafa;
  --color-neutral-900: #171717;

  /* Semantic Colors */
  --color-success: #10b981;
  --color-error: #ef4444;
  --color-warning: #f59e0b;

  /* CTA Color */
  --color-cta: var(--color-primary-500);
  --color-cta-hover: var(--color-primary-600);
}
```

#### 색상 선택 전략

| 타겟 오디언스 | 추천 Primary Color | 이유 |
|-------------|-------------------|------|
| 개발자 | #6366f1 (Indigo) | 기술적, 신뢰감 |
| 비즈니스 | #0ea5e9 (Blue) | 전문성, 안정감 |
| 크리에이티브 | #ec4899 (Pink) | 창의적, 활기찬 |
| 일반 SaaS | #8b5cf6 (Purple) | 혁신적, 현대적 |

### 2. 타이포그래피 시스템

```css
:root {
  /* Font Families */
  --font-display: 'Inter', -apple-system, sans-serif;
  --font-body: 'Inter', -apple-system, sans-serif;
  --font-mono: 'JetBrains Mono', monospace;

  /* Font Sizes (Type Scale 1.250 - Major Third) */
  --text-xs: 0.75rem;    /* 12px */
  --text-sm: 0.875rem;   /* 14px */
  --text-base: 1rem;     /* 16px */
  --text-lg: 1.125rem;   /* 18px */
  --text-xl: 1.25rem;    /* 20px */
  --text-2xl: 1.5rem;    /* 24px */
  --text-3xl: 1.875rem;  /* 30px */
  --text-4xl: 2.25rem;   /* 36px */
  --text-5xl: 3rem;      /* 48px */
  --text-6xl: 3.75rem;   /* 60px */

  /* Line Heights */
  --leading-tight: 1.25;
  --leading-normal: 1.5;
  --leading-relaxed: 1.75;

  /* Font Weights */
  --font-normal: 400;
  --font-medium: 500;
  --font-semibold: 600;
  --font-bold: 700;
}
```

#### 타이포그래피 사용 규칙

| 요소 | 크기 | 굵기 | 용도 |
|-----|------|------|------|
| H1 (Hero) | text-6xl (60px) | bold (700) | 메인 헤드라인 |
| H2 (Section) | text-4xl (36px) | semibold (600) | 섹션 제목 |
| H3 (Feature) | text-2xl (24px) | semibold (600) | 기능 제목 |
| Body | text-base (16px) | normal (400) | 본문 |
| CTA Button | text-lg (18px) | medium (500) | 버튼 텍스트 |
| Caption | text-sm (14px) | normal (400) | 캡션, 부가 정보 |

### 3. 여백 시스템 (Spacing Scale)

```css
:root {
  --space-1: 0.25rem;   /* 4px */
  --space-2: 0.5rem;    /* 8px */
  --space-3: 0.75rem;   /* 12px */
  --space-4: 1rem;      /* 16px */
  --space-6: 1.5rem;    /* 24px */
  --space-8: 2rem;      /* 32px */
  --space-12: 3rem;     /* 48px */
  --space-16: 4rem;     /* 64px */
  --space-24: 6rem;     /* 96px */
  --space-32: 8rem;     /* 128px */
}
```

### 4. 디자인 레퍼런스 분석

디자인 레퍼런스 URL이 제공된 경우:

1. **WebFetch로 페이지 스크린샷 획득**
2. **색상 추출**: 주요 색상 5-7개 식별
3. **폰트 식별**: DevTools로 font-family 확인
4. **여백 패턴**: 섹션 간 간격, 내부 패딩 측정
5. **레이아웃 구조**: 그리드 시스템, 컬럼 수 파악

### 5. 출력 형식

CSS 변수 형태로 디자인 토큰 출력:

```css
/* Generated Design Tokens */
:root {
  /* Colors */
  --color-primary: #6366f1;
  --color-background: #ffffff;
  --color-text: #171717;

  /* Typography */
  --font-display: 'Inter', sans-serif;
  --text-hero: 3.75rem;

  /* Spacing */
  --section-padding: 6rem;
  --container-max-width: 1200px;

  /* Effects */
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.05);
  --shadow-md: 0 4px 6px rgba(0,0,0,0.1);
  --radius-sm: 0.375rem;
  --radius-md: 0.5rem;
  --radius-lg: 0.75rem;
}
```

## 사용 예시

입력: "다크모드, 개발자 타겟, 레퍼런스: https://vercel.com"

행동:
1. WebFetch로 vercel.com 분석
2. 색상: 다크 배경 (#0a0a0a), Accent (Cyan #00e5ff)
3. 폰트: Inter 사용 확인
4. 레이아웃: 최대 너비 1200px, 섹션 패딩 96px
5. CSS 변수 생성 후 반환
```

---

### 3.4 Copywriter Agent

**파일 경로**: `.claude/agents/copywriter.md`

```markdown
---
name: copywriter
description: 전환율을 높이는 마케팅 카피를 작성하는 전문가. Hero 헤드라인, 기능 설명, CTA 문구를 생성합니다.
tools: Read, Grep
model: sonnet
permissionMode: default
skills: landing-page-patterns
---

# Copywriter Agent

당신은 전환율을 극대화하는 설득력 있는 카피를 작성하는 전문가입니다.

## 핵심 책임

### 1. Hero 헤드라인 작성

#### 효과적인 헤드라인 공식

| 공식 | 예시 | 사용 상황 |
|-----|------|----------|
| **결과 중심** | "10배 빠른 웹사이트 빌드" | 명확한 성과 제시 |
| **문제 해결** | "복잡한 배포, 이제 클릭 한 번으로" | 고객 고민 해결 |
| **대비 강조** | "코드는 간단하게, 성능은 강력하게" | 장점 극대화 |
| **질문형** | "랜딩페이지 제작에 일주일씩 쓰시나요?" | 공감 유도 |

#### 작성 체크리스트
- [ ] 60자 이내 (모바일 화면 고려)
- [ ] 구체적인 가치 제안 포함
- [ ] 타겟 오디언스의 언어 사용
- [ ] 긴박감 또는 독창성 표현

### 2. 서브헤드라인 작성

헤드라인을 **보완하고 구체화**:

```
헤드라인: "개발자를 위한 가장 빠른 배포 플랫폼"
서브헤드라인: "Git push 한 번으로 글로벌 엣지 네트워크에 자동 배포.
               설정 파일도, 복잡한 설정도 필요 없습니다."
```

### 3. Feature 카피 작성

각 기능마다 **3-4줄 설명** 작성:

```markdown
### ⚡ 즉시 배포
Git에 푸시하면 자동으로 전 세계 150개 엣지 로케이션에 배포됩니다.
CDN 설정, 캐싱 전략, SSL 인증서 모두 자동 처리됩니다.
```

**공식**: [아이콘] + [기능명] + [구체적 이점] + [기술적 디테일]

### 4. CTA 문구 최적화

#### 일반적 CTA vs 최적화된 CTA

| 일반적 | 최적화 | 전환율 차이 |
|--------|--------|------------|
| "시작하기" | "무료로 시작하기" | +25% |
| "가입" | "30초 만에 가입" | +18% |
| "더 알아보기" | "3분 데모 보기" | +32% |
| "다운로드" | "지금 무료 다운로드" | +21% |

#### CTA 작성 원칙
1. **명확한 행동**: "시작", "가입", "다운로드"
2. **가치 제시**: "무료", "즉시", "간단하게"
3. **긴박감**: "오늘", "지금", "한정"
4. **위험 제거**: "무료", "취소 가능", "신용카드 불필요"

### 5. 사회적 증거 카피

#### 숫자 강조
```
"1만+ 개발자가 선택한 플랫폼"
"월 10억 요청 처리"
"99.99% 업타임 보장"
```

#### 고객 후기
```
"배포 시간이 1시간에서 30초로 단축됐어요."
— 김철수, 스타트업 CTO
```

### 6. 출력 형식

JSON 형식으로 모든 카피 제공:

```json
{
  "hero": {
    "headline": "개발자를 위한 가장 빠른 배포 플랫폼",
    "subheadline": "Git push 한 번으로 글로벌 배포. 복잡한 설정 불필요.",
    "cta_primary": "무료로 시작하기",
    "cta_secondary": "3분 데모 보기"
  },
  "features": [
    {
      "title": "즉시 배포",
      "description": "Git에 푸시하면 자동으로 전 세계 150개 엣지에 배포됩니다."
    }
  ],
  "social_proof": {
    "numbers": "10,000+ 개발자 사용 중",
    "testimonial": "배포 시간 98% 단축"
  },
  "final_cta": {
    "headline": "지금 바로 시작하세요",
    "button": "무료 계정 만들기",
    "subtext": "신용카드 불필요"
  }
}
```

## 사용 예시

입력: "SaaS 제품, 타겟: 개발자, 기능: 자동 배포, API 우선, 글로벌 CDN"

출력:
```json
{
  "hero": {
    "headline": "코드를 푸시하면, 세계가 연결됩니다",
    "subheadline": "Git 기반 자동 배포 + API 우선 아키텍처 + 150개국 CDN",
    "cta_primary": "5분 만에 배포하기",
    "cta_secondary": "GitHub으로 시작"
  }
}
```
```

---

### 3.5 Frontend Coder Agent

**파일 경로**: `.claude/agents/frontend-coder.md`

```markdown
---
name: frontend-coder
description: 시맨틱 HTML, 모던 CSS, 바닐라 JS로 고품질 랜딩페이지 코드를 생성하는 전문가. 접근성, 성능, 유지보수성을 최우선으로 합니다.
tools: Write, Read, Edit, Bash
model: sonnet
permissionMode: default
skills: frontend-best-practices
---

# Frontend Coder Agent

당신은 프로덕션 레디 랜딩페이지 코드를 작성하는 전문가입니다.

## 핵심 책임

### 1. 시맨틱 HTML 작성

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="제품 설명 (SEO용)">
  <title>제품명 - 간단한 설명</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <!-- Hero Section -->
  <header class="hero">
    <nav class="navbar" role="navigation" aria-label="Main navigation">
      <div class="logo">로고</div>
      <ul class="nav-links">
        <li><a href="#features">기능</a></li>
        <li><a href="#pricing">가격</a></li>
      </ul>
    </nav>

    <section class="hero-content">
      <h1>메인 헤드라인</h1>
      <p>서브헤드라인</p>
      <a href="#cta" class="btn btn-primary">무료로 시작하기</a>
    </section>
  </header>

  <!-- Features Section -->
  <main>
    <section id="features" class="features">
      <h2>주요 기능</h2>
      <div class="feature-grid">
        <article class="feature-card">
          <div class="feature-icon" aria-hidden="true">⚡</div>
          <h3>빠른 배포</h3>
          <p>설명</p>
        </article>
      </div>
    </section>
  </main>

  <!-- Footer -->
  <footer class="footer">
    <p>&copy; 2025 회사명. All rights reserved.</p>
  </footer>

  <script src="script.js"></script>
</body>
</html>
```

### 2. 모던 CSS (CSS Variables + Flexbox/Grid)

```css
/* ===== Design Tokens ===== */
:root {
  --color-primary: #6366f1;
  --color-background: #ffffff;
  --color-text: #171717;
  --font-display: 'Inter', sans-serif;
  --space-section: 6rem;
  --container-max: 1200px;
}

/* ===== Reset ===== */
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: var(--font-display);
  color: var(--color-text);
  background: var(--color-background);
  line-height: 1.6;
}

/* ===== Layout ===== */
.container {
  max-width: var(--container-max);
  margin: 0 auto;
  padding: 0 1.5rem;
}

/* ===== Hero Section ===== */
.hero {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  padding: var(--space-section) 1.5rem;
}

.hero h1 {
  font-size: clamp(2rem, 5vw, 3.75rem);
  font-weight: 700;
  margin-bottom: 1.5rem;
}

/* ===== Buttons ===== */
.btn {
  display: inline-block;
  padding: 1rem 2rem;
  border-radius: 0.5rem;
  text-decoration: none;
  font-weight: 500;
  transition: all 0.2s ease;
}

.btn-primary {
  background: var(--color-primary);
  color: white;
}

.btn-primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(99, 102, 241, 0.3);
}

/* ===== Responsive ===== */
@media (max-width: 768px) {
  .hero h1 {
    font-size: 2rem;
  }

  .feature-grid {
    grid-template-columns: 1fr;
  }
}
```

### 3. 바닐라 JavaScript (선택적)

```javascript
// 스크롤 애니메이션 (Intersection Observer)
const observerOptions = {
  threshold: 0.1,
  rootMargin: '0px 0px -100px 0px'
};

const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('animate-in');
    }
  });
}, observerOptions);

document.querySelectorAll('.feature-card').forEach(card => {
  observer.observe(card);
});

// 부드러운 스크롤
document.querySelectorAll('a[href^="#"]').forEach(anchor => {
  anchor.addEventListener('click', function (e) {
    e.preventDefault();
    const target = document.querySelector(this.getAttribute('href'));
    target.scrollIntoView({ behavior: 'smooth' });
  });
});
```

### 4. 접근성 체크리스트

- [ ] 모든 이미지에 alt 속성
- [ ] 색상 대비 비율 4.5:1 이상 (WCAG AA)
- [ ] 키보드 네비게이션 가능
- [ ] ARIA 레이블 적절히 사용
- [ ] focus-visible 스타일 제공
- [ ] 시맨틱 HTML 태그 사용

### 5. 성능 최적화

```html
<!-- Critical CSS 인라인 -->
<style>
  /* 첫 화면 렌더링에 필요한 CSS만 */
  .hero { ... }
</style>

<!-- 나머지 CSS는 비동기 로드 -->
<link rel="preload" href="styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">

<!-- 이미지 최적화 -->
<img
  src="hero.jpg"
  alt="설명"
  loading="lazy"
  width="800"
  height="600"
>
```

### 6. 파일 구조

각 버전마다 별도 디렉토리:

```
output/
├── version-1/
│   ├── index.html
│   ├── styles.css
│   ├── script.js
│   └── README.md (버전 설명)
├── version-2/
│   └── ...
└── version-3/
    └── ...
```

## 사용 예시

입력:
- Layout JSON (from Layout Designer)
- Design Tokens CSS (from UI Stylist)
- Copy JSON (from Copywriter)

출력:
- `output/version-1/index.html` (완전한 HTML)
- `output/version-1/styles.css` (모든 스타일)
- `output/version-1/script.js` (인터랙션)
- `output/version-1/README.md` (사용 방법)

코드 품질:
- ESLint/Prettier 규칙 준수
- 주석으로 섹션 명확히 구분
- CSS 변수로 유지보수 용이
- 모바일 우선 반응형
```

---

## 4. 스킬 구현 계획

PRD.md 섹션 7에 명시된 3개 스킬 구현

### 4.1 Landing Page Patterns Skill

**디렉토리**: `.claude/skills/landing-page-patterns/`

**SKILL.md**:
```markdown
---
name: landing-page-patterns
description: 전환율을 최적화하는 랜딩페이지 패턴과 레이아웃 전략을 제공합니다. Hero-Problem-Solution-CTA 구조를 가르칩니다.
allowed-tools: Read, Glob, Grep
---

# Landing Page Patterns Skill

## 핵심 패턴

### 1. Hero-Problem-Solution-CTA 구조

```
┌─────────────────────────────┐
│  HERO SECTION               │
│  - 헤드라인 (가치 제안)      │
│  - 서브헤드라인 (구체화)     │
│  - CTA 버튼                  │
│  - 히어로 이미지/비디오      │
└─────────────────────────────┘
         ↓
┌─────────────────────────────┐
│  SOCIAL PROOF               │
│  - 고객 로고                 │
│  - 사용자 수 / 통계          │
└─────────────────────────────┘
         ↓
┌─────────────────────────────┐
│  PROBLEM SECTION            │
│  - 타겟의 고민 3가지         │
└─────────────────────────────┘
         ↓
┌─────────────────────────────┐
│  SOLUTION SECTION           │
│  - 제품이 해결하는 방법      │
└─────────────────────────────┘
         ↓
┌─────────────────────────────┐
│  FEATURES                   │
│  - 주요 기능 3-6개           │
│  - 아이콘 + 제목 + 설명      │
└─────────────────────────────┘
         ↓
┌─────────────────────────────┐
│  TESTIMONIALS               │
│  - 고객 후기 2-3개           │
│  - 사진 + 이름 + 직함        │
└─────────────────────────────┘
         ↓
┌─────────────────────────────┐
│  FINAL CTA                  │
│  - 강력한 재유도             │
│  - 위험 제거 문구            │
└─────────────────────────────┘
         ↓
┌─────────────────────────────┐
│  FOOTER                     │
│  - 링크, 소셜, 법적 정보     │
└─────────────────────────────┘
```

### 2. 전환율 최적화 원칙

1. **Above the Fold**: CTA는 스크롤 없이 보여야 함
2. **F-Pattern**: 사용자는 F자 형태로 읽음 → 왼쪽 정렬
3. **3초 룰**: 3초 안에 가치 이해 가능해야 함
4. **단일 목표**: 한 페이지에 하나의 CTA 목표

### 3. CTA 배치 전략

| 위치 | 전환율 | 사용 상황 |
|-----|--------|----------|
| Hero (Above Fold) | 35% | 즉시 전환 유도 |
| Feature 섹션 후 | 25% | 가치 이해 후 전환 |
| Testimonial 후 | 20% | 신뢰 구축 후 전환 |
| Footer | 15% | 페이지 끝에서 재유도 |

더 자세한 내용은 `conversion-patterns.md` 참조.
```

**conversion-patterns.md**:
```markdown
# 전환율 최적화 패턴

## A/B 테스트 검증된 패턴

### 헤드라인 공식

1. **결과 중심**: "[숫자]% [결과] 달성"
   - 예: "3배 빠른 웹사이트 로딩"

2. **문제 해결**: "[고민] 해결하는 [솔루션]"
   - 예: "복잡한 배포를 5분으로 단축하는 플랫폼"

3. **대비 강조**: "[A]는 [형용사], [B]는 [형용사]"
   - 예: "설정은 간단하게, 성능은 강력하게"

### CTA 버튼 최적화

| 요소 | 일반 | 최적화 | 전환율 증가 |
|-----|------|--------|------------|
| 색상 | 파랑 | 주황/초록 (대비) | +21% |
| 크기 | 보통 | 큼 (48px 이상) | +17% |
| 문구 | "시작하기" | "무료로 시작하기" | +25% |
| 위치 | 중앙 | 왼쪽 정렬 (F-Pattern) | +11% |

(더 많은 패턴 계속...)
```

**examples/saas-minimal.html**: (실제 HTML 예제 포함)

---

### 4.2 Design System Skill

**디렉토리**: `.claude/skills/design-system/`

**SKILL.md**:
```markdown
---
name: design-system
description: 일관된 시각적 디자인 시스템을 구축합니다. 색상, 타이포그래피, 여백 규칙을 제공합니다.
allowed-tools: Read, Glob
---

# Design System Skill

## 색상 시스템

### Tailwind 스타일 색상 스케일 (50-950)

각 색상마다 10단계 제공:

```css
:root {
  --color-primary-50: #f0f9ff;
  --color-primary-100: #e0f2fe;
  --color-primary-200: #bae6fd;
  --color-primary-300: #7dd3fc;
  --color-primary-400: #38bdf8;
  --color-primary-500: #0ea5e9;  /* 메인 */
  --color-primary-600: #0284c7;
  --color-primary-700: #0369a1;
  --color-primary-800: #075985;
  --color-primary-900: #0c4a6e;
  --color-primary-950: #082f49;
}
```

### 색상 대비 규칙 (WCAG AA)

| 텍스트 크기 | 최소 대비 비율 |
|-----------|---------------|
| 일반 텍스트 (< 18px) | 4.5:1 |
| 큰 텍스트 (≥ 18px) | 3:1 |
| UI 요소 | 3:1 |

**검증 도구**: https://webaim.org/resources/contrastchecker/

## 타이포그래피

### Type Scale (1.250 - Major Third)

| 레벨 | 크기 | 사용 용도 |
|-----|------|----------|
| 6xl | 60px | Hero 헤드라인 |
| 5xl | 48px | 섹션 주 제목 |
| 4xl | 36px | 섹션 부 제목 |
| 3xl | 30px | 카드 제목 |
| 2xl | 24px | Feature 제목 |
| xl | 20px | 강조 텍스트 |
| lg | 18px | 버튼, 링크 |
| base | 16px | 본문 |
| sm | 14px | 캡션 |
| xs | 12px | 라벨 |

자세한 내용은 `typography.md` 참조.
```

**typography.md**, **spacing-system.md**: (상세 규칙 포함)

---

### 4.3 Frontend Best Practices Skill

**디렉토리**: `.claude/skills/frontend-best-practices/`

**SKILL.md**:
```markdown
---
name: frontend-best-practices
description: 시맨틱 HTML, 접근성, Tailwind CSS 패턴 등 프론트엔드 베스트 프랙티스를 가르칩니다.
allowed-tools: Read, Grep
---

# Frontend Best Practices Skill

## 시맨틱 HTML

### 올바른 태그 사용

| 용도 | 나쁜 예 | 좋은 예 |
|-----|---------|---------|
| 내비게이션 | `<div class="nav">` | `<nav>` |
| 메인 콘텐츠 | `<div class="main">` | `<main>` |
| 섹션 구분 | `<div class="section">` | `<section>` |
| 독립 콘텐츠 | `<div class="card">` | `<article>` |
| 보조 정보 | `<div class="sidebar">` | `<aside>` |
| 헤더 영역 | `<div class="header">` | `<header>` |
| 푸터 영역 | `<div class="footer">` | `<footer>` |

## 접근성 (A11Y)

### ARIA 레이블

```html
<!-- 버튼 역할 명확히 -->
<button aria-label="메뉴 열기" class="menu-btn">
  <span aria-hidden="true">☰</span>
</button>

<!-- 내비게이션 구분 -->
<nav aria-label="메인 네비게이션">
  <ul>...</ul>
</nav>

<!-- 폼 접근성 -->
<label for="email">이메일 주소</label>
<input
  type="email"
  id="email"
  aria-required="true"
  aria-describedby="email-help"
>
<span id="email-help">유효한 이메일을 입력하세요</span>
```

### 키보드 네비게이션

```css
/* 포커스 스타일 필수 */
*:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

/* 마우스 클릭 시에는 아웃라인 제거 */
*:focus:not(:focus-visible) {
  outline: none;
}
```

## 성능 최적화

### Critical CSS 인라인

```html
<head>
  <style>
    /* 첫 화면에 필요한 CSS만 */
    body { margin: 0; font-family: sans-serif; }
    .hero { min-height: 100vh; }
  </style>

  <!-- 나머지는 비동기 로드 -->
  <link rel="preload" href="styles.css" as="style" onload="this.rel='stylesheet'">
</head>
```

### 이미지 최적화

```html
<!-- WebP + 폴백 -->
<picture>
  <source srcset="hero.webp" type="image/webp">
  <img src="hero.jpg" alt="설명" loading="lazy">
</picture>

<!-- Responsive Images -->
<img
  srcset="
    hero-400.jpg 400w,
    hero-800.jpg 800w,
    hero-1200.jpg 1200w
  "
  sizes="(max-width: 600px) 400px, (max-width: 1000px) 800px, 1200px"
  src="hero-800.jpg"
  alt="설명"
>
```

자세한 내용은 `accessibility.md`, `performance.md` 참조.
```

---

## 5. MCP 서버 설정

### 5.1 .mcp.json 파일 생성

**파일 경로**: `.claude/.mcp.json`

```json
{
  "mcpServers": {
    "web-tools": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"],
      "description": "웹페이지 스크린샷 및 DOM 분석"
    },
    "github": {
      "url": "https://api.githubcopilot.com/mcp/",
      "description": "GitHub 레포지토리 관리"
    }
  }
}
```

### 5.2 설치 확인

```bash
# MCP 서버 목록 확인
claude mcp list

# 특정 서버 테스트
claude mcp test web-tools
```

---

## 6. 단계별 구현 로드맵

### Phase 1: 기반 구축 (1-2일)

**목표**: 프로젝트 구조 및 기본 에이전트/스킬 생성

```bash
# 1. 디렉토리 구조 생성
mkdir -p .claude/agents .claude/skills output templates

# 2. Main Orchestrator 에이전트 생성
/agents
# → "main-orchestrator" 생성
# → 위 섹션 3.1 내용을 기반으로 작성

# 3. Landing Page Patterns 스킬 생성
mkdir -p .claude/skills/landing-page-patterns
# → SKILL.md 작성 (섹션 4.1 참조)

# 4. CLAUDE.md 작성
cat > CLAUDE.md << 'EOF'
# 랜딩페이지 제작 에이전트 프로젝트

## 프로젝트 목적
사용자 요구사항에 따라 고품질 랜딩페이지를 자동 생성하는 에이전트 시스템

## 주요 에이전트
- main-orchestrator: 전체 프로세스 조율
- layout-designer: 레이아웃 설계
- ui-stylist: 디자인 시스템 정의
- copywriter: 마케팅 카피 작성
- frontend-coder: HTML/CSS/JS 코드 생성

## 주요 스킬
- landing-page-patterns: 전환 최적화 패턴
- design-system: 색상, 타이포그래피 규칙
- frontend-best-practices: 접근성, 성능 최적화

## 출력 디렉토리
- output/version-1/ : 첫 번째 버전
- output/version-2/ : 두 번째 버전
- output/version-3/ : 세 번째 버전
EOF
```

**체크리스트**:
- [ ] 디렉토리 구조 생성 완료
- [ ] main-orchestrator.md 작성 완료
- [ ] landing-page-patterns 스킬 작성 완료
- [ ] CLAUDE.md 작성 완료

---

### Phase 2: 핵심 에이전트 구현 (2-3일)

**목표**: Layout Designer, UI Stylist, Copywriter 에이전트 생성

```bash
# 1. Layout Designer 생성
/agents
# → "layout-designer" 생성 (섹션 3.2)

# 2. UI Stylist 생성
/agents
# → "ui-stylist" 생성 (섹션 3.3)

# 3. Copywriter 생성
/agents
# → "copywriter" 생성 (섹션 3.4)

# 4. Design System 스킬 생성
mkdir -p .claude/skills/design-system
# → SKILL.md 작성 (섹션 4.2)
```

**테스트**:
```bash
# 각 에이전트 개별 테스트
Use the layout-designer agent to create a layout for a SaaS landing page

Use the ui-stylist agent to create a design system for a developer-focused product

Use the copywriter agent to write hero copy for a deployment platform
```

**체크리스트**:
- [ ] layout-designer.md 작성 완료
- [ ] ui-stylist.md 작성 완료
- [ ] copywriter.md 작성 완료
- [ ] design-system 스킬 작성 완료
- [ ] 각 에이전트 개별 테스트 통과

---

### Phase 3: 코드 생성 에이전트 (2일)

**목표**: Frontend Coder 구현 및 통합

```bash
# 1. Frontend Coder 생성
/agents
# → "frontend-coder" 생성 (섹션 3.5)

# 2. Frontend Best Practices 스킬 생성
mkdir -p .claude/skills/frontend-best-practices
# → SKILL.md 작성 (섹션 4.3)

# 3. 템플릿 예시 파일 생성
mkdir -p templates/hero-sections
# → 재사용 가능한 HTML/CSS 스니펫 저장
```

**테스트**:
```bash
# 전체 플로우 테스트
Use the main-orchestrator agent to create a landing page for:
- Product: AI-powered deployment platform
- Target: Developers
- Style: Minimal, dark mode
- Features: Auto-deploy, API-first, Global CDN
```

**체크리스트**:
- [ ] frontend-coder.md 작성 완료
- [ ] frontend-best-practices 스킬 작성 완료
- [ ] 템플릿 예시 3개 이상 생성
- [ ] 전체 플로우 End-to-End 테스트 통과

---

### Phase 4: 디자인 레퍼런스 통합 (2일)

**목표**: Mobbin 등 디자인 레퍼런스 URL 분석 기능

```bash
# 1. MCP Web Tools 설치
claude mcp add --transport stdio web-tools -- npx -y @modelcontextprotocol/server-puppeteer

# 2. UI Stylist에 WebFetch 기능 추가
# → ui-stylist.md의 tools에 "WebFetch" 추가
# → 레퍼런스 URL 분석 로직 구현

# 3. 테스트
Use the ui-stylist agent to analyze this design reference:
https://mobbin.com/browse/web/screens/...
```

**체크리스트**:
- [ ] MCP Web Tools 설치 완료
- [ ] UI Stylist에 URL 분석 기능 추가
- [ ] 실제 Mobbin URL로 테스트 성공

---

### Phase 5: 버전 생성 자동화 (1-2일)

**목표**: 동일 요구사항으로 3가지 스타일 버전 자동 생성

```bash
# Main Orchestrator 업데이트
# → 스타일 변형 로직 추가:
#   1. Minimal & Clean
#   2. Bold Typography & Vibrant
#   3. Dark Mode & Modern
```

**테스트**:
```bash
Use the main-orchestrator to generate 3 landing page versions for:
- Product: SaaS Analytics Platform
- Target: Marketing Teams
```

**예상 결과**:
```
output/
├── version-1/ (미니멀)
│   ├── index.html
│   ├── styles.css
│   └── README.md
├── version-2/ (대담한)
│   └── ...
└── version-3/ (다크모드)
    └── ...
```

**체크리스트**:
- [ ] 3가지 스타일 프리셋 정의 완료
- [ ] 자동 버전 생성 로직 구현
- [ ] 각 버전 README.md 자동 생성

---

### Phase 6: 최적화 및 문서화 (1일)

**목표**: 성능 최적화, 사용자 가이드 작성

```bash
# 1. 접근성 검증 추가
# → frontend-coder에 자동 체크리스트 추가

# 2. 성능 체크
# → Lighthouse 스코어 90+ 목표

# 3. 사용자 가이드 작성
cat > USER_GUIDE.md << 'EOF'
# 랜딩페이지 제작 에이전트 사용 가이드

## 빠른 시작

1. 에이전트 호출:
Use the main-orchestrator to create a landing page

2. 요구사항 입력:
- 제품명: ...
- 타겟 오디언스: ...
- 주요 기능: ...
- 스타일 선호: ...

3. 결과 확인:
output/version-{1,2,3}/index.html

## 고급 사용법
...
EOF
```

**체크리스트**:
- [ ] 접근성 자동 검증 추가
- [ ] 성능 최적화 완료
- [ ] USER_GUIDE.md 작성
- [ ] 예제 프로젝트 3개 생성

---

## 7. 테스트 시나리오

### 7.1 시나리오 1: SaaS 제품 (텍스트 기반)

**입력**:
```
Product: AI-powered code review platform
Target: Enterprise development teams
Features:
  - Automated security scanning
  - PR comment automation
  - Custom rule engine
Style: Professional, trustworthy, blue color scheme
```

**예상 출력**:
- 3가지 버전 (미니멀, 대담한, 다크모드)
- Hero 헤드라인: "Ship Secure Code 10x Faster"
- CTA: "Start Free 14-Day Trial"
- Features 섹션: 3 컬럼 그리드
- Testimonials: Enterprise 고객 후기

---

### 7.2 시나리오 2: 디자인 레퍼런스 기반

**입력**:
```
Product: Task management app
Target: Remote teams
Design Reference: https://linear.app (예시)
```

**행동**:
1. UI Stylist가 linear.app 분석
2. 색상 추출: Purple (#5E6AD2)
3. 폰트: Inter
4. 레이아웃: 왼쪽 텍스트 + 오른쪽 스크린샷
5. 유사한 스타일로 3가지 버전 생성

---

### 7.3 시나리오 3: 개발자 타겟

**입력**:
```
Product: API documentation platform
Target: Developers
Style: Dark mode, code-centric, minimal
```

**예상 출력**:
- 다크 배경 (#0a0a0a)
- Accent color: Cyan (#00e5ff)
- 코드 블록 강조
- GitHub 로그인 CTA
- 터미널 스타일 디자인

---

## 8. 확장 계획 (Post-MVP)

### 8.1 React/Next.js 지원

```bash
# 새 에이전트 추가
.claude/agents/react-converter.md

# 역할: HTML을 React 컴포넌트로 변환
# 출력: JSX + Tailwind CSS
```

### 8.2 이미지 생성 통합

```bash
# DALL-E 3 또는 Midjourney MCP 서버 추가
claude mcp add --transport http dalle https://api.openai.com/mcp/
```

### 8.3 A/B 테스트 버전 생성

```bash
# A/B 테스트용 에이전트
.claude/agents/ab-test-generator.md

# 기능:
# - CTA 버튼 색상 2가지 버전
# - 헤드라인 변형 3가지
# - Google Optimize 연동 코드 자동 삽입
```

### 8.4 브랜드 키트 고정

```bash
# 브랜드 스킬 생성
.claude/skills/brand-kit/

# 내용:
# - 고정 색상 (#FF5733)
# - 고정 폰트 (Helvetica Neue)
# - 로고 위치 규칙
# - 톤 앤 매너 가이드
```

---

## 9. 문제 해결 (Troubleshooting)

### 9.1 에이전트가 호출되지 않을 때

**증상**: `main-orchestrator`를 호출했는데 실행 안 됨

**해결**:
```bash
# 1. 에이전트 목록 확인
What agents are available?

# 2. YAML frontmatter 검증
# description 필드에 트리거 키워드 있는지 확인
# "landing page", "랜딩페이지" 등

# 3. 명시적 호출
Use the main-orchestrator agent to...
```

### 9.2 스킬이 로드되지 않을 때

**증상**: 에이전트가 스킬 내용을 모름

**해결**:
```bash
# 1. 스킬 목록 확인
What skills are available?

# 2. SKILL.md 파일 위치 확인
ls .claude/skills/landing-page-patterns/SKILL.md

# 3. 에이전트 frontmatter에 skills 필드 추가
skills: landing-page-patterns, design-system
```

### 9.3 MCP 서버 연결 실패

**증상**: `web-tools` MCP 서버 사용 불가

**해결**:
```bash
# 1. MCP 서버 상태 확인
claude mcp list

# 2. 재설치
claude mcp remove web-tools
claude mcp add --transport stdio web-tools -- npx -y @modelcontextprotocol/server-puppeteer

# 3. 수동 테스트
claude mcp test web-tools
```

---

## 10. 체크리스트 요약

### 설치 및 설정
- [ ] Claude Code 최신 버전 확인
- [ ] MCP Web Tools 설치
- [ ] 프로젝트 디렉토리 구조 생성

### 에이전트 생성
- [ ] main-orchestrator.md
- [ ] layout-designer.md
- [ ] ui-stylist.md
- [ ] copywriter.md
- [ ] frontend-coder.md

### 스킬 생성
- [ ] landing-page-patterns/SKILL.md
- [ ] design-system/SKILL.md
- [ ] frontend-best-practices/SKILL.md

### 테스트
- [ ] 시나리오 1 (텍스트 기반) 통과
- [ ] 시나리오 2 (레퍼런스 기반) 통과
- [ ] 시나리오 3 (개발자 타겟) 통과
- [ ] 3가지 버전 자동 생성 확인

### 문서화
- [ ] CLAUDE.md 작성
- [ ] USER_GUIDE.md 작성
- [ ] README.md 작성

---

## 11. 참고 자료

### 공식 문서
- Claude Code Sub-agents: https://code.claude.com/docs/en/sub-agents.md
- Claude Code Skills: https://code.claude.com/docs/en/skills.md
- MCP Servers: https://github.com/modelcontextprotocol/servers

### 디자인 레퍼런스
- Mobbin: https://mobbin.com/discover/sites/latest
- Land-book: https://land-book.com
- Lapa Ninja: https://www.lapa.ninja

### 참고 프로젝트
- PPT Team Agent: https://github.com/uxjoseph/ppt_team_agent
- AI PPT: https://github.com/studioKjm/aippt

---

**작성자**: Claude Sonnet 4.5
**최종 수정**: 2025-12-29
**문서 버전**: 1.0

