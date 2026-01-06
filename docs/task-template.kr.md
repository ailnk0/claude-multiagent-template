# Multi-Agent Task Template (Starter)

## Progress Tracker

| Task | Status | Completed | Output |
|------|--------|-----------|--------|
| Task 0: 사전 조사 | ⬜ not_started | - | `docs/research/` |
| Task 1a: 기능 A 구현 | ⬜ not_started | - | `src/featureA/` |
| Task 1b: 기능 B 구현 | ⬜ not_started | - | `src/featureB/` |
| Task 1c: 기능 C 구현 | ⬜ not_started | - | `src/featureC/` |
| Task 2: 통합 및 검증 | ⬜ not_started | - | `src/`, `tests/` |

**병렬 실행 가능:** Task 1a, 1b, 1c는 동시에 실행 가능

**Status Legend:**
- ⬜ `not_started` - 시작 전
- 🔄 `in_progress` - 진행 중
- ✅ `completed` - 완료 및 검증됨
- ⏸️ `blocked` - 의존성 대기 중

---

## Execution Order

```
Task 0: 사전 조사
    │
    ▼
    ┌──────────────┬──────────────┐
    │              │              │
    ▼              ▼              ▼
Task 1a:       Task 1b:       Task 1c:
기능 A 구현    기능 B 구현    기능 C 구현
(병렬 실행 가능)
    │              │              │
    └──────────────┼──────────────┘
                   │
                   ▼
           Task 2: 통합 및 검증
```

**병렬 실행:** Task 1a, 1b, 1c는 서로 독립적이므로 동시에 실행 가능

---

## Task 0: 사전 조사 (Research)

### Goal
구현 전에 필요한 정보를 수집하고 문서화한다.

### Prerequisites
- 없음 (첫 번째 Task)

### 수행할 작업
```bash
# 예시: API 스펙 수집
curl https://api.example.com/docs > docs/research/api-spec.json

# 예시: 기존 코드 구조 파악
find src/ -name "*.java" -type f | head -20 > docs/research/file-structure.txt
```

### Success Criteria
- [ ] 필요한 API 스펙이 `docs/research/`에 저장됨
- [ ] 기존 코드 구조가 문서화됨
- [ ] 다음 Task에서 참조할 정보가 명확히 정리됨

### Output
다음 Task가 사용할 파일:
- `docs/research/api-spec.json`
- `docs/research/implementation-notes.md`

### Dependencies
- 없음

---

## Task 1a: 기능 A 구현

### Goal
[기능 A의 구체적인 구현 목표를 작성]

### Prerequisites
- Task 0 완료
- `docs/research/api-spec.json` 파일 존재

### Context for Agent
```
이 Task를 수행하는 에이전트에게:
1. docs/research/api-spec.json을 먼저 읽으세요
2. src/featureA/ 폴더에만 파일을 생성하세요
3. 다른 폴더(featureB, featureC)는 절대 수정하지 마세요
4. 구현 완료 후 이 문서의 체크박스를 업데이트하세요
```

### 구현 상세
```java
// 예시: 구현할 클래스 구조
public class FeatureA {
    // Task 0에서 수집한 API 스펙 기반으로 구현
}
```

### Success Criteria
- [ ] `src/featureA/FeatureA.java` 파일 생성됨
- [ ] 컴파일 성공: `./build.sh`
- [ ] 단위 테스트 통과: `./test.sh featureA`

### Test Method
```bash
# 빌드 확인
./build.sh

# 기능 A 테스트
./test.sh featureA
```

### Output
- `src/featureA/FeatureA.java`
- `src/featureA/` 하위 관련 파일들

### Dependencies
- Task 0

---

## Task 1b: 기능 B 구현

### Goal
[기능 B의 구체적인 구현 목표를 작성]

### Prerequisites
- Task 0 완료
- `docs/research/api-spec.json` 파일 존재

### Context for Agent
```
이 Task를 수행하는 에이전트에게:
1. docs/research/api-spec.json을 먼저 읽으세요
2. src/featureB/ 폴더에만 파일을 생성하세요
3. 다른 폴더(featureA, featureC)는 절대 수정하지 마세요
4. 구현 완료 후 이 문서의 체크박스를 업데이트하세요
```

