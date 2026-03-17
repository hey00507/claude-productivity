## 프로젝트 현황 브리핑 (/project-status)

진행 중인 프로젝트들의 상태를 한눈에 브리핑한다.

### 1단계: 모드 판별

`$ARGUMENTS`를 보고 모드를 결정한다:

| 패턴 | 모드 | 예시 |
|------|------|------|
| 비어있음 | **A (전체 브리핑)** | `/project-status` |
| 프로젝트명 | **B (상세 조회)** | `/project-status habitflow` |

---

### 모드 A — 전체 브리핑

1. **프로젝트 목록 확인**: `mcp__obsidian__read_note`로 `010.Work/진행중인 프로젝트.md` 읽기

2. **각 프로젝트별 데이터 수집** (병렬):
   - PRD/CLAUDE.md에서 마일스톤 상태 추출
   - `git log --oneline -5`로 최근 커밋 확인
   - 테스트 수 확인 (가능한 경우)

3. **출력**:
```markdown
## 프로젝트 현황

| 프로젝트 | 현재 단계 | 테스트 | 최근 커밋 | 상태 |
|---------|----------|--------|----------|------|
| HabitFlow | Phase 1a (M7) | 35개 | 오늘 | 🟢 활발 |
| CloudPocket | MS5 완료 | 717개 | 어제 | 🟢 활발 |
| Dashboard | Phase 3 | 41개 | 오늘 | 🟢 활발 |
| Blog | - | - | 오늘 | 🟢 활발 |

### 다음 할 일
- **HabitFlow**: M6 히트맵 구현
- **CloudPocket**: MS4 iOS 위젯
- **Dashboard**: v0.3.0 npm publish
```

---

### 모드 B — 상세 조회

특정 프로젝트의 상세 현황:

1. **PRD 읽기**: Obsidian vault에서 해당 프로젝트 PRD 검색
2. **마일스톤 체크리스트**: 완료/미완료 항목 추출
3. **Git 히스토리**: 최근 10개 커밋
4. **테스트 현황**: 테스트 수 + 최근 실행 결과

**출력**:
```markdown
## HabitFlow 상세 현황

### 마일스톤
| MS | 이름 | 상태 | 완료일 |
|----|------|------|--------|
| M1 | 프로젝트 초기화 | ✅ | 03/17 |
| M2 | 데이터 모델 | ✅ | 03/17 |
| ...
| M6 | 히트맵 | ⏳ | - |

### 테스트: 35개 (전체 통과)
### 최근 커밋
- b6d652a Feat: M7 Streak 계산 + TodayView 표시
- ...
```

---

### 프로젝트 경로 매핑

| 프로젝트 | 로컬 경로 | PRD 경로 (Obsidian) |
|---------|----------|-------------------|
| HabitFlow | `~/WebstormProjects/habitflow` | `010.Work/016.HabitFlow/HabitFlow-PRD.md` |
| CloudPocket | `~/XcodeProjects/CloudPocket` | `010.Work/015.CloudPocket/CloudPocket-PRD.md` |
| Dashboard | `~/WebstormProjects/claude-dashboard` | `022.ClaudeProductivity/ClaudeDashboard/` |
| Blog | `~/WebstormProjects/blog` | - |

### 상태 판별 기준

| 아이콘 | 기준 |
|--------|------|
| 🟢 활발 | 7일 내 커밋 있음 |
| 🟡 일시정지 | 7~30일 내 커밋 |
| 🔴 중단 | 30일+ 커밋 없음 |

### 주의사항
- 경로가 존재하지 않으면 해당 프로젝트 스킵
- PRD가 없으면 CLAUDE.md 또는 README.md에서 정보 추출
