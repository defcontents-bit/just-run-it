# just-run-it

AI 채팅이 "이 명령 실행하고 출력 붙여 주세요"라고 하면, 사람이 복사·붙여넣기를 계속 오가게 된다. 이 스킬은 그 중계를 없앤다.

- **Claude Code (셸 있음):** 붙여넣은 안내를 직접 실행하고, 오류는 고쳐 가며 성공 기준 문구가 나올 때까지 돌린다. API 키, 결제, 관리자 권한처럼 사람만 할 수 있는 일만 남기고, 그것도 명령 한 줄로 줄여 준다.
- **claude.ai 채팅 (셸 없음):** 명령을 하나씩 시키지 않고, Claude Code에 한 번만 붙여 넣으면 끝나는 인계 블록을 준다.

When a chat assistant tells you to run commands locally and paste the output back, this skill makes the agent do it instead. With a shell (Claude Code), it runs, fixes, and loops until the stated success output appears, and stops only for secrets, payments, admin rights, or logins. Without a shell (claude.ai chat), it writes one self-contained handoff block to paste into Claude Code.

## 설치

Claude Code:

```bash
npx -y skills add defcontents-bit/just-run-it -g -y
```

claude.ai 채팅·프로젝트: 이 폴더를 zip으로 묶어 설정 → 기능(Capabilities) → 스킬에서 업로드한다.

## 사용 예

채팅이 준 안내를 Claude Code에 그대로 붙여 넣고 "해줘"라고만 쓰면 된다.

```
✅ npm run db:reset — seeded: ...\data\app.db
✅ npm run sync:prices — 기준가 갱신 40/40개 · 기준일 20260930
❌ npm run sync:dart — DART_API_KEY missing (.env)
고친 것: data 폴더가 없어 ENOENT → 스크립트가 폴더를 먼저 만들게 수정
할 일: 프로젝트 폴더에서 Copy-Item .env.example .env; notepad .env → 키 넣고 저장 → "넣었어"
```

## License

MIT