### 구현 상세
```java
// 예시: 구현할 클래스 구조
public class FeatureB {
    // Task 0에서 수집한 API 스펙 기반으로 구현
}
```

### Success Criteria
- [ ] `src/featureB/FeatureB.java` 파일 생성됨
- [ ] 컴파일 성공: `./build.sh`
- [ ] 단위 테스트 통과: `./test.sh featureB`

### Test Method
```bash
# 빌드 확인
./build.sh

# 기능 B 테스트
./test.sh featureB
```

### Output
- `src/featureB/FeatureB.java`
- `src/featureB/` 하위 관련 파일들

### Dependencies
- Task 0

---

## Task 1c: 기능 C 구현

### Goal
[기능 C의 구체적인 구현 목표를 작성]

### Prerequisites
- Task 0 완료
- `docs/research/api-spec.json` 파일 존재

### Context for Agent
```
이 Task를 수행하는 에이전트에게:
1. docs/research/api-spec.json을 먼저 읽으세요
2. src/featureC/ 폴더에만 파일을 생성하세요
3. 다른 폴더(featureA, featureB)는 절대 수정하지 마세요
4. 구현 완료 후 이 문서의 체크박스를 업데이트하세요
```

### 구현 상세
```java
// 예시: 구현할 클래스 구조
public class FeatureC {
    // Task 0에서 수집한 API 스펙 기반으로 구현
}
```

### Success Criteria
- [ ] `src/featureC/FeatureC.java` 파일 생성됨
- [ ] 컴파일 성공: `./build.sh`
- [ ] 단위 테스트 통과: `./test.sh featureC`

### Test Method
```bash
# 빌드 확인
./build.sh

# 기능 C 테스트
./test.sh featureC
```

### Output
- `src/featureC/FeatureC.java`
- `src/featureC/` 하위 관련 파일들

### Dependencies
- Task 0

---

## Task 2: 통합 및 검증

### Goal
기능 A, B, C를 통합하고 전체 시스템이 요구사항을 만족하는지 검증한다.

### Prerequisites
- Task 1a 완료: `src/featureA/` 존재
- Task 1b 완료: `src/featureB/` 존재
- Task 1c 완료: `src/featureC/` 존재
- 모든 개별 테스트 통과 상태

### Context for Agent
```
이 Task를 수행하는 에이전트에게:
1. src/featureA/, src/featureB/, src/featureC/ 코드를 먼저 읽으세요
2. 각 기능을 통합하는 코드를 src/에 작성하세요
3. docs/research/의 요구사항과 비교하며 검증하세요
4. 통합 테스트 실패 시 원인을 기록하세요
```

### 통합 작업
```java
// 예시: 통합 클래스 구조
public class Application {
    private FeatureA featureA;
    private FeatureB featureB;
    private FeatureC featureC;

    // 기능들을 조합하여 전체 흐름 구현
}
```

### 검증 항목
| 항목 | 검증 방법 | 예상 결과 |
|------|----------|----------|
| 기능 A 단위 | `./test.sh featureA` | PASS |
| 기능 B 단위 | `./test.sh featureB` | PASS |
| 기능 C 단위 | `./test.sh featureC` | PASS |
| 통합 테스트 | `./test.sh integration` | PASS |
| 성능 | `./benchmark.sh` | < 100ms |

### Success Criteria
- [ ] `src/Application.java` 통합 코드 작성됨
- [ ] 모든 단위 테스트 통과
- [ ] 통합 테스트 통과
- [ ] 성능 기준 충족
- [ ] 결과가 `docs/results/`에 기록됨

### Test Method
```bash
# 전체 테스트
./test.sh

# 통합 테스트만
./test.sh integration

# 결과 확인
cat docs/results/test-report.json
```

### Output
- `src/Application.java`
- `docs/results/test-report.json`
- `docs/results/summary.md`

### Dependencies
- Task 1a
- Task 1b
- Task 1c
