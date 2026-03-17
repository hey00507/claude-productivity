---
description: Obsidian vault의 글을 블로그에 게시합니다. 단일 글 투고, #publish 태그 일괄 투고 모두 지원합니다. "이 글 블로그에 올려줘", "노트 투고", "글 발행", "블로그 동기화", "publish 태그 글 올려줘", "일괄 투고" 등.
allowed-tools: AskUserQuestion, Bash, Read, Write, Edit, Glob, Grep, mcp__obsidian__read_note, mcp__obsidian__search_notes, mcp__obsidian__manage_tags, mcp__obsidian__get_frontmatter, mcp__google-calendar__get-current-time
argument-hint: <노트 경로|키워드|sync>
---

## 블로그 글 투고 (/blog-write)

Obsidian vault의 노트를 블로그 게시글로 변환하여 투고한다. 단일 글과 일괄 투고를 모두 지원한다.

### 1단계: 모드 판별

`$ARGUMENTS`를 보고 모드를 결정한다:

| 패턴 | 모드 | 예시 |
|------|------|------|
| 노트 경로 또는 키워드 | **A (단일 투고)** | `/blog-write 독서감상` |
| `sync` 또는 비어있음 | **B (일괄 투고)** | `/blog-write sync`, `/blog-write` |

---

### 모드 A — 단일 글 투고

#### Step 1: 원본 노트 읽기

- 경로 → `mcp__obsidian__read_note`로 직접 읽기
- 키워드 → `mcp__obsidian__search_notes`로 검색 후 후보 표시

#### Step 2: 카테고리 & 메타데이터 결정

내용 분석하여 카테고리 제안:
- `reading` — 책 리뷰, 독후감
- `essay` — 일상, 생각, 에세이
- `dev` — 기술, 코딩, 개발

subcategory도 함께 제안 (아래 매핑 참고):

| category | subcategory | 설명 |
|----------|-------------|------|
| `reading` | `review` | 서평 |
| `reading` | `note` | 독서노트 |
| `essay` | `retrospective` | 회고 |
| `essay` | `diary` | 일기/일상 |
| `dev` | `til` | TIL |
| `dev` | `project` | 프로젝트 |

`AskUserQuestion`으로 확인:

```markdown
## 📝 투고 준비

원본: `{노트 경로}`

- **제목**: {제안}
- **카테고리**: {reading / essay / dev}
- **서브카테고리**: {subcategory 제안}
- **설명**: {한 줄 요약}
- **태그**: {제안}

이대로 진행할까요? 수정할 부분 있으면 알려주세요.
```

#### Step 3: 글 다듬기 (Clarify 방식)

필요한 경우 `AskUserQuestion`으로 하나씩 질문 (ONE question per turn):
- 글의 톤, 추가/삭제할 내용, 핵심 메시지
- (독서) 별점, 추천 대상
- (코딩) 시리즈명

질문은 2~3개 이내로 최소화. "그대로 올려줘"하면 즉시 진행.

#### Step 4: 변환 & 투고

공통 투고 절차(아래)를 따른다.

---

### 모드 B — 일괄 투고 (#publish 스캔)

#### Step 1: #publish 태그 스캔

1. `mcp__obsidian__search_notes`로 `#publish` 태그 노트 검색
2. `#published` 태그가 있는 노트는 제외
3. 블로그에 동일 제목 파일 존재 여부 확인 (중복 방지)

#### Step 2: 후보 목록 & 확인

```markdown
## 📋 투고 대기 노트 ({N}개)

| # | 노트 | 카테고리 | 태그 |
|---|------|---------|------|
| 1 | 050.Etc/051.Diary/독서감상.md | reading | #publish/reading |
| 2 | 020.Growth/021.Study/리액트입문.md | dev | #publish/dev |

어떤 글을 투고할까요? (번호, 전부, 스킵)
```

없으면: "투고할 노트가 없습니다. Obsidian에서 `#publish` 태그를 추가해주세요." 후 종료.

#### Step 3: 카테고리 결정

| Obsidian 태그 | 블로그 카테고리 |
|--------------|---------------|
| `#publish/reading` | `reading` |
| `#publish/essay` | `essay` |
| `#publish/dev` | `dev` |
| `#publish` (단독) | AI가 내용 분석하여 추론 |

#### Step 4: 변환 & 투고

선택된 노트 각각에 대해 공통 투고 절차를 따른다.
투고 완료 후 `mcp__obsidian__manage_tags`로 `#publish` → `#published` 변경.

---

### 공통 투고 절차

**1. frontmatter 생성:**

반드시 `mcp__google-calendar__get-current-time`으로 현재 시각을 확인하여 `pubDate`에 시:분:초까지 포함한다. 미래 시간을 넣으면 글이 표시되지 않으므로 주의한다.

```yaml
---
title: "제목"
description: "한 줄 요약"
category: "reading" | "essay" | "dev"
subcategory: "review" | "note" | "retrospective" | "diary" | "til" | "project"
tags: ["태그1", "태그2"]
pubDate: YYYY-MM-DDTHH:mm:ss
draft: false
# reading: bookTitle, bookAuthor, rating
# dev: series
---
```

**2. Obsidian → Markdown 변환:**
- `[[링크]]` → 일반 텍스트
- `[[링크|표시텍스트]]` → 표시텍스트
- `![[이미지.확장자]]` → 이미지 자동 처리 (아래 참고)
- `![[동영상.확장자]]` → 동영상 자동 처리 (아래 참고)
- `> [!note]` 등 callout → `> **Note:** ...` blockquote
- Obsidian frontmatter → 블로그 frontmatter로 대체

**3. 이미지/동영상 처리:**

이미지 발견 시:
1. `sips -Z 800 --setProperty formatOptions 70` 으로 이미지 최적화 (최대 800px, 품질 70%)
2. `public/images/{category}/` 디렉토리로 복사
3. 마크다운에 `<img src="/images/{category}/{파일명}" alt="{설명}" />` 삽입

동영상 발견 시:
1. `public/videos/` 디렉토리로 복사
2. 마크다운에 `<video src="/videos/{파일명}" controls></video>` 삽입

**4. 파일 저장:**
`/Users/ethankim/WebstormProjects/blog/src/content/posts/{category}/{파일명}.md`

**5. 빌드 & 배포:**
```bash
cd /Users/ethankim/WebstormProjects/blog && pnpm build
git add src/content/posts/ public/images/ public/videos/
git commit -m "Post: {글 제목}" # 또는 "Sync: {N}개 글 투고"
git push
```

**6. 완료 보고:**
```markdown
## ✅ 투고 완료!

- **제목**: {제목}
- **카테고리**: {카테고리}
- **URL**: https://hey00507.github.io/posts/{yyyymmdd}/

배포 완료까지 약 30초 소요.
```

---

### 중복 방지 (일괄 투고)

1. `#published` 태그 → skip
2. 동일 제목 파일 존재 → "이미 투고된 것 같습니다. 업데이트할까요?"
3. 업데이트 시 기존 파일 덮어쓰기 + `updatedDate` 추가

### 주의사항

- 원본 Obsidian 노트 내용은 수정하지 않는다 (태그만 변경)
- `pubDate`는 반드시 `mcp__google-calendar__get-current-time`으로 현재 시각을 조회하여 설정한다. 미래 시간을 넣으면 글이 사이트에 표시되지 않는다.
- 10개 초과 일괄 투고 시 경고 후 확인
- `draft: true` 요청 시 초안으로 저장
- 블로그 repo: `/Users/ethankim/WebstormProjects/blog`
