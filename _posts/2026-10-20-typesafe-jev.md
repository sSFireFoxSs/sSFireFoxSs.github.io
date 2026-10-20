---
title: "[AI] TypeSafe Jev - 대화 안 하는 AI 모델과 Claude Code에서 쓰기"
date: 2026-10-20 22:40:00 +0900
categories: [AI, LLM]
tags: [ai, llm, jev, typesafe, claude-code, mcp]
description: 채팅 대신 타입이 정해진 판단만 돌려주는 Jev가 뭔지, 에이전트에 어떻게 붙이는지 정리했다.
---

요즘 LLM은 다 "대화"를 하는데, 반대로 **대화를 아예 안 하는 모델**이 나와서 화제다.
TypeSafe의 **Jev**. 처음엔 이게 왜 필요하지 싶었는데, 에이전트랑 같이 쓰는 걸 보고 이해가 됐다.

## 1. Jev가 뭔가

> 질문에 **글이 아니라 타입이 정해진 답(선택지, 점수, 확률)** 만 돌려주는 "System One" 모델
{: .prompt-info }

- 만든 곳 : TypeSafe AI (샌프란시스코)
- 공개 : 2026년 9월 15일 (현재는 대기자 명단으로 접근)
- 설명이나 코드를 생성하지 않는다. **분류, 라우팅, 승인, 에스컬레이션** 같은 판단만 한다

"System One"은 사람이 깊게 생각하지 않고 바로 판단하는 빠른 사고를 말한다.
Claude 같은 모델이 천천히 생각하는 System 2라면, Jev는 빠르게 판단만 하는 쪽이다.

## 2. 왜 필요한가

에이전트로 일을 시키다 보면 중간중간 **판단만 하면 되는 단계**가 많다.

- 이 테스트 실패가 진짜 버그인가, 그냥 불안정한 테스트인가?
- 이 문의는 환불 / 배송 / 기타 중 어디인가?
- 이 로그는 심각한가?

이걸 매번 큰 모델한테 글로 물어보면 느리고 비싸고, 답 형식도 들쭉날쭉하다.
Jev는 여기에 특화되어 있다.

| 특징 | 내용 |
| --- | --- |
| 타입 보장 | 정해진 스키마대로만 답한다 (없는 필드를 지어내지 않음) |
| 확률 제공 | 각 판단에 확신도가 붙어서, 높으면 자동 처리 / 낮으면 사람에게 넘기기 가능 |
| 속도 | 응답 70ms ~ 500ms 수준이라고 함 |
| 가격 | 입력 100만 토큰당 $0.042, 출력은 무료 |

> "환각 없음"은 **형식을 지킨다**는 뜻이지 답이 항상 맞다는 뜻은 아니다. 공개 시점엔 독립적인 평가도 없었다.
{: .prompt-warning }

## 3. Claude Code에서 쓰기 (jev-code)

jev-code는 Jev를 Claude Code, Codex 같은 에이전트의 **도구(MCP)** 로 붙여주는 오픈소스다. TypeSafe 공식은 아니다.

### 설치

```bash
export TYPESAFE_API_KEY=ts_...        # console.typesafe.ai 에서 발급
npx -y @francoischastel/jev-code setup claude
npx -y @francoischastel/jev-code doctor --live   # 확인 (선택)
```

또는 플러그인으로

```text
claude plugin marketplace add FrancoisChastel/jev-code
claude plugin install jev-code@jev-code
```

### 제공하는 도구

| 도구 | 하는 일 |
| --- | --- |
| `jev_classify` | 여러 항목을 내가 정한 클래스 중 하나로 분류 |
| `jev_check` | 하나의 근거에 대해 예 / 아니오 판단 |
| `jev_score` | 여러 항목을 하나의 척도(심각도, 우선순위)로 점수화 |
| `jev_rank` | 질문과 관련 있는 후보 순서대로 정렬 |
| `jev_ask` | 여러 종류 질문을 한 번에 |

### 예시

```text
지난 CI에서 실패한 테스트들 중에 불안정한 테스트랑 진짜 버그를 나눠줘
```

Claude Code가 jev 스킬을 불러서 `jev_classify`에 실패 로그와 `infrastructure`, `assertion_bug` 같은 클래스를 넘기고,
결과마다 라벨, 확률, `auto` / `review` 판단을 받는다. `auto`는 바로 처리하고 `review`만 사람이 보면 된다.

## 정리

| | Claude 같은 모델 | Jev |
| --- | --- | --- |
| 출력 | 글, 코드 | 선택지, 점수, 확률 |
| 강점 | 생각, 설명, 생성 | 빠르고 싼 판단 |
| 역할 | 일을 계획하고 수행 | 중간 판단 담당 |

큰 모델 하나로 다 하는 게 아니라, **생각은 큰 모델이, 반복되는 판단은 작고 빠른 모델이** 나눠 맡는 구조가 앞으로 많아질 것 같다.
부트캠프 프로젝트에서도 문의 분류 같은 단계에 써볼 만하다는 생각이 들었다.

## 참고
- [TypeSafe Jev: the frontier model built to kill chat (StartupHub)](https://www.startuphub.ai/news/typesafe-jev-model-kills-chat)
- [jev-code - GitHub](https://github.com/FrancoisChastel/jev-code)