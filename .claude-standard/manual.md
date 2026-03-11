# .claude-standard 적용 매뉴얼

> 이 문서는 `.claude-standard/`의 각 파일을 프로젝트에 맞게 채우는 방법을 설명합니다.
> **Version 2.1** | 2026.03 | 시니어 워크플로우 + 에스컬레이션 + 품질 체크리스트 확장 + 일반화

---

## 0. 빠른 시작

### Step 1: 복사
`.claude-standard/` 전체를 프로젝트 루트의 `.claude/`로 복사합니다.

### Step 2: 이 매뉴얼 순서대로 각 파일을 채웁니다
아래 섹션 번호 순서대로 진행하면 됩니다.

### Step 3: 불필요한 파일 제거
- 마이그레이션이 아닌 프로젝트 → `rules/MIGRATION.md` 삭제
- 사용하지 않는 MCP 서버 → `.mcp.json`에서 해당 항목 삭제
- 사용하지 않는 스킬 → `skills/` 하위 삭제

### Step 4: .gitignore에 추가
```
CLAUDE.local.md
.claude/settings.local.json
.claude/agent-memory/
```

---

## 1. CLAUDE.md 작성법

**위치**: `.claude/CLAUDE.md` (프로젝트 루트)
**역할**: Claude Code가 매 세션 시작 시 자동 로드하는 최상위 가이드
**핵심 원칙**: **50줄 이내** 유지. 상세 내용은 rules/, docs/에 분리

### 작성 항목
1. **프로젝트명** + 한 줄 설명
2. **기술 스택** (Backend/Frontend 각 1줄)
3. **`.claude/` 디렉토리 구조** (트리 형태)
4. **작업 워크플로우** 요약 (6단계)
5. **보호 파일** 목록
6. **주요 규칙 요약** (각 rules 파일 참조 링크)
7. **관련 문서 색인** (docs/ 내 파일 목록)

### `@import` 활용법
CLAUDE.md가 길어지면 다른 파일을 import하여 분리할 수 있습니다:
```markdown
@agents/AGENTS_RULE.md
@docs/PROJECT.md
```
- 최대 깊이: 5단계
- import된 파일은 세션 시작 시 함께 로드됨

### 나쁜 예 vs 좋은 예
| 나쁜 예 | 좋은 예 |
|---------|---------|
| "코드를 깔끔하게 작성" | "2스페이스 인덴테이션 사용" |
| "JavaScript는 동적 타입 언어" | "API 키는 .env에서 로드" |
| 200줄 넘는 장황한 설명 | 핵심만 50줄 이내로 |

---

## 2. CLAUDE.local.md 작성법

**위치**: 프로젝트 루트의 `CLAUDE.local.md` (`.gitignore`에 추가)
**역할**: 개인별 환경 설정. 팀원마다 다른 로컬 경로, 디버그 설정 등

### 작성 항목
- 로컬 개발 환경 특이사항 (예: 포트 번호, DB 접속 정보)
- 개인 선호 워크플로우
- 디버깅 시 참고할 개인 노트

---

## 3. settings.json 작성법 (팀 공유)

**위치**: `.claude/settings.json`
**역할**: 팀 전체에 적용되는 권한, 환경 변수, 훅 설정. **Git으로 추적**

### 필수 설정 항목

#### permissions.allow (팀 공통 허용 명령어)
프로젝트에서 공통으로 사용하는 빌드/테스트 명령어를 등록합니다:
```json
{
  "permissions": {
    "allow": [
      "Bash({린트 명령어})",
      "Bash({테스트 명령어})",
      "Bash({빌드 명령어})"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force*)",
      "Bash(git reset --hard*)"
    ]
  }
}
```

