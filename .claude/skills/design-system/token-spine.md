# 디자인 토큰 스파인 (Token Spine)

프로젝트마다 매번 처음부터 색상/폰트를 새로 발명하지 않도록, **실제로 만들어서 시각 검수를 통과한 조합**을 여기 누적합니다. `ui-stylist`는 새 프로젝트를 시작하기 전에 이 파일을 먼저 읽고, 톤이 비슷한 항목이 있으면 **그대로 복제하지 말고 참고해서 변형**하세요(완전히 새로 만드는 것도 여전히 허용됩니다 — 이 파일은 출발점이지 강제 목록이 아닙니다).

## 3계층 구조

- **Primitive**: 원시 값 (특정 HEX/OKLCH 색상, 특정 폰트 이름)
- **Semantic**: 그 값이 맡는 역할 (지배색/포인트색/배경/텍스트, Display/Body/Mono)
- **Component**: 실제 컴포넌트에 적용된 방식 (버튼 배경, 카드 표면, 히어로 배경 등)

각 항목은 실제 생성물에서 검증된 값만 기록합니다(추측 금지). **색상은 원본 기록이 HEX인 경우에도, 실제 사용 시에는 `design-system` §1의 OKLCH 변환 절차를 거쳐 적용하세요** — 이 스파인은 "무엇이 통했는지"의 기록이지, HEX를 그대로 써도 된다는 뜻이 아닙니다.

---

## 톤: 다크 테크니컬 (예: "미션 컨트롤 터미널")

- **레이아웃 패턴**: F. 스크롤 스토리텔링 / C. 그리드 기반
- **Primitive — 색상**: 배경 `oklch(15-17% 0.012 205)`(그래파이트), 지배 액센트 민트 `oklch(78% 0.155 168)`, 포인트 앰버 `oklch(78% 0.165 62)`
- **Primitive — 폰트**: Display `Martian Mono`(가변), Body `IBM Plex Sans`, Mono `JetBrains Mono`
- **Semantic**: 다크 온리(`color-scheme: dark`), 무채색 계열 + 지배 1색 + 포인트 1색
- **Component**: Glassmorphism(카드) + 그리드 패턴 + 그레인 텍스처 3중 레이어. CTA는 민트, 미결/경고 상태만 앰버
- **적합한 타겟**: 개발자, 기술 제품, 원격 협업 툴

## 톤: 브루탈 미니멀 (예: "활자 조판 문서")

- **레이아웃 패턴**: A. 단일 컬럼 (좌측 정렬, 컨테이너 이탈 오프셋)
- **Primitive — 색상**: 배경 off-white(종이톤), 잉크(near-black) 텍스트, 포인트 버밀리언 1색 (`#C9452C` 계열 — HEX 원본, OKLCH 변환 후 사용)
- **Primitive — 폰트**: Display `Archivo`(가변, wdth 극단 대비) 또는 `Fraunces`, Body `Newsreader`/`Public Sans`(세리프 또는 정제된 산세리프), Mono `Sometype Mono`/`JetBrains Mono`
- **Semantic**: 곡률 0(`--radius-*: 0`), 그라디언트/블러 0, 그림자는 하드 오프셋만
- **Component**: 넘버링 리스트, 1px 룰 라인, 물리적으로 눌리는 버튼(하드 섀도우 이동)
- **적합한 타겟**: B2B SaaS, 전문성/신뢰 강조, 에디토리얼

## 톤: 벤토/맥시멀

