---
description: Obsidian vault에서 #publish 태그가 달린 노트를 일괄 스캔하여 블로그에 자동 투고합니다.
allowed-tools: AskUserQuestion, Bash, Read, Write, Edit, Glob, Grep, mcp__obsidian__read_note, mcp__obsidian__search_notes, mcp__obsidian__manage_tags, mcp__obsidian__get_frontmatter
argument-hint: [선택] 특정 폴더만 스캔할 경우 폴더 경로
---

## Obsidian → 블로그 자동 투고 (/blog-sync)

Obsidian vault에서 `#publish` 태그가 붙은 노트를 일괄 탐색하여 블로그에 투고한다.

### 태그 규칙

| Obsidian 태그 | 블로그 카테고리 | 비고 |
|--------------|---------------|------|
| `#publish/reading` | `reading` | 책 관련 필드 자동 추출 시도 |
| `#publish/essay` | `essay` | |
| `#publish/dev` | `dev` | |
| `#publish` (단독) | AI가 내용 분석하여 추론 | |

**투고 완료 후**: 원본 노트의 `#publish` → `#published`로 변경 (재실행 시 skip)

### 동작 방식

#### Step 1: `#publish` 태그 스캔

1. `mcp__obsidian__search_notes`로 `#publish` 태그가 있는 노트를 검색한다.
2. `$ARGUMENTS`가 있으면 해당 폴더 내만 필터링한다.
3. 이미 `#published` 태그가 있는 노트는 제외한다.
4. 블로그 `src/content/posts/` 에 동일 제목의 파일이 있는지 확인하여 중복 방지한다.

#### Step 2: 후보 목록 표시 & 사용자 확인

`AskUserQuestion`으로 후보를 보여준다:

```markdown
## 📋 투고 대기 노트 ({N}개)

| # | 노트 | 카테고리 | 태그 |
|---|------|---------|------|
| 1 | 050.Etc/051.Diary/독서감상.md | reading | #publish/reading |
| 2 | 020.Growth/021.Study/리액트입문.md | dev | #publish/dev |
| 3 | 050.Etc/어느날의생각.md | essay (추론) | #publish |

어떤 글을 투고할까요? (번호, 전부, 스킵)
```

- **없으면**: "투고할 노트가 없습니다. Obsidian에서 `#publish` 태그를 추가해주세요." 출력 후 종료

#### Step 3: 일괄 변환 & 투고

선택된 노트 각각에 대해:

1. **노트 읽기**: `mcp__obsidian__read_note`로 전체 내용 읽기

2. **카테고리 결정**:
   - `#publish/reading` → `reading`
   - `#publish/essay` → `essay`
   - `#publish/dev` → `dev`
   - `#publish` 단독 → 내용 분석하여 추론 (코드블록 多 → dev, 책/저자 언급 → reading, 그 외 → essay)

3. **메타데이터 자동 생성**:
   ```yaml
   ---
   title: "노트 제목"
   description: "첫 문단 또는 내용 요약 (AI 생성)"
   category: "추론된 카테고리"
   tags: ["Obsidian 태그에서 변환"]
   pubDate: YYYY-MM-DDTHH:MM:SS  # 현재 시각
   draft: false
   # reading인 경우: bookTitle, bookAuthor, rating 추출 시도
   # dev인 경우: series 추출 시도
   ---
   ```

4. **Obsidian → Markdown 변환**:
   - `[[링크]]` → 일반 텍스트
   - `[[링크|표시텍스트]]` → 표시텍스트
   - `![[이미지]]` → 이미지 발견 시 사용자에게 알림
   - `> [!note]` 등 callout → `> **Note:** ...` blockquote
   - Obsidian frontmatter 태그 제거 (블로그 frontmatter로 대체)

5. **파일 저장**: `/Users/ethankim/WebstormProjects/blog/src/content/posts/{category}/{파일명}.md`

6. **태그 업데이트**: `mcp__obsidian__manage_tags`로 원본 노트의 `#publish` → `#published` 변경

#### Step 4: 빌드 & 배포

모든 글 처리 후 한번에:

1. `cd /Users/ethankim/WebstormProjects/blog && pnpm build`
2. 빌드 성공 시:
   ```bash
   git add src/content/posts/
   git commit -m "Sync: {N}개 글 투고 ({제목1}, {제목2}, ...)"
   git push
   ```

#### Step 5: 완료 보고

```markdown
## ✅ 동기화 완료!

| # | 제목 | 카테고리 | URL |
|---|------|---------|-----|
| 1 | 독서감상 | reading | /posts/yyyymmdd/ |
| 2 | 리액트입문 | dev | /posts/yyyymmdd-2/ |

총 {N}개 투고, 배포까지 약 30초 소요.
Obsidian 원본 노트에 #published 태그가 추가되었습니다.
```

### 중복 방지 로직

1. `#published` 태그가 있는 노트 → skip
2. 블로그에 동일 제목 파일 존재 → "이미 투고된 것 같습니다. 업데이트할까요?" 질문
3. 업데이트 선택 시 기존 파일 덮어쓰기 + `updatedDate` 추가

### 주의사항

- 원본 Obsidian 노트 내용은 수정하지 않는다 (태그만 변경)
- 이미지가 포함된 노트는 텍스트만 투고하고 이미지 목록을 알림
- 한 번에 너무 많은 글 (10개 초과)이면 경고 후 확인
- 블로그 repo 경로: `/Users/ethankim/WebstormProjects/blog`
