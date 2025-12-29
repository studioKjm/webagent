# 허브 랜딩페이지 사용 가이드

## 🎉 축하합니다!

3가지 버전의 **랜딩페이지 허브**에 **프로젝트 추가 기능**과 **이미지 업로드 기능**이 성공적으로 추가되었습니다!

---

## ✨ 추가된 기능

모든 3가지 버전 (Minimal & Clean, Bold & Vibrant, Glassmorphism)에 동일한 기능이 추가되었습니다:

### 1. **프로젝트 추가 기능** ✅
- "새 프로젝트 추가" 플레이스홀더 카드 클릭
- 모달 폼이 열림 (각 버전의 디자인 스타일에 맞게)
- 프로젝트 정보 입력 (제목, 설명, 카테고리, 이미지, 버전들)

### 2. **이미지 업로드 기능** 🖼️
- **방법 1**: 이미지 URL 입력 (예: https://example.com/image.jpg)
- **방법 2**: 로컬 파일 업로드 (컴퓨터에서 이미지 선택)
- 실시간 미리보기 지원

### 3. **다중 버전 관리** 📋
- 각 프로젝트에 여러 디자인 버전 추가 가능
- "버전 추가" 버튼으로 무제한 추가
- × 버튼으로 버전 제거 (최소 1개 필수)

### 4. **프로젝트 삭제 기능** 🗑️
- 각 프로젝트 카드 우측 상단의 × 버튼 클릭
- 확인 메시지 후 삭제

### 5. **자동 통계 업데이트** 📊
- 총 프로젝트 수 자동 계산
- 총 디자인 버전 수 자동 계산
- 최근 업데이트 날짜 자동 갱신

### 6. **데이터 영구 저장** 💾
- LocalStorage에 자동 저장
- 브라우저를 닫아도 데이터 유지
- 페이지 새로고침 시 자동 로드

---

## 📖 사용 방법

### **1단계: 새 프로젝트 추가하기**

1. 허브 페이지에서 **"새 프로젝트 추가"** 카드를 클릭합니다
2. 모달 창이 열립니다

### **2단계: 프로젝트 정보 입력**

#### 필수 정보:
- **프로젝트 제목** *
  ```
  예: AI 챗봇 서비스
  ```

- **설명** *
  ```
  예: 고객 상담을 자동화하는 AI 기반 챗봇 솔루션
  ```

- **카테고리** *
  ```
  예: Tech/AI, Business/SaaS, E-commerce 등
  ```

#### 선택 정보:
- **프로젝트 이미지**

### **3단계: 이미지 추가 (선택)**

**방법 1: 이미지 URL 사용**
```
1. "이미지 URL" 입력란에 이미지 URL 붙여넣기
   예: https://images.unsplash.com/photo-...
2. Enter 또는 포커스 이동 시 자동 미리보기
```

**방법 2: 로컬 파일 업로드**
```
1. "파일 선택" 버튼 클릭
2. 컴퓨터에서 이미지 선택 (JPG, PNG, WebP 등)
3. 자동으로 base64로 인코딩되어 저장
4. 미리보기 즉시 표시
```

**추천 이미지 크기**: 1200x600px (2:1 비율)

**무료 이미지 소스**:
- [Unsplash](https://unsplash.com) - 고품질 무료 이미지
- [Pexels](https://www.pexels.com) - 무료 스톡 사진
- [Lorem Picsum](https://picsum.photos/) - 랜덤 플레이스홀더 이미지
  - 예: `https://picsum.photos/1200/600` (1200x600 랜덤 이미지)

### **4단계: 디자인 버전 추가**

최소 1개의 버전이 필요합니다.

**첫 번째 버전** (기본으로 1개 제공):
```
버전 이름: Dark & Modern
URL: https://example.com/version-1/index.html
```

**추가 버전 추가하기**:
```
1. "+ 버전 추가" 버튼 클릭
2. 버전 이름 입력 (예: Bold & Vibrant)
3. URL 입력 (해당 버전의 링크)
4. 필요한 만큼 반복
```

**버전 제거하기**:
```
- 각 버전 우측의 × 버튼 클릭
- 단, 최소 1개는 유지해야 함
```

### **5단계: 프로젝트 추가 완료**

1. "프로젝트 추가" 버튼 클릭
2. 모달이 닫히고 새 프로젝트 카드가 자동으로 생성됨
3. 통계가 자동으로 업데이트됨

---

## 🎨 3가지 버전별 모달 스타일

각 버전마다 디자인에 맞는 모달 스타일이 적용됩니다:

### **Version 1: Minimal & Clean**
- 깔끔한 흰색 배경
- 부드러운 그림자
- 미묘한 그라디언트 버튼
- 우아한 애니메이션

### **Version 2: Bold & Vibrant**
- 두꺼운 검은 테두리 (6px)
- 하드 브루탈리스트 섀도우
- 폭발적인 그라디언트 헤더
- 강렬한 색상의 버튼
- 회전 애니메이션 (X 버튼)

### **Version 3: Glassmorphism**
- 반투명 frosted glass 효과
- backdrop-filter: blur(40px)
- 그라디언트 타이틀
- 프리미엄 hover 효과
- 부드러운 scale 애니메이션

---

## 💡 예시: 프로젝트 추가 실습

### 예시 1: E-commerce 프로젝트

```yaml
프로젝트 제목: 프리미엄 패션 스토어
설명: 최신 트렌드의 패션 아이템을 판매하는 온라인 쇼핑몰
카테고리: E-commerce/Fashion
이미지 URL: https://picsum.photos/seed/fashion/1200/600
버전 1: Minimal Clean / https://example.com/fashion-v1
버전 2: Bold Trendy / https://example.com/fashion-v2
```

### 예시 2: SaaS 제품

```yaml
프로젝트 제목: TeamSync - 협업 도구
설명: 원격 팀을 위한 올인원 프로젝트 관리 및 커뮤니케이션 플랫폼
카테고리: Business/SaaS
이미지 URL: https://picsum.photos/seed/saas/1200/600
버전 1: Professional / https://example.com/teamsync-pro
버전 2: Modern / https://example.com/teamsync-modern
버전 3: Minimal / https://example.com/teamsync-minimal
```

### 예시 3: 로컬 이미지 사용

```yaml
프로젝트 제목: 포트폴리오 웹사이트
설명: 크리에이티브 디자이너를 위한 미니멀 포트폴리오
카테고리: Portfolio/Design
이미지: (파일 선택 버튼으로 컴퓨터에서 업로드)
버전 1: Light / https://mysite.com/portfolio-light
버전 2: Dark / https://mysite.com/portfolio-dark
```

---

## 🗑️ 프로젝트 삭제하기

### 방법:
1. 삭제하려는 프로젝트 카드로 마우스 이동
2. 우측 상단의 **× 버튼** 클릭
3. 확인 메시지에서 "확인" 클릭
4. 프로젝트가 즉시 삭제되고 통계 업데이트

**주의**: 삭제된 프로젝트는 복구할 수 없습니다!

---

## 🔧 고급 기능

### **LocalStorage 데이터 관리**

모든 프로젝트는 브라우저의 LocalStorage에 `lp_hub_projects` 키로 저장됩니다.

#### 데이터 확인 (개발자 도구):
```javascript
// 브라우저 콘솔에서 실행
console.log(localStorage.getItem('lp_hub_projects'));
```

#### 데이터 내보내기 (백업):
```javascript
// 브라우저 콘솔에서 실행
const data = localStorage.getItem('lp_hub_projects');
console.log(data);
// 복사 후 텍스트 파일로 저장
```

#### 데이터 가져오기 (복원):
```javascript
// 백업한 데이터를 복사 후
const backupData = '...'; // 여기에 백업 데이터 붙여넣기
localStorage.setItem('lp_hub_projects', backupData);
location.reload(); // 페이지 새로고침
```

#### 모든 데이터 초기화:
```javascript
// 브라우저 콘솔에서 실행 (주의!)
localStorage.removeItem('lp_hub_projects');
location.reload();
```

---

## 🚀 팁 & 트릭

### 1. **이미지 최적화**
- 웹용 이미지는 1200x600px 권장
- 파일 크기 500KB 이하 권장
- WebP 포맷 사용 시 더 작은 파일 크기

### 2. **버전 URL 구성**
```
같은 도메인: ./version-1/index.html
다른 도메인: https://example.com/landing
상대 경로: ../ai-trends-2026/version-1/index.html
```

### 3. **빠른 이미지 URL 얻기**
- Unsplash에서 이미지 선택 → 우클릭 → "이미지 주소 복사"
- Lorem Picsum: `https://picsum.photos/1200/600` (즉시 사용 가능)
- Placekitten: `https://placekitten.com/1200/600` (고양이 이미지)

### 4. **카테고리 일관성 유지**
같은 형식으로 입력하세요:
```
✅ Tech/AI
✅ Business/SaaS
✅ E-commerce/Fashion

❌ tech ai (대소문자, 슬래시 없음)
❌ Business-SaaS (하이픈 대신 슬래시)
```

### 5. **ESC 키로 모달 닫기**
- 모달이 열려 있을 때 `ESC` 키 누르기
- 또는 모달 바깥 영역 클릭

---

## 🐛 문제 해결

### Q: 프로젝트가 추가되지 않아요
**A**: 필수 항목(*표시)이 모두 입력되었는지 확인하세요.
- 프로젝트 제목
- 설명
- 카테고리
- 최소 1개의 버전 (이름 + URL)

### Q: 이미지가 표시되지 않아요
**A**:
1. 이미지 URL이 https://로 시작하는지 확인
2. URL이 실제 이미지 파일을 가리키는지 확인 (.jpg, .png 등)
3. CORS 문제가 없는지 확인 (일부 웹사이트는 외부 이미지 링크 차단)
4. 로컬 파일 업로드를 대신 사용해보세요

### Q: 페이지 새로고침 후 프로젝트가 사라졌어요
**A**:
1. LocalStorage가 활성화되어 있는지 확인
2. 시크릿 모드에서는 데이터가 저장되지 않을 수 있음
3. 브라우저 설정에서 "쿠키 및 사이트 데이터" 삭제하지 않았는지 확인

### Q: AI Trends 2026 프로젝트를 삭제할 수 없어요
**A**:
- AI Trends 프로젝트는 HTML에 하드코딩되어 있어 삭제 불가
- 새로 추가한 프로젝트만 삭제 가능

---

## 📊 데이터 구조

프로젝트는 다음과 같은 JSON 구조로 저장됩니다:

```json
{
  "id": 1704067200000,
  "title": "AI 챗봇 서비스",
  "description": "고객 상담을 자동화하는 AI 기반 챗봇 솔루션",
  "category": "Tech/AI",
  "image": "https://picsum.photos/1200/600",
  "date": "2025-12-29",
  "status": "Live",
  "versions": [
    {
      "name": "Dark & Modern",
      "url": "https://example.com/version-1",
      "target": "사용자 지정"
    },
    {
      "name": "Minimal & Clean",
      "url": "https://example.com/version-2",
      "target": "사용자 지정"
    }
  ]
}
```

---

## 🎯 다음 단계

### 1. **첫 프로젝트 추가해보기** (5분)
- 테스트용 프로젝트 1개 추가
- Lorem Picsum 이미지 사용
- 2-3개 버전 추가

### 2. **실제 프로젝트 추가** (10분)
- 실제 작업 중인 랜딩페이지 프로젝트 추가
- 적절한 이미지 선택
- 실제 URL 링크

### 3. **버전 비교**
- 3가지 허브 버전 모두 확인
- 가장 마음에 드는 스타일 선택
- 해당 버전을 메인으로 사용

---

## 🌐 배포하기

허브 페이지는 단일 HTML 파일이므로 어디든 쉽게 배포 가능합니다:

### **Netlify Drop**
```
1. https://app.netlify.com/drop 방문
2. index.html 파일 드래그 & 드롭
3. 즉시 공개 URL 생성
```

### **Vercel**
```bash
vercel deploy
```

### **GitHub Pages**
```
1. GitHub 저장소 생성
2. index.html 업로드
3. Settings → Pages → Deploy from main branch
```

### **Surge**
```bash
npm install -g surge
surge
```

---

## 📝 요약

### ✅ 가능한 기능
- ✅ 새 프로젝트 추가
- ✅ 이미지 업로드 (URL 또는 파일)
- ✅ 다중 버전 관리
- ✅ 프로젝트 삭제
- ✅ 자동 통계 업데이트
- ✅ 데이터 영구 저장 (LocalStorage)
- ✅ 모바일 반응형
- ✅ ESC 키로 모달 닫기

### 🎨 3가지 버전
- Version 1: Minimal & Clean (전문적)
- Version 2: Bold & Vibrant (에너지 넘침)
- Version 3: Glassmorphism (프리미엄)

---

**생성일**: 2025-12-29
**버전**: 2.0 (프로젝트 추가 기능 포함)
**작성**: Claude Sonnet 4.5

🎉 이제 랜딩페이지 허브를 마음껏 활용하세요!
