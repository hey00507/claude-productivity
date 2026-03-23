---
description: Obsidian vault의 글을 블로그에 게시합니다. "reading" 키워드로 서평을 대화형으로 작성할 수도 있습니다.
allowed-tools: AskUserQuestion, Bash, Read, Write, Edit, Glob, Grep, mcp__obsidian__read_note, mcp__obsidian__search_notes
argument-hint: <Obsidian 노트 경로 또는 키워드> | reading
---

## 블로그 글 투고 (/blog-write)

Obsidian vault의 노트를 블로그 게시글로 변환하여 투고한다.
`reading` 키워드를 사용하면 대화형으로 서평을 작성하여 투고한다.

### 모드 판별

`$ARGUMENTS`를 확인하여 모드를 결정한다:

| 입력 | 모드 |
|------|------|
| `reading` | **서평 모드** — 대화로 서평 정보 수집 → 자동 생성 |
| Obsidian 경로/키워드 | **일반 모드** — Obsidian 노트 변환 |
| 비어있음 | "어떤 글을 투고할까요?" 질문 |

---

### 서평 모드 (`/blog-write reading`)

#### Step R1: 기본 정보 수집

`AskUserQuestion`으로 한 번에 핵심 정보를 수집한다:

```
질문 1 (multiSelect: false):
- 책 제목
- 저자

질문 2 (multiSelect: false):
- 별점 (1~5) — 옵션으로 제시

질문 3 (multiSelect: false):
- 장르/태그 — 소설, 에세이, 자기계발, 인문, 시 등
```

#### Step R2: 감상 수집

`AskUserQuestion`으로 서평 내용을 수집한다. 사용자가 자유롭게 텍스트를 입력하도록 한다.
각 항목은 개별 질문으로 순서대로 물어본다:

1. **한줄 감상** — "이 책을 한 문장으로 표현한다면?"
2. **기억에 남는 문장** — "책에서 가장 인상 깊었던 문장이나 구절이 있나요? (없으면 스킵)"
3. **느낀 점 / 감상** — "이 책을 읽고 어떤 생각이 들었나요? 자유롭게 적어주세요"
4. **추천 대상** — "어떤 사람에게 이 책을 추천하고 싶나요? (없으면 스킵)"

**중요**: 사용자가 "스킵" 또는 빈 내용을 주면 해당 섹션을 생략한다.

#### Step R3: 이미지 처리

`AskUserQuestion`으로 이미지가 있는지 확인:
- "책 사진이 있으면 파일 경로를 알려주세요 (없으면 스킵)"

이미지가 제공된 경우:
1. `sips`로 리사이즈 (최대 너비 700px) + 퀄리티 70%로 압축
2. `public/images/reading/{파일명}.jpg`에 저장
3. 파일명 규칙: 책제목에서 공백 제거 (예: `단한번의삶-1.jpg`)
4. `image-gallery` div로 배치

#### Step R4: 서평 생성

수집한 내용으로 통일 템플릿에 맞춰 서평을 자동 생성한다.

**서평 템플릿:**
```markdown
---
title: "[서평] '{bookTitle}'을 읽고"
description: "{한줄 요약}"
category: "reading"
tags: ["서평", "{저자명}", "{장르}"]
pubDate: {현재 날짜 시각}
bookTitle: "{bookTitle}"
bookAuthor: "{bookAuthor}"
rating: {1~5}
draft: false
---

{이미지가 있으면}
<div class="image-gallery">
  <img src="/images/reading/{파일명}.jpg" alt="{책 제목}" />
</div>

## 책 소개

{사용자 감상에서 책 내용 관련 부분을 자연스럽게 정리. 스포일러 없이 책의 분위기와 주제를 소개}

## 한줄 감상

"{한줄 감상}"

{한줄 감상에 대한 부연 설명 1~2문장}

## 기억에 남는 문장

> "{인용구}"

{인용구에 대한 사용자의 생각}

## 느낀 점

{사용자가 입력한 감상을 자연스러운 글로 다듬기. 사용자의 톤과 표현을 최대한 유지하면서 문단 나누기와 흐름만 정리}

## 추천 대상

- {추천 대상 1}
- {추천 대상 2}
- {추천 대상 3}
```

**글 다듬기 원칙:**
- 사용자의 원래 표현과 톤을 최대한 유지
- 내용을 추가하거나 창작하지 않음
- 맞춤법과 문단 흐름만 정리
- 스킵된 섹션은 통째로 제거

#### Step R5: 확인 & 투고

