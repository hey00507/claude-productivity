## 하루 루틴 (/daily)

하루 시작과 마감을 정리하는 루틴 스킬이다.

### 1단계: 모드 판별

`$ARGUMENTS`를 보고 모드를 결정한다:

| 패턴 | 모드 | 예시 |
|------|------|------|
| 비어있음 / `morning` / `아침` | **A (아침 브리핑)** | `/daily`, `/daily 아침` |
| `evening` / `저녁` / `마감` / `정리` | **B (저녁 정리)** | `/daily 저녁`, `/daily 정리` |

---

### 모드 A — 아침 브리핑

1. **현재 시각 확인**: `mcp__google-calendar__get-current-time` (Asia/Seoul)

2. **오늘 일정 조회** (병렬):
   - `mcp__google-calendar__list-events` — 오늘 캘린더
   - `mcp__apple-reminders__reminders_tasks` — 오늘 마감 리마인더
   - `mcp__apple-reminders__reminders_tasks` — 기한 없는 Daily 리스트
   - `mcp__apple-reminders__reminders_tasks` — 밀린(overdue) 리마인더

3. **어제 미완료 확인**:
   - `mcp__obsidian__read_note`로 어제 로그 확인
   - "다음 할 일" 섹션에서 미완료 항목 추출

4. **출력**:
```markdown
## 좋은 아침! (N월 D일 요일)

### 오늘 일정
| 시간 | 일정 |
|------|------|
| ... | ... |

### 밀린 항목
- (있으면 표시)

### 어제 이어서 할 것
- (어제 로그의 "다음 할 일"에서 추출)

### 리마인더
| 리스트 | 항목 | 마감 |
|--------|------|------|
| ... | ... | ... |
```

---

### 모드 B — 저녁 정리

1. **현재 시각 확인**: `mcp__google-calendar__get-current-time` (Asia/Seoul)

2. **오늘 데이터 수집** (병렬):
   - `mcp__google-calendar__list-events` — 오늘 캘린더
   - `mcp__apple-reminders__reminders_tasks` — 오늘 마감 리마인더 (완료/미완료)
   - `mcp__obsidian__read_note` — 오늘 대화 로그
   - 블로그 포스트 확인 — 오늘 작성한 글 있는지

3. **내일 프리뷰**:
   - `mcp__google-calendar__list-events` — 내일 캘린더
   - `mcp__apple-reminders__reminders_tasks` — 내일 마감 리마인더

4. **출력**:
```markdown
## 오늘 하루 정리 (N월 D일 요일)

### 완료한 것
- (캘린더 + 리마인더 완료 항목 + 로그에서 추출)

### 미완료 / 내일로 이월
- (미완료 리마인더, 로그의 미완료 항목)

### 오늘 작성한 글
- (블로그 포스트, Obsidian 노트)

### 내일 프리뷰
| 시간 | 일정 |
|------|------|
| ... | ... |
```

5. **Obsidian 로그 업데이트 제안**:
   - 오늘 로그가 없으면 → `/obsidian` 제안
   - 있으면 → "다음 할 일" 섹션 업데이트 제안

---

### 주의사항

- 아침/저녁 판별이 애매하면 현재 시각으로 자동 결정 (12시 이전 → 아침, 이후 → 저녁)
- 밀린 항목은 강조 표시
- 내일 프리뷰에서 이른 일정이 있으면 알림