- **레이아웃 패턴**: D. Bento Grid (`grid-template-areas`, 7블록 혼합 크기)
- **Primitive — 색상**: 배경 크림 페이퍼, 지배 퍼시몬 + 액센트 4색(시트러스/제이드/울트라마린/마젠타) — 어두운 면은 2개 이하로 제한
- **Primitive — 폰트**: Display `Bricolage Grotesque`(가변, 압축), Body `Instrument Sans`/`Hanken Grotesk`, Mono `DM Mono`/`Fragment Mono`
- **Semantic**: 5색 리소 팔레트, 밝은 블록:어두운 블록 = 5:2 명도 리듬
- **Component**: 블록마다 개별 노이즈 레이어, 전면 그레인 텍스처(`feTurbulence` + `mix-blend-mode: multiply`)
- **적합한 타겟**: 기능이 많은 제품, 대시보드형 소개, 생동감 필요한 소비자 제품
- **⚠️ 한글 주의**: `Bricolage Grotesque` 등 영문 전용 가변 폰트는 한글 글리프가 없어 그대로 두면 시스템 기본 고딕으로 전량 폴백됨 — 반드시 `Gowun Batang`/`IBM Plex Sans KR` 등 한글 폴백을 별도 `:root` 블록에 추가할 것(2026-09-10 실측에서 실제로 발견된 결함)

## 톤: 에디토리얼 ("Ink & Vermilion")

- **레이아웃 패턴**: E. 비대칭/오버랩
- **Primitive — 색상**: 배경 페이퍼톤(`--c-paper-*`), 텍스트 잉크(`--c-ink-*`), 포인트 버밀리언(`--c-verm-500: #C9452C`)
- **Primitive — 폰트**: Display `Fraunces`(세리프), Body `Public Sans`, Mono `JetBrains Mono`
- **Semantic**: 뉴트럴 + 포인트 컬러 1개, 다크 패널을 액센트로 삽입(제품 미리보기 등)
- **Component**: 왼쪽 텍스트 / 오른쪽 다크 터미널풍 제품 미리보기 패널(비대칭 히어로)
- **적합한 타겟**: 브랜드 스토리, 크리에이티브, 전문 서비스

## 톤: 플레이풀 ("Sticker Studio")

- **레이아웃 패턴**: A. 단일 컬럼 스토리텔링
- **Primitive — 색상**: 배경 네이비/라벤더 톤(`--c-navy-*`), 지배 마젠타/핑크(`--c-mag-500: #E92C8D`), 형광 하이라이트(그린 계열)
- **Primitive — 폰트**: Display `Cabinet Grotesk`, Body `Plus Jakarta Sans`, Mono `DM Mono`
- **Semantic**: 파스텔+비비드 혼합, 라운드 코너, 알약형(pill) 배지/버튼
- **Component**: 헤드라인 부분에 형광 언더라인/하이라이트, 컬러풀한 통계 뱃지(원형)
- **적합한 타겟**: 소비자 앱, 젊은 타겟, B2C

## 톤: 오가닉 ("Moss & Oat")

- **레이아웃 패턴**: B. 번갈아 나타나는 2컬럼 (지그재그)
- **Primitive — 색상**: 배경 오트밀톤(`--c-oat-*`), 지배 모스그린(`--c-moss-500: #5B8B67`), 포인트 클레이(`--c-clay-700`)
- **Primitive — 폰트**: Display `Bricolage Grotesque` + 한글 폴백 `Gowun Batang`, Body `Hanken Grotesk` + 한글 폴백 `IBM Plex Sans KR`, Mono `Fragment Mono`
- **Semantic**: 저채도 어스톤, 부드러운 톤
- **Component**: 원형 아바타, 하단 통계 밴드(숫자 강조)
- **적합한 타겟**: 헬스케어, 라이프스타일, 차분한 톤이 필요한 B2B

---

## 갱신 규칙

- 새 프로젝트에서 **표에 없는 새로운 톤 조합**을 만들었고 시각 검수 80점 이상을 받았다면, 완료 후 이 파일에 같은 형식으로 항목을 추가하세요.
- 기존 항목을 재사용할 때는 **색상 하나 정도는 바꿔서 완전 동일한 반복은 피하세요** (design-system §0 "AI 슬롭 방지 원칙" — 매번 같은 조합 수렴 금지).
- 항목이 12개를 넘으면 가장 낮은 점수를 받았거나 가장 오래된 것부터 정리해 파일이 무한정 커지지 않게 하세요.
