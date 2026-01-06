# Claude Code 멀티에이전트 가이드

> 이 문서는 멀티에이전트 구조가 **왜** 필요한지, **어떻게** 설계하는지를 설명합니다.

[English Version](README.md)

---

## Quick Start - 예제 실행하기

Claude Code를 실행하고 아래 프롬프트를 입력하세요:

```
https://github.com/ailnk0/claude-multiagent-template.git 클론하고 해당 폴더로 이동해줘.
그 다음 docs/example-task-kr.md를 읽고, 각 Task를 Task 도구(서브에이전트)로 실행해줘.
각 Task는 완료 후 Progress Tracker 업데이트하고, 산출물을 기록해줘.
```

**예제가 하는 일:**
```
Task 0: 시장 조사 (WebSearch로 경쟁사 3개 선정)
    │
    ├── Task 1a: 경쟁사 A 리서치 ─┐
    ├── Task 1b: 경쟁사 B 리서치 ─┼── (병렬)
    └── Task 1c: 경쟁사 C 리서치 ─┘
                                  │
                                  ▼
            Task 2: 비교 분석 (기능 매트릭스, 인사이트)
                                  │
                                  ▼
            Task 3: HTML 리포트 (브라우저에서 확인)
```

**최종 결과물:** `output/report.html` - 전문적인 경쟁사 분석 리포트

## Quick Start - 나만의 프로젝트 만들기

`docs/task-template-kr.md`를 복사하고 Claude와 함께 커스터마이징하세요.

이 단계는 상당한 노력이 필요합니다. Claude와 반복적으로 협업하며 정의해야 할 것들:
- 각 Task의 명확한 목표
- 선행조건과 의존성
- 성공 기준 (에이전트가 스스로 검증 가능해야 함)
- Task 간 파일 기반 지식 전달 방식

Task 정의의 품질이 멀티에이전트 실행의 성패를 결정합니다. 아래 가이드를 읽고 원칙을 이해하세요.

끝. 왜 이렇게 하는지, 어떻게 커스터마이징하는지는 아래 내용을 참고하세요.

---

## 왜 멀티에이전트인가?

### 단일 에이전트의 한계

Claude Code로 복잡한 작업을 시키면 자주 발생하는 현상:

```
A 방식으로 구현 시도
    ↓ 실패
B 방식으로 전환
    ↓ 실패
다시 A 방식으로 돌아감  ← 실패 루프
    ↓ 실패
...반복
```

**원인 1: Context는 유한하다**
- Claude Code의 컨텍스트 윈도우는 약 200K 토큰
- 대규모 코드베이스 + 긴 대화 = 빠르게 한계 도달

**원인 2: Context rot (컨텍스트 부패)**
- 대화가 길어질수록 앞에서 내린 결정이 "묻힘"
- Auto Compact가 실행되면 이전 맥락이 요약되며 세부사항 손실
- 에이전트가 "아까 왜 A를 포기했는지" 잊어버림

### 멀티에이전트가 해결하는 것

```
메인 에이전트 (오케스트레이터)
    │
    ├── 서브에이전트 1: Task 0 수행 → 결과 기록
    │
    ├── 서브에이전트 2: Task 1 수행 (Task 0 결과 읽음) → 결과 기록
    │
    └── 서브에이전트 3: Task 2 수행 (Task 1 결과 읽음) → 결과 기록
```

각 서브에이전트는:
- **신선한 컨텍스트**로 시작
- **필요한 정보만** 읽고 시작
- **결과를 명시적으로 기록**하고 종료

---

## 핵심 원칙

### 1. Task는 "한 에이전트가 완결할 수 있는 크기"

**나쁜 예:**
```
Task 1: 전체 시스템 리팩토링
```

**좋은 예:**
```
Task 1: UserService 클래스 추출
Task 2: UserRepository 인터페이스 정의
Task 3: UserService 테스트 작성
```

**기준:**
- 한 세션에서 Auto Compact 없이 완료 가능한가?
- 목표가 하나인가?
- 성공/실패를 명확히 판단할 수 있는가?

### 2. 에이전트 간 지식 전달은 "파일"로

