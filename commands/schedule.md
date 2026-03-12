---
description: 구글 캘린더 일정을 조회하고 브리핑합니다. 오늘/이번주/특정 기간의 일정을 정리해줍니다.
allowed-tools: mcp__google-calendar__list-events, mcp__google-calendar__search-events, mcp__google-calendar__get-current-time, mcp__apple-reminders__reminders_tasks, mcp__apple-reminders__reminders_lists, mcp__obsidian__search_notes, mcp__obsidian__read_note
argument-hint: <today|week|tomorrow|날짜 또는 기간>
---

## 일정 브리핑 (/schedule)

사용자의 Google Calendar 일정과 Apple Reminders를 조회하여 깔끔하게 브리핑한다.

### 동작 방식

**모드 A: 기간 브리핑** (인자가 기간/날짜인 경우)

1. `$ARGUMENTS`를 확인하여 조회 기간을 결정한다:
   - 비어있거나 `today` → 오늘
   - `tomorrow` → 내일
   - `yesterday` → 어제
   - `week` → 이번 주 (월~일)
   - `last week` → 지난 주
   - `month` → 이번 달
   - 특정 날짜 (예: 3/5, 2026-02-15) → 해당 날짜
   - 기간 (예: 3/1~3/10) → 해당 기간
2. `mcp__google-calendar__get-current-time`으로 현재 시각 확인
3. `mcp__google-calendar__list-events`로 해당 기간의 이벤트 조회
4. `mcp__apple-reminders__reminders_tasks`로 미완료 리마인더 조회 (dueWithin 또는 전체)
5. 결과를 시간순으로 정리하여 브리핑
6. 과거 일정은 "지난 일정", 오늘 이후는 "예정 일정"으로 구분

**모드 B: 특정 일정 상세 조회** (인자가 일정 이름/키워드인 경우)

사용자가 특정 일정에 대해 물어보면 (예: `/schedule 마라톤`, `/schedule 팀미팅`):
1. `mcp__google-calendar__search-events`로 키워드 검색
2. `mcp__apple-reminders__reminders_tasks`로 키워드 검색 (search 파라미터)
3. Obsidian vault에서 키워드로 노트 검색 (search_notes)
4. 세 소스의 결과를 종합하여 해당 일정의 디테일 브리핑

### Apple Reminders 조회 규칙

13개 리스트 전체를 조회한다:
- Workspace, Job Hunting, Study - Work Skills, Study - Personal Growth
- Classes & Meetups, Explore, Side Life
- Running, Workout
- Plans, Daily, Buying, Importants

리마인더 조회 시 리스트별로 그룹핑하여 표시한다.

### 출력 형식

```markdown
## 📅 오늘의 일정 (yyyy-mm-dd)

### 캘린더
| 시간 | 일정 | 캘린더 |
|------|------|--------|
| 10:00 | 팀 미팅 | 일정 |
| 14:00 | 치과 예약 | 일정 |

### 리마인더 (미완료)
| 리스트 | 항목 | 마감 |
|--------|------|------|
| 🏃 Running | 동아마라톤 10K | 03/15 06:00 |
| ✅ Daily | 장보기 | 기한없음 |

### 요약
- 오늘 총 N개의 일정이 있습니다.
- 가장 가까운 일정: HH:MM 일정명
```

### 모드 B 출력 형식 (키워드 조회)

```markdown
## 🔍 "키워드" 관련 일정

### 캘린더 이벤트
| 날짜 | 시간 | 일정 | 상태 |
|------|------|------|------|
| 03/15 | 06:00 | 동아마라톤 10K | 예정 |

### 리마인더
| 리스트 | 항목 | 마감 | 상태 |
|--------|------|------|------|
| Running | 동아마라톤 10K - 광화문 도착 | 03/15 06:00 | 미완료 |

### 관련 Obsidian 노트
- [[노트명]] (폴더/)
```

### 모드 B 키워드 검색 규칙
- `running` → 러닝, 마라톤, 달리기, running, marathon 등으로 확장 검색
- `work` → 회의, 프로젝트, 업무 + Obsidian `010.Work/` 폴더 노트 포함
- 그 외 키워드 → 캘린더/리마인더/Obsidian에서 해당 키워드 직접 검색

### 주의사항
- timeZone은 항상 `Asia/Seoul`
- 일정이 없으면 "등록된 일정이 없습니다." 출력
- 시간순 정렬 필수
- 과거 일정은 상태를 "완료"로 표시
