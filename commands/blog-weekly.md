---
description: 이번 주 활동을 기반으로 주간 회고 블로그 글 초안을 자동 생성합니다. "주간 회고 써줘", "이번주 정리", "회고 글 만들어줘" 등 주간 회고를 블로그에 올리고 싶을 때 사용하세요.
allowed-tools: AskUserQuestion, Bash, Read, Write, Edit, Glob, Grep, mcp__obsidian__read_note, mcp__obsidian__write_note, mcp__obsidian__search_notes, mcp__obsidian__list_directory, mcp__google-calendar__list-events, mcp__google-calendar__get-current-time, mcp__apple-reminders__reminders_tasks
argument-hint: [선택] 특정 주 지정 (예: last, 2026-03-10)
---

## 주간 회고 자동 생성 (/blog-weekly)

Claude 대화 로그 + Google Calendar를 기반으로 주간 회고 블로그 글 초안을 생성한다.

### 동작 방식

#### Step 1: 주간 범위 결정

1. `mcp__google-calendar__get-current-time`으로 현재 날짜 확인
2. `$ARGUMENTS`에 따라 주간 범위 결정:
   - 비어있거나 `this` → 이번 주 (월~일)
   - `last` → 지난 주 (월~일)
   - 특정 날짜 (예: `2026-03-10`) → 해당 날짜가 포함된 주
3. 주차 번호 계산 (예: "2026년 11주차")

#### Step 2: 데이터 수집

**Obsidian 로그 수집:**
1. `mcp__obsidian__list_directory`로 `040.Logs/` 폴더 확인
2. 해당 주에 해당하는 `yyyy-mm-dd claude.md` 파일들을 `mcp__obsidian__read_note`로 읽기
3. 각 로그에서 주요 토픽과 작업 내용 추출

**Google Calendar 일정 조회:**
1. `mcp__google-calendar__list-events`로 해당 주 일정 조회 (timeZone: Asia/Seoul)
2. 주요 일정 목록 정리

**Apple Reminders 완료 항목 수집:**
1. `mcp__apple-reminders__reminders_tasks`로 해당 주에 완료된 리마인더 조회 (showCompleted: true)
2. 리스트별로 완료 항목 정리 (Running, Workspace, Study 등)

**블로그 포스팅 확인:**
1. `/Users/ethankim/WebstormProjects/blog/src/content/posts/` 에서 해당 주 pubDate의 글 확인
2. 작성한 블로그 글 목록 추출

#### Step 3: 회고 초안 생성

수집한 데이터를 기반으로 회고 글 초안을 작성한다:

```markdown
---
title: "2026년 {N}주차 회고"
description: "{시작일} ~ {종료일} 이번 주를 돌아보며"
category: essay
subcategory: retrospective
tags: ["회고", "주간회고"]
pubDate: {현재시각 — get-current-time으로 확인, 시:분:초 포함}
---

## 이번 주 한 일
- (로그에서 자동 추출)

## 주요 일정
- (Google Calendar에서 추출)

## 완료한 리마인더
- (Apple Reminders 완료 항목 — 리스트별 정리)

## 블로그 포스팅
- (해당 주에 작성한 글 목록)

## 배운 것
- (로그에서 학습/트러블슈팅 내용 추출)

## 느낀 점
- (AI가 활동 패턴을 분석하여 초안 작성)

## 다음 주 계획
- (사용자 입력 대기)

---
## 한 줄 회고
- (사용자 입력 대기)
```

#### Step 4: 사용자 확인 & 수정

`AskUserQuestion`으로 초안을 보여준다:

```markdown
## 📝 주간 회고 초안 ({N}주차)

{초안 전체 내용}

---

이대로 투고할까요? 수정할 부분이 있으면 알려주세요.
- **다음 주 계획**과 **한 줄 회고**는 직접 입력해주세요.
- `draft`로 저장하려면 "초안으로" 라고 답해주세요.
```

사용자 수정을 반영하고 최종 확인 후 진행.

#### Step 5: 투고

1. 파일 저장: `/Users/ethankim/WebstormProjects/blog/src/content/posts/essay/{주차}-회고.md`
2. 빌드: `cd /Users/ethankim/WebstormProjects/blog && pnpm build`
3. 빌드 성공 시:
   ```bash
   git add src/content/posts/essay/
   git commit -m "Post: 2026년 {N}주차 회고"
   git push
   ```

#### Step 6: 완료 보고

```markdown
## ✅ 주간 회고 투고 완료!

- **제목**: 2026년 {N}주차 회고
- **기간**: {시작일} ~ {종료일}
- **URL**: https://hey00507.github.io/posts/{yyyymmdd}/

배포 완료까지 약 30초 소요.
```

### 주의사항

- 로그가 없는 주는 "이번 주 로그가 없습니다. 직접 작성하시겠습니까?" 질문
- Google Calendar 조회 실패 시 로그만으로 진행
- `draft: true` 옵션 지원 (사용자가 "초안으로" 요청 시)
- 이미 해당 주차 회고가 존재하면 "이미 작성됨. 업데이트할까요?" 확인
- 블로그 repo 경로: `/Users/ethankim/WebstormProjects/blog`
