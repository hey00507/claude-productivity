---
description: 블로그를 관리합니다. 현황 조회, 글 삭제 등 블로그 관리가 필요할 때 사용하세요. "블로그 어때?", "글 몇 개야?", "최근에 뭐 썼지?", "이 글 지워줘", "블로그 상태" 등.
allowed-tools: AskUserQuestion, Bash, Read, Glob, Grep
argument-hint: <비워두면 현황 조회 | 글제목/키워드로 삭제>
---

## 블로그 관리 (/blog)

블로그 현황 조회와 글 삭제를 처리한다.

### 1단계: 모드 판별

`$ARGUMENTS`를 보고 모드를 결정한다:

| 패턴 | 모드 | 예시 |
|------|------|------|
| 비어있음 | **A (현황 조회)** | `/blog` |
| 글 제목/키워드 + 삭제 의도 | **B (삭제)** | `/blog 블로그를 시작하며 삭제` |

---

### 모드 A — 현황 조회

1. `/Users/ethankim/WebstormProjects/blog/src/content/posts/` 디렉토리에서 모든 `.md` 파일을 찾는다.
2. 각 파일의 frontmatter에서 `title`, `category`, `pubDate`, `draft`, `tags` 추출.
3. `draft: true`인 글은 "초안"으로 별도 표시.

**출력 형식:**

```markdown
## 📝 블로그 현황

**전체**: N개 (독서 X / 일상 Y / 코딩 Z) | 초안: N개

### 최근 게시글 (최신 5개)
| # | 날짜 | 카테고리 | 제목 | 태그 |
|---|------|---------|------|------|
| 1 | 2026-03-13 | 코딩 | Astro 블로그 만들기 | #astro #tailwind |
| 2 | 2026-03-13 | 일상 | 블로그를 시작하며 | #일상 #블로그 |

### 💡 한마디
(상황에 맞는 메시지)

🔗 https://hey00507.github.io/
```

**독려 메시지 규칙:**
- 글이 3개 이하 → "아직 시작 단계예요! 짧은 글이라도 하나 올려보는 건 어때요?"
- 최근 7일 내 글 없음 → "지난 일주일간 새 글이 없네요. 오늘 하나 써볼까요?"
- 최근 7일 내 글 있음 → "꾸준히 잘 쓰고 계시네요!"
- 10개 이상 → "벌써 N개나 썼어요! 대단한 꾸준함이에요."
- 초안 있음 → "초안이 N개 있어요. 마무리해서 올려볼까요?"

---

### 모드 B — 글 삭제

#### Step 1: 삭제 대상 찾기

검색 경로: `/Users/ethankim/WebstormProjects/blog/src/content/posts/`

- 파일명 정확 일치 → 바로 선택
- 부분 일치/키워드 → 후보 목록 보여주고 선택
- 비어있으면 → 전체 목록 보여주고 선택

#### Step 2: 삭제 확인

`AskUserQuestion`으로 확인:

```markdown
## ⚠️ 삭제 확인

- **제목**: {제목}
- **카테고리**: {카테고리}
- **날짜**: {pubDate}

정말 삭제할까요? (y/n)
```

#### Step 3: 삭제 & 배포

1. 해당 `.md` 파일 삭제
2. `cd /Users/ethankim/WebstormProjects/blog && pnpm build`
3. 빌드 성공 시 git commit & push (커밋 메시지: `Remove post: {글 제목}`)

```markdown
## ✅ 삭제 완료

- **삭제된 글**: {제목}
- 배포가 완료되면 사이트에서 제거됩니다 (약 30초 소요).
```

---

### 주의사항

- 삭제 전 반드시 사용자 확인
- 한 번에 하나의 글만 삭제
- 블로그 repo: `/Users/ethankim/WebstormProjects/blog`