#### hooks (이벤트 기반 자동화)
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "{포맷터 명령어} $CLAUDE_FILE_PATH"
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "if [ $(git branch --show-current) = main ]; then echo 'deny: main 브랜치 직접 편집 금지'; fi"
      }
    ]
  }
}
```

#### 주요 훅 이벤트
| 이벤트 | 실행 시점 | 용도 |
|--------|----------|------|
| PreToolUse | 도구 실행 전 | 권한 제어, 브랜치 보호 |
| PostToolUse | 도구 실행 후 | 자동 포맷팅, 린트 |
| Notification | 알림 발생 시 | 데스크톱/Slack 알림 |
| Stop | 응답 완료 시 | 후처리 |
| SubagentStop | 서브에이전트 완료 시 | 결과 후처리 |
| UserPromptSubmit | 사용자 입력 시 | 입력 전처리 |
| SessionStart | 세션 시작 시 | 초기화 |
| SessionEnd | 세션 종료 시 | 정리 |

#### PreToolUse 반환값
| 반환값 | 동작 |
|--------|------|
| `allow` | 권한 프롬프트 없이 허용 |
| `deny: 사유` | 차단 (사유를 Claude에게 전달) |
| (빈 값) | 기본 동작 (사용자에게 확인) |

#### env (환경 변수)
팀 전체에 동일한 환경 변수를 배포할 때:
```json
{
  "env": {
    "NODE_ENV": "development",
    "LOG_LEVEL": "debug"
  }
}
```

#### attribution (커밋 자동 서명)
```json
{
  "attribution": {
    "commit": true,
    "pr": true
  }
}
```

---

## 4. settings.local.json 작성법 (개인)

**위치**: `.claude/settings.local.json`
**역할**: 개인 권한 설정. `.gitignore`에 자동 추가됨
**우선순위**: `settings.json`보다 높음 (개인 설정이 팀 설정을 오버라이드)

### 작성 원칙
- `settings.json`의 allow 목록을 기반으로, 개인이 추가로 필요한 명령어를 등록
- deny 목록은 팀 설정보다 더 엄격하게 설정 가능
- 이미 템플릿에 공통 패턴이 포함되어 있으므로, 프로젝트별로 조정

### 평가 순서
`deny` → `ask` → `allow` (첫 번째 매치가 적용)

---

## 5. agents/ 작성법

**위치**: `.claude/agents/*.md`
**역할**: 커스텀 서브에이전트 정의. Claude가 필요 시 자동 위임

### YAML Frontmatter (필수)
모든 에이전트 파일 최상단에 반드시 포함해야 합니다:
```yaml
---
name: agent-name          # 고유 식별자 (lowercase + hyphens)
description: 설명           # Claude가 이 에이전트를 언제 사용할지 판단
model: inherit             # inherit / claude-sonnet-4-6 / claude-opus-4-6 / claude-haiku-4-5
tools:                     # 허용 도구 목록
  - Read
  - Glob
  - Grep
  - Bash
disallowedTools: []        # 금지 도구 (선택)
maxTurns: 10               # 최대 턴 수 (선택)
permissionMode: default    # default / acceptEdits / dontAsk / plan (선택)
memory: project            # user / project / local (선택)
---
```

### 각 에이전트별 작성 포인트

#### AGENTS_RULE.md
- 에이전트 계층 구조 (시니어 → 프론트/백엔드, Git은 사용자 직접 호출)
- 자동 할당 규칙 (키워드/경로 기반 + 할당 방식 명시)
- 실행 방식 에스컬레이션 규칙 (단일 → 서브에이전트 → 에이전트 팀)
- 시니어 주도 워크플로우 (plan → 명령 → 평가 → 피드백/재수행 → 문서화)
- 핵심 규칙 (Git 사용자 직접 호출, 보호 파일, 평가 및 피드백 루프)

#### SENIOR.md
- 6단계 워크플로우: 요청 수신 → 실행 방식 결정 + plan 수립 → 명령 부여 → 평가 → 피드백/재수행 → 문서 최신화
- 실행 방식 에스컬레이션: 단일 에이전트 → 서브에이전트 → 에이전트 팀 (정확성 최우선)
- 리뷰 체크리스트 (공통 7개 + 프론트엔드 7개 + 백엔드 5개, 유지보수 관점)
- 피드백 심각도 기준 (Critical/Major/Minor/Suggestion)
- 문서화 의무 (issues, outcomes, roadmap 최신화)

#### FRONTEND.md
- 수정 가능/금지 파일 범위 (프로젝트에 맞게 경로 수정)
- 컴포넌트 패턴 (프로젝트 프레임워크에 맞게 작성)
- 품질 체크리스트 (보안/안정성/설계/성능/코드/UI 6개 분류, 16개 항목)

#### BACKEND.md
- 수정 가능/금지 파일 범위
- 필수 워크플로우 (분석 → 계획 → 승인 → 실행)
- 코드 패턴 (프레임워크에 맞게 작성)
- 품질 체크리스트 (보안/안정성/설계/성능/코드/디버깅 6개 분류, 13개 항목)

#### GIT.md
- **사용자 직접 호출만 가능** (시니어가 자동 할당 불가)
- 호출 방법 (`[git]` 접두사 필수)
- 커밋 메시지 규칙 (Conventional Commits)
- 위험 명령어 목록 (사용자 승인 필수)
- **disallowedTools에 Write, Edit 포함** (코드 수정 방지)

### 프로젝트별 에이전트 추가
다중 서버 등 특수한 구성이 있으면 추가 에이전트를 생성합니다:
- `SQL-PROXY-AGENT.md` - SQL 프록시 전담
- `MAP-BACKEND-AGENT.md` - 지도 백엔드 전담
- 탐색용 에이전트는 `model: haiku`로 비용 절감 가능

---

## 6. rules/ 작성법

**위치**: `.claude/rules/*.md`
**역할**: 경로별 조건부 지침. CLAUDE.md가 길어지면 여기로 분리

### YAML Frontmatter (paths)
```yaml
---
paths:
  - "src/components/**/*.tsx"
  - "src/pages/**/*.tsx"
