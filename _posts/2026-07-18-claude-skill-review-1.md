---
title: "[AI] Claude Code 스킬 리뷰 ① - ponytail, Superpowers"
date: 2026-07-18 21:50:00 +0900
categories: [AI, Agent]
tags: [ai, claude-code, skills, ponytail, superpowers]
description: 요즘 많이 쓰는 Claude Code 스킬 두 개를 직접 깔아서 써봤다. 성격이 정반대라 같이 정리.
---

하네스 정리하고 나서 실제로 남들이 많이 쓰는 스킬을 깔아봤다.
이번엔 **ponytail**이랑 **Superpowers**. 하나는 "덜 만들게", 하나는 "제대로 절차를 밟게" 만드는 스킬이라 성격이 거의 반대다.

## 1. ponytail - "코드를 덜 쓰게 만드는 스킬"

### 어떤 스킬인가

에이전트가 **과하게 만드는 것(over-engineering)** 을 막는 스킬.
Claude Code한테 간단한 걸 시켜도 클래스, 설정 파일, 추상화까지 잔뜩 만들어올 때가 있는데 그걸 줄여준다.

- 만든 사람 : DietrichGebert (GitHub)
- 2026년 6월 공개, 7월에 엄청 화제가 됨
- 라이선스 : MIT

### 동작 방식

코드를 쓰기 전에 사다리 타듯이 질문을 하나씩 거친다.

1. 이게 꼭 있어야 하나?
2. 코드베이스에 이미 있나?
3. 표준 라이브러리로 되나?
4. 플랫폼 기본 기능이나 이미 설치된 라이브러리로 되나?
5. 한 줄로 되나?
6. 다 아니면 → **동작하는 최소한만** 작성

대신 **검증, 에러 처리, 보안, 접근성은 줄이지 않는다**는 예외가 있다. 이게 있어서 그나마 믿고 쓸 수 있다.
일부러 대충 만든 부분에는 `ponytail:` 주석으로 한계와 나중에 고칠 방법을 남기게 되어 있다.

### 설치

```text
/plugin marketplace add DietrichGebert/ponytail
/plugin install ponytail@ponytail
```

- 세션 시작할 때 훅으로 규칙을 자동으로 넣어주는 방식이라 **Node가 PATH에 있어야** 한다
- 모드 : `/ponytail lite`, `/ponytail full`(기본), `/ponytail ultra`, 끄기는 `stop ponytail`
- `/ponytail-review` : 변경 사항에 과한 부분이 있는지 검사
- `/ponytail-audit` : 저장소 전체 검사

> SKILL.md만 복사해서 넣으면 모델이 알아서 잘 안 부른다고 한다. **플러그인으로 설치**하는 게 맞다.
{: .prompt-tip }

### 써본 느낌

- 간단한 스크립트나 기능 하나 추가할 때 확실히 결과물이 짧아진다
- 원래 짧게 끝날 작업에서는 별 차이가 없다. 효과는 **큰 기능 만들 때** 크다
- 홍보 문구의 "코드 54% 감소" 같은 숫자는 그대로 믿긴 어렵다. JetBrains에서 직접 테스트한 글을 보면 코드 약 15%, 비용 약 10% 정도 줄었고 품질은 비슷했다고 한다
- review / audit는 **정확성이나 보안은 안 본다**. 리뷰는 따로 해야 한다

## 2. Superpowers - "절차대로 일하게 만드는 스킬 묶음"

### 어떤 스킬인가

스킬 하나가 아니라 **개발 방법론 전체를 스킬로 묶은 패키지**.
아이디어 → 설계 → 계획 → 구현 → 리뷰 → 마무리를 정해진 순서대로 밟게 만든다.

- 만든 사람 : Jesse Vincent (obra)
- Claude Code 외에 Codex, Cursor, Gemini CLI 등에서도 쓸 수 있다

### 흐름

| 단계 | 스킬 | 하는 일 |
| --- | --- | --- |
| 1 | brainstorming | 질문을 주고받으며 아이디어를 다듬고 설계를 조각조각 보여줌 |
| 2 | using-git-worktrees | 작업용 브랜치 공간을 따로 만들고 테스트가 깨끗한지 확인 |
| 3 | writing-plans | 2~5분짜리 작은 작업들로 쪼갠 계획 작성 (파일 경로, 확인 방법 포함) |
| 4 | subagent-driven-development | 작업마다 새 서브에이전트가 구현, 스펙 → 코드 품질 2단계 리뷰 |
| 5 | test-driven-development | 실패하는 테스트 먼저 → 통과 → 리팩터링 |
| 6 | requesting-code-review | 계획 대비 변경 사항 리뷰, 치명적인 문제는 진행 막음 |
| 7 | finishing-a-development-branch | 테스트 통과 확인 후 머지 / PR / 폐기 선택 |

그 외에 **systematic-debugging**(원인 찾는 4단계 디버깅), **verification-before-completion**(끝났다고 하기 전에 진짜 확인) 같은 보조 스킬도 있다.
각 단계가 알아서 다음 단계로 넘어가서 내가 하나하나 부를 필요가 없다.

### 설치

```text
/plugin install superpowers@claude-plugins-official
```

또는

```text
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

설치 후 "What skills are available?" 물어보면 brainstorming, test-driven-development 같은 스킬이 보이면 성공.

### 써본 느낌

- 브레인스토밍 단계에서 질문을 계속 해주는 게 좋았다. 내가 대충 말한 요구사항이 정리된다
- 테스트를 먼저 쓰게 강제하니까 "다 됐어요" 했는데 안 돌아가는 경우가 확실히 줄었다
- 대신 **작은 작업에는 너무 무겁다**. 버튼 색 하나 바꾸는데 브레인스토밍부터 시작하면 답답하다
- 작업을 2~5분 단위로 너무 잘게 쪼개서 흐름이 끊기는 느낌이 들 때도 있다

## 3. 둘 비교

| | ponytail | Superpowers |
| --- | --- | --- |
| 목적 | 덜 만들기 (과설계 방지) | 제대로 만들기 (절차 강제) |
| 형태 | 규칙 + 리뷰 명령어 | 개발 방법론 스킬 묶음 |
| 잘 맞는 작업 | 기능 추가, 스크립트 | 새 기능을 처음부터, 긴 작업 |
| 아쉬운 점 | 정확성 / 보안은 안 봄 | 작은 작업엔 과함 |

> 같이 켜두면 서로 반대 방향으로 당기는 느낌이라, 나는 **작은 작업엔 ponytail, 큰 기능엔 Superpowers**로 나눠서 쓰기로 했다.
{: .prompt-info }

다음엔 문서 / 영상 다루는 스킬들을 리뷰해볼 예정.

## 참고
- [ponytail - GitHub](https://github.com/DietrichGebert/ponytail)
- [Ponytail Skill for Claude Code: Does It Really Cut Tokens (JetBrains)](https://blog.jetbrains.com/ai/2026/07/ponytail-skill-claude-tested/)
- [Superpowers - GitHub](https://github.com/obra/superpowers)