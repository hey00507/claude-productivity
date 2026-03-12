---
name: alarm-on
description: Claude 응답 완료 알림을 켭니다
disable-model-invocation: true
allowed-tools: Read, Edit
---

`~/.claude/settings.json`의 hooks에서 Stop 이벤트를 활성화한다.

## 동작

1. `~/.claude/settings.json`을 읽는다
2. hooks.Stop 배열이 없으면 아래 구조를 추가한다:

```json
"Stop": [
  {
    "matcher": "",
    "hooks": [
      {
        "type": "command",
        "command": "~/.claude/hooks/notify-stop.sh"
      }
    ]
  }
]
```

3. hooks.Stop이 이미 있으면 "이미 켜져 있습니다"라고 알려준다
4. 완료 후 "알림이 켜졌습니다. 재시작 후 적용됩니다." 메시지를 출력한다
