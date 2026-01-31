---
layout: post
title: "Swarm Orchestration Skill - AI 에이전트 조율 기술"
date: 2026-01-31
categories: [Technical]
toc: true
---

# Claude Code 스웜 오케스트레이션 기술 (Swarm Orchestration Skill)

Claude Code의 **TeammateTool과 Task 시스템**을 사용해 **다중 에이전트 협업을 완전히 마스터**하기 위한 가이드입니다.

여러 에이전트를 조율하거나, 병렬 코드리뷰를 수행하거나, 의존관계 파이프라인 워크플로우를 만들거나, 자체 조직화 작업 큐를 구축하는 등 **분할 정복 패턴이 필요한 모든 상황에 사용됩니다**.

---

## 원시(Primitives)

| 개념 | 의미 | 파일 위치 |
|------|------|------------|
| **에이전트(Agent)** | 도구를 사용할 수 있는 Claude 인스턴스. 당신 자신도 에이전트입니다. 서브에이전트는 spawn된 에이전트입니다. | 해당 없음 (프로세스) |
| **팀(Team)** | 함께 작업하는 이름있는 에이전트 그룹. 한 리더 + 여러 동료 | `~/.claude/teams/{name}/config.json` |
| **동료(Teammate)** | 팀에 참여한 에이전트. 이름, 색상, 인박스를 갖습니다. `team_name` + `name`으로 Task를 통해 생성됩니다. | 팀 설정 |
| **리더(Leader)** | 팀을 만든 에이전트. 동료 메시지를 받고 계획/종료를 승인합니다. | 설정 첫 항목 |
| **작업(Task)** | 주제, 설명, 상태, 소유자, 의존성을 가진 작업 단위 | `~/.claude/tasks/{team}/N.json` |
| **인박스(Inbox)** | 에이전트가 다른 동료들로부터 메시지를 받는 JSON 파일 | `~/.claude/teams/{name}/inboxes/{agent}.json` |
| **메시지(Message)** | 에이전트 간 주고받는 JSON 객체 (text 또는 구조화된 형식) | 인박스 파일 |
| **백엔드(Backend)** | 동료(Teammate) 실행 방법. 자동 감지됨: `in-process`, `tmux`, `iterm2` | 환경 기반 감지 |  [oai_citation:1‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

### 연결 구조

```
 TEAM
 ┌─────────────────────┐
 │       리더(Leader)  │
 │         │           │
 │─── 메시지 via 인박스 ──┐
 │         │             │
 │  동료1  동료2 …      │
 └─────────────────────┘
```

동료와 리더는 인박스 JSON 파일로 서로 메시지를 주고받습니다.  [oai_citation:2‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

## 목차

1. 코어 아키텍처  
2. 에이전트 생성 방식  
3. 기본 내장 에이전트 타입  
4. 플러그인 에이전트 타입  
5. TeammateTool 동작  
6. Task 시스템 통합  
7. 메시지 포맷  
8. 오케스트레이션 패턴  
9. 환경 변수  
10. 백엔드(Spawn Backends)  
11. 에러 처리  
12. 전체 워크플로우 예시  [oai_citation:3‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

## 1. 코어 아키텍처

### 스웜(Swarm)이 작동하는 방식

스웜은 다음으로 구성됩니다:

* **리더(Leader)** – 팀 생성, 작업 생성, 동료 스폰, 전체 조율  
* **동료(Teammates)** – 작업 실행 및 리더에게 보고  
* **작업 목록(Task List)** – 상태/의존성이 있는 공유 작업 큐  
* **인박스(Inboxes)** – 동료 간 메시징용 JSON 저장소  [oai_citation:4‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

### 파일 구조 예시

```
~/.claude/teams/{team-name}/
├── config.json              # 팀 메타데이터 및 멤버 목록
└── inboxes/
    ├── team-lead.json       # 리더 인박스
    ├── worker-1.json        # 워커1 인박스
    └── worker-2.json        # 워커2 인박스

~/.claude/tasks/{team-name}/
├── 1.json                   # Task #1
├── 2.json                   # Task #2
└── 3.json                   # Task #3
```

### config.json 구조 예시

```json
{
  "name": "my-project",
  "description": "기능 X 작업",
  "leadAgentId": "team-lead@my-project",
  "createdAt": 1706000000000,
  "members": [
    {
      "agentId": "team-lead@my-project",
      "name": "team-lead",
      "agentType": "team-lead",
      "color": "#4A90D9",
      "joinedAt": 1706000000000,
      "backendType": "in-process"
    },
    {
      "agentId": "worker-1@my-project",
      "name": "worker-1",
      "agentType": "Explore",
      "model": "haiku",
      "prompt": "코드베이스 구조 분석...",
      "color": "#D94A4A",
      "planModeRequired": false,
      "joinedAt": 1706000001000,
      "tmuxPaneId": "in-process",
      "cwd": "/Users/me/project",
      "backendType": "in-process"
    }
  ]
}
```

---

## 2. 에이전트 생성 방식 (두 가지)

### ① Task Tool (서브 에이전트)

단기적/포커스된 작업에 사용합니다.

```js
Task({
  subagent_type: "Explore",
  description: "인증 파일 찾기",
  prompt: "코드베이스 내 인증 관련 모든 파일 찾기",
  model: "haiku"  // 선택: haiku, sonnet, opus
})
```

특징:

* 동기 또는 비동기(`run_in_background: true`)
* 결과를 바로 반환
* **팀 참여 없음**
* 빠른 검색/분석/연구에 적합  [oai_citation:5‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

### ② Task + team_name + name (동료 생성)

동료로 영구 참여시킵니다.

```js
// 1) 팀 생성
Teammate({ operation: "spawnTeam", team_name: "my-project" })

// 2) 동료 스폰
Task({
  team_name: "my-project",
  name: "security-reviewer",
  subagent_type: "security-sentinel",
  prompt: "인증 코드 취약점 점검 및 결과 리더에게 전송.",
  run_in_background: true
})
```

특징:

* 팀에 참여 (`config.json`에 기록)
* 인박스 메시지로 통신
* 공유 Task 목록에서 작업 Claim 가능
* 종료될 때까지 실행  [oai_citation:6‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

### 주요 차이점

| 항목 | Task (서브) | Task + team + name |
|------|-------------|--------------------|
| 생명주기 | 작업 종료 시까지 | 종료 요청 시까지 |
| 통신 | 반환 값 | 인박스 메시지 |
| 작업 접근 | 없음 | 공유 작업 목록 |
| 팀 멤버십 | ❌ | ✅ |
| 조율 | ❌ | ✅ |  [oai_citation:7‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

## 3. 기본 내장 에이전트 타입

### Bash

```js
Task({
  subagent_type: "Bash",
  description: "Git 명령 실행",
  prompt: "git 상태 확인과 최신 커밋 보기"
})
```

* Bash 도구만 사용
* 모델: 부모 상속
* Git/시스템 명령에 적합  [oai_citation:8‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

### Explore

```js
Task({
  subagent_type: "Explore",
  description: "API 엔드포인트 찾기",
  prompt: "코드베이스 내 API 엔드포인트 모두 찾기",
  model: "haiku"
})
```

* 읽기 전용 도구만 사용
* 빠르고 저렴
* 코드 탐색/파일 검색용  [oai_citation:9‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

### Plan

```js
Task({
  subagent_type: "Plan",
  description: "OAuth2 인증 설계",
  prompt: "OAuth2 인증 구현 계획"
})
```

* 설계/전략 중심
* 읽기 전용 도구 사용  [oai_citation:10‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

### general-purpose

* 모든 도구 사용 가능
* 복합 작업 수행에 적합  [oai_citation:11‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

### claude-code-guide

* Claude Code 관련 질문/도움  [oai_citation:12‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

## 4. 플러그인 에이전트 타입

예: `compound-engineering` 플러그인으로 제공되는 리뷰/연구/설계/워크플로우 특화 에이전트들

```js
Task({
  subagent_type: "compound-engineering:review:security-sentinel",
  description: "보안 감사",
  prompt: "취약점 집중 리뷰"
})
```

* 보안, 성능, 코드 심플리시티, 아키텍처 리뷰 등 다양한 특화 에이전트 포함  [oai_citation:13‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea)

---

## 5. TeammateTool 기본 동작

### spawnTeam (팀 생성)

```js
Teammate({
  operation: "spawnTeam",
  team_name: "feature-auth",
  description: "OAuth2 인증 구현"
})
```

이후:

* 팀 디렉터리 + config.json 생성
* Shared task 폴더 생성
* **자신이 리더가 됨**  [oai_citation:14‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea)

---

### discoverTeams (참여 가능한 팀 조회)

```js
Teammate({ operation: "discoverTeams" })
```

* 참여 가능 팀 목록 반환  [oai_citation:15‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea)

---

### requestJoin (팀 참여 요청)

```js
Teammate({
  operation: "requestJoin",
  team_name: "feature-auth",
  proposed_name: "helper",
  capabilities: "코드리뷰·테스트 지원 가능"
})
```

* 리더에게 참여 요청 메시지 전송  [oai_citation:16‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea)

---

### approveJoin / rejectJoin (참여 승인/거절)

```js
Teammate({
  operation: "approveJoin",
  target_agent_id: "helper",
  request_id: "join-123"
})
```

* 리더가 참여 요청 승인/거절  [oai_citation:17‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea)

---

### write (특정 동료에게 메시지)

```js
Teammate({
  operation: "write",
  target_agent_id: "worker-1",
  value: "진행 상황 보고"
})
```

* 출력 텍스트는 팀에게 보이지 않음 → 항상 write를 사용  [oai_citation:18‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea)

---

### broadcast (전체 브로드캐스트)

```js
Teammate({
  operation: "broadcast",
  name: "team-lead",
  value: "상태 체크"
})
```

* N명 에이전트에게 각각 메시지 전송 → 비용 큼  [oai_citation:19‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea)

---

## 6. Task 시스템 통합

Task 시스템은 작업 생성/조회/업데이트/의존관계를 지원합니다.

### Task 만들기

```js
TaskCreate({
  subject: "인증 모듈 리뷰",
  description: "...",
  activeForm: "...중..."
})
```

### TaskList 조회

* 완료/진행중/대기 등 상태 표시  [oai_citation:20‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

### TaskGet

```js
TaskGet({ taskId: "2" })
```

### TaskUpdate

```js
TaskUpdate({ taskId: "2", owner: "security-reviewer" })
TaskUpdate({ taskId: "2", status: "completed" })
TaskUpdate({ taskId: "3", addBlockedBy: ["2"] })
```

의존 관계 기반 자동 해제 처리  [oai_citation:21‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

## 7. 메시지 포맷

메시지는 JSON 구조이며, structured 메시지 타입(예: shutdown_request, idle_notification 등)을 포함합니다.  [oai_citation:22‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

## 8. 오케스트레이션 패턴

### Pattern 1: 병렬 전문가 리뷰

리더가 여러 전문 리뷰어를 병렬로 스폰:

```js
// 팀 생성
Teammate({ operation: "spawnTeam", team_name: "code-review" })

// 보안 리뷰어
Task({ team_name:"code-review", name:"security", subagent_type:"compound-engineering:review:security-sentinel", prompt:"...", run_in_background:true })
```

* 성능/보안/간결성 등 역할별 병렬 리뷰  [oai_citation:23‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

### Pattern 2: 스웜 (Self-Organizing)

일반 워커들이 공유 작업 큐에서 자체적으로 작업을 Claim:

```js
// 팀 생성
Teammate({ operation:"spawnTeam", team_name:"file-review-swarm" })

// 작업들 생성
for (const file of files) {
  TaskCreate({ subject:`${file} 리뷰`, description:`${file} 품질 체크`, activeForm:`${file} 리뷰중...` })
}

// 워커 워너
const swarmPrompt = `
LOOP:
1. TaskList()로 작업 조회
2. 할당 가능한 작업 찾기
3. TaskClaim + TaskUpdate(진행)
4. 품질 체크 → 완료
...`
```

* 워커들이 자연스럽게 로드밸런스하며 작업 처리  [oai_citation:24‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

## 9. 환경 변수

동료 에이전트는 자동으로 다음 변수를 받습니다:

```
CLAUDE_CODE_TEAM_NAME="my-project"
CLAUDE_CODE_AGENT_ID="worker-1@my-project"
CLAUDE_CODE_AGENT_NAME="worker-1"
CLAUDE_CODE_AGENT_TYPE="Explore"
...
```

프롬프트 내 사용 가능  [oai_citation:25‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

## 10. 백엔드(Spawn Backends)

### in-process

* 같은 Node.js 프로세스  
* 보이지 않음  
* 빠름  
* 리더가 중단되면 모두 종료  [oai_citation:26‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

### tmux

* tmux 분할창에서 독립 프로세스 실행  
* 리더/워커 분리  
* real-time 출력 확인 가능  [oai_citation:27‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

### iterm2 (macOS)

* iTerm2 API 통해 패널 분할  
* macOS 전용 시각적 디버깅 가능  [oai_citation:28‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

## 11. 오류 및 디버깅

* 워커가 중단되면 5분 후 자동 inactive 처리  
* 작업은 다시 다른 워커가 Claim 가능  
* 인박스/TaskList 상태 확인을 통한 디버그  [oai_citation:29‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

## 12. 전체 워크플로우 예시

### 코드 리뷰 스웜

```js
// Task 생성
for (const file of files) {
  TaskCreate({ subject:`${file} 리뷰`, description:`보안/품질/성능`, activeForm:`${file} 리뷰중...` })
}

// 워커 스웜
Task({ team_name:"codebase-review", name:"worker-1", subagent_type:"general-purpose", prompt: swarmPrompt, run_in_background:true })
```

* 워커들이 TaskList에서 역할 분담  [oai_citation:30‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

## Best Practices (모범 사례)

* 항상 `cleanup`으로 팀 정리  
* 의미있는 이름 사용  
* 명확한 프롬프트 작성  
* Task 의존관계 적극 사용  
* 실패 처리/재시도 논리 포함  
* 방송(broadcast)보다 개별 write 권장  [oai_citation:31‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)

---

## Quick Reference

```js
// Subagent 생성
Task({ subagent_type:"Explore", ... })

// Teammate 생성
Teammate({ operation:"spawnTeam", team_name:"my-team" })
Task({ team_name:"my-team", name:"worker", subagent_type:"general-purpose", ... })

// Write 메시지
Teammate({ operation:"write", target_agent_id:"worker-1", value:"..." })

// Pipeline
TaskCreate(...)
TaskUpdate(...addBlockedBy...)

// 종료
Teammate({ operation:"requestShutdown", target_agent_id:"worker-1" })
Teammate({ operation:"cleanup" })
```

---

**원문 기준 Claude Code v2.1.19 — 2026-01-25 테스트/검증 완료**  [oai_citation:32‡Gist](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea?utm_source=chatgpt.com)