---
```
- `paths`가 **없으면** 글로벌 규칙 (모든 파일에 적용)
- `paths`가 **있으면** 해당 패턴의 파일 작업 시에만 로드
- glob 패턴 지원: `**/*.ts`, `src/**/*`, `*.{ts,tsx}`

### 각 규칙 파일별 작성 포인트

#### COMMANDS.md
- 프로젝트의 빌드/실행/테스트 명령어를 실제 값으로 교체
- 백엔드/프론트엔드/Docker 등 섹션별 정리

#### SECURITY.md
- 접근 금지 파일 패턴을 프로젝트에 맞게 조정
- 마스킹 대상 패턴 확인
- 코드 작성 시 보안 수칙

#### MIGRATION.md
- 마이그레이션 프로젝트가 아니면 **삭제**
- 원본 시스템 경로, UI/UX 보존 규칙 등을 프로젝트에 맞게 작성

#### TESTING.md (신규)
프로젝트의 테스트 전략을 정의합니다:
- paths frontmatter에 테스트 파일 패턴 설정: `"**/*.test.ts"`, `"**/*.spec.ts"`
- 테스트 명명 규칙 (should + 동사, describe 그룹화)
- AAA 패턴 (Arrange → Act → Assert)
- 최소 커버리지 기준
- 모킹 전략

### 경로별 규칙 추가 예시
프론트엔드/백엔드 분리 규칙이 필요하면 새 파일을 생성합니다:
```yaml
# rules/frontend-style.md
---
paths:
  - "{프론트엔드 소스 경로}/**/*.{확장자}"
  - "{프론트엔드 소스 경로}/**/*.ts"
---
```

---

## 7. skills/ 작성법

**위치**: `.claude/skills/{name}/SKILL.md`
**역할**: 커맨드의 상위 호환. 보조 파일 포함 가능, Claude 자동 호출 가능

### 디렉토리 구조 (중요!)
```
skills/
└── review/
    ├── SKILL.md          # 필수 진입점
    └── templates/        # 선택: 보조 파일
        └── checklist.md
