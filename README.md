# 🥤 ZeroPick — Frontend

> **성분을 알면 바뀌는 선택.**
> 제로·저당 식품의 실제 성분을 분석하고, 취향과 알레르기에 맞춰 추천·비교해주는 웹 서비스
> 2026-1 ECC 프로젝트 2팀

- 🔗 배포: https://ecc2team.github.io/frontend/
- ⚙️ Backend: [ecc2team/Backend](https://github.com/ecc2team/Backend) · [API 문서(Swagger)](https://backend-jdau.onrender.com/swagger-ui/index.html)
- 🧪 Data: [ecc2team/Data](https://github.com/ecc2team/Data)

<br>

## 🧩 기획 배경

'제로', '무설탕'이라고 적혀 있어도 제품마다 성분 구성이 다릅니다. 설탕 대신 말티톨 같은 대체 감미료가 들어가기도 하고, 소비자는 원재료명만 보고 각 성분이 무엇인지 바로 알기 어렵습니다.
ZeroPick은 **문구가 아니라 실제 성분을 기준으로** 나에게 맞는 제품을 고를 수 있게 돕습니다.

<br>

## 📌 주요 기능

| 기능 | 설명 |
| --- | --- |
| 검색 · 카테고리 | 키워드 검색, 카테고리별 BEST 5, 추천순·최신순·가나다순·인기순·조회순 정렬 |
| 상품 상세 | ZeroPick 점수, 영양 정보, 성분 분석, 성분 클릭 시 상세 설명 팝업 |
| 비교함 | 같은 카테고리 상품 최대 3개 비교, 열량·당류 등은 막대 그래프로 시각화 |
| 맞춤 추천 | 선호 카테고리·비선호 성분·알레르기 정보 기반 추천 |
| 랭킹 | 카테고리별 인기 상품 순위 |
| 섭취 기록 | 권장 섭취량 대비 오늘의 섭취 현황, 기록 삭제 |
| 회원 | 이메일 인증, 이메일·닉네임 중복 확인, 카카오·구글 로그인, 온보딩(추가 정보·취향 설정) |
| 기타 | 최근 조회 상품, 프로필 관리, 반응형 UI(모바일 햄버거 메뉴) |

<br>

## 🛠 기술 스택

| 분류 | 기술 |
| --- | --- |
| Frontend | React, Vite, JavaScript, React Router |
| Mock API | MSW (Mock Service Worker) |
| Lint / Format | ESLint, Prettier |
| Deploy | GitHub Pages, GitHub Actions |
| Backend (팀) | Spring Boot 3, PostgreSQL(Supabase), Flyway, JWT, OAuth2(Kakao·Google), Mailgun, Render |

<br>

## 🏗 서비스 구조

```
[공공데이터 수집·정제·AI 성분 표준화 (Python)]
                    │
                    ▼
[React SPA · GitHub Pages] ──REST/JSON──▶ [Spring Boot 3 · Render] ──▶ [PostgreSQL · Supabase]
     Access Token: Authorization 헤더          Refresh Token: HttpOnly Cookie
```

<br>

## 💡 프론트엔드 구현 포인트

### 1. MSW로 백엔드와 병렬 개발
초기에는 API가 완성되지 않아 MSW로 Mock API를 먼저 만들었습니다. 검색, 상품 상세, 성분 분석 화면을 백엔드와 동시에 개발하고, API가 완성되면 실제 요청으로 교체했습니다.

### 2. 검색·정렬 상태와 API 요청 연결
정렬은 프론트에서 배열을 직접 정렬하지 않고 query parameter로 서버에 넘겼습니다. 카테고리 페이지는 BEST 5와 전체 목록 요청을 분리해, **정렬만 바뀔 때는 전체 목록만 다시 요청**하도록 불필요한 호출을 줄였습니다.

```jsx
useEffect(() => { fetchBestProducts(selectedCode); }, [selectedCode]);
useEffect(() => { fetchCategoryProducts(selectedCode, page, sort); }, [selectedCode, page, sort]);
```

### 3. 회원가입 다단계 상태 관리
이메일 인증과 닉네임 중복 확인 상태를 따로 관리했습니다. 확인 후 값을 다시 수정하면 기존 확인 결과를 초기화해, 바뀐 값으로 다시 확인하게 했습니다.

### 4. 소셜 로그인 분기
OAuth callback의 인증 코드를 백엔드에 전달하고, 응답의 `isNewUser` 값으로 신규 사용자는 온보딩, 기존 사용자는 메인으로 이동시켰습니다.

### 5. GitHub Pages 배포
하위 경로(`/frontend/`)에서 실행되므로 Vite `base`와 React Router `basename`을 맞추고, URL로 직접 접근해도 라우팅이 유지되도록 SPA fallback을 적용했습니다. API 주소와 OAuth Redirect URI는 환경변수로 분리했습니다.

<br>

## 👥 팀원 및 역할

| 이름 | 역할 | 담당 |
| --- | --- | --- |
| 이시은 | Frontend | UI/UX 설계, 프론트엔드 전체 개발, API 연동, 배포 |
| 김미리 | Backend · Data | 데이터 전처리, 로그인·회원 관리, 카테고리, 추천 API |
| 김아인 | Backend | 상품 조회, 성분 사전, 섭취 기록, 비교함, 최근 조회 API |

<br>

## 🚀 실행 방법

```bash
git clone https://github.com/ecc2team/frontend.git
cd frontend
npm install
cp .env.example .env   # API 주소, OAuth Redirect URI 입력
npm run dev
```

<br>

## 📈 회고

- 공동구매 기능은 이번에 완성하지 못해 다음에 보완할 계획
- 더 다양한 제품 데이터 추가
