# 디자인 기술 업데이트 계획 (2026-09)

> 작성 배경: 현재 랜딩페이지 생성 시스템(main-orchestrator / layout-designer / ui-stylist / copywriter / frontend-coder + 3개 스킬)의 공식 디자인 지침이 2022-2023년 수준의 "Tailwind 스케일 + CSS 변수 + 바닐라 JS" 웹 표준에 머물러 있음을 확인. 최신 웹디자인 트렌드 및 Claude 자체 내장 스킬(frontend-design 등)과 비교해 격차를 정리하고 업데이트 로드맵을 제시한다.

---

## 1. 현황 진단 요약

| 파일 | 현재 수준 | 핵심 공백 |
|---|---|---|
| `main-orchestrator.md` | 색상/폰트/다크모드만 다른 고정 3버전(Minimal·Bold·Dark) 프리셋 | 레이아웃 구조 자체는 3버전이 사실상 동일. "AI 슬롭" 금지 규칙 없음 |
| `layout-designer.md` | Hero(100vh 고정)-Features-CTA-Footer, 그리드 3종 | Bento grid, 비대칭/오버랩, 스크롤 스토리텔링 없음 |
| `ui-stylist.md` | HEX 기반 50-950 컬러 스케일, `clamp()` 반응형 타입, `prefers-color-scheme` | OKLCH, variable font, glassmorphism/텍스처, 사용자 다크모드 토글 없음 |
| `copywriter.md` | SaaS 카피 공식(헤드라인/CTA), 2021년경 패턴 | 마이크로카피, 개인화 카피 없음 |
| `frontend-coder.md` | 시맨틱 HTML + 바닐라 CSS/JS, IntersectionObserver 애니메이션, 접근성 기본기 우수 | Container Queries, `:has()`, scroll-driven animation, View Transitions API 없음 |
| `landing-page-patterns` 스킬 | Hero-Problem-Solution-CTA, 3초룰 | **문서에 언급된 `conversion-patterns.md`, `layout-templates.md`, `examples/*` 파일이 실제로 존재하지 않음(정합성 버그)** |
| `design-system` 스킬 | `ui-stylist.md`와 내용 90% 중복 | 이원화된 SSOT — 유지보수 리스크 |
| `frontend-best-practices` 스킬 | WCAG AA, Critical CSS, lazy loading | Core Web Vitals의 INP 지표, AVIF, `font-display:optional` 없음 |

**참고**: `SESSION_CONTEXT.md`의 과거 산출물(예: coinsir, lobai 등)에는 이미 글래스모피즘·브루탈리즘이 즉흥적으로 쓰인 흔적이 있음 → 에이전트가 트렌드를 직관적으로는 알지만 **공식 지침에 없어 재현성·일관성이 없는 상태**. 이번 업데이트는 이런 즉흥 적용을 체계적 지침으로 승격하는 작업이기도 하다.

또한 `PRD.md`는 "이미지 자동 생성"을 명시적 스코프 제외로 규정하고 있음 → AI 생성 이미지 관련 항목은 이번 계획에서 "가이드 문서화"까지만 다루고 실제 생성 파이프라인 통합은 별도 승인 필요.

---

## 2. 2025-2026 웹디자인 트렌드 요약 (리서치 기반)

### 디자인 방향
- **"AI 슬롭" 회피가 업계 표준 화두**: purple-to-blue 그라디언트, Inter/Roboto 남용, 카드 3개 나열형 Features, 손대지 않은 shadcn 기본값이 "AI가 만든 티"로 인식됨. 뚜렷한 톤(브루탈/미니멀/맥시멀/에디토리얼 등) 하나를 선택해 끝까지 밀어붙이는 것이 핵심.
- **오가닉·유동적 레이아웃**: 엄격한 그리드에서 곡선·비대칭·오버랩으로 이동.
- **볼드 타이포그래피**: 초대형 헤드라인, weight 100/900 같은 극단 대비, variable font 활용.
- **비비드 컬러 회귀**: Y2K/도파민 디자인 — 고채도·고대비 팔레트가 뮤트톤을 대체.
- **Bento Block 레이아웃**: 콘텐츠를 카드형 블록으로 그룹화해 시선 흐름 유도.
- **3D/WebGL 인터랙션**: 정적 이미지 대신 스크롤 트리거 애니메이션, 인터랙티브 3D 모델(과용 시 성능 리스크 — 선택적 적용).

