# CLAUDE.md

## Project Overview

**SnapCal** — 음식 사진을 찍으면 AI가 칼로리를 분석해주는 크로스플랫폼 모바일 앱.

## Tech Stack

- **Framework**: React Native (Expo SDK 52+)
- **Language**: TypeScript
- **Styling**: NativeWind (Tailwind for RN)
- **Navigation**: Expo Router (file-based routing)
- **AI/Vision**: Google Gemini API (이미지 분석 및 칼로리 추정)
- **Local DB**: expo-sqlite (SQLite)
- **Camera**: expo-camera, expo-image-picker
- **Package Manager**: pnpm

## Commands

```bash
pnpm install              # 의존성 설치
pnpm start                # Expo dev server
pnpm android              # Android 실행
pnpm ios                  # iOS 실행
pnpm lint                 # ESLint
pnpm typecheck            # tsc --noEmit
```

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

## Design Constraints

- **색상**: 무채색(white, gray, black) 기반 + 포인트 색상 **#FF6B35** (오렌지) 1가지만 사용
- **로그인 없음**: 인증 기능 미구현. 앱 실행 즉시 사용 가능
- **로컬 전용**: 모든 데이터(분석 기록, 사진)는 기기 내 SQLite + FileSystem에만 저장
- **서버 없음**: AI 분석은 클라이언트에서 직접 Gemini API 호출

## Core Flow

1. 사용자가 홈 탭에서 음식 사진 촬영 또는 갤러리 선택
2. 이미지를 Base64 인코딩 → Gemini Vision API로 전송
3. AI가 음식 종류, 예상 중량, 칼로리를 JSON으로 반환
4. 결과를 SQLite에 저장하고 결과 화면에 표시
5. 기록 탭에서 날짜별 분석 히스토리 조회

## Key Rules

- `expo-sqlite`는 동기 API 사용 (`useSQLiteDatabase` 훅)
- Gemini API 키는 `.env`에 `EXPO_PUBLIC_GEMINI_API_KEY`로 저장
- 이미지는 `FileSystem.documentDirectory`에 저장, DB에는 경로만 기록
- 컴포넌트는 반드시 `'use client'` 없이 React Native 표준 방식으로 작성
- 에러 시 사용자에게 토스트로 안내 (무한 로딩 금지)
