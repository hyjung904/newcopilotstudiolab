---
layout: default
title: Workflow 생성
nav_order: 2
parent: Workflow 심화
---

# SharePoint List 첨부파일 조회 Workflow 생성

## Trigger 설정 

새로운 Workflow를 생성하고, **Trigger Type을 Connector**로 변경합니다. 그리고 When an agent calls the workflow 로 변경합니다. 
Text Input을 하나 추가합니다. 이 Input이 추후 에이전트와 연결되었을 경우 사용자의 요청을 받아오는 Input이 됩니다. 

## Classify 설정 

사용자의 요청 (예: IR 자료 찾아줘.) 을 카테고리별로 분류하기 위해 Classify를 설정합니다. 오늘은 크게 HR 및 Finance로 설정하겠습니다. 

아래와 같이 Input to classify 를 동적컨텐츠 (when an agent calls the workflow 의 Text Input) 로 설정합니다. 

Categories 는 
- HR : 인사, HR, 이력서, 레쥬메 등 
- Finance : 재무, 회계, Finance, IR, 투자, 보험 등 

로 Description을 작성합니다.

## SharePoint Get Items 설정 

아까 생성한 `fileextract_성함_날짜` List에서, filechoice 열의 값이 Classify에서 분류된 카테고리와 일치하는 항목들만을 조회하도록 설정합니다. 

Site Address 와 List Name 을 선택합니다. 

Filter Query 에는 아래와 같이 작성합니다. 

```text
filechoice eq '[Classify Output 동적 콘텐츠 선택]'
``` 

이렇게 되면, 다수의 HR 카테고리 항목들을 `Get Items`를 통해 조회할 수 있습니다. 

### Tip

Filter Query 값 예시 : 

```text
filechoice eq 'HR'
filechoice eq 'Finance' and Created ge '2026-08-01T00:00:00Z'
```

추가로 사용할 수 있는 연산자 예시:

```text
ne : "같지 않다"
gt : "크다"
lt : "작다"
le : "작거나 같다"
```

## Respond to the agent 설정

`Respond to the agent` 액션을 추가합니다. 지금 작성하는 Workflow의 경우 Workflow (Agent flow) Time limit 120초를 넘어가기 때문에, 최종 결과물을 에이전트에게 반환할 수 없습니다. 따라서 중간에 Respond to the agent 액션을 추가하여, 에이전트에게 미래에 결과가 이메일을 통해 전달될 것임을 알려주는 역할을 합니다.

Text Output을 추가하고 다음과 같이 작성합니다. 

```text
[Category 동적콘텐츠] document will be processed, and the result will be sent to your Outlook email. 

[Category 동적콘텐츠] 문서가 처리될 예정이며, 아웃룩 이메일로 결과가 전달될 예정입니다.
```

> 한국어가 작성되지 않는 경우, 영어로 작성해주세요. 

## Variable 설정 1 

Initialize Variable 을 설정하고 

Variable Name : `filesummary`
Type : Array
Value : 

해당 변수가 앞으로의 파일 요약을 담을 Array (배열) 역할을 하게 됩니다. 

> Initialize Variable 액션은 반드시 For each 액션 전에 위치해야 합니다. 

## SharePoint Get Attachments 설정 

## SharePoint Get file metadata 설정 

## Get file content using path 설정 

## Inline Agent 설정 1

## Variable 설정 2 

## Inline Agent 설정 2 

## Office 365 Outlook Send an email 설정 