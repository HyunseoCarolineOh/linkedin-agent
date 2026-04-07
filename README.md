# LinkedIn Agent PRD

> Claude Code 작업을 소재로 LinkedIn 게시글을 자동 생성하는 글로벌 스킬

## 1. Overview

### 배경

ceo-staff 테크트리의 **TechEquity AI 트랙**에 "실리콘밸리 네트워크 구축" 마일스톤이 있다.
달성 조건: startup/VC/tech 분야 10+명 커넥션. LinkedIn은 이 네트워크를 만드는 핵심 채널이다.

### 문제

Claude Code로 매일 의미 있는 작업을 하지만, 이를 LinkedIn 콘텐츠로 전환하는 데 시간과 에너지가 든다.
작업 직후가 콘텐츠 소재가 가장 생생한 시점인데, 이 타이밍을 놓치면 게시글 작성 동기가 사라진다.

### 솔루션

Claude Code 세션에서 `/linkedin` 슬래시 커맨드로 현재 작업을 기반으로 LinkedIn 게시글 초안을 즉시 생성한다.
어떤 프로젝트/폴더에서든 사용 가능한 **글로벌 스킬**(`~/.claude/commands/linkedin.md`)로 구현한다.

---

## 2. Goals & Success Metrics

| 목표           | 지표                  | 타겟           |
| -------------- | --------------------- | -------------- |
| 게시 습관 형성 | 주간 LinkedIn 게시 수 | 2+회/주        |
| 네트워크 확장  | 월간 새 커넥션 수     | 10+명/월       |
| 도달 범위      | 게시글 평균 노출 수   | 500+ views     |
| 전환           | 게시글당 댓글/DM      | 2+ engagements |

---

## 3. User Flow

```
[Claude Code 작업 중 — 어떤 프로젝트/폴더든]
        │
        ▼
  사용자: /linkedin 입력
        │
        ▼
  스킬이 현재 세션 컨텍스트 분석:
  - git diff / git log (최근 작업 내용)
  - 현재 프로젝트 구조
  - 대화 히스토리 (무엇을 했는지)
        │
        ▼
  톤 자동 선택 + 게시글 초안 생성
  (영어, 해시태그 포함)
        │
        ▼
  사용자 리뷰 & 수정 요청
        │
        ▼
  [MVP] 최종본을 클립보드 복사 or 파일 저장
  [Phase 2] LinkedIn API로 자동 발행
```

---

## 4. Architecture

### 글로벌 스킬 구조

**파일 위치**: `~/.claude/commands/linkedin.md`

이 파일은 Claude Code의 글로벌 slash command로, 어떤 디렉토리에서든 `/linkedin`으로 호출 가능하다.

### 스킬이 활용하는 컨텍스트

| 소스                                     | 용도                          |
| ---------------------------------------- | ----------------------------- |
| `git diff` / `git log --oneline -10` | 최근 작업 내용 파악           |
| 현재 대화 히스토리                       | 무엇을 했고 왜 했는지         |
| 프로젝트 README / CLAUDE.md              | 프로젝트 맥락                 |
| `$ARGUMENTS` (선택)                    | 사용자가 강조하고 싶은 포인트 |

### 출력 형식

```markdown
---
## LinkedIn Post Draft

[게시글 본문 — 영어]

---
**Hashtags:** #tag1 #tag2 ...
**Tone:** [선택된 톤]
**Word count:** [단어 수]
---
```

### Phase 2 추가 구성요소

```
~/.claude/
├── commands/
│   └── linkedin.md          # 글로벌 스킬 (MVP)
└── linkedin-agent/
    ├── config.json           # LinkedIn API credentials
    ├── templates/            # 커스텀 톤 템플릿
    └── history/              # 발행 이력 로그
```

---

## 5. Content Strategy

### 타겟 오디언스

- 실리콘밸리 소프트웨어 엔지니어 / AI 엔지니어
- 스타트업 창업자 / VC
- 스탠포드 관련 네트워크 (교수, 졸업생)
- 헬스케어 + IT 관련자
- AI/Dev Tools 커뮤니티

### 3가지 톤 템플릿

스킬이 작업 내용을 분석하여 가장 적합한 톤을 자동 선택한다.

#### A. Provocative Hook (도발적 훅)

