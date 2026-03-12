---
name: alarm-volume
description: Claude 알림 레벨을 설정합니다 (1=무음, 2=기본, 3=사이렌)
disable-model-invocation: true
argument-hint: [1|2|3]
allowed-tools: Read, Write
---

Claude 알림 소리 레벨을 설정한다. 레벨 값은 `~/.claude/alarm-level` 파일에 저장된다.

## 레벨

| 레벨 | 이름 | 동작 |
|------|------|------|
| 1 | 무음 | 배너(위젯)만 표시, 소리 없음 |
| 2 | 기본 | Glass 효과음 + 배너 (기본값) |
| 3 | 사이렌 | Sosumi 경고음 + beep 3회 + 배너 |

## 동작

1. 인자가 1, 2, 3 중 하나인지 확인한다
2. 인자가 없으면 `~/.claude/alarm-level` 파일을 읽어 현재 레벨을 알려준다. 파일이 없으면 "현재 레벨: 2 (기본)"
3. 유효한 인자면 `~/.claude/alarm-level` 파일에 해당 숫자를 쓴다
4. 완료 후 설정된 레벨을 위 표 기준으로 알려준다. 예: "알림 레벨 3 (사이렌)으로 설정했습니다."
5. 이 설정은 즉시 적용된다 (재시작 불필요)
