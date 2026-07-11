---
layout: default
title: 시작 가이드
nav_order: 2
nav_exclude: true
---

# 시작 가이드

## 이 사이트는 어떻게 만드나요?

- 일반 텍스트처럼 마크다운 파일(`.md`)을 작성합니다.
- 파일 맨 위의 `title`, `nav_order`로 메뉴 순서를 정합니다.
- 저장소에 커밋/푸시하면 GitHub Pages가 사이트를 자동 빌드합니다.

## 새 페이지 추가 방법

1. 새 파일을 만들기: `my-page.md`
2. 파일 상단에 아래 형식 추가

```md
---
layout: default
title: 내 새 페이지
nav_order: 3
---

# 내 새 페이지

내용 작성
```

3. 푸시 후 사이트에서 확인