### 기술(CSS/웹표준)
- **Container Queries**: 사실상 전 브라우저 지원, 즉시 도입 가능.
- **Scroll-driven animations** (`animation-timeline: scroll()/view()`): JS 스크롤 리스너 없이 스크롤 연동 애니메이션. 폴백 자연 열화 → 지금 도입 가능.
- **View Transitions API**: 동일 문서 내 전환은 이미 Baseline. 문서 간 전환은 2026년 확산 중.
- **`:has()` 셀렉터, CSS Nesting, Cascade Layers**: 프로덕션 사용 가능.
- **Anchor Positioning**: 아직 브라우저 지원 불균일 → 폴백 필요, 2026년 말 확대 전망.
- **OKLCH/P3 색공간**: 지각적으로 균일한 컬러 스케일 생성에 유리, 다크모드 색상 왜곡 방지.

### 랜딩페이지/전환 최적화
- 미니멀·집중형 레이아웃(선택지 축소)이 여전히 전환율 우세.
- 모바일 퍼스트(엄지 친화 버튼, 적응형 레이아웃) 필수.
- 신뢰 우선(투명 가격, 검증된 소셜프루프, 실시간성) 요소 강화.
- 의미 있는 모션만 사용 — "노이즈 아닌 의미"가 2026 SaaS 트렌드 키워드.

### Claude 자체 역량
- 내장 `frontend-design` 스킬이 이미 "AI 슬롭 회피" 원칙(폰트/컬러/모션/배경 구체 가이드)을 갖고 있음 — **현재 프로젝트 에이전트들이 이 스킬을 전혀 참조하지 않고 있어 가장 큰 손실 지점.**
- 내장 `theme-factory` 스킬(10개 프리셋 컬러/폰트 테마)은 랜딩페이지엔 직접 안 맞지만 팔레트 영감 소스로 참고 가능.

---

## 3. 업데이트 계획

### Phase 1 — 즉시 수정 (문서 정합성 + 무료로 얻는 개선)
1. `landing-page-patterns` 스킬: 존재하지 않는 참조 파일 문제 해결 — `conversion-patterns.md`, `layout-templates.md`, `examples/*.html`을 실제로 작성하거나, 없는 참조를 SKILL.md에서 제거.
2. `design-system` 스킬과 `ui-stylist.md` 중복 통합: `design-system` 스킬을 색상/타이포/스페이싱 토큰의 SSOT로 지정하고, `ui-stylist.md`는 "이 스킬을 로드해 토큰을 생성"하는 역할로 축소.
3. `main-orchestrator.md`·`ui-stylist.md`·`frontend-coder.md`에 **"AI 슬롭 금지 리스트"** 명문화:
   - 금지 폰트: Inter, Roboto, Open Sans, Lato, Arial, 시스템 기본 폰트
   - 금지 패턴: 흰 배경 위 purple→blue 그라디언트, 손대지 않은 아이콘+제목+2줄 설명 카드 3개 나열, 이유 없는 hover bounce
   - 대신 `frontend-design` 스킬 원칙(극단적 톤 선택, 지배색+포인트 컬러, 폰트 페어링 규칙)을 orchestrator가 각 버전 생성 시 명시적으로 로드하도록 지시문 추가.

