---
title: "[AI] Claude Code 스킬 리뷰 ② - 유튜브 자막, 책(PDF/EPUB), skill-creator"
date: 2026-08-24 22:15:00 +0900
categories: [AI, Agent]
tags: [ai, claude-code, skills, youtube, pdf, epub, skill-creator]
description: 공부할 때 쓰기 좋은 스킬 두 개랑, 스킬을 직접 만들 때 쓰는 skill-creator까지 정리.
---

지난번엔 코딩 방식을 바꾸는 스킬을 봤다면, 이번엔 **공부 / 자료 정리에 쓰는 스킬**이랑 **스킬을 만드는 스킬**을 써봤다.
부트캠프 하면서 강의 영상이랑 기술 서적을 정리할 일이 많아서 찾아보게 됐다.

## 1. YouTube 자막 추출 (youtube-transcribe-skill)

### 어떤 스킬인가

유튜브 링크를 주면 **자막을 타임스탬프와 함께 텍스트 파일로 저장**해준다.
영상을 다 보지 않고 내용을 요약하거나, 필요한 부분만 찾아볼 때 좋다.

### 동작 방식

1. **yt-dlp가 있으면** 자막 트랙을 내려받아서 `.vtt` / `.srt`를 깔끔한 텍스트로 정리
2. 로그인 제한이 있으면 브라우저 쿠키를 사용 (기본은 Chrome)
3. **yt-dlp가 없거나 실패하면** 브라우저 자동화로 영상 페이지의 "스크립트 표시"를 열어서 긁어옴
4. 결과는 `영상제목.txt`, 한 줄에 `타임스탬프 + 내용`

### 설치

```bash
npx skills add https://github.com/feiskyer/claude-code-settings --skill youtube-transcribe-skill
```

빠르게 쓰려면 yt-dlp를 먼저 깔아두는 게 좋다.

```bash
pip install yt-dlp
```

### 써본 느낌

- "이 영상 자막 뽑아서 핵심만 정리해줘" 한 줄이면 끝나서 강의 복습할 때 편했다
- 자막이 **아예 없는 영상은 안 된다** (음성 인식을 하는 게 아님)
- 자동 생성 자막은 오타가 많아서 요약은 괜찮은데 그대로 인용하긴 어렵다

## 2. 책을 스킬로 (Book-to-SKILL)

### 어떤 스킬인가

기술 서적 **PDF / EPUB을 Claude Code 스킬로 변환**해준다.
책 한 권을 통째로 넣으면 컨텍스트가 터지는데, 이건 책을 미리 분석해서 필요한 장만 불러 쓰게 만든다.

### 동작 방식

일반 RAG처럼 비슷한 문단을 찾는 게 아니라, **변환할 때 한 번 책을 분석해서** 구조화된 자료를 만들어둔다.

| 생성 파일 | 내용 |
| --- | --- |
| `SKILL.md` | 핵심 개념 + 장 목차 (진입점) |
| `chapters/ch01-*.md` | 장별 요약 (필요할 때만 불러옴) |
| `glossary.md` | 용어 사전 |
| `patterns.md` | 자주 나오는 기법 / 패턴 |
| `cheatsheet.md` | 결정표, 빠른 규칙 |

- 표랑 코드가 많은 기술 서적은 docling으로 파싱 (느리지만 표 / 코드 보존)
- 글 위주 책은 pdftotext 계열로 빠르게
- EPUB도 지원

### 설치 / 사용

Claude Code에 이렇게 말하면 설치된다.

```text
Install book-to-skill: https://raw.githubusercontent.com/virgiliojr94/book-to-skill/master/SKILL.md
```

```text
/book-to-skill ~/Downloads/책이름.pdf     # 변환
/책-이름                                  # 핵심 개념 불러오기
/책-이름 replication                      # 주제 찾아서 설명
/책-이름 ch05                             # 5장 자세히
```

### 써본 느낌

- 공부하던 기술 서적을 넣어봤는데, "이 책 기준으로 이 코드 리뷰해줘"가 되는 게 신기했다
- 변환할 때 시간이랑 토큰이 꽤 든다. 한 번 만들어두고 계속 쓰는 용도
- 여러 책을 넘나들며 검색하는 용도는 아니고 **책 한 권을 깊게** 쓰는 용도

> 내가 산 책 / 합법적으로 가진 파일로만 쓰자.
{: .prompt-warning }

## 3. skill-creator - 스킬을 만드는 스킬

### 어떤 스킬인가

Anthropic이 공식으로 만든 스킬. 남이 만든 스킬을 쓰다 보면 결국 **내 작업에 맞는 스킬**을 만들고 싶어지는데, 그때 쓰는 도구다.
스킬을 만들어주는 것뿐 아니라 **테스트하고 고치는 과정까지** 같이 해준다.

### 흐름

1. 어떤 스킬을 만들지 목적과 범위 정하기
2. 폴더 구조와 `SKILL.md` 초안 작성
3. 실제로 쓸 법한 **테스트 요청** 만들기
4. 스킬을 켜고 테스트 요청 실행
5. 결과를 HTML 뷰어로 보면서 피드백 남기기
6. 지침 / 참고자료 / 스크립트 고치기
7. **description이 제대로 트리거되는지** 확인
8. 다시 테스트

### 설치

```text
/plugin marketplace add anthropics/skills
/plugin install example-skills@anthropic-agent-skills
```

### 써본 느낌

- 제일 도움 된 건 **description 테스트**. 스킬을 만들어놔도 description이 애매하면 Claude가 안 불러서 무용지물인데, "불려야 하는 요청 / 안 불려야 하는 요청"으로 확인해준다
- 그냥 SKILL.md 대충 써서 넣을 때보다 시간은 더 걸리지만 결과물이 확실히 안정적이다
- 이걸로 블로그 글 정리용 스킬을 하나 만들어봤다

## 정리

| 스킬 | 용도 | 한 줄 평 |
| --- | --- | --- |
| youtube-transcribe-skill | 영상 자막 → 텍스트 | 강의 복습용으로 편함, 자막 없는 영상은 불가 |
| Book-to-SKILL | 책 → 스킬 | 책 한 권 깊게 쓸 때 좋음, 변환 비용 있음 |
| skill-creator | 스킬 만들기 / 테스트 | 내 스킬 만들 거면 필수 |

## 참고
- [youtube-transcribe-skill (feiskyer/claude-code-settings)](https://github.com/feiskyer/claude-code-settings)
- [Book-to-SKILL 소개 - 파이토치 한국 사용자 모임](https://discuss.pytorch.kr/t/book-to-skill-pdf-epub-claude-code/10094)
- [anthropics/skills - GitHub](https://github.com/anthropics/skills)