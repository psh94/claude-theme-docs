# Claude Code 문서 표준화 가이드 v2.1

> v1.0: `.claude-example1`(HSHI-MAP)과 `.claude-example2`(KSATDB) 분석 기반
> v2.0: 공식 문서 + PDF(.claude 완전 매뉴얼, 45 Tips) + GitHub 베스트 프랙티스 반영
> v2.1: 시니어 주도 워크플로우, 에스컬레이션 규칙, 품질 체크리스트 확장, 일반화

---

## 1. 변경 이력

### v1.0 → v2.0

| # | 개선 항목 | v1.0 상태 | v2.0 변경 |
|---|---------|----------|----------|
| 1 | agents/ frontmatter | 없음 (순수 마크다운) | YAML frontmatter 추가 (name, description, model, tools) |
| 2 | rules/ frontmatter | 없음 | paths frontmatter 추가 (경로별 조건부 로드) |
| 3 | settings.json | 없음 | 팀 공유 설정 파일 신규 생성 (hooks, env, permissions) |
| 4 | skills/ 구조 | 단일 파일 (`{name}.md`) | 공식 구조 (`{name}/SKILL.md`) |
| 5 | CLAUDE.md | 100줄 | 35줄로 경량화, `@import` 구문 활용 |
| 6 | CLAUDE.local.md | 없음 | 개인 설정 템플릿 추가 |
| 7 | .mcp.json | 없음 | MCP 서버 연동 템플릿 추가 |
| 8 | launch.json | 없음 | 개발 서버 프리뷰 설정 추가 |
| 9 | rules/TESTING.md | 없음 | 테스트/검증 규칙 추가 (v2.1에서 플레이스홀더→실제 템플릿) |
| 10 | agent-memory/ | 없음 | 에이전트별 세션 간 영구 기억 디렉토리 추가 |
| 11 | .gitignore 가이드 | 미흡 | 추적/제외 대상 명확화 |
| 12 | manual.md | 없음 | 전체 작성법 매뉴얼 신규 생성 |

### v2.0 → v2.1

| # | 개선 항목 | v2.0 상태 | v2.1 변경 |
|---|---------|----------|----------|
| 13 | 시니어 주도 워크플로우 | 단순 6단계 | plan 문서화 → 명령 → 평가 → 피드백/재수행 → 문서화 상세화 |
| 14 | 실행 방식 에스컬레이션 | 없음 | 단일 → 서브에이전트 → 에이전트 팀 (정확성 기준 단계적 에스컬레이션) |
| 15 | Git 사용자 직접 호출 | `[git]` 접두사만 | 시니어 자동 할당 불가, 사용자 직접 호출만 가능 명시 |
| 16 | 품질 체크리스트 확장 | 프론트 5개 / 백엔드 5개 | 프론트 16개 / 백엔드 13개 (유지보수 관점 6개 분류) |
| 17 | 시니어 리뷰 체크리스트 확장 | 공통 4 + 프론트 4 + 백엔드 4 | 공통 7 + 프론트 7 + 백엔드 5 |
| 18 | 콘텐츠 일반화 | 기술 스택 종속 (Vue, Spring 등) | 전체 파일 플레이스홀더 기반 범용 템플릿화 |

---

## 2. v1.0 분석 요약 (유지)

### 분석 대상
| 항목 | Example1 (HSHI-MAP) | Example2 (KSATDB) |
|------|---------------------|---------------------|
| 프로젝트 유형 | GIS 조선소 지도 | 위성 데이터베이스 |
| 서버 구성 | 3개 (Main + SQL Proxy + SAP Proxy) | 1개 (Main) |
| 에이전트 수 | 5개 공통 + 4개 프로젝트별 | 5개 공통 |
| 문서 수 | 6개 | 2개 |
| 기능 추적 | 8개 feature | 8개 feature |

### v1.0에서 도출된 공통점 (계속 유지)
1. 동일한 에이전트 체계: AGENTS_RULE, SENIOR, FRONTEND, BACKEND, GIT
2. 동일한 규칙 구조: COMMANDS, SECURITY, MIGRATION
3. 동일한 기능 추적 패턴: `features/{name}/issues.md` + `outcomes/`
4. 동일한 보호 파일 정책
5. 동일한 Git 독점 규칙

---

## 3. v2.1 표준 디렉토리 구조