### Phase 2 — 컬러/타이포/모션 시스템 현대화 (1주)
1. `ui-stylist.md`/`design-system` 스킬: HEX 스케일 → **OKLCH 기반 컬러 스케일**로 전환(다크모드 대비 왜곡 방지, 더 넓은 색역).
2. **Variable font 가이드 추가**: weight/optical-size 애니메이션 예시, distinctive 폰트 페어링 카탈로그(Playfair Display, Space Grotesk, IBM Plex 등 — 단, `frontend-design` 스킬처럼 "매번 다르게" 규칙도 함께 명시해 특정 폰트로 수렴하는 것 방지).
3. **Glassmorphism/Neumorphism/그라디언트 메시/노이즈 텍스처**를 "선택 가능한 배경 처리 모듈"로 정식 스타일 옵션에 편입(현재는 즉흥 적용 상태).
4. `frontend-coder.md`: 모션 섹션에 scroll-driven animations(`animation-timeline`), View Transitions API(동일 문서), 스프링 이징 기반 마이크로인터랙션 추가. "1회의 잘 조율된 페이지 로드 애니메이션 > 산발적 마이크로인터랙션" 원칙 반영.

### Phase 3 — 레이아웃 다양성 확장 (2주)
1. `layout-designer.md`: Bento grid 패턴, 비대칭/오버랩 레이아웃, 가변 높이 히어로(100vh 고정 탈피), 스크롤 기반 스토리텔링 섹션 템플릿 추가.
2. `main-orchestrator.md`: 고정 3-프리셋(Minimal/Bold/Dark)을 **"톤 매트릭스"**로 재설계 — 매 프로젝트마다 브루탈리즘/에디토리얼/오가닉/럭셔리/플레이풀 등에서 다른 조합을 뽑아 실제로 레이아웃 구조까지 달라지게 함(현재는 색상·폰트만 다르고 구조는 동일).
3. `frontend-coder.md`: Container Queries, `:has()` 셀렉터 도입 → 컴포넌트 단위 반응형으로 전환.

### Phase 4 — 성능/접근성 지표 업데이트 및 QA
1. `frontend-best-practices` 스킬: Core Web Vitals에 **INP(Interaction to Next Paint)** 지표 추가(LCP/CLS만으로는 최신 기준 부족), AVIF 이미지 포맷 우선, `font-display: optional` + variable font subsetting 가이드.
2. 신규 QA 체크리스트 작성: 생성된 3버전에 대해 (a) AI 슬롭 패턴 금지 리스트 위반 여부, (b) Lighthouse/INP 측정, (c) 대비 4.5:1, (d) `prefers-reduced-motion` 대응을 자동/수동 점검.
3. `copywriter.md`: 마이크로카피(빈 상태, 로딩, 에러, 폼 피드백) 가이드 추가.

### 범위 밖 (별도 승인 필요)
- AI 이미지 생성 파이프라인 통합 — `PRD.md`에서 명시적으로 제외된 항목. 필요 시 PRD 개정 먼저 논의.
- 3D/WebGL(Three.js) 본격 도입 — 성능/개발 복잡도 트레이드오프가 커서 "선택적 고급 옵션"으로만 문서화 권장, 기본 3버전에는 비포함.

---

## 4. 우선순위 요약

1. **가장 시급 & 저비용**: Phase 1 (문서 정합성 버그 수정 + AI 슬롭 금지 리스트 + 내장 `frontend-design` 스킬 연계) — 코드 변경 없이 프롬프트/문서 수정만으로 즉시 산출물 품질 상승.
2. **중기**: Phase 2 (컬러/타이포/모션 현대화) — 시각적 "요즘 느낌"에 가장 직접적 영향.
3. **구조적 개선**: Phase 3 (레이아웃 다양성) — 3버전이 진짜로 달라 보이게 만드는 근본 수정.
4. **품질 보증**: Phase 4 — 회귀 방지 및 성능 지표 최신화.

## 5. 진행 현황

### ✅ Phase 1 완료 (2026-09-09)
- `landing-page-patterns` 스킬: 존재하지 않는 참조 파일(`conversion-patterns.md` 등) 안내 제거, 정합성 수정
- `design-system` 스킬: "0. AI 슬롭 방지 원칙" 섹션 신설, SSOT 명시(ui-stylist가 이 스킬의 토큰 구조를 따르도록 지정)
- `ui-stylist.md`: 색상/스페이싱/그림자 토큰 중복 정의 제거하고 `design-system` 스킬 참조로 대체, 폰트 추천 테이블에서 Inter 등 기본 폰트 제외 후 distinctive 폰트로 교체, 상단에 `frontend-design` 내장 스킬 연계 지시 추가
- `main-orchestrator.md`: AI 슬롭 금지 리스트를 품질 기준 체크리스트에 추가, UI Stylist 호출 시 `frontend-design`/`design-system` 스킬 참조를 명시적으로 지시
- `frontend-coder.md`: 코드 생성 전 `frontend-design` 스킬 참고 지시 + "AI 슬롭 금지 체크리스트" 신설, 최종 체크리스트에도 반영

