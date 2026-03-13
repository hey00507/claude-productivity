---
description: 블로그 게시글을 삭제합니다.
allowed-tools: AskUserQuestion, Bash, Read, Glob, Grep
argument-hint: <글 제목, 파일명, 또는 slug>
---

## 블로그 글 삭제 (/blog-del)

블로그 게시글을 찾아서 삭제한다.

### 동작 방식

#### Step 1: 삭제 대상 찾기

`$ARGUMENTS`로 전달된 값으로 글을 찾는다.

검색 경로: `/Users/ethankim/WebstormProjects/blog/src/content/posts/`

- 파일명이 정확히 일치하는 경우 → 바로 선택
- 부분 일치 또는 키워드인 경우 → 후보 목록을 보여주고 사용자가 선택
- 비어있는 경우 → 전체 글 목록을 보여주고 사용자가 선택

각 후보는 제목, 카테고리, 날짜를 함께 표시한다.

#### Step 2: 삭제 확인

`AskUserQuestion`으로 반드시 확인:

```markdown
## ⚠️ 삭제 확인

다음 글을 삭제하려고 합니다:

- **제목**: {제목}
- **카테고리**: {카테고리}
- **날짜**: {pubDate}
- **파일**: {파일 경로}

정말 삭제할까요? (y/n)
```

#### Step 3: 삭제 & 배포

사용자가 확인하면:

1. 해당 `.md` 파일을 삭제 (`rm`)
2. 로컬 빌드 테스트: `cd /Users/ethankim/WebstormProjects/blog && pnpm build`
3. 빌드 성공 시 git commit & push:
   - 커밋 메시지: `Remove post: {글 제목}`
4. 완료 보고

#### Step 4: 완료 보고

```markdown
## ✅ 삭제 완료

- **삭제된 글**: {제목}
- 배포가 완료되면 사이트에서 제거됩니다 (약 30초 소요).
```

### 주의사항

- 삭제 전 반드시 사용자 확인을 받는다
- 한 번에 하나의 글만 삭제한다
- 블로그 repo 경로: `/Users/ethankim/WebstormProjects/blog`
