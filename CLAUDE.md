# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Turnly(대화턴 프로그램)는 대화 세션의 턴을 관리하는 타이머 웹 앱이다. 빌드 시스템이나 패키지 매니저 없이 단일 `index.html` 파일로 구성된다.

## Running the App

별도 서버 없이 `index.html`을 브라우저에서 직접 열면 된다.

```bash
open index.html
```

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
