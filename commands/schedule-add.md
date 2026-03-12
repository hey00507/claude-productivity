---
description: 일정을 Google Calendar와 Apple Reminders에 동시 등록합니다.
allowed-tools: mcp__google-calendar__create-event, mcp__google-calendar__get-current-time, mcp__apple-reminders__reminders_tasks, mcp__apple-reminders__reminders_subtasks, mcp__apple-reminders__reminders_lists
argument-hint: "<언제 무엇 [URL]> (예: 오늘 오후 2시 팀점심, 내일 10시 팀미팅, 3/15 09:00 치과)"
---

## 일정 등록 (/schedule-add)

사용자가 입력한 `$ARGUMENTS`를 파싱하여 Google Calendar 이벤트 + Apple Reminders 리마인더를 동시에 등록한다.

### 파싱 규칙

`$ARGUMENTS`에서 **날짜/시간**과 **일정명**을 추출한다.

**날짜 해석:**
- `오늘` → 오늘 날짜
- `내일` → 내일 날짜
- `모레` → 모레 날짜
- `이번 주 금요일`, `다음 주 월요일` 등 → 해당 요일로 계산
- `3/15`, `03/15`, `3월 15일` → 해당 날짜 (올해 기준)
- `2026-03-15` → 해당 날짜
- 날짜 생략 시 → 오늘

**시간 해석:**
- `오전 9시`, `09:00`, `9시` → 09:00
- `오후 2시`, `14시`, `14:00` → 14:00
- `오후 2시 30분`, `14:30` → 14:30
- 시간 생략 시 → 종일 이벤트로 등록

**종료 시간:**
- 명시적으로 `~16시`, `~오후 4시` 등 지정 시 해당 시간 사용
- 미지정 시 → 시작 시간 + 1시간

**부가 정보 (description) 해석:**
- URL(http:// 또는 https://로 시작하는 문자열)이 포함되어 있으면 추출
- 일정명 외에 추가 설명, 메모, 상세 내용이 있으면 모두 description으로 처리

**일정명:**
- 날짜/시간을 제외한 텍스트에서 핵심 키워드가 일정명 (짧고 간결하게)

### 리마인더 자동 분류 규칙

일정의 내용을 분석하여 아래 13개 리스트 중 가장 적합한 곳에 자동 분류한다:

| 리스트 | 분류 기준 |
|--------|-----------|
| **Workspace** | 업무, 회의, 프로젝트, 출근, 미팅 |
| **Job Hunting** | 이직, 면접, 이력서, 포트폴리오, 채용 |
| **Study - Work Skills** | CS, 코딩, 개발, 알고리즘, Vibe 코딩, 기술 학습 |
| **Study - Personal Growth** | 부동산, 저축, 재테크, 영어, 자격증 |
| **Classes & Meetups** | 수업, 원데이클래스, 강의, 세미나, 모임 |
| **Explore** | 여행, 전시, 페스티벌, 새로운 경험 |
| **Side Life** | 사이드 프로젝트, 부업, 블로그 |
| **Running** | 러닝, 마라톤, 대회, 달리기 |
| **Workout** | 운동, 헬스, 수영, 등산 (러닝 제외) |
| **Plans** | 약속, 일정, 예약 (병원, 미용실 등) |
| **Daily** | 장보기, 택배, 청소, 세탁 등 일상 투두 |
| **Buying** | 구매, 주문, 쇼핑 |
| **Importants** | 중요, 마감, 긴급, 필수 |

**분류가 애매한 경우:** Plans에 기본 등록하되, 사용자에게 더 적합한 리스트가 있는지 또는 새 리스트 생성이 필요한지 제안한다.

### 알람 자동 등록 규칙

모든 리마인더에 알람을 자동으로 설정한다:

| 이벤트 유형 | 알람 |
|-------------|------|
| 시간 지정 이벤트 | 1시간 전 (-3600) + 10분 전 (-600) |
| 종일 이벤트 | 당일 오전 9시 (absoluteDate) |
| 중요 일정 (Importants) | 전날 오후 9시 + 1시간 전 + 10분 전 |

### 등록 절차

1. `mcp__google-calendar__get-current-time`으로 현재 시각 확인
2. `$ARGUMENTS` 파싱 (날짜, 시간, 일정명, description 추출)
3. 일정 내용을 분석하여 적합한 리마인더 리스트 자동 선택
4. 동시에 실행:
   - Google Calendar에 이벤트 생성 (`mcp__google-calendar__create-event`)
     - calendarId: `일정`
     - timeZone: `Asia/Seoul`
     - description: URL, 부가 설명 포함
   - Apple Reminders에 리마인더 생성 (`mcp__apple-reminders__reminders_tasks`)
     - targetList: 자동 분류된 리스트
     - alarms: 알람 자동 설정
     - note: description 내용 포함
5. 등록 결과를 브리핑

### 출력 형식

```markdown
## ✅ 등록 완료

| 항목 | 내용 |
|------|------|
| 일정 | 일정명 |
| 날짜 | yyyy-MM-dd (요일) |
| 시간 | HH:MM ~ HH:MM |
| 메모 | description 내용 (있을 때만) |
| Google Calendar | ✅ 등록 (일정) |
| Apple Reminders | ✅ 등록 (리스트명) |
| 알람 | 1시간 전, 10분 전 |
```

### 주의사항
- timeZone은 항상 `Asia/Seoul`
- Google Calendar와 Reminders 등록은 병렬로 실행
- 파싱이 애매한 경우 사용자에게 확인하지 말고 최선의 해석으로 등록 (자연어 우선)
- 등록 실패 시 어떤 서비스가 실패했는지 명시
- 리스트 분류가 불가능하면 Plans에 넣되 새 리스트 생성을 제안
