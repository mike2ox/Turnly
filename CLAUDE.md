# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Turnly(대화턴 프로그램)는 대화 세션의 턴을 관리하는 타이머 웹 앱이다. 빌드 시스템이나 패키지 매니저 없이 단일 `index.html` 파일로 구성된다.

## Running the App

```bash
npm run dev      # 개발 서버 (http://localhost:5173), HMR 지원
npm run build    # dist/ 에 정적 파일 빌드
npm run preview  # 빌드 결과 미리보기
```

`index.html`을 브라우저에서 직접 열어도 동작한다 (CDN 의존 유지).

## Architecture

모든 로직이 `index.html` 하나에 담겨 있다.

- **CSS** (`<style>`): CSS 변수 기반 라이트/다크 테마. `prefers-color-scheme` 미디어 쿼리로 자동 전환.
- **SVG 아날로그 시계** (`#analogClock`): 순수 SVG. 60개 틱 마크를 JS로 동적 생성하고, `stroke-dashoffset`으로 남은 시간 arc를 표현.
- **JS** (`<script>`, IIFE): 상태 머신 방식.
  - `phase`: `"idle" | "countdown" | "overtime" | "done"` 네 가지 상태
  - `setInterval(tick, 1000)`으로 1초마다 상태 갱신
  - 상태 변화 → `render()`가 DOM 직접 조작

**외부 의존성**: Tabler Icons (CDN, `@tabler/icons-webfont@3.19.0`)

## Key State Flow

1. 시작 → `countdown` (발화 제한시간 카운트다운 + 전체 시간 감소)
2. 제한시간 소진 → `overtime` (초과 시간 카운트업, "대화 넘기기" 버튼 활성화)
3. 대화 넘기기(`logTurn`) → `countdown` 재설정, 기록 추가
4. 전체 시간 소진 → `done`

## 표준 작업 플로우

개발 작업은 아래 순서로 진행합니다. 각 커맨드는 `/my-<name>` 형식입니다.

| 단계 | 커맨드 | 설명 |
|------|--------|------|
| 1. 기획 | `/my-plan [기능명]` | 기능 아이디어와 설계 방향 정리 (plan 모드) → docs/plan/ 저장 |
| 2. 작업 분해 | `/my-split` | plan → 주니어 친화적 step별 작업 분해 (기본: step별 확인) |
| 3. 커밋 | `/my-commit` | step 완료마다 attribution 없이 커밋 |
| 4. 완료 검사 | `/my-check` | 타입 체크 후 최적화 기회 검토 |
| 5. PR 제목 | `/my-pr` | 브랜치 변경 분석 후 PR 제목 추천 |
| 6. 리뷰 반영 | `/my-review [피드백]` | 코드 리뷰 피드백 분류 및 적용 → docs/review/ 저장 |
| 7. 개선 분해 | `/my-iterate` | 리뷰 피드백 기반 개선 작업을 step별로 분해 |
| 8. 회고 | `/my-retro` | 작업 회고 문서 생성 (피드백 이력 + 코드 변화 포함) |
