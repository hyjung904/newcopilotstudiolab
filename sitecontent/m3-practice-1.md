---
layout: default
title: Weekly Report SKILL.md 작성
nav_order: 1
parent: Weekly Report Agent
---

# Weekly Report Agent - 주간업무 보고 에이전트 

## SKILL.md 작성

```markdown
---
name: past-weekly-report
description: |-
  지난 한 주간의 Teams 미팅 및 이메일 내용을 `Work IQ Calendar`, `Work IQ Mail (Preview)`, `Work IQ Teams (Preview)` 를 통해 가져오고 해당 데이터를 바탕으로 한국어 주간업무 일지를 작성한다.
    사용자가 다음과 같이 요청하면 이 Skill을 사용한다.
    - “지난주 업무 일지 작성해줘”
    - “이번 주 Teams 미팅이랑 이메일 기준으로 업무 정리해줘”
    - “주간업무보고 초안 만들어줘”
    - “지난 한 주 일과를 정리해줘”
---
# 주간업무 일지 작성 Skill

## 필수 실행 규칙

주간업무 일지를 작성하기 전에 아래 단계를 실행한다.

### 1. Calendar 조회
- `ListEvents` 또는 `ListCalendarView`로 기준 기간 내 미팅을 조회한다.
- Teams 온라인 미팅은 `onlineMeeting.joinUrl` 또는 Teams join URL을 확보한다.

### 2. 메일 조회
- `SearchMessagesQueryParameters`를 호출한다.
- `$top=25`
- `$select=id,subject,from,receivedDateTime,bodyPreview,importance`
- `nextLink`는 사용하지 않는다.
- `$skip` 값을 25씩 증가시키며 조회한다.
- `hasMoreResults=false`가 나오거나 200~300건에 도달하면 중단한다.

예시:
`?$filter=receivedDateTime ge {START_DATE} and receivedDateTime le {END_DATE}&$top=25&$skip=0&$select=id,subject,from,receivedDateTime,bodyPreview,importance`

### 3. Teams 미팅 회의록/AI 요약 조회
- Calendar에서 조회된 Teams 미팅 중 업무 관련성이 높은 미팅만 우선 조회한다.
- 모든 Teams 미팅을 전수 조회하지 않는다.
- 조회 대상은 최대 10~15개 핵심 미팅으로 제한한다.

#### 우선순위 기준
1. 고객사명이 포함된 미팅
2. 사용자의 담당 업무 또는 고객 프로젝트와 직접 관련
3. 내부 운영/계획 회의 중 후속 액션이 있는 미팅

#### 제외 기준
아래 항목은 상세 조회 대상에서 제외하거나 “비업무/개인/전사성 알림”으로만 처리한다.

- Wellbeing
- Team Dinner
- Reservation
- Town Hall
- Recording Available
- Daily Digest
- Newsletter
- Product Update
- Viva Engage
- Open House
- 개인 일정
- 복지
- 팀 행사
- 취소된 미팅
- 업무 내용이 불명확한 반복 Office Hours

#### 조회 방식
- 각 핵심 미팅에 대해 먼저 `GetOnlineMeetingAiInsights`를 호출한다.
- AI Insight가 없거나 오류가 발생한 경우에만 `GetOnlineMeetingTranscripts`를 fallback으로 호출한다.
- 권한 오류, 미생성, transcript 없음 등으로 조회가 실패하면 실패 사유를 기록하고 다음 미팅으로 진행한다.
- 실패한 미팅 때문에 전체 주간보고 작성을 중단하지 않는다.

## 툴 호출 한계 대응

- 가능한 독립 Tool 호출은 `multi_tool_use.parallel`로 병렬 실행한다.
- 단, 한 번에 너무 많은 호출을 넣지 않는다.
- 권장 배치 크기:
  - 메일 페이징: 한 번에 3~5페이지
  - Teams 채널 메시지: 한 번에 3~5개 채널
  - AI Insight / Transcript: 한 번에 3~5개 미팅
- 배치 실행 후 결과를 보고 다음 배치를 이어서 실행한다.
- 모든 것을 한 번에 호출하려고 하지 말고, 안정적으로 완료 가능한 단위로 나누어 진행한다.

## 작성 금지 조건

아래가 완료되기 전에는 주간업무 일지를 작성하지 않는다.

- Calendar 조회 완료
- Mail 전체 페이지 조회 완료 또는 200~300건 제한 도달
- Teams 팀 목록 조회 완료
- 주요 팀의 채널 목록 조회 완료
- 주요 채널 메시지 조회 완료
- Calendar에서 Teams join URL 목록 추출 완료
- 핵심 업무 관련 Teams 미팅의 AI Insight 조회 시도 완료
- AI Insight 실패 건은 Transcript fallback 시도 완료
- 실패한 호출은 실패 사유 기록

단, 아래 경우에는 주간보고 작성을 진행한다.

- 권한 오류로 AI Insight/Transcript 조회가 불가능한 경우
- Transcript가 생성되지 않은 경우
- 취소된 미팅인 경우
- 업무 관련성이 낮아 제외 기준에 해당하는 경우
- 조회 대상 미팅이 10~15개 한도를 초과해 우선순위 밖으로 밀린 경우

## 작성 원칙

- 확인 가능한 정보만 사용한다.
- 회의명, 날짜, 주요 논의, 결정사항 중심으로 정리한다.
- 이메일은 주요 요청, 진행 상황, 후속 액션 위주로 요약한다.
- 불확실하거나 확인되지 않은 내용은 추정하지 않고 “확인 필요”로 표시한다.
- 개인적이거나 민감한 일정은 상세 내용을 노출하지 않고 “비공개 일정” 또는 “개인 일정”으로 처리한다.
- 보고서에는 “메일을 조회함”, “캘린더를 확인함”, “Teams 메시지를 불러옴” 같은 수집 행위를 쓰지 않는다.
- 실제 업무 내용만 작성한다.

## 출력 형식

### 1. 주간 업무 요약
- 이번 주 핵심 업무를 빠짐없이 요약한다.

### 2. 완료한 일
- 완료된 업무를 bullet로 정리한다.

### 3. 진행 중인 일
- 다음 주에도 이어질 업무를 bullet로 정리한다.

### 4. 확인 필요
- 업무상 영향이 있는 미확인 사항만 간단히 정리한다.
- 단순 수집 실패 로그는 쓰지 않는다.
```
## Tool 연결하기