**미해결(다음 단계로 이월)**: `ui-stylist.md`의 "사용 예시" 절과 HTML 보일러플레이트(`frontend-coder.md` 템플릿)에는 여전히 Inter/단색 배경 예시 코드가 구조 예시로 남아있음 — Phase 2(컬러/타이포/모션 현대화)에서 정리 예정.

### ✅ Phase 2 완료 (2026-09-09)
- `design-system` 스킬:
  - "1. 색상 시스템"을 HEX → **OKLCH** 기반 50-950 스케일로 전환(지각적 균일성, P3 색역, 다크모드 색 왜곡 방지), 구형 브라우저용 HEX 폴백 패턴도 함께 명시
  - "9. 다크 모드 지원"을 `color-scheme` 속성 + 시스템 자동 감지 + **사용자 수동 토글**(`data-theme`) 3중 전략으로 고도화
  - "10. 완전한 디자인 토큰 템플릿"을 OKLCH 색상 + distinctive 폰트(Fraunces/IBM Plex 예시)로 갱신
  - 신규 "13. Variable Font 가이드" 섹션: `font-variation-settings` 활용법, 폰트 로딩 전략(`font-display: optional`, preload)
  - 신규 "14. 배경 & 텍스처 모듈" 섹션: Glassmorphism·Gradient Mesh·Noise/Grain·Neumorphism을 실제 CSS 스니펫과 함께 "선택 가능한 모듈"로 정식 편입(Neumorphism은 접근성 위험 경고 포함)
- `ui-stylist.md`: 색상 표를 "OKLCH로 변환해서 사용" 안내로 연결, 표면 처리 지시를 `design-system`의 신규 섹션 14로 연결
- `frontend-coder.md`:
  - 신규 "3.5 모던 모션: Scroll-driven Animations & View Transitions" 섹션 — `animation-timeline: view()`로 JS 스크롤 리스너 없는 애니메이션(열화 안전), View Transitions API로 다크모드 토글 등 상태 전환 예시, 스프링 이징을 핵심 CTA 1-2곳에만 제한 적용하는 지침
  - CSS 디자인 토큰 예시와 HTML 폰트 링크를 OKLCH/distinctive 폰트 예시로 갱신
  - 체크리스트에 "스크롤 애니메이션 CSS 우선 + `prefers-reduced-motion` 확인" 항목 추가

**미해결(다음 단계로 이월)**: `ui-stylist.md`·`frontend-coder.md`의 "사용 예시"/JSON 출력 예시 일부에는 여전히 구버전 HEX·Inter 값이 illustrative 예시로 남아있음 — Phase 3에서 레이아웃 예시와 함께 정리 예정.

### ✅ Phase 3 완료 (2026-09-09)
- `layout-designer.md`:
  - Hero 높이를 "100vh 고정"에서 **콘텐츠 우선(content-first, 80-100vh 가변)**으로 전환
  - 신규 레이아웃 패턴 3종 추가: **D. Bento Grid**, **E. 비대칭/오버랩**, **F. 스크롤 기반 스토리텔링**(CSS `animation-timeline: view()` 연계)
  - 타겟별 추천 표에 신규 패턴 반영 + "프로젝트마다 다른 조합 선택" 경고 추가
  - 체크리스트에서 100vh 강제 규칙 제거, "버전마다 다른 레이아웃 패턴 배정" 항목 추가
  - JSON 출력 스키마에 `layout_type` 옵션 목록(bento/asymmetric-overlap/scroll-storytelling 포함) 명시
