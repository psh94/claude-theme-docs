# 에이전트 메모리

이 디렉토리에는 에이전트별 세션 간 영구 기억이 저장됩니다.

## 구조
```
agent-memory/
├── senior/
│   └── MEMORY.md
├── ui/
│   └── MEMORY.md
├── ux/
│   └── MEMORY.md
└── ds/
    └── MEMORY.md
```

## 규칙
- `.gitignore`에 추가 (개인 메모리)
- MEMORY.md 첫 200줄만 세션 시작 시 로드됨
- 디자인 패턴, 디버깅 인사이트, 프로젝트 특이사항을 기록
