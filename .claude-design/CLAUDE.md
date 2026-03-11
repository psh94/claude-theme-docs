# {PROJECT_NAME} - Claude Code 디자인 프로젝트 가이드

> 이 문서는 Claude Code가 매 세션 시작 시 자동 로드합니다. 50줄 이내 유지 권장.
> 상세 작성법은 `manual.md`를 참고하세요.

## 프로젝트 개요
- **프로젝트명**: {PROJECT_NAME}
- **설명**: {한 줄 설명}
- **디자인 시스템**: {디자인 시스템명}
- **기술 스택**: {프레임워크} / {스타일링 방식}

## 주요 규칙
1. **에이전트**: @agents/AGENTS_RULE.md
2. **접근성**: @rules/ACCESSIBILITY.md
3. **디자인 토큰**: @rules/DESIGN-TOKENS.md
4. **보안**: @rules/SECURITY.md
5. **빌드/실행**: @rules/COMMANDS.md
6. **테스트**: @rules/TESTING.md
7. **Git**: 사용자 직접 호출만 가능 (`[git]` 접두사 필수)

## 보호 파일 (수정 금지)
- 디자인 토큰 정의: `{토큰 파일 패턴}` (디자인 시스템 에이전트만 수정)
- 빌드 설정: `{빌드 설정 파일 패턴}`
- 환경 설정: `{환경 설정 파일 패턴}`
- 폰트 라이선스: `{라이선스 파일 패턴}`

## 워크플로우
사용자 요청 → 시니어: 실행 방식 결정(단일/서브/팀) + plan 수립 → 명령 부여 → 완료 평가 → 피드백/재수행 → 문서 최신화

## 문서 색인
| 문서 | 경로 |
|------|------|
| 에이전트 규칙 | `agents/AGENTS_RULE.md` |
| 프로젝트 기술 문서 | `docs/PROJECT.md` |
| 인수인계 메모 | `docs/MEMO.md` |
| 로드맵 | `memory/roadmap.md` |
