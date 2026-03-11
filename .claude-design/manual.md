# .claude-design 적용 매뉴얼

> 이 문서는 `.claude-design/`의 각 파일을 디자인 프로젝트에 맞게 채우는 방법을 설명합니다.
> **Version 1.0** | 2026.03 | .claude-standard 기반 디자인 특화 커스텀

---

## 0. 빠른 시작

### Step 1: 복사
`.claude-design/` 전체를 프로젝트 루트의 `.claude/`로 복사합니다.

### Step 2: 이 매뉴얼 순서대로 각 파일을 채웁니다

### Step 3: 불필요한 파일 제거
- 디자인 토큰을 사용하지 않는 프로젝트 → `rules/DESIGN-TOKENS.md` 삭제
- MCP 미사용 → `.mcp.json` 삭제
- 스킬 미사용 → `skills/` 삭제

### Step 4: .gitignore에 추가
```
CLAUDE.local.md
.claude/settings.local.json
.claude/agent-memory/
```

---

## 1. CLAUDE.md 작성법

**핵심 원칙**: **50줄 이내** 유지. 상세 내용은 rules/, docs/에 분리

### 작성 항목
1. **프로젝트명** + 디자인 시스템명
2. **기술 스택** (프레임워크, 스타일링 방식)
3. **주요 규칙** 참조 링크 (접근성, 토큰, 보안 등)
4. **보호 파일** 목록 (토큰 정의, 빌드 설정 등)
5. **워크플로우** 요약
6. **문서 색인**

---

## 2. agents/ 작성법

### 에이전트 구성 (.claude-standard과의 차이)

| .claude-standard | .claude-design | 역할 변경 |
|-----------------|---------------|----------|
| 프론트엔드 엔지니어 | **UI 디자이너** | 비주얼 구현, 스타일링 특화 |
| 백엔드 엔지니어 | **UX 디자이너** | 사용자 흐름, 인터랙션, 접근성 |
| (없음) | **디자인 시스템 엔지니어** | 토큰, 테마, 공통 컴포넌트 관리 |

### 각 에이전트별 작성 포인트

#### AGENTS_RULE.md
- 디자인 에이전트 계층 구조 (시니어 → UI/UX/디자인시스템)
- 자동 할당 규칙 (키워드/경로 기반)
- 실행 방식 에스컬레이션 (단일 → 서브에이전트 → 에이전트 팀)
- Git 사용자 직접 호출 규칙

#### SENIOR.md
- 6단계 워크플로우 + 실행 방식 에스컬레이션
- 디자인 리뷰 체크리스트 (공통 6개 + UI 6개 + UX 6개 + DS 4개)
- 피드백 심각도 (Critical: 접근성 위반, Major: 토큰 불일치 등)

#### UI-DESIGNER.md
- 비주얼 디자인 구현 범위 (컴포넌트, 스타일, 에셋)
- 스타일링 원칙 (토큰 우선, 시맨틱 토큰, rem 단위)
- 품질 체크리스트 (시각적 일관성/반응형/접근성(시각)/성능/코드)

#### UX-DESIGNER.md
- 인터랙션 디자인 범위 (라우팅, 이벤트, 접근성)
- 상태 전환 패턴 (default→hover→active→focus→disabled)
- 피드백 패턴 (로딩/에러/빈 상태)
- 품질 체크리스트 (사용자 흐름/접근성 WCAG/인터랙션/코드)

#### DESIGN-SYSTEM.md
- 토큰/테마/공통 컴포넌트 관리 범위
- 토큰 계층 (Primitive → Semantic → Component)
- 토큰 명명 규칙 (`--{카테고리}-{속성}-{변형}-{상태}`)
- 품질 체크리스트 (토큰 관리/컴포넌트/영향 범위/코드)

#### GIT.md
- 사용자 직접 호출만 가능
- 디자인 전용 커밋 타입 추가: `design` (디자인 변경), `a11y` (접근성)

---

## 3. rules/ 작성법 (.claude-standard과의 차이)

| .claude-standard | .claude-design | 변경 |
|-----------------|---------------|------|
| COMMANDS.md | COMMANDS.md | 스토리북/토큰 빌드/접근성 테스트 명령어 추가 |
| SECURITY.md | SECURITY.md | 폰트 라이선스, CDN 토큰 마스킹 추가 |
| TESTING.md | TESTING.md | 비주얼 리그레션, 접근성 자동 테스트 추가 |
| MIGRATION.md | (삭제) | 디자인에서 불필요 |
| (없음) | **ACCESSIBILITY.md** | WCAG 2.1 AA 전체 규칙 (신규) |
| (없음) | **DESIGN-TOKENS.md** | 토큰 계층/명명/사용 규칙 (신규) |

### ACCESSIBILITY.md 핵심 내용
- WCAG 4원칙 (인지/조작/이해/견고)
- 색상 대비 기준 (4.5:1 텍스트, 3:1 대형)
- 키보드 접근성, 포커스 관리
- 시맨틱 HTML, ARIA
- 모션 감소 대응