```
- **레거시**: `skills/review.md` (단일 파일) → 작동은 하지만 비권장
- **공식**: `skills/review/SKILL.md` (디렉토리 구조) → 보조 파일, frontmatter 지원

### SKILL.md Frontmatter
```yaml
---
description: "코드 리뷰를 수행합니다"    # Claude가 자동 호출 판단에 사용
disable-model-invocation: false         # true면 사용자만 호출 가능
user-invocable: true                    # false면 Claude만 호출 가능
allowed-tools:                          # 스킬 실행 중 허용할 도구 (선택)
  - Read
  - Grep
---
```

### 호출 제어 조합
| disable-model-invocation | user-invocable | 사용자 | Claude |
|--------------------------|----------------|--------|--------|
| false (기본) | true (기본) | O | O |
| true | true | O | X |
| false | false | X | O |

### 인자 변수
- `$ARGUMENTS` - 사용자가 전달한 전체 인자
- `$ARGUMENTS[0]`, `$1` - 개별 인자
- `${CLAUDE_SESSION_ID}` - 세션 ID
- `${CLAUDE_SKILL_DIR}` - 스킬 디렉토리 경로

### 동적 컨텍스트
쉘 명령어 결과를 스킬에 주입할 수 있습니다:
```markdown
현재 브랜치: !`git branch --show-current`
```

---

## 8. .mcp.json 작성법

**위치**: 프로젝트 루트의 `.mcp.json`
**역할**: MCP(Model Context Protocol) 서버 연동

### 작성 예시
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "env:GITHUB_TOKEN" }
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/dir"]
    }
  }
}
```

### 도구 명명 규칙
`mcp__<서버이름>__<도구이름>` (예: `mcp__github__list_issues`)

### 필요 없으면
사용하지 않는 MCP 서버가 없으면 빈 객체로 유지하거나 파일 삭제

---

## 9. launch.json 작성법

**위치**: `.claude/launch.json`
**역할**: 개발 서버 프리뷰 설정

### 작성 예시
```json
{
  "version": "0.0.1",
  "configurations": [
    {
      "name": "frontend-dev",
      "runtimeExecutable": "{프론트엔드 실행 커맨드}",
      "runtimeArgs": ["{인자}"],
      "port": 3000
    },
    {
      "name": "backend-dev",
      "runtimeExecutable": "{백엔드 실행 커맨드}",
      "runtimeArgs": ["{인자}"],
      "port": 8080
    }
  ]
}
```

### 필요 없으면
개발 서버 프리뷰를 사용하지 않으면 빈 configurations로 유지하거나 삭제

---

## 10. docs/ 작성법

### PROJECT.md (필수)
- 기술 스택 표: Backend/Frontend 각 항목과 버전
- 서버 아키텍처 다이어그램 (ASCII art)
- 주요 디렉토리 구조
- DB 구성, API 엔드포인트, 배포 환경

### MEMO.md (권장)
- 시스템 개요 (한 줄)
- 핵심 용어 사전
- 알려진 이슈 목록
- 외부 연동 시스템

### 추가 문서 (선택)
프로젝트별로 필요한 문서를 `docs/` 아래에 추가:
- `BUILD-GUIDE.md` - 빌드 가이드
- `DEPLOY-GUIDE.md` - 배포 가이드
- `SSL-SETTING.md` - SSL 설정
- `api-docs.json` - API 스펙 (읽기 전용)

---

## 11. features/ 작성법

### 디렉토리 생성
프로젝트의 주요 기능별로 생성:
```
features/
├── auth/
│   ├── issues.md
│   └── outcomes/
├── dashboard/
│   ├── issues.md
│   └── outcomes/
└── settings/
    ├── issues.md
    └── outcomes/
```

