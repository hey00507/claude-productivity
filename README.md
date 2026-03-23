# Claude Productivity

[Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI에서 사용하는 커스텀 Skills과 Slash Commands 모음입니다.

## Skills

자동으로 트리거되는 스킬입니다. `~/.claude/skills/` 에 배치합니다.

| Skill | 설명 | 파일 |
|-------|------|------|
| **alarm-on** | Claude 응답 완료 시 알림을 켭니다 (hooks 기반) | [SKILL.md](skills/alarm-on/SKILL.md) |
| **alarm-off** | Claude 응답 완료 알림을 끕니다 | [SKILL.md](skills/alarm-off/SKILL.md) |
| **alarm-volume** | 알림 레벨을 설정합니다 (1=무음, 2=기본, 3=사이렌) | [SKILL.md](skills/alarm-volume/SKILL.md) |

## Slash Commands

`/명령어`로 직접 호출하는 커맨드입니다. `~/.claude/commands/` 에 배치합니다.

### 일정 & 루틴

| Command | 설명 | 파일 |
|---------|------|------|
| `/schedule` | 일정 조회·분석·등록·완료 처리 (Google Calendar + Apple Reminders 통합) | [schedule.md](commands/schedule.md) |
| `/daily` | 하루 루틴 — 아침 브리핑 / 저녁 정리 | [daily.md](commands/daily.md) |

### 블로그

| Command | 설명 | 파일 |
|---------|------|------|
| `/blog` | 블로그 현황 조회 + 글 삭제 | [blog.md](commands/blog.md) |
| `/blog-write` | Obsidian 노트를 블로그에 투고 (단일 + #publish 일괄, subcategory 지원) | [blog-write.md](commands/blog-write.md) |
| `/blog-weekly` | 주간 회고 자동 생성 (Claude 로그 + Google Calendar + Apple Reminders 기반) | [blog-weekly.md](commands/blog-weekly.md) |
| `/blog-dev` | 블로그 개발 서버 관리 (시작/중지/재시작/배포) | [blog-dev.md](commands/blog-dev.md) |

### 노트 & 아이디어

| Command | 설명 | 파일 |
|---------|------|------|
| `/obsidian` | 대화 내용을 분석하여 Obsidian vault에 구조화된 노트로 정리 | [obsidian.md](commands/obsidian.md) |
| `/clarify` | 소크라테스식 질문으로 모호한 아이디어를 구체화 → 후속 액션 연결 | [clarify.md](commands/clarify.md) |

### 건강 & 운동

| Command | 설명 | 파일 |
|---------|------|------|
| `/coach` | 개인 PT/러닝 코치 — 체중·식단·러닝·웨이트 기록, 추이 분석, 목표 예측, 코칭 조언 | [coach.md](commands/coach.md) |

### 프로젝트

| Command | 설명 | 파일 |
|---------|------|------|
| `/project-status` | 멀티 프로젝트 현황 한눈에 브리핑 | [project-status.md](commands/project-status.md) |

## 최근 변경사항 (2026-03-17)

- `/schedule` — **모드 D(완료 처리)** 추가: "치과 완료" → 리마인더 체크 + 캘린더 [완료]
- `/blog-write` — **pubDate 시:분:초** 현재 시각 기준 강제, **subcategory** 지원, **이미지/동영상 자동 최적화**
- `/blog-weekly` — **Apple Reminders 완료 항목** + **블로그 포스팅 통계** 수집
- `/obsidian` — 로그 경로 `040.Logs/{연도}/{월}/` 반영, **태그 체계 보강**, 블로그 TIL 연결 제안
- `/clarify` — **후속 액션 연결** (블로그/옵시디언/PRD 분기)
- `/blog-dev` — `npm` → `pnpm` 통일
- `/daily` — **신규** (아침 브리핑 + 저녁 정리 루틴)
- `/project-status` — **신규** (멀티 프로젝트 현황 브리핑)

## 설치

원하는 파일을 복사하여 사용합니다.

```bash
# Skills
cp -r skills/alarm-on ~/.claude/skills/
cp -r skills/alarm-off ~/.claude/skills/
cp -r skills/alarm-volume ~/.claude/skills/

# Commands
cp commands/schedule.md ~/.claude/commands/
cp commands/daily.md ~/.claude/commands/
cp commands/blog.md ~/.claude/commands/
cp commands/blog-write.md ~/.claude/commands/
cp commands/blog-weekly.md ~/.claude/commands/
cp commands/blog-dev.md ~/.claude/commands/
cp commands/clarify.md ~/.claude/commands/
cp commands/obsidian.md ~/.claude/commands/
cp commands/project-status.md ~/.claude/commands/
cp commands/coach.md ~/.claude/commands/
```

> `/schedule`, `/daily`, `/obsidian`, `/coach`는 MCP 서버 연동이 필요합니다 (google-calendar, apple-reminders, obsidian).

## 요구사항

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)
- `/schedule`, `/daily` — [google-calendar MCP](https://github.com/nspady/google-calendar-mcp), [apple-reminders MCP](https://github.com/pzingg/apple-reminders-mcp)
- `/obsidian`, `/blog-write` — [Obsidian Local REST API](https://github.com/coddingtonbear/obsidian-local-rest-api) + Obsidian MCP
- `/blog`, `/blog-write`, `/blog-weekly`, `/blog-dev` — Astro 블로그 프로젝트 (GitHub Pages 배포)

## License

MIT
