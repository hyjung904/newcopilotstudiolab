---
layout: default
title: 실습 2 - Skill 작성하기  
nav_order: 2
parent: News Letter Agent
---

# 실습 2 - Skill 작성하기 

앞선 실습에서 작성한 reference HTML 파일을 기반으로, 뉴스레터를 생성하는 SKILL.md를 작성해보겠습니다.

## SKILL.md (초안) 작성하기 

**스킬 초안**

```markdown
---
name : ai-news-edm-html
description : 뉴스 보내줘, 뉴스 만들어줘, 뉴스 초안보내줘, 뉴스를 검색해서 초안 만들어 등과 같은 사용자 발화 시 사용합니다. 뉴스 EDM HTML을 생성하는 데 사용됩니다. 뉴스 EDM HTML은 뉴스레터 형식의 이메일 콘텐츠를 생성하는 데 사용됩니다.
---

# 뉴스 EDM HTML 생성 SKILL

## 뉴스 EDM HTML 생성 Step

1. 사용자의 요청 키워드 기준으로 최신 뉴스 10개를 `Work IQ Copilot MCP` 의 `copilot_chat`을 통해 수집해 목록을 생성합니다. (필수)
  - 각 뉴스 항목은 반드시 `제목 / 요약 / 링크`를 포함합니다.
  - 사용자가 키워드를 주지 않은 경우 키워드를 먼저 요청합니다.

2. Step 1에서 생성한 목록을 검증합니다.
  - 뉴스 항목 수가 정확히 10개인지 확인합니다.
  - 10개 항목 모두 링크가 있는지 확인합니다.
  - 조건 불충족 시 Step 1로 돌아가 목록을 다시 생성합니다.

3. 검증된 목록을 `ai-newsletter-template.html`에 매핑하여 HTML을 생성합니다.
  - 템플릿 구조는 유지하고 placeholder만 치환합니다.

4. 생성된 HTML을 검증합니다.
  - `{{ }}` placeholder가 하나라도 남아 있으면 HTML을 다시 생성합니다.
  - 뉴스 개수가 10개가 아니면 Step 1로 돌아갑니다.
  - 링크 누락이 있으면 목록부터 재생성합니다.

5. 최종 검증을 통과한 HTML로 `CreateDraftMessage`를 호출해 Draft를 생성합니다. (contentType = HTML)
  - 절대 Send Email 하지 않습니다.
  - 생성된 draft의 webLink를 사용자에게 전달합니다.

```
간단하게 작성한 SKILL.md 초안입니다. 위 SKILL을 기반으로 점차 세부적인 규칙과 검증 로직을 추가하여 최종 스킬을 완성해보세요. 

## Tool 연결하기 

## SKILL.zip 생성 및 업로드 

## 테스트 

## 최종 SKILL.md 로 업데이트 

**스킬 최종본**

```markdown
---
name : ai-news-edm-html
description : 뉴스 보내줘, 뉴스 만들어줘, 뉴스 초안보내줘, 뉴스를 검색해서 초안 만들어 등과 같은 사용자 발화 시 사용합니다. 뉴스 EDM HTML을 생성하는 데 사용됩니다. 뉴스 EDM HTML은 뉴스레터 형식의 이메일 콘텐츠를 생성하는 데 사용됩니다.
---

# 뉴스 EDM HTML 생성 SKILL

## 뉴스 EDM HTML 생성 Step

1. 사용자의 요청 키워드 기준으로 최신 뉴스 10개를 `Work IQ Copilot MCP` 의 `copilot_chat`을 통해 수집해 JSON을 생성합니다. (필수)
  - 각 뉴스 항목은 반드시 `제목 / 요약 / 링크`를 포함합니다.
  - 사용자가 키워드를 주지 않은 경우 키워드를 먼저 요청합니다.
2. Step 1에서 생성한 JSON을 검증합니다.
  - 뉴스 항목 수가 정확히 10개인지 확인합니다.
  - 10개 항목 모두 링크가 있는지 확인합니다.
  - 조건 불충족 시 Step 1로 돌아가 JSON을 다시 생성합니다.
3. 검증된 JSON을 `ai-newsletter-template.html`에 매핑하여 HTML을 생성합니다.
  - 템플릿 구조는 유지하고 placeholder만 치환합니다.
  - 스타일은 반드시 inline만 사용합니다.
4. 생성된 HTML을 검증합니다.
  - `{{ }}` placeholder가 하나라도 남아 있으면 HTML을 다시 생성합니다.
  - 뉴스 개수가 10개가 아니면 Step 1로 돌아갑니다.
  - 링크 누락이 있으면 JSON부터 재생성합니다.
5. 최종 검증을 통과한 HTML로 `CreateDraftMessage`를 호출해 Draft를 생성합니다. (contentType = HTML)
  - 절대 Send Email 하지 않습니다.
  - 생성된 draft의 webLink를 사용자에게 전달합니다.

## ✅ Outlook-safe 규칙 (매우 중요)

HTML 생성 시 반드시 다음 규칙을 준수하여 Outlook 호환성을 보장합니다:

- **`<style>` 사용 금지** - 외부 스타일 태그 절대 불가
- **모든 스타일은 inline으로 작성** - 모든 CSS는 태그의 `style=""` 속성에 직접 작성
- **`background: linear-gradient` 사용 금지** - 단색 배경만 사용 (예: `#0f6cbd`)
- **flex / grid 사용 금지** - `<table>` 기반 레이아웃만 사용
- **margin 최소화** - `margin` 대신 `padding` 우선 사용

## 검증 규칙 

- 아래 조건 하나라도 실패하면 HTML 재생성:
  1. {{ }} 남아있음 ❌
  2. 뉴스 개수 < 10 ❌
  3. inline 스타일 없음 ❌
  4. 링크 누락 ❌
  5. `<style>` 태그 포함 ❌
  6. `linear-gradient` 사용 ❌
  7. `flex` 또는 `grid` 사용 ❌
  8. `margin` 과다 사용 (padding 미사용) ❌

## 재시도 루프 규칙 (필수)

- `{{ }}`가 남아 있으면 Step 3부터 다시 생성합니다.
- 뉴스가 10개가 아니면 Step 1부터 다시 시작합니다.
- JSON 검증 실패(링크 누락/개수 불일치) 시 Step 1로 돌아갑니다.

```
## SKILL.md 다운로드 

[SKILL.md 다운로드]({{ '/practice1_downloads/SKILL.md' | relative_url }})

## SKILL.zip 다운로드

[newsletter-html-email.zip 다운로드]({{ '/practice1_downloads/newsletter-html-email.zip' | relative_url }})