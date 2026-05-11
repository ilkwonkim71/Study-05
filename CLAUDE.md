# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

**AI 공감 다이어리** — 오늘 있었던 일을 한 줄로 쓰면 AI가 감정을 분석하고 공감 · 위로 메시지를 생성하는 일기 앱.

완전한 단일 파일(`index.html`) 구성. Node.js 서버 없음. 브라우저에서 파일을 직접 열어 실행.

## 실행

```bash
# 브라우저에서 직접 열기
start index.html          # Windows
open index.html           # macOS
```

서버 불필요. `index.html`을 더블클릭하거나 브라우저에 드래그 앤 드롭해도 동작.

## Architecture

`index.html` 하나에 HTML · CSS · JS 전부 포함.

- **API 호출**: 브라우저 → OpenRouter API (`https://openrouter.ai/api/v1/chat/completions`) 직접 호출. 서버 없음.
- **모델**: `anthropic/claude-sonnet-4-6`
- **API 키 저장**: `localStorage` (`aidiaryv1_key`). 최초 실행 시 설정 모달이 자동 표시됨.
- **일기 기록**: `localStorage` (`aidiaryv1_history`), 최대 60건.

## Key JS Objects

| 객체 | 역할 |
|------|------|
| `API` | OpenRouter 호출, JSON 파싱 (마크다운 코드펜스 자동 제거) |
| `App` | 분석 흐름 제어 (analyze / retry / reset / loading / error state) |
| `History` | localStorage 읽기·쓰기·렌더링·삭제 |
| `UI` | 설정 모달 열기·닫기·저장 |

## Environment

`.env`에 `OPENROUTER_API_KEY`가 있어도 정적 HTML은 읽을 수 없음. 앱 첫 실행 시 UI에서 키를 직접 입력 → localStorage에 저장.

## LLM Prompt

`API.call()`의 프롬프트는 JSON 형식을 강제함:
```
{ emotion, emotion_emoji, empathy_message, comfort_message, tomorrow_hope }
```
모델 응답에 마크다운 코드펜스가 포함될 경우 `parseJSON()`이 자동으로 제거.
