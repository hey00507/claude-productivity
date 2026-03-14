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
| `/schedule` | Google Calendar + Apple Reminders 일정을 조회·분석하고, 필요하면 등록까지 합니다 | [schedule.md](commands/schedule.md) |
| `/clarify` | 소크라테스식 질문을 통해 모호한 아이디어를 구체화합니다 | [clarify.md](commands/clarify.md) |
| `/obsidian` | 현재 대화 내용을 분석하여 Obsidian vault에 구조화된 노트로 정리합니다 | [obsidian.md](commands/obsidian.md) |
| `/blog` | 블로그 현황 조회 — 전체 통계 + 최근 5개 글 + 독려 메시지 | [blog.md](commands/blog.md) |
| `/blog-write` | Obsidian 노트를 블로그에 투고 — 카테고리/태그 자동 매칭 + clarify 방식 다듬기 | [blog-write.md](commands/blog-write.md) |
| `/blog-del` | 블로그 게시글 삭제 — 확인 후 삭제 + 자동 재배포 | [blog-del.md](commands/blog-del.md) |
| `/blog-sync` | Obsidian vault에서 `#publish` 태그 노트를 일괄 스캔하여 블로그에 자동 투고합니다 | [blog-sync.md](commands/blog-sync.md) |
| `/blog-weekly` | 이번 주 활동을 기반으로 주간 회고 블로그 글 초안을 자동 생성합니다 | [blog-weekly.md](commands/blog-weekly.md) |

## 설치

원하는 파일을 복사하여 사용합니다.

```bash
# Skills
cp -r skills/alarm-on ~/.claude/skills/
cp -r skills/alarm-off ~/.claude/skills/
cp -r skills/alarm-volume ~/.claude/skills/

# Commands
cp commands/schedule.md ~/.claude/commands/
cp commands/clarify.md ~/.claude/commands/
cp commands/obsidian.md ~/.claude/commands/
cp commands/blog.md ~/.claude/commands/
cp commands/blog-write.md ~/.claude/commands/
cp commands/blog-del.md ~/.claude/commands/
cp commands/blog-sync.md ~/.claude/commands/
cp commands/blog-weekly.md ~/.claude/commands/
```

> `/schedule`, `/obsidian`은 MCP 서버 연동이 필요합니다 (google-calendar, apple-reminders, obsidian).

## 요구사항

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)
- `/schedule` — [google-calendar MCP](https://github.com/nspady/google-calendar-mcp), [apple-reminders MCP](https://github.com/pzingg/apple-reminders-mcp)
- `/obsidian`, `/blog-write`, `/blog-sync` — [Obsidian Local REST API](https://github.com/coddingtonbear/obsidian-local-rest-api) + Obsidian MCP
- `/blog`, `/blog-write`, `/blog-del`, `/blog-sync`, `/blog-weekly` — Astro 블로그 프로젝트 (GitHub Pages 배포)

## License

MIT