- `main-orchestrator.md` — **가장 큰 구조 변경**:
  - 고정 "Minimal & Clean / Bold & Vibrant / Dark & Modern" 3-프리셋 폐기
  - 신규 **"톤 매트릭스"**: 8개 톤(브루탈 미니멀·벤토맥시멀·에디토리얼·오가닉·럭셔리·레트로퓨처리즘·플레이풀·다크 테크니컬) × 색상 방향 × 폰트 방향 × 레이아웃 패턴(layout-designer A~F) × 텍스처 모듈(design-system §14) 매핑 테이블
  - 선택 규칙 4가지 명문화: 레이아웃 패턴 비중복, 직전 프로젝트와 조합 반복 금지, 표 외 톤 자유 추가 허용, 사용자 지정 스타일 고정 규칙
  - Step 1(Layout Designer 호출)이 이제 **버전마다 다른 레이아웃 구조**를 요청하도록 변경(기존: 레이아웃 1개를 3버전이 공유)
  - Step 4-6(Frontend Coder 호출)을 톤별 고유 레이아웃+토큰 사용으로 통합 재작성
  - 실행 흐름 다이어그램, 사용 예시 3종, 폴백 기본값을 모두 톤 매트릭스 기준으로 갱신
- `frontend-coder.md`: 신규 "3.6 컴포넌트 단위 반응형: Container Queries & `:has()`" 섹션 — 뷰포트 기준 미디어쿼리 대신 컨테이너 크기 기준 반응형(Bento grid에 특히 유용), `:has()`로 JS 없이 상태 기반 스타일링

### ✅ Phase 4 완료 (2026-09-09)
- `frontend-best-practices` 스킬:
  - 신규 "Core Web Vitals: INP" 절 — FID를 대체한 지표, 200ms 기준, 개선 방법(스크롤 애니메이션 CSS 우선 등)
  - 이미지 최적화를 **AVIF 우선** → WebP → JPEG 폴백 순서로 갱신
  - 폰트 최적화 절을 variable font + `font-display: optional` + preload 전략으로 갱신(Inter 하드코딩 예시 제거)
  - 신규 "11. 출시 전 QA 체크리스트" — 자동 측정(Lighthouse/INP/CLS/LCP) + 수동 확인(대비/키보드/reduced-motion/뷰포트 3종/AI 슬롭/레이아웃 차별화) 항목을 버전별로 실행하도록 명문화
- `main-orchestrator.md`: 품질 기준·최종 체크리스트에 INP/AVIF/QA 체크리스트 실행 항목 반영
- `frontend-coder.md`: 이미지 예시 AVIF 우선으로, README 템플릿에 INP/CLS 스코어 항목 추가, 하드코딩된 저작권 연도(2025) 제거
- `copywriter.md`: 신규 "마이크로카피 가이드"(로딩/성공/빈 상태/에러/개인화 카피 표) 추가, 하드코딩 연도 플레이스홀더로 수정
- `ui-stylist.md`: 남아있던 "사용 예시" 절의 Inter/HEX 하드코딩을 OKLCH·distinctive 폰트 예시로 정리

**남은 항목(범위 밖, 별도 승인 필요)**: `PRD.md`가 명시적으로 제외한 AI 이미지 자동 생성, 3D/WebGL 본격 도입.

## 6. 실측 검증 (2026-09-09)

Phase 1~4 완료분을 `design-system-update-2026-09` 브랜치에 커밋한 뒤, `main-orchestrator`로 실제 프로젝트("FocusFlow" — AI 회의록 요약 툴)를 생성해 실측했다.

**grep 기반 검증 결과(코드 레벨)**: 통과. OKLCH 사용(111/32/79회), Inter/Roboto/Arial 미검출, purple→blue 그라디언트 미검출, 100vh 하드코딩 미검출, 9개 폰트 모두 상이, `animation-timeline`/`@container`/`:has()`/`grid-template-areas` 등 신규 CSS 기법 실제 코드에 존재.

**사용자 피드백**: "결과물 퀄리티가 예전 버전에 비해 딱히 크게 개선된 느낌은 아닌데"

