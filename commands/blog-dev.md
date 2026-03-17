---
description: 블로그 개발 서버를 관리합니다. 시작, 중지, 재시작, 배포를 처리합니다. "블로그 서버 켜줘", "dev 서버 꺼", "블로그 재시작", "블로그 배포해줘", "deploy" 등.
allowed-tools: Bash
argument-hint: <start | stop | restart | deploy | 비워두면 상태 확인>
---

## 블로그 개발/배포 (/blog-dev)

블로그 개발 서버 관리와 배포를 처리한다.

- **블로그 경로**: `/Users/ethankim/WebstormProjects/blog`
- **dev 서버 포트**: `4321`
- **사이트 URL**: `https://hey00507.github.io/`

### 1단계: 모드 판별

`$ARGUMENTS`를 보고 모드를 결정한다:

| 패턴 | 모드 |
|------|------|
| 비어있음 / `status` | **A (상태 확인)** |
| `start` / 켜 / 시작 / 구동 | **B (시작)** |
| `stop` / 꺼 / 중지 / 종료 | **C (중지)** |
| `restart` / 재시작 / 다시 | **D (재시작)** |
| `deploy` / 배포 / 올려 / 푸시 | **E (배포)** |

---

### 모드 A — 상태 확인

```bash
lsof -i :4321 -P -n 2>/dev/null | head -5
```

**포트 사용 중** → "dev 서버 실행 중 (http://localhost:4321/)"
**포트 미사용** → "dev 서버가 꺼져 있습니다. `/blog-dev start`로 시작하세요."

---

### 모드 B — 시작

1. 포트 4321이 이미 사용 중인지 확인
2. 사용 중이면 → "이미 실행 중입니다 (http://localhost:4321/)"
3. 미사용이면:

```bash
cd /Users/ethankim/WebstormProjects/blog && pnpm dev &
```

3초 대기 후 포트 확인하여 성공 여부 출력:

```
✅ dev 서버 시작 → http://localhost:4321/
```

---

### 모드 C — 중지

```bash
kill $(lsof -ti :4321) 2>/dev/null
```

**프로세스 있었음** → "dev 서버를 종료했습니다."
**프로세스 없었음** → "실행 중인 dev 서버가 없습니다."

---

### 모드 D — 재시작

1. 모드 C (중지) 실행
2. 1초 대기
3. 모드 B (시작) 실행

```
🔄 dev 서버 재시작 → http://localhost:4321/
```

---

### 모드 E — 배포

1. dev 서버가 실행 중이면 그대로 두고 별도로 빌드한다 (dev 서버를 끄지 않는다).
2. 빌드:

```bash
cd /Users/ethankim/WebstormProjects/blog && pnpm build
```

3. 빌드 성공 시 git add, commit, push:

```bash
cd /Users/ethankim/WebstormProjects/blog && git add -A && git commit -m "Deploy: $(date '+%Y-%m-%d %H:%M')" && git push
```

4. 결과 출력:

**성공:**
```
✅ 배포 완료
- 빌드: 성공
- 푸시: 완료
- 🔗 https://hey00507.github.io/ (반영까지 약 30초~1분)
```

**빌드 실패:**
```
❌ 빌드 실패
(에러 메시지 요약)
```

---

### 주의사항

- 항상 포트 4321을 사용한다.
- 다른 포트에 블로그 서버가 떠있으면 종료 후 4321로 재시작한다.
- 배포 시 빌드 에러가 있으면 푸시하지 않는다.
- dev 서버는 background로 실행한다 (`run_in_background` 또는 `&`).