생성된 서평 초안을 보여주고 확인받는다:
- "이대로 투고할까요? 수정할 부분 있으면 알려주세요"
- 수정 요청 시 해당 부분만 수정
- 확인 시 일반 모드의 Step 4-5 (파일 저장 → 빌드 → 커밋 → 푸시)로 진행

---

### 일반 모드 (기존)

#### Step 1: 원본 노트 읽기

`$ARGUMENTS`로 전달된 경로 또는 키워드로 Obsidian 노트를 찾아 읽는다.
- 경로가 주어진 경우: `mcp__obsidian__read_note`로 직접 읽기
- 키워드가 주어진 경우: `mcp__obsidian__search_notes`로 검색 후 후보를 보여주고 사용자가 선택
- 비어있는 경우: "어떤 글을 투고할까요?" 질문

#### Step 2: 카테고리 & 메타데이터 결정

원본 글의 내용을 분석하여 적절한 카테고리를 제안한다:
- `reading` — 책 리뷰, 독후감 관련 내용
- `essay` — 일상, 생각, 에세이
- `dev` — 기술, 코딩, 개발 경험

`AskUserQuestion`으로 확인:

```markdown
## 투고 준비

원본: `{노트 경로}`

---

이 글을 **{제안 카테고리}** 카테고리로 투고하려고 합니다.

- **제목**: {원본 제목 또는 제안}
- **카테고리**: {reading / essay / dev}
- **설명(한 줄 요약)**: {제안}
- **태그**: {제안}

이대로 진행할까요? 수정하고 싶은 부분이 있으면 알려주세요.
```

#### Step 3: 글 다듬기 (Clarify 방식)

원본 내용을 블로그 글에 맞게 다듬을 부분을 `AskUserQuestion`으로 하나씩 질문한다:

**질문 영역 (필요한 것만, ONE question per turn):**
- 글의 톤 (존댓말 / 반말 / 혼합)
- 추가하고 싶은 내용이나 빼고 싶은 부분
- 독자에게 전달하고 싶은 핵심 메시지
- (독서) 별점, 추천 대상
- (코딩) 시리즈명, 코드 추가 여부

**중요**: 질문은 2~3개 이내로 최소화. 사용자가 "그대로 올려줘"라고 하면 즉시 투고 진행.

#### Step 4: 최종 글 작성 & 투고

1. frontmatter를 블로그 스키마에 맞게 생성:
   ```yaml
   ---
   title: "제목"
   description: "한 줄 요약"
   category: "reading" | "essay" | "dev"
   tags: ["태그1", "태그2"]
   pubDate: YYYY-MM-DDTHH:mm:ss
   draft: false
   # 독서 전용 (해당 시)
   bookTitle: "책 제목"
   bookAuthor: "저자"
   rating: 4
   # 코딩 전용 (해당 시)
   series: "시리즈명"
   ---
   ```

2. Obsidian 문법을 표준 Markdown으로 변환:
   - `[[링크]]` → 일반 텍스트 또는 적절한 마크다운 링크
   - `![[이미지]]` → 표준 이미지 문법 (이미지가 있을 경우 알림)
   - Obsidian 전용 callout → 표준 blockquote
   - frontmatter의 Obsidian 태그 → 블로그 태그로 변환

3. 파일을 `/Users/ethankim/WebstormProjects/blog/src/content/posts/{category}/` 에 저장
   - 파일명: 한글 또는 영어 자유 (예: `블로그를-시작하며.md`, `astro-setup.md`)
   - URL은 pubDate 기반으로 자동 생성되므로 파일명은 관리 편의에 맞게 설정

4. 로컬 빌드 테스트: `cd /Users/ethankim/WebstormProjects/blog && pnpm build`

5. 빌드 성공 시 git commit & push:
   - 커밋 메시지: `Post: {글 제목}`
   - 자동으로 GitHub Actions가 배포

#### Step 5: 완료 보고

```markdown
## 투고 완료!

- **제목**: {제목}
- **카테고리**: {카테고리}
- **URL**: https://hey00507.github.io/posts/{yyyymmdd}/

배포가 완료되면 위 URL에서 확인할 수 있어요 (약 30초 소요).
(URL은 pubDate 기준 자동 생성. 같은 날 여러 글이면 -1, -2 붙음)
```

---

### 주의사항

- 원본 Obsidian 노트는 절대 수정하지 않는다 (복사만)
- `draft: true`로 투고하고 싶다고 하면 초안으로 저장 (빌드에서 제외됨)
- 서평 이미지: `sips`로 최대 700px + 퀄리티 70% 압축 → `public/images/reading/`에 저장
- 블로그 repo 경로: `/Users/ethankim/WebstormProjects/blog`