**브라우저로 직접 렌더링을 확인한 결과, 이 피드백이 정확했다.** 3버전 중 Version 2(브루탈 미니멀)·Version 3(벤토 맥시멀)는 실제로 확연히 달라 보였지만, **Version 1(다크 테크니컬)의 히어로는 README가 약속한 "왼쪽 파형+오른쪽 요약" 비주얼 없이 흔한 "중앙정렬 뱃지+헤드라인+버튼 2개" SaaS 템플릿 그대로 렌더링됐다.** 즉 컨셉 문서와 실제 구현이 불일치했고, 코드 레벨 체크리스트(OKLCH·distinctive 폰트·모던 CSS 사용 여부)는 전부 통과했음에도 **구도 자체가 평범해 시각적으로는 개선이 체감되지 않았다.**

### 근본 원인
Phase 1~4의 QA 체크리스트(`frontend-best-practices` §11 등)가 전부 **텍스트/grep으로 검증 가능한 항목**(폰트명, 색상 함수, CSS 속성 존재 여부)으로만 구성되어 있었고, `frontend-coder`가 README에 적는 "검증 결과"도 **자기 코드를 다시 읽고 요약한 자체 판단**이었지 실제 렌더링을 본 게 아니었다. 브라우저/스크린샷 도구가 애초에 주어지지 않았으므로, README의 "Chrome 실측: 콘솔 에러 0건" 같은 문구도 실제로는 확인 불가능한 상태에서 작성된 것이었다(사실상 근거 없는 자신감 있는 서술).

### ✅ Phase 5 완료 (2026-09-09) — 시각 QA 루프 추가
- `frontend-coder.md`:
  - `skills:` 프론트매터에 `webapp-testing` 추가
  - 신규 "6.5 시각 검수(Visual QA) — 필수" 섹션: 파일 작성 직후 Playwright(정적 HTML이므로 `file://` URL, 서버 불필요)로 데스크톱/모바일 스크린샷 + 콘솔 로그를 실제로 캡처하고, **Read 도구로 이미지를 직접 확인**한 뒤 컨셉-구현 일치 여부·평범한 구도 여부·레이아웃 붕괴·콘솔 에러를 자체 비판적으로 점검. 미달 시 1회 수정 후 재확인(무한 루프 방지를 위해 상한 설정)
  - README의 "검증 결과"/"성능" 섹션은 이 단계에서 **실제로 확인한 내용만** 기록하도록 강제 — 미실측 항목은 "목표치"로만 표기, "확인됨"이라고 쓰지 않도록 명문화
  - 최종 체크리스트에 "시각 검수 실행 여부" 항목 추가
- `main-orchestrator.md`: frontend-coder의 완료 보고에 스크린샷 확인 근거가 없으면 시각 검수를 건너뛴 것으로 간주하고 재지시하도록 명시, 최종 체크리스트에 반영
- `frontend-best-practices` 스킬 §11: 체크리스트를 "도구로 실측 가능(스크린샷/콘솔 기반)" / "Lighthouse 등 별도 도구 필요(없으면 목표치로만 표기)" / "코드 검토로 확인 가능"으로 재분류해, 코드만 읽고 체크할 수 없는 항목(대비, 레이아웃 붕괴, AI 슬롭 여부, 버전 간 차별화)을 명확히 구분

## 7. Version 1 재작업 실측 (2026-09-09)

Phase 5 시각 QA 루프를 실제로 Version 1(다크 테크니컬)에 적용해 히어로를 재작업했다. `frontend-coder`가 스크린샷을 3회 재캡처(초안 → 결함 3개 발견 → 수정 → 재확인)하며 컨셉대로(왼쪽 파형+전사, 가운데 AI 처리 파이프, 오른쪽 요약+액션아이템) 구현했고, **사용자가 직접 브라우저로 재검증** — 폴드 안에 핵심 비주얼이 전부 들어오고 다음 섹션과의 경계도 깨끗함을 확인했다. Phase 5가 실제로 문제를 잡아낸다는 것을 증명한 케이스.

## 8. 경쟁 레퍼런스 조사 및 완성도 평가 (2026-09-10)

