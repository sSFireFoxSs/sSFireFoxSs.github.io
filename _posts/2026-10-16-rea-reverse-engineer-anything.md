---
title: "[AI] REA (Reverse Engineer Anything) - 에이전트로 리버스 엔지니어링하기"
date: 2026-10-16 23:05:00 +0900
categories: [AI, Agent]
tags: [ai, claude-code, mcp, reverse-engineering, rea]
description: 요즘 깃허브에서 난리 난 REA가 뭔지, 어떻게 동작하고 어디까지 되는지 정리했다.
---

이번 주 깃허브 트렌딩이랑 개발자 커뮤니티에서 계속 보이는 게 **REA(Reverse Engineer Anything)**.
"소스코드 없이 앱이 어떻게 동작하는지 에이전트가 알아낸다"는 게 신기해서 찾아보고 정리했다.

## 1. REA가 뭔가

> 코딩 에이전트(Claude Code, Codex 등)에 **리버스 엔지니어링 도구를 MCP로 붙여주는** 오픈소스
{: .prompt-info }

- 만든 사람 : morluto (GitHub)
- 라이선스 : MIT
- 형태 : **MCP 서버 + CLI**, 로컬에서 실행
- 공식 MCP 레지스트리에도 등록되어 있다

원래 리버스 엔지니어링은 Ghidra 같은 도구를 사람이 직접 띄워서 함수 하나하나 따라가야 하는데,
REA는 이걸 **에이전트가 도구로 호출**할 수 있게 만들어준다.
에이전트가 디스어셈블러에 질문하고, 함수랑 참조를 따라가고, 가설을 세워서 바이너리로 확인하는 식이다.

## 2. 무엇을 분석할 수 있나

| 대상 | 사용하는 도구 |
| --- | --- |
| 네이티브 바이너리 | Ghidra, IDA, Hopper (설치되어 있는 것 사용) |
| JavaScript / Electron 앱 | 별도 분석 엔진 없이 가능 |
| .NET 어셈블리 | 별도 분석 엔진 없이 가능 |
| 안드로이드 APK | JADX |
| 웹사이트 | Chrome 계열 브라우저 |
| 펌웨어 | Binwalk, Unblob (리눅스) |

Ghidra 어댑터는 **읽기 전용 작업**만 제공한다고 한다. 분석만 하고 바이너리를 바꾸지는 않는다.

## 3. 설치

```bash
npx rea-agents setup     # 사용할 에이전트(Claude Code 등) 선택 → 변경 내용 확인 후 승인
rea doctor               # 필요한 도구가 다 있는지 점검
```

- Node.js 버전 조건이 있다 (22.19 이상 등)
- Claude Code를 고르면 MCP 서버랑 함께 쓰는 스킬이 `~/.claude/skills`에 들어간다
- 기존 설정은 백업해준다

## 4. 어떻게 쓰나

README 예시처럼 그냥 자연어로 시킨다.

```text
메모 앱에서 검색 기능이 어떻게 동작하는지 알아내고, 근거를 보여준 다음
비슷한 기능을 만들어줘
```

공개된 사례들이 꽤 재밌다.

- 고전 게임 DX-Ball의 사운드 좌우 패닝 함수를 복원 → **원본 x86 결과 3,205개와 전부 일치**
- 윈도우 계산기가 `200 + 10%`에 220을 내는 이유 분석
- 크롬 공룡 게임 속도 규칙(6에서 시작해서 13까지 증가) 찾아내기
- Notion 데스크톱 앱(Electron)의 클립보드 처리 방식 추적

## 5. 생각해볼 점

- **원본 소스코드를 복원해주는 건 아니다.** 동작을 이해하고 비슷하게 다시 만드는 쪽
- "그냥 에이전트한테 Ghidra 설치해서 쓰라고 하면 되는 거 아니냐"는 의견도 있다
- 반대로 게임이나 유료 앱을 베끼거나 결제 기능을 우회하는 데 쓰일 수 있다는 걱정도 많다

> 분석 대상에 대한 **권한이 있는지, 법적으로 문제없는지**는 쓰는 사람이 책임져야 한다. 내 앱이나 공부용 예제로만.
{: .prompt-warning }

## 정리

개인적으로는 "에이전트 + 전문 도구를 MCP로 붙이면 이런 것까지 된다"는 걸 보여주는 사례라 인상적이었다.
하네스 정리할 때 MCP를 외부 서비스 연결 정도로만 생각했는데, **전문가용 도구를 에이전트 손에 쥐여주는 방법**이기도 하다는 걸 느꼈다.

## 참고
- [REA - GitHub](https://github.com/morluto/rea)
- [Reverse Engineer Anything Project Brings AI Agents to Binary Analysis (InfoQ)](https://www.infoq.com/news/2026/10/reverse-engineer-anything/)