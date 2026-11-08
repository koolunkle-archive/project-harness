# Project Harness

> Claude Code에서 사용하는 개인 스킬, 커맨드, 서브에이전트와 설정을 백업하고 새 환경에 복원하기 위한 저장소입니다.

## 구성

```text
project-harness/
 ├── global/                    # 사용자 범위 (~/.claude)
 │    ├── skills/
 │    │    ├── archive-import/      # 외부 저장소를 archive 저장소로 옮기는 절차
 │    │    ├── archive-readme/      # archive 저장소 README 작성 규칙
 │    │    ├── commit-history/      # 저장소별 커밋 메시지 규칙과 이력 메시지 재작성
 │    │    ├── deploy-check/        # 배포 전 git, 환경 변수, 빌드/테스트/린트 점검
 │    │    └── summarize-changes/   # 커밋 전 변경 사항 요약과 위험 요소 확인
 │    └── commands/
 │         └── seo.md               # 정적 HTML 사이트의 SEO/GEO 점검 및 수정 (/seo)
 ├── project/                   # 프로젝트 범위 (<repo>/.claude)
 │    ├── commands/
 │    │    └── check.md             # HTML 제목, 깨진 링크, 콘솔 에러 점검 (/check)
 │    └── agents/
 │         └── reviewer.md          # 출시 전 SEO, OG 태그, 링크, 반응형 검수
 └── settings/
      └── settings.example.json     # 공유 가능한 사용자 설정 예시
```

## 복원 방법

### 스킬과 커맨드

```bash
# 사용자 범위
mkdir -p ~/.claude/skills ~/.claude/commands
cp -r global/skills/.   ~/.claude/skills/
cp -r global/commands/. ~/.claude/commands/

# 프로젝트 범위 (대상 저장소 경로로 바꿔서 실행)
mkdir -p "<저장소 경로>/.claude/commands" "<저장소 경로>/.claude/agents"
cp -r project/commands/. "<저장소 경로>/.claude/commands/"
cp -r project/agents/.   "<저장소 경로>/.claude/agents/"
```

### 설정

`settings/settings.example.json`의 항목을 `~/.claude/settings.json`에 직접 병합합니다. 기존 설정을 덮어쓰지 않도록 파일을 통째로 복사하지 않습니다.

| 항목 | 설명 |
| --- | --- |
| `attribution` | 커밋과 PR에 Claude Code 서명(Co-Authored-By)과 세션 링크를 넣지 않도록 설정. 이전 버전과도 호환되도록 `false` 대신 빈 문자열과 `sessionUrl: false`를 사용 |
| `enabledPlugins`, `extraKnownMarketplaces` | 아래 플러그인 목록과 마켓플레이스 |
| `env.CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` | 실험 기능인 에이전트 팀 활성화 |
| `autoUpdatesChannel`, `theme` | 업데이트 채널, 테마 |

`autoMode`, `modelSettings`처럼 프로젝트나 기기에 따라 달라지는 설정은 포함하지 않습니다.

## 플러그인

```bash
claude plugin marketplace add anthropics/claude-plugins-official
claude plugin install frontend-design@claude-plugins-official
claude plugin install postman@claude-plugins-official

# 프로젝트 범위: Expo 프로젝트에서만 설치
claude plugin install expo@claude-plugins-official

# 외부 마켓플레이스
claude plugin marketplace add DietrichGebert/ponytail
claude plugin install ponytail@ponytail

claude plugin marketplace add Leonxlnx/taste-skill
claude plugin install taste-skill@taste-skill
```

| 플러그인 | 용도 |
| --- | --- |
| `frontend-design` | 템플릿처럼 보이지 않는 UI 디자인 가이드 |
| `taste-skill` | 랜딩 페이지·포트폴리오용 디자인 스킬 ([Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill), MIT). 이전에 복사해 두었던 `design-taste-frontend`를 대체 |
| `postman` | API 설계, 테스트, 문서화 |
| `ponytail` | 필요한 최소 변경만 하도록 작업 방식 조정 |
| `expo` | Expo / React Native 개발 (프로젝트 범위) |

## MCP 서버 (사용자 범위)

```bash
claude mcp add --transport http supabase https://mcp.supabase.com/mcp
claude mcp add --transport http claude-design https://api.anthropic.com/v1/design/mcp

# Stitch API 키가 필요합니다. 실제 키는 커밋하지 않습니다.
claude mcp add --transport http stitch https://stitch.googleapis.com/mcp \
  --header "X-Goog-Api-Key: <YOUR_STITCH_API_KEY>"

# Spline 데스크톱 앱이 설치되어 있어야 합니다. 경로는 설치 위치에 맞게 바꿉니다.
claude mcp add Spline --env ELECTRON_RUN_AS_NODE=1 -- \
  "<SPLINE_INSTALL_DIR>\Spline.exe" \
  "<SPLINE_INSTALL_DIR>\resources\spline-mcp.cjs"
```

Windows의 기본 Spline 설치 위치는 `%LOCALAPPDATA%\Programs\Spline`입니다.
