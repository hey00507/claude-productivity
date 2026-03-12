---
name: alarm-off
description: Claude 응답 완료 알림을 끕니다
disable-model-invocation: true
allowed-tools: Read, Edit
---

`~/.claude/settings.json`의 hooks에서 Stop 이벤트를 비활성화한다.

## 동작

1. `~/.claude/settings.json`을 읽는다
2. hooks.Stop 배열이 있으면 해당 키를 제거한다 (Notification 등 다른 hook은 유지)
3. hooks.Stop이 없으면 "이미 꺼져 있습니다"라고 알려준다
4. hooks 객체가 비어있으면 hooks 키 자체는 유지한다
5. 완료 후 "알림이 꺼졌습니다. 재시작 후 적용됩니다." 메시지를 출력한다
