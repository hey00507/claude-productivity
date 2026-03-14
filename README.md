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

| Command | 설명 | 파일 |
|---------|------|------|
| `/schedule` | 일정 조회·분석·등록 (Google Calendar + Apple Reminders 통합) | [schedule.md](commands/schedule.md) |
| `/blog` | 블로그 현황 조회 + 글 삭제 | [blog.md](commands/blog.md) |
| `/blog-write` | Obsidian 노트를 블로그에 투고 (단일 + #publish 일괄 투고) | [blog-write.md](commands/blog-write.md) |
| `/blog-weekly` | 주간 회고 자동 생성 (Claude 로그 + Google Calendar 기반) | [blog-weekly.md](commands/blog-weekly.md) |
| `/clarify` | 소크라테스식 질문으로 모호한 아이디어를 구체화 | [clarify.md](commands/clarify.md) |
| `/obsidian` | 대화 내용을 분석하여 Obsidian vault에 구조화된 노트로 정리 | [obsidian.md](commands/obsidian.md) |

## 설치

원하는 파일을 복사하여 사용합니다.

```bash
# Skills
cp -r skills/alarm-on ~/.claude/skills/
cp -r skills/alarm-off ~/.claude/skills/
cp -r skills/alarm-volume ~/.claude/skills/

# Commands
cp commands/schedule.md ~/.claude/commands/
cp commands/blog.md ~/.claude/commands/
cp commands/blog-write.md ~/.claude/commands/
cp commands/blog-weekly.md ~/.claude/commands/
cp commands/clarify.md ~/.claude/commands/
cp commands/obsidian.md ~/.claude/commands/
```

> `/schedule`, `/obsidian`은 MCP 서버 연동이 필요합니다 (google-calendar, apple-reminders, obsidian).

## 요구사항

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)
- `/schedule` — [google-calendar MCP](https://github.com/nspady/google-calendar-mcp), [apple-reminders MCP](https://github.com/pzingg/apple-reminders-mcp)
- `/obsidian`, `/blog-write` — [Obsidian Local REST API](https://github.com/coddingtonbear/obsidian-local-rest-api) + Obsidian MCP
- `/blog`, `/blog-write`, `/blog-weekly` — Astro 블로그 프로젝트 (GitHub Pages 배포)

## License

MIT