### issues.md
`TEMPLATE-issues.md`를 복사하여 사용:
- Open 섹션: 미해결 이슈 나열
- Closed 섹션: 해결된 이슈 + outcomes 링크
- 이슈 ID 형식: `[시스템-유형-우선순위]` (예: `[인증-오류-1]`)

### outcomes/
`TEMPLATE-outcome.md`를 복사하여 사용:
- 파일명: `UPPER-KEBAB-CASE.md` (예: `LOGIN-API-FIX.md`)
- 구조: 배경 → 분석 → 변경 사항 → 검증 → 리뷰

---

## 12. memory/roadmap.md 작성법

### 섹션 구조
- **대기 (To-Do)**: 예정된 작업
- **진행 중 (In Progress)**: 현재 작업 + 담당 에이전트
- **완료 (Done)**: 완료된 작업 + 날짜 + outcomes 링크
- **향후 계획 (Future)**: 장기 로드맵

### 이슈 ID 형식
```
[시스템-유형-우선순위]
시스템: 프로젝트 모듈명
유형: 오류, 개선, 변경, 기타
우선순위: 1(높음), 2(중간), 3(낮음)
```

---

## 13. agent-memory/ 활용법

**위치**: `.claude/agent-memory/`
**역할**: 에이전트별 세션 간 영구 기억

### 구조
```
agent-memory/
├── senior/
│   └── MEMORY.md
├── frontend/
│   └── MEMORY.md
└── backend/
    └── MEMORY.md
```

### 작성 원칙
- 에이전트가 세션 중 학습한 프로젝트 패턴, 디버깅 인사이트를 자동 기록
- MEMORY.md 첫 200줄만 세션 시작 시 로드됨
- 상세 내용은 별도 파일로 분리하여 MEMORY.md에서 참조
- `.gitignore`에 추가 (개인 메모리)

---

## 14. .gitignore 가이드

### Git 추적 대상
| 파일 | Git 추적 | 이유 |
|------|---------|------|
| CLAUDE.md | O | 팀 공유 프로젝트 지침 |
| settings.json | O | 팀 공유 권한/훅 설정 |
| agents/*.md | O | 에이전트 정의 (팀 공유) |
| rules/*.md | O | 프로젝트 규칙 (팀 공유) |
| skills/**/* | O | 스킬 정의 (팀 공유) |
| docs/*.md | O | 프로젝트 문서 (팀 공유) |
| features/**/* | O | 이슈/성과 추적 (팀 공유) |
| .mcp.json | O | MCP 설정 (팀 공유) |
| launch.json | O | 개발 서버 설정 (팀 공유) |

### Git 제외 대상
| 파일 | Git 추적 | 이유 |
|------|---------|------|
| CLAUDE.local.md | X | 개인 설정 |
| settings.local.json | X | 개인 권한 |
| agent-memory/ | X | 개인 에이전트 기억 |
| memory/roadmap.md | 선택 | 개인 작업 추적이면 X, 팀 공유면 O |

---

## 15. 컨텍스트 관리 팁

### 세션 관리
- 컨텍스트가 길어지면 `/compact`로 압축
- 새로운 주제는 새 세션에서 시작 (신선한 컨텍스트)
- 대규모 작업은 Git worktree로 격리

### 비용 최적화
- 탐색용 에이전트: `model: haiku` (저비용)
- 핵심 작업 에이전트: `model: inherit` 또는 `model: sonnet` (균형)
- 복잡한 설계: `model: opus` (고품질)

### 안전한 작업 습관
- settings.local.json의 deny 목록을 주기적으로 점검
- 위험 명령어 (rm -rf, git push --force 등) 반드시 deny에 등록
- 커밋 전 린트 + 테스트 자동 실행 설정 권장

---

## 부록 A: 전체 디렉토리 구조 (개선 후)