### DESIGN-TOKENS.md 핵심 내용
- 3단계 토큰 계층 (Primitive → Semantic → Component)
- 하드코딩 금지 항목 (색상, 간격, 폰트 크기, 그림자 등)
- 토큰 변경 프로세스 (디자인 시스템 에이전트만 수행)

---

## 4. 나머지 파일

settings.json, settings.local.json, .mcp.json, launch.json, docs/, features/, memory/, agent-memory/, skills/ 의 작성법은 `.claude-standard/manual.md`와 동일합니다.

### 디자인 프로젝트 특화 포인트
- `docs/PROJECT.md`: 디자인 시스템 개요, 토큰 체계, 테마 구성, 브라우저 지원 범위
- `docs/MEMO.md`: 디자인 원칙, 디자인 결정 로그, Figma/외부 리소스 링크
- `skills/design-review/SKILL.md`: 토큰 준수, 접근성, 반응형 자동 검사

---

## 5. .gitignore 가이드

### Git 추적 대상
| 파일 | Git 추적 | 이유 |
|------|---------|------|
| CLAUDE.md | O | 팀 공유 디자인 지침 |
| settings.json | O | 팀 공유 권한/훅 설정 |
| agents/*.md | O | 에이전트 정의 (팀 공유) |
| rules/*.md | O | 디자인 규칙 (팀 공유) |
| docs/*.md | O | 디자인 문서 (팀 공유) |

### Git 제외 대상
| 파일 | Git 추적 | 이유 |
|------|---------|------|
| CLAUDE.local.md | X | 개인 설정 |
| settings.local.json | X | 개인 권한 |
| agent-memory/ | X | 개인 에이전트 기억 |

---

## 부록 A: 전체 디렉토리 구조

```
.claude/
├── CLAUDE.md                    # [필수] 디자인 프로젝트 가이드 (50줄 이내)
├── CLAUDE.local.md              # [개인] 로컬 설정
├── settings.json                # [필수] 팀 공유 설정
├── settings.local.json          # [필수] 개인 설정
├── .mcp.json                    # [선택] MCP 서버 연동
├── launch.json                  # [선택] 개발 서버 프리뷰
│
├── agents/                      # [필수] 디자인 에이전트
│   ├── AGENTS_RULE.md           #   계층 구조 + 핵심 규칙
│   ├── SENIOR.md                #   시니어 디자이너 (리뷰/조율)
│   ├── UI-DESIGNER.md           #   UI 디자이너 (비주얼)
│   ├── UX-DESIGNER.md           #   UX 디자이너 (인터랙션/접근성)
│   ├── DESIGN-SYSTEM.md         #   디자인 시스템 엔지니어
│   └── GIT.md                   #   Git 전담
│
├── rules/                       # [필수] 디자인 규칙
│   ├── ACCESSIBILITY.md         #   접근성 (WCAG 2.1 AA)
│   ├── DESIGN-TOKENS.md         #   디자인 토큰 규칙
│   ├── COMMANDS.md              #   빌드/실행 명령어
│   ├── SECURITY.md              #   보안
│   └── TESTING.md               #   테스트/검증
│
├── skills/                      # [선택] 디자인 스킬
│   └── design-review/
│       └── SKILL.md
│
├── docs/                        # [필수] 디자인 문서
│   ├── PROJECT.md               #   디자인 시스템/기술 스택
│   └── MEMO.md                  #   디자인 결정 로그
│
├── features/                    # [필수] 기능별 이슈/성과 추적
│   ├── TEMPLATE-issues.md
│   ├── TEMPLATE-outcome.md
│   └── {feature-name}/
│
├── memory/                      # [권장] 작업 추적
│   └── roadmap.md
│
└── agent-memory/                # [선택] 에이전트 영구 기억
    └── {agent-name}/
        └── MEMORY.md
```

---

## 부록 B: .claude-standard 대비 차이 요약

| # | 항목 | .claude-standard | .claude-design |
|---|------|-----------------|---------------|
| 1 | 에이전트 | 프론트/백엔드 | UI/UX/디자인시스템 |
| 2 | 접근성 규칙 | 없음 | ACCESSIBILITY.md (WCAG 2.1 AA) |
| 3 | 토큰 규칙 | 없음 | DESIGN-TOKENS.md |
| 4 | 커밋 타입 | 표준 | `design`, `a11y` 추가 |
| 5 | 리뷰 기준 | 코드 품질 중심 | 시각적 일관성 + 접근성 + 토큰 준수 |
| 6 | 테스트 | 단위/통합 | 비주얼 리그레션 + 접근성 자동 테스트 |
| 7 | 마이그레이션 | 있음 | 삭제 |
| 8 | 스킬 | 코드 리뷰 | 디자인 리뷰 |
| 9 | 품질 체크 | 보안/안정성/성능 | 시각적 일관성/반응형/접근성/토큰 준수 |