에이전트는 대화 기록을 공유하지 않습니다.
지식 전달은 반드시 **파일**을 통해 이루어져야 합니다.

```markdown
### Output
다음 Task가 사용할 파일:
- `docs/research/api-spec.json`
- `docs/research/decisions.md`
```

```markdown
### Prerequisites
- Task 0 완료
- `docs/research/api-spec.json` 파일 존재
```

### 3. Success Criteria는 "에이전트가 스스로 검증 가능"해야

**나쁜 예:**
```
- [ ] 코드가 깔끔함
- [ ] 성능이 좋음
```

**좋은 예:**
```
- [ ] `./build.sh` 성공 (exit code 0)
- [ ] `./test.sh` 모든 테스트 통과
- [ ] `./benchmark.sh` 결과 < 100ms
```

에이전트가 스스로 체크박스를 채울 수 있어야 합니다.

### 4. Dependencies는 명시적으로

```markdown
### Dependencies
- Task 0 (api-spec.json 필요)
- Task 1 (MyService.java 필요)
```

이를 통해:
- 메인 에이전트가 실행 순서 결정
- 병렬 실행 가능 여부 판단
- 실패 시 어디서부터 재시작할지 결정

---

## Task 설계 패턴

### 패턴 1: 리서치 → 구현 → 검증

가장 기본적인 구조:

```
Task 0: Research (사전 조사)
    │   - API 스펙 수집
    │   - 기존 코드 분석
    │   - 제약 조건 문서화
    ▼
Task 1: Implementation (구현)
    │   - 핵심 기능 구현
    │   - Task 0 결과 참조
    ▼
Task 2: Verification (검증)
        - 테스트 실행
        - 결과 문서화
```

### 패턴 2: 병렬 구현

독립적인 모듈을 동시에 개발:

```
Task 0: Research
    │
    ├── Task 1a: Module A 구현  ──┐
    │                            │
    ├── Task 1b: Module B 구현  ──┼── Task 2: Integration
    │                            │
    └── Task 1c: Module C 구현  ──┘
                                 │
                                 ▼
                          Task 3: Verification
```

### 패턴 3: 반복 실험

최적화나 튜닝이 필요한 경우:

```
Task 0: Baseline 측정
    │
    ▼
Task 1: 실험 1 → 결과 기록
    │
    ▼
Task 2: 실험 2 (Task 1 결과 참조) → 결과 기록
    │
    ▼
Task 3: 실험 3 (Task 1,2 결과 참조) → 결과 기록
    │
    ▼
Task 4: 최적 설정 선택 및 적용
```

**핵심:** 각 실험 결과가 파일로 기록되어, 다음 에이전트가 "이미 시도한 것"을 알 수 있음.

---

## Decision Points 다루기

어떤 결정은 **실행 전에 할 수 없습니다.**

### 예시: API 응답 구조
```
Task 0에서 API를 호출해봐야 응답 구조를 알 수 있음
→ Task 1의 파싱 로직은 Task 0 결과에 의존
```

### 해결: Decision Points 섹션

```markdown
## Decision Points (Runtime-Dependent)

### After Task 0 (based on API response)
| Decision | How to Verify | Impact |
|----------|---------------|--------|
| Response 구조 | Task 0 결과 JSON 확인 | Task 1 파싱 로직 |
| 페이지네이션 방식 | API 응답 헤더 확인 | Task 1 반복 로직 |
```

Task 0 완료 후, 사람이 Decision Points를 검토하고 Task 1 스펙을 업데이트.

---

## 실패 패턴과 해결

### 패턴 1: 에이전트가 같은 실수 반복

**증상:** A → B → A → B 실패 루프

**원인:**
- Task가 너무 큼 (Auto Compact 발생)
- 이전 시도 결과가 기록되지 않음

**해결:**
1. Task를 더 작게 분할
2. 각 시도 결과를 파일로 기록
```markdown
### Output
- `docs/experiments/attempt-1.md` - 실패 원인: X
- `docs/experiments/attempt-2.md` - 실패 원인: Y
```

### 패턴 2: 에이전트가 Prerequisites 무시

**증상:** Task 1이 Task 0 결과를 안 읽음

**원인:**
- Prerequisites가 명확하지 않음
- "Context for Agent" 섹션 없음