- **적합한 상황**: 패러다임 전환, 새로운 접근법 발견
- **구조**: 도발적 오프닝 → old way vs new way → 핵심 인사이트 → CTA
- **예시 오프닝**: "If you're still doing X manually, you're mass-producing technical debt."
- **해시태그**: 7-8개, 넓은 범위 (#AI #FutureOfWork #DeveloperProductivity)

#### B. Practical Tips (실용 팁 공유)

- **적합한 상황**: 워크플로우 개선, 도구 설정, 구체적 방법론
- **구조**: 번호 리스트 → 각 항목 설명 → 실전 팁 → follow-up 유도
- **예시 오프닝**: "3 things I changed in my dev workflow this week that saved hours:"
- **해시태그**: 5개, 구체적 (#ClaudeCode #DevWorkflow #BuildInPublic)

#### C. Discovery Share (발견 공유)

- **적합한 상황**: 숨겨진 기능 발견, 디버깅 과정, 실험 결과
- **구조**: 대화체 오프닝 → 발견 과정 → 결과/임팩트 → 한계점 솔직 공유
- **예시 오프닝**: "I've been experimenting with X and found something interesting..."
- **해시태그**: 0-3개, 최소한 (또는 없음)

### 해시태그 풀

**Core (항상 고려)**: #AI #ClaudeCode #BuildInPublic
**Domain**: #HealthcareIT #DevTools #AgenticAI #AIAgents
**Reach**: #FutureOfWork #TechCareers #SiliconValley #StartupLife
**Specific**: #TypeScript #NextJS #Supabase (프로젝트에 따라)

### 콘텐츠 가이드라인

- 단어 수: 150-300 words (LinkedIn 알고리즘 최적)
- 첫 2줄이 "...더 보기" 클릭을 결정 — 훅에 집중
- 개인 경험 기반 서술 (I did / I found / I built)
- 지나친 자기 과시 금지 — 배움과 과정 중심
- 이모지 절제 (톤 B에서만 번호 대용으로 허용)

---

## 6. Phases

### Phase 1: MVP — 초안 생성 스킬

**범위**: `/linkedin` 커맨드 → 게시글 초안 텍스트 출력

**구현 내용**:

- `~/.claude/commands/linkedin.md` 스킬 파일 작성
- git diff/log 기반 작업 내용 분석
- 톤 자동 선택 로직
- 영어 게시글 초안 생성
- 클립보드 복사 안내

**완료 조건**: 아무 프로젝트에서나 `/linkedin` 실행 시 게시글 초안이 생성됨

### Phase 2: LinkedIn API 연동

**범위**: 초안 승인 후 자동 발행

**구현 내용**:

- LinkedIn Developer App 등록 & OAuth 2.0 설정
- `~/.claude/linkedin-agent/config.json` 에 credentials 저장
- 발행 전 사용자 확인 프롬프트
- 발행 이력 로깅

**완료 조건**: `/linkedin` → 초안 확인 → "발행" 선택 시 LinkedIn에 게시됨

### Phase 3: 고도화

**범위**: 스마트 기능 추가

**구현 내용**:

- 이미지/스크린샷 첨부 지원
- 발행 최적 시간대 추천 (US Pacific timezone 기준)
- 이전 게시글 성과 기반 톤/해시태그 최적화
- 시리즈 게시글 (thread) 지원

---

## 7. LinkedIn API Setup Guide (Phase 2 대비)

### 사전 준비

1. **LinkedIn Developer App 생성**

   - https://www.linkedin.com/developers/apps 에서 앱 생성
   - Products에서 **"Share on LinkedIn"** 과 **"Sign In with LinkedIn using OpenID Connect"** 추가
2. **필요한 OAuth Scopes**

   - `openid` — 인증
   - `profile` — 사용자 프로필 읽기
   - `w_member_social` — 게시글 작성
3. **OAuth 2.0 Flow**

   - Authorization Code Flow 사용
   - Redirect URI: `http://localhost:3000/callback` (로컬 서버)
   - Access Token 발급 후 `config.json`에 저장
   - Token 만료 시 자동 갱신 로직 필요 (60일 유효)
4. **게시글 발행 API**

   - Endpoint: `POST https://api.linkedin.com/v2/ugcPosts`
   - Content-Type: `application/json`
   - Author: `urn:li:person:{USER_ID}`

### config.json 구조

```json
{
  "client_id": "...",
  "client_secret": "...",
  "access_token": "...",
  "refresh_token": "...",
  "token_expires_at": "2026-06-07T00:00:00Z",
  "user_id": "..."
}
```

---

## 8. Constraints & Risks

| 항목              | 설명                                    | 대응                                                  |
| ----------------- | --------------------------------------- | ----------------------------------------------------- |
| LinkedIn API 제한 | 일일 게시 수 제한 존재                  | 하루 1-2개로 제한                                     |
| OAuth 토큰 만료   | Access token 60일 유효                  | 자동 갱신 로직, 만료 알림                             |
| 콘텐츠 품질       | AI 생성 티가 나면 역효과                | 개인 경험 기반 서술, 사용자 수정 단계 필수            |
| 개인정보 노출     | git diff에 민감 정보 포함 가능          | 스킬에서 .env, credentials 등 자동 필터링             |
| 톤 부적절         | 자동 선택된 톤이 맥락에 안 맞을 수 있음 | 사용자가 톤 오버라이드 가능:`/linkedin --tone=tips` |
| 과도한 게시       | 너무 자주 올리면 스팸 인식              | 주 3-4회 상한 권장, 이력 기반 경고                    |

---

## References

- **테크트리**: `c:\Projects\ceo-staff\docs\tech-tree-nodearch-V2.md` (TechEquity AI 트랙)
- **라이프 목표**: `c:\Projects\ceo-staff\docs\life-goals.md` (Stanford networking 섹션)
- **LinkedIn Post 예시 (톤 참고)**:
  - [Sai — Provocative Hook](https://www.linkedin.com/posts/saidurgaprasadbattula_ai-coding-claude-share-7445701991823175680-gg26)
  - [Sohee — Practical Tips](https://www.linkedin.com/posts/designersoheekim_aiagents-claudecode-agentworkflow-share-7445938809357201409-4sM0)
  - [Nate — Discovery Share](https://www.linkedin.com/posts/nateherkelman_ive-been-poking-around-with-some-hidden-ugcPost-7447279591964106754-HI-8)