```
.claude/
├── CLAUDE.md                    # [필수] 프로젝트 가이드 (50줄 이내)
├── CLAUDE.local.md              # [개인] 로컬 설정 (.gitignore)
├── settings.json                # [필수] 팀 공유 설정 (Git 추적)
├── settings.local.json          # [필수] 개인 설정 (Git 제외)
├── .mcp.json                    # [선택] MCP 서버 연동
├── launch.json                  # [선택] 개발 서버 프리뷰
├── manual.md                    # [참고] 작성법 매뉴얼
│
├── agents/                      # [필수] YAML frontmatter 포함
│   ├── AGENTS_RULE.md
│   ├── SENIOR.md
│   ├── FRONTEND.md
│   ├── BACKEND.md
│   └── GIT.md
│
├── rules/                       # [필수] paths frontmatter 지원
│   ├── COMMANDS.md
│   ├── SECURITY.md
│   ├── TESTING.md               # v2.0 신규
│   └── MIGRATION.md
│
├── skills/                      # [선택] 디렉토리 + SKILL.md 구조
│   └── review/
│       └── SKILL.md
│
├── docs/
│   ├── PROJECT.md
│   ├── MEMO.md
│   └── {TOPIC}.md
│
├── features/
│   ├── TEMPLATE-issues.md
│   ├── TEMPLATE-outcome.md
│   └── {feature-name}/
│       ├── issues.md
│       └── outcomes/
│
├── memory/
│   └── roadmap.md
│
└── agent-memory/                # v2.0 신규
    └── {agent-name}/
        └── MEMORY.md
```

---

## 4. 필수/권장/선택 분류 (v2.1)

| 분류 | 항목 | 이유 |
|------|------|------|
| **필수** | CLAUDE.md | 세션 자동 로드, 프로젝트 인식 |
| **필수** | settings.json | 팀 공유 권한/훅 (v2.0 추가) |
| **필수** | settings.local.json | 개인 권한 |
| **필수** | agents/ (5개 + frontmatter) | 에이전트 체계 |
| **필수** | rules/COMMANDS.md, SECURITY.md | 빌드/보안 |
| **필수** | docs/PROJECT.md | 기술 스택 |
| **필수** | features/ | 작업 추적 |
| **권장** | rules/TESTING.md | 테스트 품질 (v2.0 추가) |
| **권장** | docs/MEMO.md | 인수인계 |
| **권장** | memory/roadmap.md | 연속 작업 추적 |
| **선택** | CLAUDE.local.md | 개인 환경 (v2.0 추가) |
| **선택** | .mcp.json | 외부 도구 연동 (v2.0 추가) |
| **선택** | launch.json | 개발 서버 (v2.0 추가) |
| **선택** | skills/ | 커스텀 스킬 |
| **선택** | agent-memory/ | 에이전트 기억 (v2.0 추가) |
| **선택** | rules/MIGRATION.md | 마이그레이션 전용 |

---

## 5. 네이밍 규칙 (유지)

| 대상 | 규칙 | 예시 |
|------|------|------|
| 에이전트 파일 | `UPPER_CASE.md` | `SENIOR.md`, `BACKEND.md` |
| 규칙 파일 | `UPPER_CASE.md` | `SECURITY.md`, `COMMANDS.md` |
| 문서 파일 | `UPPER_CASE.md` 또는 `KEBAB-CASE.md` | `PROJECT.md`, `BUILD-GUIDE.md` |
| 기능 디렉토리 | `lowercase` | `features/main/`, `features/map/` |
| 이슈 파일 | `issues.md` (고정) | `features/map/issues.md` |
| 결과 파일 | `UPPER-KEBAB-CASE.md` | `CATALOG-API.md`, `LOGIN-FIX.md` |
| 이슈 ID | `[시스템-유형-우선순위]` | `[인증-오류-1]`, `[대시보드-개선-2]` |
| 에이전트 ID | `lowercase` + 번호 | `front1`, `back2`, `senior1` |
| 커밋 메시지 | `<type>: <subject>` | `feat: 로그인 API 추가` |

---

## 6. 새 프로젝트 적용 (v2.1)

### Step 1: 복사
`.claude-standard/` → 프로젝트 `.claude/`

### Step 2: `manual.md` 순서대로 각 파일 채우기
manual.md의 섹션 1~15를 순서대로 따라가며 프로젝트에 맞게 작성

### Step 3: .gitignore 설정
```
CLAUDE.local.md
.claude/settings.local.json
.claude/agent-memory/
```

### Step 4: 불필요한 파일 제거
- 마이그레이션 아님 → `rules/MIGRATION.md` 삭제
- MCP 미사용 → `.mcp.json` 삭제
- 프리뷰 미사용 → `launch.json` 삭제

### Step 5: manual.md 삭제 (선택)
프로젝트 적용 완료 후 `manual.md`는 삭제하거나 docs/로 이동
