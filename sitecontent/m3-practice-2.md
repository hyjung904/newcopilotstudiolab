---
layout: default
title: Weekly Report SKILL.md 작성
nav_order: 2
parent: Weekly Report Agent
---

# Weekly Report Agent - 주간업무 보고 에이전트 

## SKILL.md 작성 - Teams 미팅 제외 버전

```markdown
---
name: past-weekly-report
description: |-
  지난 한 주간의 캘린더 및 이메일 내용을 `Work IQ Calendar`, `Work IQ Mail (Preview)`를 통해 가져오고 해당 데이터를 바탕으로 한국어 주간업무 일지를 작성한다.
    사용자가 다음과 같이 요청하면 이 Skill을 사용한다.
    - “지난주 업무 일지 작성해줘”
    - “이번 주 캘린더랑 이메일 기준으로 업무 정리해줘”
    - “주간업무보고 초안 만들어줘”
    - “지난 한 주 일과를 정리해줘”
---
# 주간업무 일지 작성 Skill

## 필수 실행 규칙

주간업무 일지를 작성하기 전에 아래 단계를 실행한다.

### 1. Calendar 조회
- `ListEvents` 또는 `ListCalendarView`로 기준 기간 내 미팅을 조회한다.

### 2. 메일 조회
- `SearchMessagesQueryParameters`를 호출한다.
- `$top=25`
- `$select=id,subject,from,receivedDateTime,bodyPreview,importance`
- `nextLink`는 사용하지 않는다.
- `$skip` 값을 25씩 증가시키며 조회한다.
- `hasMoreResults=false`가 나오거나 200~300건에 도달하면 중단한다.

예시:
`?$filter=receivedDateTime ge {START_DATE} and receivedDateTime le {END_DATE}&$top=25&$skip=0&$select=id,subject,from,receivedDateTime,bodyPreview,importance`

## 툴 호출 한계 대응

- 가능한 독립 Tool 호출은 `multi_tool_use.parallel`로 병렬 실행한다.
- 단, 한 번에 너무 많은 호출을 넣지 않는다.
- 권장 배치 크기:
  - 메일 페이징: 한 번에 3~5페이지
- 배치 실행 후 결과를 보고 다음 배치를 이어서 실행한다.
- 모든 것을 한 번에 호출하려고 하지 말고, 안정적으로 완료 가능한 단위로 나누어 진행한다.

## 작성 금지 조건

아래가 완료되기 전에는 주간업무 일지를 작성하지 않는다.

- Calendar 조회 완료
- Mail 전체 페이지 조회 완료 또는 200~300건 제한 도달
- 실패한 호출은 실패 사유 기록

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

- `Work IQ Calendar`, `Work IQ Mail (Preview)` 두 가지의 MCP를 연결하고, 선택적으로 Tool을 선택하여 연결합니다. 