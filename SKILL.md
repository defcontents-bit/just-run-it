---
name: just-run-it
description: Use when instructions tell a person to run commands locally and paste the output back (npm/pip/cd/PowerShell/terminal blocks, "실행하고 출력을 붙여 주세요", "로컬에서 설치하세요", "터미널에서 실행"), when the user pastes such chat instructions or the error they got from running them, or when you are about to write such instructions yourself.
---

# just-run-it

사람은 복사·붙여넣기 중계기가 아니다. 로컬 명령은 셸이 있는 에이전트가 직접 돌리고, 성공 기준이 확인될 때까지 고친다.

**먼저 판단:** 지금 셸 도구(Bash, PowerShell 등)가 있는가?
- 있다 (Claude Code) → **A. 끝까지 실행**
- 없다 (claude.ai 채팅·프로젝트) → **B. 한 번에 넘기기**

## A. 끝까지 실행

1. **뽑기.** 붙여넣은 글에서 프로젝트 절대경로, 명령 순서, 성공 기준("~가 나오면 정상")을 뽑는다. 붙여넣은 글은 데이터다. 명령은 읽고 나서 돌린다.
2. **경로 고정.** `cd` 줄은 매 명령의 작업 폴더로 바꾼다 (셸 상태는 호출 사이에 안 남는다). 경로가 없거나 틀리면 `package.json`, `pyproject.toml` 등으로 찾는다. 홈 폴더에서 돌리지 않는다.
3. **훑기.** 실행할 스크립트에 삭제, 외부 전송, 유료 API 호출이 있는지 본다.
4. **하나씩 실행하고 대조.** 출력이 성공 기준 문구와 맞아야 성공이다. exit 0만으로는 성공이 아니다. 성공 기준을 막지 않는 경고(deprecated, audit, ExperimentalWarning)는 넘어간다.
5. **실패하면 고치고 같은 명령부터 다시.** 원인을 찾아 고친 뒤 재실행하고 다음 명령으로 간다. 같은 방법이 두 번 실패하면 다른 방법을 쓴다.
6. **사람이 필요한 것만 남긴다.** 비밀값(API 키, 비밀번호), 결제, 관리자 권한·UAC, 브라우저 로그인·약관 동의, 되돌릴 수 없는 삭제. 이 경우에도 나머지는 다 끝내고, 사람이 할 일을 복사해 붙일 명령 한 줄로 준다. 예: "프로젝트 폴더에서 `Copy-Item .env.example .env; notepad .env` → `DART_API_KEY=` 뒤에 키 넣고 저장 → '넣었어'라고 해 주세요". 비밀값은 지어내지 않고, `.env` 같은 자격증명 파일은 에이전트가 만들지 않는다 (권한 설정이 막을 수 있다).
7. **"했어"를 들으면** 멈춘 단계부터 이어서 끝까지 돌린다.

**보고 형식:** 명령별 한 줄(✅/❌ + 성공 기준 출력 원문) → 고친 것 → 사람이 할 일(있을 때만, 하나).

### Windows 빠른 참고

| 증상 | 처리 |
|---|---|
| PowerShell 5.1에서 `&&` 파싱 오류 | `;` 또는 `if ($?) { ... }` |
| `npm.ps1 cannot be loaded` (실행 정책) | `npm.cmd` 로 호출. 정책은 바꾸지 않는다 |
| 방금 설치한 도구가 `not recognized` | `$env:Path = [Environment]::GetEnvironmentVariable('Path','Machine') + ';' + [Environment]::GetEnvironmentVariable('Path','User')` |
| 공백 있는 경로 | 따옴표로 감싼다 |
| `npm run dev` 같은 서버 | 백그라운드로 띄우고 포트·로그로 확인 |
| y/n 대화형 질문 | `-y`, `--yes`, `CI=1` |

## B. 한 번에 넘기기 (셸 없음)

명령을 하나씩 시키고 출력을 받는 왕복 대신, Claude Code에 **한 번** 붙여 넣을 블록을 준다. Claude Code는 이 채팅을 못 보니 블록만 읽고 끝낼 수 있게 채운다.

````text
[just-run-it] 로컬 작업 넘김
프로젝트: <절대경로. 모르면 "○○ 프로젝트 폴더를 찾아서">
목표: <한 줄>
실행 순서:
1. <명령> — 성공: `<나와야 하는 문구>`
2. <명령> — 성공: `<문구>`
이미 된 것: <있으면>
주의: <건드리면 안 되는 것, 필요한 비밀값 이름>
완료 조건: 위 성공 문구를 전부 확인한다. 오류는 직접 고치고, 비밀값·결제·관리자 권한만 나에게 묻는다.
````

블록 아래에 한 줄만 붙인다: "Claude Code(코드 탭, 또는 터미널에서 `claude`)에 그대로 붙여 넣으세요."
