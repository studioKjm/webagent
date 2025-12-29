# 랜딩페이지 제작 에이전트 (Landing Page Generation Agent)

## 1. 프로젝트 개요

### 1.1 프로젝트명
랜딩페이지 제작 에이전트

### 1.2 프로젝트 개요
Claude Code의 **서브 에이전트(Sub-agent)**와 **스킬(Skill)** 구조를 활용하여  
사용자가 입력한 **텍스트 요구사항 또는 디자인 레퍼런스**를 기반으로  
수준 높은 디자인의 **랜딩페이지를 자동 생성**하는 개인용 생산성 도구를 구축한다.

- 텍스트로 원하는 웹페이지 스타일을 요청할 수 있음
- Mobbin 등 디자인 아카이브 사이트의 URL을 참고 예시로 제공 가능
- 단일 결과물이 아닌 **여러 버전의 랜딩페이지 생성**을 목표로 함

---

## 2. 서비스 타겟

- 개인 사용 (1인 개발자 / 외주 작업 / 사이드 프로젝트)
- 랜딩페이지 시안이 빠르게 필요한 상황
- 디자인 퀄리티와 코드 품질을 동시에 확보하고 싶은 경우

---

## 3. 서비스 형태

- Claude Code 기반
  - Sub Agent + Skill 조합
- 로컬 개발 환경에서 실행
- 결과물:
  - HTML / CSS / JS
  - (확장 시 React / Next.js 가능)

---

## 4. 핵심 기능

### 4.1 입력 방식

#### 4.1.1 텍스트 기반 요청
- 예:
  - “미니멀한 SaaS 랜딩페이지”
  - “다크모드, 개발자 타겟, 기능 중심”
  - “전환율을 강조한 구조”

#### 4.1.2 디자인 레퍼런스 기반 요청
- 디자인 참고 URL 제공
- 기존 웹사이트 또는 디자인 아카이브 링크
- 스크린샷 또는 레이아웃 설명

---

### 4.2 출력 기능

- 랜딩페이지 자동 생성
  - Hero Section
  - Feature Section
  - CTA Section
  - Footer
- 동일 요구사항 기준 **여러 디자인 버전 생성**
- 유지보수가 쉬운 코드 구조
- 컴포넌트 단위 분리

---

## 5. 아키텍처 개요 (MVP 기준)
User Input
├─ 텍스트 요구사항
├─ 디자인 레퍼런스
↓
Main Orchestrator Agent
├─ 요구사항 정제
├─ 작업 분배
↓
Sub Agents
├─ Layout Design Agent
├─ UI Style Agent
├─ Copywriting Agent
├─ Frontend Code Agent
↓
Skills
├─ Design Skill
├─ Frontend Best Practice Skill
├─ Landing Page Pattern Skill
↓
Landing Page Code Output


---

## 6. 서브 에이전트 구성

### 6.1 Main Orchestrator Agent
- 사용자 입력 분석
- 작업 흐름 제어
- 각 에이전트 결과 병합

---

### 6.2 Layout Design Agent
- 섹션 구조 설계
- 레이아웃 패턴 결정

---

### 6.3 UI Style Agent
- 색상, 타이포그래피, 여백 정의
- 디자인 레퍼런스 해석
- 디자인 토큰 생성

---

### 6.4 Copywriting Agent
- Hero 카피 생성
- Feature 설명 작성
- CTA 문구 생성

---

### 6.5 Frontend Code Agent
- HTML / CSS / JS 코드 생성
- 컴포넌트 분리
- 클린 코드 및 가독성 우선

---

## 7. 스킬(Skill) 구성

### 7.1 Design Skill
- 시각적 위계
- 색상 대비 규칙
- 여백 기준

---

### 7.2 Frontend Best Practice Skill
- 시맨틱 HTML
- 접근성 기본 규칙
- Tailwind CSS 권장 패턴

---

### 7.3 Landing Page Pattern Skill
- 전환 중심 레이아웃 패턴
- Hero → Problem → Solution → CTA 구조

---

## 8. Claude 공식 템플릿 활용 전략

- 공식 템플릿을 초기 구조로 활용
- 프로젝트 목적에 맞게 최소 수정
- 템플릿 의존도를 낮추고 내부 규칙으로 재정의

---

## 9. 설계 및 품질 원칙

- SOLID 원칙 준수
- Agent는 행위, Skill은 규칙
- 책임 분리 명확화
- 유지보수 용이한 구조

---

## 10. MVP 범위

### 포함
- 텍스트 기반 랜딩페이지 생성
- 기본 스타일 2~3종
- 단일 HTML/CSS 출력
- 버전 2~3개 생성

### 제외
- 이미지 자동 생성
- CMS 연동
- 배포 자동화

---

## 11. 주요 기술 목표

1. 동일 요구사항 기준 여러 랜딩페이지 버전 생성
2. 디자인 레퍼런스 해석 정확도 확보
3. 실무에서 바로 수정 가능한 코드 출력
4. 외주 및 사이드 프로젝트 활용 가능 수준 도달

---

## 12. 향후 확장 (Post-MVP)

- React / Next.js 템플릿 지원
- A/B 테스트용 랜딩페이지 생성
- 브랜드 키트 고정 스킬
- 이미지 생성 에이전트 추가

---

## 13. 참고 링크 (References)

- Mobbin – 최신 웹사이트 디자인 아카이브  
  https://mobbin.com/discover/sites/latest

- PPT Team Agent (참고 프로젝트)  
  https://github.com/uxjoseph/ppt_team_agent

- AI PPT 프로젝트 (참고 구현 사례)  
  https://github.com/studioKjm/aippt


