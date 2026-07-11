---
layout: default
title: Skill Bundle
nav_order: 2
parent: 새로운 Copilot Studio
---

# Skill Bundle

## Skill Bundle (Skill.md + 추가 파일들)

아래는 대표적인 Skill Bundle 구조 예시입니다. 

```text
skill-bundle/
├─ skill.md
├─ instructions.md
├─ references/
│  ├─ overview.md
│  ├─ examples.md
│  ├─ faq.md
│  └─ assets/
│     ├─ diagram.png
│     └─ sample.json
└─ prompts/
	└─ system.prompt.md
```
- [Agent Skills - directory structure](https://agentskills.io/specification#directory-structure)
- `skill.md`에는 스킬의 이름과 설명, 언제 쓰는지 같은 기본 정보가 들어가고, 예를 들어 `references/`에는 에이전트가 참고할 추가 문서, 예시, 자주 묻는 질문, 보조 자료를 넣습니다. 이런 식으로 나누면 스킬 본문은 짧고 깔끔하게 유지하면서도 필요한 참고 자료를 따로 관리할 수 있습니다.

### 선택적으로 둘 수 있는 폴더

#### `references/`

에이전트가 필요할 때 읽어볼 추가 문서를 넣는 폴더입니다.

- `REFERENCE.md`: 자세한 기술 참고 문서
- `FORMS.md`: 양식 템플릿이나 구조화된 데이터 형식

> 어떨 때는 `references/` 폴더를 두지 않고, `skill.md` 안에 모든 내용을 넣어도 됩니다. 하지만 스킬이 복잡해지면 참고 자료를 별도 폴더로 분리하는 것이 좋습니다. 

> 사용 예시 : Word docx 생성 스킬에서 Word 템플릿을 `references/` 폴더에 넣어두고, 스킬 본문에서는 템플릿을 어떻게 쓰는지 간단히 설명만 하는 식으로 구성할 수 있습니다.

#### `assets/`

템플릿, 이미지, 데이터 파일처럼 정적인 자료를 넣는 폴더입니다.

- 템플릿 파일
- 다이어그램이나 예시 이미지
- 조회표, 스키마, 샘플 데이터 같은 파일

## Skill - 점진적 로딩

에이전트는 Skill을 한 번에 전부 읽지 않고, 필요한 만큼만 단계적으로 불러옵니다. 이렇게 하면 기본 정보는 가볍게 시작하고, 작업이 필요할 때만 상세 내용을 읽을 수 있습니다.

1. **메타데이터**: `name`과 `description`만 먼저 읽습니다. 대략 100토큰 정도의 가벼운 정보입니다.
2. **지침**: `SKILL.md` 본문 전체를 읽습니다. 보통 5000토큰 이하로 유지하는 것이 좋습니다.
3. **리소스**: `scripts/`, `references/`, `assets/` 안의 파일은 실제로 필요할 때만 불러옵니다.

즉, 핵심은 `SKILL.md`는 짧고 명확하게 유지하고, 자세한 내용은 별도 파일로 분리하는 것입니다.