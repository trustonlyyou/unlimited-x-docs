# unlimited-x 문서 저장소

JUST SHOOT 재구축(unlimited-x)의 설계·결정·작업 지시·진행 상황을 두는 저장소다. 사무실과 자택, Claude와 Codex 어디서 열어도 같은 상태로 이어서 일하기 위한 곳이다. 코드는 다른 저장소에 둔다.

| 저장소 | 역할 | 상태 |
| --- | --- | --- |
| `unlimited-x-docs` (이 저장소) | 설계·결정·작업 지시·보고·작업현황 | 뼈대 생성 |
| `unlimited-x-api` | 서버: 공통 도메인, 서비스 API, 관리자 API, 워커 | 미생성 |
| `unlimited-x-web` | 사용자 화면 + iOS·Android 앱(Capacitor) | 미생성 |
| `unlimited-x-admin` | 관리자 화면 | 미생성 |

기존 서비스 `just-shoot`은 참조용이다. 수정하지 않는다.

## 구조

```
unlimited-x-docs/
  README.md          ← 이 파일. 사람이 읽는 안내
  AGENTS.md          ← AI 공통 규칙 (Codex가 직접 읽음)
  CLAUDE.md          ← AGENTS.md를 불러오는 한 줄 (Claude가 읽음)
  작업현황.md         지금 어디까지 됐고 다음에 뭘 할지
  결정기록.md         왜 그렇게 정했는지 (날짜순으로 덧붙임)
  0.설계/
    아키텍처.md       전체 구조의 결론 (덮어씀)
    기능/             기능별 명세, 기능당 1개 (덮어씀)
  1.작업/
    지시서/           구현 세션에 줄 작업
    보고서/           구현 세션의 결과
  sessions/          AI 세션 내보내기 (근거를 찾을 때만 봄)
  etc/               그날 넘기는 메모, 전달용 파일
```

**설계**는 결론, **결정기록**은 이유, **작업현황**은 현재 위치다. 새 AI 세션은 이 셋을 읽으면 이어갈 수 있다.

## 두 환경

| 장소 | 프로젝트 루트 |
| --- | --- |
| 사무실 (Windows) | `D:\Users\project\` |
| 자택 | `~/project/` |

문서의 경로는 사무실 기준이다. 자택에서는 `D:\Users\project\`를 `~/project/`로 바꿔 읽는다. 루트 아래 폴더 이름은 양쪽이 같아야 한다.

## 두 도구 (Claude, Codex)

- 규칙은 `AGENTS.md` 한곳에만 쓴다. Codex는 `AGENTS.md`를, Claude는 `CLAUDE.md`를 자동으로 읽고, `CLAUDE.md`는 `AGENTS.md`를 그대로 불러온다.
- 두 도구는 서로의 대화와 기억을 모른다. 같은 도구도 다른 컴퓨터의 대화를 모른다. 넘길 내용은 반드시 `작업현황.md`와 보고서에 남긴다.
- 한 저장소에서는 한 번에 한 세션만 파일을 고친다. 두 도구를 동시에 같은 저장소에 붙이지 않는다.
- 보고서에는 어떤 도구로 작업했는지 적는다.

## 작업 흐름

1. docs 세션이 `0.설계/`에 명세를 쓰고 `1.작업/지시서/`에 저장소별 지시서를 만든다.
2. 서버와 화면이 함께 걸린 기능은 API 계약부터 정한다.
3. 구현 세션(api, web, admin)에 지시서 번호로 작업을 요청한다.
4. 구현 세션이 `1.작업/보고서/`에 결과를 남긴다.
5. docs 세션이 보고서를 읽고 명세와 `작업현황.md`를 갱신한다.

세션에 보낼 말은 `작업현황.md` 맨 위에 있다.

## 매번 작업을 시작할 때

다른 곳에서 올린 것을 먼저 받는다. 받기 전에 작업을 시작하지 않는다.

```bash
cd ~/project          # 사무실(Git Bash)은 cd /d/Users/project
for d in unlimited-x-*; do echo "== $d"; git -C $d status -sb | head -1; git -C $d pull --ff-only; done
```

받은 뒤 `작업현황.md`의 현재 상태와 다음 할 일을 읽는다.

## 매번 작업을 끝낼 때

문서를 먼저 갱신하고, 코드와 문서를 같이 올린다. 커밋하지 않은 변경을 남기고 자리를 뜨지 않는다.

1. docs 세션에 "오늘 한 일을 작업현황.md와 결정기록.md에 반영해 줘"라고 한다.
2. 코드 저장소를 커밋하고 푸시한다.
3. 마지막에 이 저장소를 커밋하고 푸시한다.
4. 확인한다.

```bash
cd ~/project
for d in unlimited-x-*; do echo "== $d"; git -C $d status -sb | head -1; done
```

## 처음 한 번

로컬 커밋까지 되어 있고 GitHub에는 아직 올리지 않았다. GitHub에 빈 저장소를 만든 뒤 연결한다.

```bash
cd /d/Users/project/unlimited-x-docs
git remote add origin <GitHub 저장소 주소>
git push -u origin main
```

이 컴퓨터에는 git 작성자 설정이 없다. 직접 커밋하려면 이 저장소에 한 번 설정한다.

```bash
git config user.name "<이름>"
git config user.email "<이메일>"
```

다른 컴퓨터에서는 받아서 시작한다.

```bash
cd ~/project
git clone <GitHub 저장소 주소> unlimited-x-docs
```
