# Composer.md — SnapCal 프로젝트 가이드 (통합본)

## 역할·목표 (gemini.md 반영)

- **역할**: 시니어 풀스택 개발자 + UX에 익숙한 구현자로서 동작합니다.
- **제품명**: **SnapCal** — 음식 사진을 찍으면 AI가 칼로리를 분석해 주는 크로스플랫폼 모바일 앱.
- **핵심 가치**: 극단적 단순함, 빠른 피드백, **프라이버시(로컬 우선)**.


## Tech Stack (CLAUDE.md 기준 — 단일 진실)

| 영역 | 선택 |
|------|------|
| **Framework** | React Native (Expo SDK 52+) |
| **Language** | TypeScript |
| **Styling** | NativeWind (Tailwind for RN) |
| **Navigation** | Expo Router (file-based routing) |
| **AI/Vision** | Google Gemini API (이미지 분석·칼로리 추정) |
| **Local DB** | expo-sqlite (SQLite) |
| **Camera** | expo-camera, expo-image-picker |
| **Package Manager** | pnpm |

---

## Commands

```bash
pnpm install              # 의존성 설치
pnpm start                # Expo dev server
pnpm android              # Android 실행
pnpm ios                  # iOS 실행
pnpm lint                 # ESLint
pnpm typecheck            # tsc --noEmit
```

---

## Architecture

```
src/
  app/                    # Expo Router 파일 기반 라우팅
    (tabs)/               # 탭 네비게이션 그룹
      index.tsx           # 홈 (카메라/촬영)
      history.tsx         # 분석 기록 목록
    result/[id].tsx       # 분석 결과 상세
  components/             # 공용 UI 컴포넌트
  lib/
    db.ts                 # SQLite 초기화 및 CRUD
    vision.ts             # Gemini API 호출 (이미지→칼로리)
  hooks/                  # 커스텀 React 훅
  types/                  # 공용 타입 정의
  constants/              # 색상, 설정값
```

---

## Design Constraints (통합)

- **색상 (프로젝트 표준)**: 무채색(white, gray, black) 기반 + 포인트 **#FF6B35** (오렌지) **1가지만** 사용. (`gemini.md`의 #FF5722 등은 본 저장소에서는 채택하지 않음.)
- **스타일 방향 (gemini.md 정렬)**: 미니멀·고대비 그레이스케일 느낌을 유지하고, 가독성 좋은 산세리프 계열 타이포그래피를 지향합니다.
- **로그인 없음**: 인증 미구현. 앱 실행 즉시 사용.
- **로컬 전용**: 분석 기록·사진 경로는 기기 내 SQLite + FileSystem.
- **서버 없음(백엔드 앱 없음)**: AI 분석은 클라이언트에서 Gemini API로 직접 호출(네트워크는 AI 호출에만 사용).

---

## Core Flow (한 줄 요약 + 단계)

**앱 열기 → 촬영/선택 → 분석 → 결과 표시 → 기록 조회.**

1. 홈 탭에서 음식 사진 촬영 또는 갤러리 선택  
2. 이미지 Base64 인코딩 → Gemini Vision API 전송  
3. AI가 음식 종류, 예상 중량, 칼로리를 JSON으로 반환  
4. 결과를 SQLite에 저장하고 결과 화면에 표시  
5. 기록 탭에서 날짜별 히스토리 조회  

(gemini.md의 “오렌지 셔터 한 번” 흐름과 동일한 UX 의도로 정렬.)

---

## Key Rules (구현 필수)

- `expo-sqlite`는 동기 API 사용 (`useSQLiteDatabase` 훅).
- Gemini API 키는 `.env`의 `EXPO_PUBLIC_GEMINI_API_KEY`.
- 이미지는 `FileSystem.documentDirectory`에 저장, DB에는 **경로만** 기록.
- 컴포넌트는 `'use client'` 없이 React Native 표준 방식.
- 에러 시 토스트로 안내하고 **무한 로딩 금지**.

---

## Definition of Done (gemini.md 형식으로 보강)

- 음식 사진을 분석해 **대략적인 칼로리**를 화면에 표시할 수 있다.
- 분석 결과를 **로컬 DB에 저장**할 수 있다(기록·경로 정책은 위 Key Rules 준수).
- UI는 **원 포인트 컬러(#FF6B35)** 규칙을 지킨다.
- 실패·취소 시 사용자가 상태를 이해할 수 있게 피드백한다(토스트 등).

---

## 구현 로드맵 참고 (스택은 Expo 기준으로 재해석)

| 단계 | 내용 |
|------|------|
| **1** | 프로젝트 스캐폴딩·테마(그레이스케일 + 오렌지 포인트) |
| **2** | SQLite 스키마·CRUD·이미지 저장 경로 (`lib/db.ts` 등) |
| **3** | 카메라·갤러리 연동 (`vision.ts`와 연결) |
| **4** | 홈·히스토리·결과 상세 UI 마감 |

---

---

*마지막 업데이트: CLAUDE.md + gemini.md 비교 통합.*
