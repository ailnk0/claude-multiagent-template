# Multi-Agent Task Template (Starter)

## Progress Tracker

| Task | Status | Completed | Output |
|------|--------|-----------|--------|
| Task 0: Research | ⬜ not_started | - | `docs/research/` |
| Task 1a: Feature A Implementation | ⬜ not_started | - | `src/featureA/` |
| Task 1b: Feature B Implementation | ⬜ not_started | - | `src/featureB/` |
| Task 1c: Feature C Implementation | ⬜ not_started | - | `src/featureC/` |
| Task 2: Integration & Verification | ⬜ not_started | - | `src/`, `tests/` |

**Parallel Execution:** Tasks 1a, 1b, 1c can run simultaneously

**Status Legend:**
- ⬜ `not_started` - Not yet started
- 🔄 `in_progress` - In progress
- ✅ `completed` - Completed and verified
- ⏸️ `blocked` - Waiting for dependencies

---

## Execution Order

```
Task 0: Research
    │
    ▼
    ┌──────────────┬──────────────┐
    │              │              │
    ▼              ▼              ▼
Task 1a:       Task 1b:       Task 1c:
Feature A      Feature B      Feature C
Implementation Implementation Implementation
(Can run in parallel)
    │              │              │
    └──────────────┼──────────────┘
                   │
                   ▼
           Task 2: Integration & Verification
```

**Parallel Execution:** Tasks 1a, 1b, 1c are independent and can run simultaneously

---

## Task 0: Research

### Goal
Gather and document necessary information before implementation.

### Prerequisites
- None (first task)

### Tasks to Perform
```bash
# Example: Collect API specs
curl https://api.example.com/docs > docs/research/api-spec.json

# Example: Analyze existing code structure
find src/ -name "*.java" -type f | head -20 > docs/research/file-structure.txt
```

### Success Criteria
- [ ] Required API specs saved in `docs/research/`
- [ ] Existing code structure documented
- [ ] Information for subsequent tasks clearly organized

### Output
Files for subsequent tasks:
- `docs/research/api-spec.json`
- `docs/research/implementation-notes.md`

### Dependencies
- None

---

## Task 1a: Feature A Implementation

### Goal
[Write specific implementation goal for Feature A]

### Prerequisites
- Task 0 completed
- `docs/research/api-spec.json` file exists

### Context for Agent
```
Instructions for the agent performing this task:
1. Read docs/research/api-spec.json first
2. Create files only in src/featureA/ folder
3. Do NOT modify other folders (featureB, featureC)
4. Update checkboxes in this document after completion
```

### Implementation Details
```java
// Example: Class structure to implement
public class FeatureA {
    // Implement based on API specs collected in Task 0
}
```

### Success Criteria
- [ ] `src/featureA/FeatureA.java` file created
- [ ] Build succeeds: `./build.sh`
- [ ] Unit tests pass: `./test.sh featureA`

### Test Method
```bash
# Verify build
./build.sh

# Test Feature A
./test.sh featureA
```

### Output
- `src/featureA/FeatureA.java`
- Related files under `src/featureA/`

### Dependencies
- Task 0

---

## Task 1b: Feature B Implementation

### Goal
[Write specific implementation goal for Feature B]

### Prerequisites
- Task 0 completed
- `docs/research/api-spec.json` file exists

### Context for Agent
```
Instructions for the agent performing this task:
1. Read docs/research/api-spec.json first
2. Create files only in src/featureB/ folder
3. Do NOT modify other folders (featureA, featureC)
4. Update checkboxes in this document after completion
```

### Implementation Details
```java
// Example: Class structure to implement
public class FeatureB {
    // Implement based on API specs collected in Task 0
}
```

### Success Criteria
- [ ] `src/featureB/FeatureB.java` file created
- [ ] Build succeeds: `./build.sh`
- [ ] Unit tests pass: `./test.sh featureB`

### Test Method
```bash
# Verify build
./build.sh

# Test Feature B
./test.sh featureB
```

### Output
- `src/featureB/FeatureB.java`
- Related files under `src/featureB/`

### Dependencies
- Task 0

---

## Task 1c: Feature C Implementation

### Goal
[Write specific implementation goal for Feature C]

### Prerequisites
- Task 0 completed
- `docs/research/api-spec.json` file exists

### Context for Agent
```
Instructions for the agent performing this task:
1. Read docs/research/api-spec.json first
2. Create files only in src/featureC/ folder
3. Do NOT modify other folders (featureA, featureB)
4. Update checkboxes in this document after completion
```

### Implementation Details
```java
// Example: Class structure to implement
public class FeatureC {
    // Implement based on API specs collected in Task 0
}
```

### Success Criteria
- [ ] `src/featureC/FeatureC.java` file created
- [ ] Build succeeds: `./build.sh`
- [ ] Unit tests pass: `./test.sh featureC`

### Test Method
```bash
# Verify build
./build.sh

# Test Feature C
./test.sh featureC
```

### Output
- `src/featureC/FeatureC.java`
- Related files under `src/featureC/`

### Dependencies
- Task 0

---

## Task 2: Integration & Verification

### Goal
Integrate Features A, B, C and verify the entire system meets requirements.

### Prerequisites
- Task 1a completed: `src/featureA/` exists
- Task 1b completed: `src/featureB/` exists
- Task 1c completed: `src/featureC/` exists
- All individual tests passing

### Context for Agent
```
Instructions for the agent performing this task:
1. Read code in src/featureA/, src/featureB/, src/featureC/ first
2. Write integration code in src/
3. Verify against requirements in docs/research/
4. Document cause if integration tests fail
```

### Integration Tasks
```java
// Example: Integration class structure
public class Application {
    private FeatureA featureA;
    private FeatureB featureB;
    private FeatureC featureC;

    // Implement complete flow by combining features
}
```

### Verification Items
| Item | Verification Method | Expected Result |
|------|---------------------|-----------------|
| Feature A Unit | `./test.sh featureA` | PASS |
| Feature B Unit | `./test.sh featureB` | PASS |
| Feature C Unit | `./test.sh featureC` | PASS |
| Integration Test | `./test.sh integration` | PASS |
| Performance | `./benchmark.sh` | < 100ms |

### Success Criteria
- [ ] `src/Application.java` integration code written
- [ ] All unit tests pass
- [ ] Integration tests pass
- [ ] Performance criteria met
- [ ] Results recorded in `docs/results/`

### Test Method
```bash
# Run all tests
./test.sh

# Run integration tests only
./test.sh integration

# Check results
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
