# CATvisor

AI 기반 고양이 건강 및 행동 모니터링 앱. 컴퓨터 비전으로 급식 세션을 분석하고, 음성 명령으로 돌봄 활동을 기록합니다.

## Tech Stack

| 분류 | 기술 |
|------|------|
| Framework | Next.js (App Router) + TypeScript |
| Styling | Tailwind CSS |
| Auth & DB | Supabase (Auth, PostgreSQL, Storage) |
| AI/Vision | Computer Vision (급식 분석) |
| Deployment | Vercel |
| Package Manager | pnpm |

## 시작하기 (Getting Started)

### 1. 사전 요구사항

- **Node.js** 18 이상
- **pnpm** (`npm install -g pnpm`)
- **Supabase CLI** (`npm install -g supabase`)
- **Git**

### 2. 저장소 클론 및 의존성 설치

```bash
git clone https://github.com/Lemonpine-ai/vibecoding-.git
cd vibecoding-
pnpm install
```

### 3. 환경 변수 설정

프로젝트 루트에 `.env.local` 파일을 생성하고 아래 값을 입력합니다:

```env
NEXT_PUBLIC_SUPABASE_URL=<your-supabase-project-url>
NEXT_PUBLIC_SUPABASE_ANON_KEY=<your-supabase-anon-key>
SUPABASE_SERVICE_ROLE_KEY=<your-supabase-service-role-key>
```

> Supabase 프로젝트의 **Settings > API** 에서 확인할 수 있습니다.
> `SUPABASE_SERVICE_ROLE_KEY`는 서버 전용이므로 절대 클라이언트에 노출하지 마세요.

### 4. Supabase 설정

```bash
# Supabase 로컬 개발 환경 시작
supabase start

# DB 마이그레이션 적용
supabase db push

# TypeScript 타입 생성
pnpm supabase gen types typescript --local > src/types/database.ts
```

### 5. 개발 서버 실행

```bash
pnpm dev
# http://localhost:3000 에서 확인
```

## 주요 명령어

```bash
pnpm install          # 의존성 설치
pnpm dev              # 개발 서버 (http://localhost:3000)
pnpm build            # 프로덕션 빌드
pnpm lint             # ESLint 검사
pnpm format           # Prettier 포맷팅
pnpm typecheck        # TypeScript 타입 체크
```

## 프로젝트 구조

```
src/
  app/                  # Next.js App Router 페이지 및 레이아웃
    (auth)/             # 비인증 페이지 (로그인, 회원가입)
    (dashboard)/        # 인증된 앱 셸
      feeding/          # 급식 세션 분석 페이지
      logs/             # 돌봄 활동 로그 페이지
    api/
      vision/           # 컴퓨터 비전 엔드포인트 (급식 분석)
      voice/            # 음성 명령 처리 엔드포인트
  components/           # 공유 UI 컴포넌트
  lib/
    supabase/           # Supabase 클라이언트 (브라우저 + 서버)
    ai/                 # AI/비전 유틸리티
  hooks/                # 커스텀 React 훅
  types/                # 공유 TypeScript 타입
```

## 아키텍처 핵심 패턴

- **Supabase 클라이언트 분리**: Server Component/Route Handler에서는 `lib/supabase/server.ts`, Client Component에서는 `lib/supabase/client.ts` 사용
- **인증**: Supabase Auth + Next.js 미들웨어(`middleware.ts`)로 `(dashboard)` 라우트 보호
- **AI 파이프라인**: 이미지 → Supabase Storage 업로드 → `/api/vision/analyze` → 결과를 `feeding_sessions` 테이블에 저장
- **음성 명령**: Web Speech API → `/api/voice/command` → 돌봄 로그 기록
- **Server Actions**: 폼 뮤테이션에 사용. Route Handler는 AI/외부 API 호출(스트리밍, 바이너리)에 사용

## 배포

Vercel에 배포됩니다. 환경 변수는 Vercel 대시보드에서 설정합니다.

## 라이선스

MIT