v0(Vercel)·Lovable·Bolt.new·Framer AI·Relume과 비교 조사한 결과, 우리 시스템은 **톤 매트릭스 기반 멀티버전 차별화**와 **시각 QA 루프**에서는 독자적 강점이 있으나, (1) v0처럼 컴포넌트 프리미티브가 품질 하한을 구조적으로 보장하지 않고 매번 바닐라 CSS를 처음부터 생성하며, (2) 배포/백엔드 통합이 없고, (3) 생성 비용이 업계 대비 10배 이상(3버전 26분·74만 토큰) 무겁다는 격차를 확인했다. "개인 프로젝트 규모 프롬프트 엔지니어링 시스템으로는 탄탄하나, 프로덕션 제품 대비 프로토타입~베타 초기 단계"로 평가.

이어서 [`ui-craft`](https://github.com/educlopez/ui-craft) 등 AI 에이전트 UI 품질 프레임워크를 조사해 우리 QA 체계가 이진 통과/실패라서 "얼마나" 나쁜지 모른다는 것, unhappy-path(로딩/에러/빈 상태) 우선 설계가 없다는 것, 프로젝트 간 누적되는 디자인 토큰 스파인이 없다는 것을 확인했다.

### ✅ Phase 6 완료 (2026-09-10) — 이진 체크리스트 → 감점제 점수 시스템
- `frontend-coder.md` "6.5 시각 검수": 통과/실패 체크리스트를 **100점 만점 감점제**로 교체 — 폴드 불일치(-20, 이번에 실제로 겪은 버그), 평범한 구도(-15), AI 슬롭 위반 1건당(-15), 레이아웃 패턴 중복(-10), 반응형 붕괴(-10), 의미 없는 모션(-5), 단색 배경(-5), 콘솔 에러(-5), 접근성 결함(-4~-8). A≥90·B≥80·C≥70·D<70 등급. **80점 미만이면 1회 재수정**, 그래도 미달이면 점수·사유를 README에 숨김없이 기록
- `frontend-best-practices` 스킬 §11: QA 체크리스트를 이 감점표 기준 채점으로 재정의
- `main-orchestrator.md`: 80점 미만 버전을 완료로 보고하지 않고, 사용자에게 점수와 감점 사유를 그대로 전달하도록 명시(낙관적 재해석 금지)

**미착수(향후 후보)**: unhappy-path(로딩/에러/빈 상태) 우선 설계, 세션 간 지속되는 3계층 디자인 토큰 스파인(primitive→semantic→component), 니엘센 휴리스티+설계법칙 기반 사업영향 태깅.

### ✅ Phase 7 완료 (2026-09-10) — 신뢰성·비용 개선
- `frontend-coder.md`: 시각 검수 재수정 상한을 고정 1회 → **80점 도달할 때까지 최대 3회**(점수 정체 시 조기 중단)로 가변화. README에 `**시각 검수 점수**: N/100 (등급) — 수정 사이클 n회` **고정 형식** 표기를 강제해 기계적으로 파싱 가능하게 함
- `main-orchestrator.md`: (1) frontend-coder 완료 보고를 텍스트로만 믿지 않고 **Grep으로 README의 "시각 검수 점수" 문자열을 직접 검색**해 미기재 시 반려·재지시하는 기계적 게이트 추가, (2) Step 4-6(Frontend Coder 3버전 호출)을 순차 → **병렬 실행**으로 변경(독립 작업이므로 안전, 소요 시간 단축 목적) — 로컬 리소스 경합 시에만 순차로 폴백
- `frontend-best-practices` 스킬 §11에도 동일하게 반영

**남은 항목(이번엔 보류)**: unhappy-path 우선 설계, 3계층 디자인 토큰 스파인, 니엘센 휴리스틱 기반 사업영향 태깅, 컴포넌트 프리미티브 도입 여부(트레이드오프 있어 별도 논의 필요).

## 10. 다음 액션
Phase 7까지 커밋 완료. `output/design-update-test/`는 테스트 산출물이며 git에 포함하지 않는다 — 사용자 요청으로 삭제하지 않고 유지. 병렬 실행 변경분은 아직 실측 검증 전이므로, 다음 실제 생성 시 소요 시간이 실제로 줄었는지 확인 권장.