**해결:**
```markdown
### Context for Agent
이 Task를 수행하는 에이전트에게:
1. 먼저 `docs/research/api-spec.json`을 읽으세요
2. 이 파일의 `endpoints` 섹션이 구현 대상입니다
3. 기존 코드 패턴은 `src/existing/` 참고
```

### 패턴 3: 병렬 Task 간 충돌

**증상:** Task 1a와 Task 1b가 같은 파일 수정

**원인:**
- 병렬 Task 간 Output 범위가 겹침

**해결:**
1. 각 Task의 Output 경로를 명확히 분리
2. 공통 파일은 Integration Task에서만 수정
```markdown
Task 1a Output: `src/moduleA/` (이 폴더만 수정)
Task 1b Output: `src/moduleB/` (이 폴더만 수정)
Task 2 (Integration): `src/main.java` (통합)
```

---

## 점진적 확장

### 시작: 3 Task
```
Research → Implementation → Verification
```

### 확장 1: 설계 추가 (4 Task)
```
Research → Design → Implementation → Verification
```
- Design Task에서 아키텍처 결정
- Implementation은 Design 문서를 따름

### 확장 2: 통합 분리 (5 Task)
```
Research → Design → Implementation → Integration → Verification
```
- 여러 모듈을 별도로 구현할 때
- Integration에서 모듈 간 연결

### 확장 3: 병렬 구현 (7+ Task)
```
Research → Design ─┬→ Impl A ─┐
                   ├→ Impl B ─┼→ Integration → Verification
                   └→ Impl C ─┘
```

---

## 체크리스트: Task 설계 검증

새 Task를 만들 때 확인:

### 크기
- [ ] 한 세션에서 완료 가능한가?
- [ ] 목표가 하나인가?
- [ ] Auto Compact 전에 끝날 수 있는가?

### 입력
- [ ] Prerequisites가 모두 나열되어 있는가?
- [ ] 필요한 파일 경로가 명시되어 있는가?
- [ ] Context for Agent 섹션이 있는가?

### 출력
- [ ] Output 파일 경로가 명시되어 있는가?
- [ ] 다음 Task가 이 Output을 어떻게 쓰는지 명확한가?

### 검증
- [ ] Success Criteria가 구체적인가?
- [ ] 에이전트가 스스로 체크박스를 채울 수 있는가?
- [ ] Test Method가 실행 가능한 명령어인가?

### 의존성
- [ ] Dependencies가 모두 나열되어 있는가?
- [ ] 순환 의존성이 없는가?
- [ ] 병렬 실행 가능 여부가 명확한가?

---

## 프롬프트 가이드

### 옵션 A: Task 도구 사용 (권장)

Claude Code의 Task 도구를 사용하면 각 Task가 **별도 컨텍스트**에서 실행됩니다.
Context rot을 방지하는 가장 효과적인 방법입니다.

```
task.md를 읽고, 각 Task를 별도 Task로 실행해줘.
각 Task 완료 후 Progress Tracker를 업데이트하고,
다음 Task 시작 전에 나에게 확인받아줘.
```

**언제 사용:**
- Task가 4개 이상일 때
- 각 Task가 복잡할 때 (코드 생성, 분석 등)
- 실패 루프가 발생할 때

---

### 옵션 B: 수동 세션 분리

각 Task마다 새 세션을 열거나 `/clear`를 사용합니다.
가장 확실하게 컨텍스트를 분리하는 방법입니다.

**세션 1:**
```
task.md의 Task 0만 수행해줘.
완료되면 Progress Tracker 업데이트하고,
Output 파일 경로를 알려줘.
```

**세션 2 (새 창 또는 /clear 후):**
```
task.md의 Task 1 수행해줘.
Task 0 결과는 docs/research/에 있어.
Prerequisites 확인하고 시작해줘.
```

**언제 사용:**
- Task 간 사람의 검토가 반드시 필요할 때
- 각 Task 결과를 꼼꼼히 확인하고 싶을 때
- 처음 멀티에이전트를 시도할 때

---

### 옵션 C: 한 세션에서 순차 실행

