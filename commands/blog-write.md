---
description: Obsidian vault의 글을 블로그에 게시합니다. "이 글 블로그에 올려줘", "노트 투고", "글 발행해줘" 등 특정 글을 블로그에 올리고 싶을 때 사용하세요. 소크라테스식 질문으로 글을 다듬어 투고합니다.
allowed-tools: AskUserQuestion, Bash, Read, Write, Edit, Glob, Grep, mcp__obsidian__read_note, mcp__obsidian__search_notes
argument-hint: <Obsidian 노트 경로 또는 검색 키워드>
---

## 블로그 글 투고 (/blog-write)

Obsidian vault의 노트를 블로그 게시글로 변환하여 투고한다.

### 동작 방식

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
## 📝 투고 준비

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
   pubDate: YYYY-MM-DD
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
## ✅ 투고 완료!

- **제목**: {제목}
- **카테고리**: {카테고리}
- **URL**: https://hey00507.github.io/posts/{yyyymmdd}/

배포가 완료되면 위 URL에서 확인할 수 있어요 (약 30초 소요).
(URL은 pubDate 기준 자동 생성. 같은 날 여러 글이면 -1, -2 붙음)
```

### 주의사항

- 원본 Obsidian 노트는 절대 수정하지 않는다 (복사만)
- `draft: true`로 투고하고 싶다고 하면 초안으로 저장 (빌드에서 제외됨)
- 이미지가 포함된 경우 사용자에게 수동 처리 안내
- 블로그 repo 경로: `/Users/ethankim/WebstormProjects/blog`
