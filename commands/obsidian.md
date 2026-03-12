---
description: 현재 대화 내용을 분석하여 Obsidian vault에 구조화된 노트로 정리합니다.
allowed-tools: Bash, AskUserQuestion, mcp__obsidian__write_note, mcp__obsidian__patch_note, mcp__obsidian__search_notes, mcp__obsidian__read_note, mcp__obsidian__read_multiple_notes, mcp__obsidian__list_directory
argument-hint: <비워두면 자동 분류>
---

## Obsidian 대화 정리 (/obsidian)

현재까지의 대화 내용을 분석하여 Obsidian vault에 적절한 노트로 정리한다.

### Vault 구조

| 폴더 | 용도 | 태그 예시 |
|------|------|----------|
| `000.Home/` | 인덱스, 홈 대시보드 | - |
| `010.Work/` | 회사/업무 (회의록, 설계서) | `work`, `work/meeting` |
| `020.Growth/` | 자기개발 (공부, 운동, 독서) | `study`, `growth` |
| `030.Plans/` | 계획/일정/약속 | `plan/running`, `plan/event` |
| `040.Logs/` | Claude 대화 로그 | `log` |
| `050.Etc/` | 일기, 사색, 여행, 기타 | `diary`, `etc` |

### 동작 프로세스 (3단계)

#### Step 1: 대화 로그 저장 (자동)
- 대화 내용을 분석하여 `040.Logs/yyyy-MM-dd claude.md`에 **항상** 저장한다.
- 이미 해당 날짜 로그가 있으면 기존 내용을 읽고, **새로운 토픽만 append** (중복 방지)
- 로그에는 대화의 모든 토픽을 요약한다.

#### Step 2: Vault 전체 탐색 및 제안 (대화형)
대화 내용과 관련된 노트를 찾기 위해 **항상** Vault 디렉토리를 탐색한다.

1. `list_directory`로 **최상위 폴더** 목록을 확인한다.
2. 관련 가능성이 있는 **하위 폴더**도 재귀적으로 탐색한다.
   - 예: `010.Work/` → `012.DailyMail/` → 내부 파일 확인
3. `search_notes`로 대화 키워드와 관련된 **기존 노트를 검색**한다.
4. 관련 노트가 발견되면 `read_note`로 내용을 확인하여 **업데이트 가능 여부**를 판단한다.

탐색 결과를 바탕으로 사용자에게 **제안**한다:
- **기존 노트 업데이트** — 관련 내용이 이미 있는 노트
- **신규 노트 생성** — 적절한 노트가 없을 때, 위치와 파일명을 제안
- **해당 없음** — 로그 외에 정리할 내용이 없으면 스킵

**제안 형식** (AskUserQuestion 사용):
```
Vault를 확인했어요. 다음 노트에 정리할 수 있을 것 같아요:

1. [업데이트] 010.Work/012.DailyMail/PRD.md — Discord 채널 분리, 스케줄 변경 반영
2. [신규] 020.Growth/021.Study/yyyy-MM-dd 주제.md — 학습 내용 정리

어떤 것을 진행할까요? (번호, 전부, 스킵)
```

#### Step 3: 사용자 선택에 따라 실행
- 사용자가 선택한 노트만 생성/업데이트한다.
- "전부"면 제안된 모든 노트를 처리한다.
- "스킵"이면 Step 1의 로그만 저장하고 끝낸다.

### 분류 기준

| 대화 내용 | 폴더 | 파일명 패턴 |
|-----------|------|------------|
| 코딩/프로젝트 작업, 버그 수정, 배포 | `040.Logs/` (필수) + `010.Work/하위/` (제안) | 로그: `yyyy-MM-dd claude.md` |
| 일정 등록/변경, 약속, 접수 | `040.Logs/` (필수) + `030.Plans/` (제안) | `yyyy-MM-dd 일정명.md` |
| 업무 회의, 설계, 문서 작업 | `040.Logs/` (필수) + `010.Work/하위/` (제안) | 주제에 맞는 파일명 |
| 공부, 기술 학습, 독서 | `040.Logs/` (필수) + `020.Growth/` (제안) | `yyyy-MM-dd 주제.md` |
| 개인 이야기, 잡담, 기타 | `040.Logs/` (필수) | - |

### 노트 작성 규칙

**공통:**
- 모든 노트에 frontmatter 필수: `tags`, `date`
- 관련 노트는 `## Related` 섹션에 `[[wikilink]]`로 연결
- 기존 노트가 있으면 덮어쓰지 말고 `patch_note`로 업데이트

**040.Logs (대화 로그):**
- 파일명: `yyyy-MM-dd claude.md`
- 형식:
```markdown
---
tags: ["log"]
date: "yyyy-MM-dd"
---

# yyyy-MM-dd Claude 대화 로그

## 토픽 1 제목
- 핵심 내용 요약
- 수행한 작업
- 결과/변경사항

## 토픽 2 제목
...

---
## Related
- [[관련 노트]]
```

**030.Plans (일정/계획):**
- 파일명: `yyyy-MM-dd 일정명.md`
- 형식:
```markdown
---
tags: ["plan/카테고리"]
date: "yyyy-MM-dd"
---

# 일정명

- **날짜**: yyyy-MM-dd
- **시간**: HH:MM ~ HH:MM
- **장소**: (있으면)
- **링크**: (있으면)
- **메모**: 부가 설명

## 연동 상태
- [x/blank] Google Calendar 등록
- [x/blank] Apple Reminders 등록
- [x/blank] Obsidian 기록
```

**010.Work (업무):**
- 기존 하위 폴더 구조에 맞춰 배치 (예: `011.의성마늘/`, `012.DailyMail/`)
- 새 프로젝트면 새 하위 폴더 생성

**020.Growth (성장/학습):**
- 기술 학습 → `021.Study/` 하위에 배치
- 운동/건강 → `020.Growth/` 직하에 배치

### 기존 노트 확인

노트를 생성하기 전에 반드시:
1. `list_directory`로 관련 폴더의 **전체 구조**를 파악한다
2. `search_notes`로 대화 키워드 관련 **기존 노트를 검색**한다
3. 발견된 노트는 `read_note`로 **내용을 확인**하여 업데이트 범위를 결정한다
4. `040.Logs/yyyy-MM-dd claude.md`가 이미 있으면 기존 내용을 읽고 **새로운 토픽만 append** (중복 방지)

### 출력 형식

정리 완료 후 결과를 브리핑:

```markdown
## Obsidian 정리 완료

| 액션 | 파일 | 폴더 |
|------|------|------|
| 생성 | 2026-03-11 일정명.md | 030.Plans/ |
| 업데이트 | 2026-03-11 claude.md | 040.Logs/ |

총 N개 노트 처리 완료.
```
