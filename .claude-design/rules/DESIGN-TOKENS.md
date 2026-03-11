---
# paths 없음 = 글로벌 규칙 (모든 파일에 적용)
---

# 디자인 토큰 규칙

> 디자인 토큰의 정의, 사용, 관리에 대한 규칙입니다.

---

## 1. 토큰 계층

```
Primitive (Global)  →  값 정의
    ↓
Semantic (Alias)    →  의미 매핑
    ↓
Component           →  컴포넌트 전용
```

### 사용 규칙
- 컴포넌트에서는 **Semantic 토큰만** 사용
- Primitive 토큰 직접 참조 금지 (디자인 시스템 에이전트만 가능)
- 새 Semantic 토큰 추가 시 모든 테마에 대응값 필수

---

## 2. 토큰 카테고리

| 카테고리 | 접두사 | 예시 |
|---------|--------|------|
| 색상 | `--color-` | `--color-text-primary` |
| 타이포그래피 | `--font-` | `--font-size-lg`, `--font-weight-bold` |
| 간격 | `--spacing-` | `--spacing-md`, `--spacing-lg` |
| 그림자 | `--shadow-` | `--shadow-elevation-1` |
| 둥글기 | `--radius-` | `--radius-sm`, `--radius-full` |
| 브레이크포인트 | `--breakpoint-` | `--breakpoint-tablet` |
| z-index | `--z-` | `--z-modal`, `--z-dropdown` |
| 애니메이션 | `--duration-` | `--duration-fast`, `--duration-normal` |

---

## 3. 하드코딩 금지 항목

다음 값은 반드시 토큰을 통해 사용합니다:

```
❌ color: #3B82F6;
✅ color: var(--color-primary);

❌ padding: 16px;
✅ padding: var(--spacing-md);

❌ font-size: 14px;
✅ font-size: var(--font-size-sm);

❌ border-radius: 8px;
✅ border-radius: var(--radius-md);

❌ box-shadow: 0 2px 4px rgba(0,0,0,0.1);
✅ box-shadow: var(--shadow-elevation-1);

❌ z-index: 999;
✅ z-index: var(--z-modal);
```

---

## 4. 토큰 변경 프로세스

```
1. 변경 필요성 분석
2. 영향받는 컴포넌트 목록 파악
3. 모든 테마 대응값 준비
4. 디자인 시스템 에이전트가 변경 실행
5. 시니어 디자이너 리뷰
6. 문서/스토리북 업데이트
```

- 토큰 변경은 **디자인 시스템 에이전트만** 수행
- Breaking change 시 마이그레이션 가이드 작성 필수