```
.claude/
├── CLAUDE.md                    # [필수] 프로젝트 최상위 가이드 (50줄 이내)
├── CLAUDE.local.md              # [개인] 로컬 설정 (.gitignore)
├── settings.json                # [필수] 팀 공유 설정 (Git 추적)
├── settings.local.json          # [필수] 개인 설정 (Git 제외)
├── .mcp.json                    # [선택] MCP 서버 연동
├── launch.json                  # [선택] 개발 서버 프리뷰
│
├── agents/                      # [필수] 에이전트 역할 (YAML frontmatter 필수)
│   ├── AGENTS_RULE.md           #   계층 구조 + 핵심 규칙
│   ├── SENIOR.md                #   시니어 (설계/리뷰)
│   ├── FRONTEND.md              #   프론트엔드
│   ├── BACKEND.md               #   백엔드
│   └── GIT.md                   #   Git 전담 (Write/Edit 금지)
│
├── rules/                       # [필수] 프로젝트 규칙 (paths frontmatter 지원)
│   ├── COMMANDS.md              #   빌드/실행 명령어
│   ├── SECURITY.md              #   보안 (민감 데이터 보호)
│   ├── TESTING.md               #   테스트/검증 규칙
│   └── MIGRATION.md             #   마이그레이션 (해당 시)
│
├── skills/                      # [선택] 스킬 (디렉토리 + SKILL.md 구조)
│   └── review/
│       └── SKILL.md
│
├── docs/                        # [필수] 프로젝트 문서
│   ├── PROJECT.md               #   기술 스택/아키텍처
│   ├── MEMO.md                  #   인수인계 메모
│   └── {TOPIC}.md               #   추가 문서
│
├── features/                    # [필수] 기능별 이슈/성과 추적
│   ├── TEMPLATE-issues.md       #   이슈 템플릿
│   ├── TEMPLATE-outcome.md      #   결과 템플릿
│   └── {feature-name}/
│       ├── issues.md
│       └── outcomes/
│           └── {DESCRIPTOR}.md
│
├── memory/                      # [권장] 활성 작업 추적
│   └── roadmap.md
│
└── agent-memory/                # [선택] 에이전트 영구 기억 (.gitignore)
    └── {agent-name}/
        └── MEMORY.md
```

---

## 부록 B: 기존 대비 개선 사항 요약

| # | 개선 항목 | 변경 내용 |
|---|---------|---------|
| 1 | agents/ frontmatter | YAML frontmatter (name, description, model, tools) 추가 |
| 2 | rules/ frontmatter | paths frontmatter 추가 (경로별 조건부 로드) |
| 3 | settings.json 분리 | 팀 공유(settings.json) + 개인(settings.local.json) 분리 |
| 4 | hooks 설정 | settings.json에 PostToolUse/PreToolUse 훅 추가 |
| 5 | skills/ 구조 변경 | 단일 파일 → 디렉토리 + SKILL.md 공식 구조 |
| 6 | CLAUDE.local.md 추가 | 개인 설정 템플릿 |
| 7 | .mcp.json 추가 | MCP 서버 연동 템플릿 |
| 8 | launch.json 추가 | 개발 서버 프리뷰 설정 |
| 9 | rules/TESTING.md 추가 | 테스트/검증 규칙 |
| 10 | agent-memory/ 추가 | 에이전트별 세션 간 영구 기억 |
| 11 | CLAUDE.md 경량화 | 100줄 → 50줄 이내, @import 활용 |
| 12 | .gitignore 가이드 | 추적/제외 대상 명확화 |

---

## 부록 C: 참고 자료

### 공식 문서
- Settings: code.claude.com/docs/en/settings
- Memory: code.claude.com/docs/en/memory
- Skills: code.claude.com/docs/en/skills
- Sub-agents: code.claude.com/docs/en/sub-agents
- Hooks: code.claude.com/docs/en/hooks-guide

### 커뮤니티
- ChrisWiles/claude-code-showcase
- feiskyer/claude-code-settings
- shanraisshan/claude-code-best-practice
- VoltAgent/awesome-claude-code-subagents