작은 프로젝트에서 간단히 사용할 수 있습니다.
단, Context rot 위험이 있으므로 Task가 3개 이하일 때만 권장합니다.

```
task.md를 읽고 Task 0부터 순차 실행해줘.
각 Task 완료 후 나에게 확인받고 다음으로 넘어가줘.
Progress Tracker는 매 Task 완료 시 업데이트해줘.
```

**언제 사용:**
- Task가 3개 이하일 때
- 각 Task가 단순할 때 (파일 복사, 간단한 수정 등)
- 빠르게 프로토타입할 때

---

### 옵션 D: 병렬 실행

독립적인 Task들을 동시에 실행할 때 사용합니다.
여러 터미널/창에서 동시에 Claude Code를 실행합니다.

**창 1:**
```
task.md의 Task 1a만 수행해줘.
Output은 src/moduleA/에만 작성해줘.
다른 폴더는 수정하지 마.
```

**창 2 (동시에):**
```
task.md의 Task 1b만 수행해줘.
Output은 src/moduleB/에만 작성해줘.
다른 폴더는 수정하지 마.
```

**언제 사용:**
- Dependencies가 없는 Task들이 있을 때
- 시간을 단축하고 싶을 때
- 각 Task의 Output 경로가 명확히 분리될 때

---

## 에이전트가 자동으로 수행하는 것

- Prerequisites 파일 읽기
- Success Criteria 검증
- Progress Tracker 업데이트
- Output 경로에 산출물 기록

## 사람이 해야 하는 것

- Task 간 전환 시점에서 결과 검토
- 방향성 의사결정 (여러 선택지가 있을 때)
- 품질이 부족할 경우 재실행 지시
- 실패 시 Task 분할 또는 스펙 수정

---

## 확장 가이드

### 현재 구조 (5 Task, 병렬 포함)
```
Task 0: 사전 조사
    │
    ├── Task 1a: 기능 A 구현  ─┐
    ├── Task 1b: 기능 B 구현  ─┼── (병렬)
    └── Task 1c: 기능 C 구현  ─┘
                               │
                               ▼
                    Task 2: 통합 및 검증
```

### 더 단순하게 (3 Task, 순차)
병렬이 필요 없는 작은 프로젝트:
```
Task 0: 사전 조사
Task 1: 핵심 구현
Task 2: 검증
```

### 더 확장하기 (설계 단계 추가)
복잡한 프로젝트:
```
Task 0: 사전 조사
Task 1: 설계 (NEW)
    │
    ├── Task 2a: 기능 A 구현  ─┐
    ├── Task 2b: 기능 B 구현  ─┼── (병렬)
    └── Task 2c: 기능 C 구현  ─┘
                               │
                               ▼
                    Task 3: 통합 및 검증
```

### 병렬 Task 추가 시 주의사항
1. **Output 경로 분리**: 각 Task가 다른 폴더에만 작성
2. **Context for Agent에 경고**: "다른 폴더는 절대 수정하지 마세요"
3. **통합 Task 필수**: 병렬 결과를 합치는 Task가 반드시 필요

---

## 실패 시 체크리스트

에이전트가 같은 실수를 반복한다면:

### 크기 문제
- [ ] 하나의 목표에만 집중하는 범위인가?
- [ ] 한 에이전트가 컨텍스트 안에서 완결 가능한가?
- [ ] Task를 더 작게 나눠야 하는가?

### 지식 전달 문제
- [ ] Prerequisites가 명확한가?
- [ ] 이전 Task의 Output 경로가 기록되어 있는가?
- [ ] Context for Agent 섹션에 필요한 정보가 있는가?

### 검증 문제
- [ ] Success Criteria가 에이전트가 스스로 확인 가능한가?
- [ ] Test Method가 구체적인가?

---

## 마무리

멀티에이전트의 핵심은 **지능이 아니라 기억**입니다.

- 컨텍스트는 작고, 길어질수록 앞의 결정은 묻힙니다
- 기억의 **크기**와 **구조**, 둘 다 엔지니어링해야 합니다

Task를 나누고, 지식을 파일로 전달하고, 성공 조건을 명확히 하세요.

그러면 Claude Code가 실패 루프에서 탈출합니다.

---

## Version
v1.0 - 2025-01-06
