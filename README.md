# Claude Code Multi-Agent Guide

> This document explains **why** multi-agent architecture is needed and **how** to design it.

[Korean](README.kr.md)

---

## Quick Start - Run the Example

Run Claude Code and enter this prompt:

```
Clone https://github.com/ailnk0/claude-multiagent-template.git and cd into it.
Then read docs/example-task.md and execute each Task using the Task tool (subagent).
After each Task completes, update the Progress Tracker and record the deliverables.
```

**What the example does:**
```
Task 0: Market Research (WebSearch to select 3 competitors)
    │
    ├── Task 1a: Competitor A Research ─┐
    ├── Task 1b: Competitor B Research ─┼── (parallel)
    └── Task 1c: Competitor C Research ─┘
                                        │
                                        ▼
            Task 2: Comparative Analysis (feature matrix, insights)
                                        │
                                        ▼
            Task 3: HTML Report (open in browser)
```

**Final output:** `output/report.html` - professional competitor analysis report

## Quick Start - Create Your Own

Copy `docs/task-template.md` and work with Claude to customize it for your needs.

This step requires significant effort. Work iteratively with Claude to define:
- Clear goals for each task
- Prerequisites and dependencies
- Success criteria (must be self-verifiable by the agent)
- File-based knowledge transfer between tasks

The quality of your task definitions determines the success of multi-agent execution. Read the guide below to understand the principles.

That's it. For details on why this works and how to customize, read on.

---

## Why Multi-Agent?

### Limitations of Single Agent

Common issues when running complex tasks with Claude Code:

```
Attempt approach A
    ↓ fails
Switch to approach B
    ↓ fails
Return to approach A  ← failure loop
    ↓ fails
...repeat
```

**Cause 1: Context is finite**
- Claude Code's context window is approximately 200K tokens
- Large codebase + long conversation = quickly hitting limits

**Cause 2: Context rot**
- As conversation lengthens, earlier decisions get "buried"
- When Auto Compact runs, previous context is summarized and details are lost
- Agent forgets "why we abandoned approach A earlier"

### What Multi-Agent Solves

```
Main Agent (Orchestrator)
    │
    ├── Sub-agent 1: Execute Task 0 → Record results
    │
    ├── Sub-agent 2: Execute Task 1 (reads Task 0 results) → Record results
    │
    └── Sub-agent 3: Execute Task 2 (reads Task 1 results) → Record results
```

Each sub-agent:
- Starts with **fresh context**
- Reads **only necessary information**
- **Explicitly records results** before terminating

---

## Core Principles

### 1. Tasks Should Be "Completable by One Agent"

**Bad example:**
```
Task 1: Refactor entire system
```

**Good example:**
```
Task 1: Extract UserService class
Task 2: Define UserRepository interface
Task 3: Write UserService tests
```

**Criteria:**
- Can it be completed in one session without Auto Compact?
- Is there a single goal?
- Can success/failure be clearly determined?

### 2. Knowledge Transfer Between Agents via Files

Agents don't share conversation history.
Knowledge transfer must happen through **files**.

```markdown
### Output
Files for the next Task:
- `docs/research/api-spec.json`
- `docs/research/decisions.md`
```

```markdown
### Prerequisites
- Task 0 completed
- `docs/research/api-spec.json` file exists
```

### 3. Success Criteria Must Be "Self-Verifiable by Agent"

**Bad example:**
```
- [ ] Code is clean
- [ ] Performance is good
```

**Good example:**
```
- [ ] `./build.sh` succeeds (exit code 0)
- [ ] `./test.sh` all tests pass
- [ ] `./benchmark.sh` result < 100ms
```

The agent must be able to check the boxes itself.

### 4. Dependencies Must Be Explicit

```markdown
### Dependencies
- Task 0 (needs api-spec.json)
- Task 1 (needs MyService.java)
```

This enables:
- Main agent to determine execution order
- Identify parallel execution opportunities
- Decide where to restart on failure

---

## Task Design Patterns

### Pattern 1: Research → Implementation → Verification

The most basic structure:

```
Task 0: Research
    │   - Collect API specs
    │   - Analyze existing code
    │   - Document constraints
    ▼
Task 1: Implementation
    │   - Implement core functionality
    │   - Reference Task 0 results
    ▼
Task 2: Verification
        - Run tests
        - Document results
```

### Pattern 2: Parallel Implementation

Develop independent modules simultaneously:

```
Task 0: Research
    │
    ├── Task 1a: Implement Module A  ──┐
    │                                  │
    ├── Task 1b: Implement Module B  ──┼── Task 2: Integration
    │                                  │
    └── Task 1c: Implement Module C  ──┘
                                       │
                                       ▼
                                Task 3: Verification
```

### Pattern 3: Iterative Experiments

When optimization or tuning is needed:

```
Task 0: Measure baseline
    │
    ▼
Task 1: Experiment 1 → Record results
    │
    ▼
Task 2: Experiment 2 (reference Task 1 results) → Record results
    │
    ▼
Task 3: Experiment 3 (reference Task 1,2 results) → Record results
    │
    ▼
Task 4: Select and apply optimal configuration
```

**Key:** Each experiment result is recorded to file, so the next agent knows "what was already tried."

---

## Handling Decision Points

Some decisions **cannot be made before execution**.

### Example: API Response Structure
```
Need to call API in Task 0 to know response structure
→ Task 1's parsing logic depends on Task 0 results
```

### Solution: Decision Points Section

```markdown
## Decision Points (Runtime-Dependent)

### After Task 0 (based on API response)
| Decision | How to Verify | Impact |
|----------|---------------|--------|
| Response structure | Check Task 0 result JSON | Task 1 parsing logic |
| Pagination method | Check API response headers | Task 1 iteration logic |
```

After Task 0 completes, humans review Decision Points and update Task 1 specs.

---

## Failure Patterns and Solutions

### Pattern 1: Agent Repeats Same Mistakes

**Symptom:** A → B → A → B failure loop

**Cause:**
- Task is too large (Auto Compact occurs)
- Previous attempt results not recorded

**Solution:**
1. Split task into smaller pieces
2. Record each attempt result to file
```markdown
### Output
- `docs/experiments/attempt-1.md` - Failure reason: X
- `docs/experiments/attempt-2.md` - Failure reason: Y
```

### Pattern 2: Agent Ignores Prerequisites

**Symptom:** Task 1 doesn't read Task 0 results

**Cause:**
- Prerequisites not clear
- Missing "Context for Agent" section

**Solution:**
```markdown
### Context for Agent
Instructions for the agent performing this task:
1. First read `docs/research/api-spec.json`
2. The `endpoints` section in this file is the implementation target
3. Reference existing code patterns in `src/existing/`
```

### Pattern 3: Parallel Task Conflicts

**Symptom:** Task 1a and Task 1b modify the same file

**Cause:**
- Output scope overlaps between parallel tasks

**Solution:**
1. Clearly separate Output paths for each task
2. Common files modified only in Integration Task
```markdown
Task 1a Output: `src/moduleA/` (modify only this folder)
Task 1b Output: `src/moduleB/` (modify only this folder)
Task 2 (Integration): `src/main.java` (integration)
```

---

## Incremental Scaling

### Start: 3 Tasks
```
Research → Implementation → Verification
```

### Extension 1: Add Design (4 Tasks)
```
Research → Design → Implementation → Verification
```
- Architecture decisions in Design Task
- Implementation follows Design document

### Extension 2: Separate Integration (5 Tasks)
```
Research → Design → Implementation → Integration → Verification
```
- When implementing multiple modules separately
- Integration connects modules

### Extension 3: Parallel Implementation (7+ Tasks)
```
Research → Design ─┬→ Impl A ─┐
                   ├→ Impl B ─┼→ Integration → Verification
                   └→ Impl C ─┘
```

---

## Checklist: Task Design Verification

When creating a new Task, verify:

### Size
- [ ] Completable in one session?
- [ ] Single goal?
- [ ] Can finish before Auto Compact?

### Input
- [ ] All Prerequisites listed?
- [ ] Required file paths specified?
- [ ] Context for Agent section exists?

### Output
- [ ] Output file paths specified?
- [ ] Clear how next Task uses this Output?

### Verification
- [ ] Success Criteria specific?
- [ ] Agent can check boxes itself?
- [ ] Test Method is executable command?

### Dependencies
- [ ] All Dependencies listed?
- [ ] No circular dependencies?
- [ ] Parallel execution possibility clear?

---

## Prompt Guide

### Option A: Using Task Tool (Recommended)

Using Claude Code's Task tool runs each Task in a **separate context**.
This is the most effective way to prevent context rot.

```
Read task.md and execute each Task as a separate Task.
After each Task completes, update the Progress Tracker,
and get my confirmation before starting the next Task.
```

**When to use:**
- 4 or more Tasks
- Each Task is complex (code generation, analysis, etc.)
- Failure loops are occurring

---

### Option B: Manual Session Separation

Open a new session or use `/clear` for each Task.
This is the most reliable way to separate context.

**Session 1:**
```
Execute only Task 0 from task.md.
When complete, update Progress Tracker,
and tell me the Output file paths.
```

**Session 2 (new window or after /clear):**
```
Execute Task 1 from task.md.
Task 0 results are in docs/research/.
Check Prerequisites and begin.
```

**When to use:**
- Human review required between Tasks
- Want to carefully check each Task result
- First time trying multi-agent

---

### Option C: Sequential Execution in One Session

Can be used simply for small projects.
However, due to context rot risk, only recommended when 3 or fewer Tasks.

```
Read task.md and execute sequentially from Task 0.
Get my confirmation after each Task before proceeding.
Update Progress Tracker on each Task completion.
```

**When to use:**
- 3 or fewer Tasks
- Each Task is simple (file copy, simple edits, etc.)
- Rapid prototyping

---

### Option D: Parallel Execution

Used when running independent Tasks simultaneously.
Run Claude Code simultaneously in multiple terminals/windows.

**Window 1:**
```
Execute only Task 1a from task.md.
Write Output only to src/moduleA/.
Do not modify other folders.
```

**Window 2 (simultaneously):**
```
Execute only Task 1b from task.md.
Write Output only to src/moduleB/.
Do not modify other folders.
```

**When to use:**
- Tasks with no Dependencies exist
- Want to reduce time
- Output paths for each Task are clearly separated

---

## What the Agent Does Automatically

- Read Prerequisites files
- Verify Success Criteria
- Update Progress Tracker
- Record deliverables to Output path

## What Humans Must Do

- Review results at Task transitions
- Make directional decisions (when multiple options exist)
- Instruct re-execution when quality is insufficient
- Split Tasks or modify specs on failure

---

## Scaling Guide

### Current Structure (5 Tasks, with parallel)
```
Task 0: Research
    │
    ├── Task 1a: Feature A Implementation  ─┐
    ├── Task 1b: Feature B Implementation  ─┼── (parallel)
    └── Task 1c: Feature C Implementation  ─┘
                                            │
                                            ▼
                             Task 2: Integration & Verification
```

### Simpler (3 Tasks, sequential)
For small projects without parallel needs:
```
Task 0: Research
Task 1: Core Implementation
Task 2: Verification
```

### More Complex (Add Design phase)
For complex projects:
```
Task 0: Research
Task 1: Design (NEW)
    │
    ├── Task 2a: Feature A Implementation  ─┐
    ├── Task 2b: Feature B Implementation  ─┼── (parallel)
    └── Task 2c: Feature C Implementation  ─┘
                                            │
                                            ▼
                             Task 3: Integration & Verification
```

### Notes for Adding Parallel Tasks
1. **Separate Output paths**: Each Task writes to different folders
2. **Warning in Context for Agent**: "Do NOT modify other folders"
3. **Integration Task required**: Task to merge parallel results is mandatory

---

## Failure Checklist

If the agent repeats the same mistakes:

### Size Issues
- [ ] Is scope focused on a single goal?
- [ ] Can one agent complete within context?
- [ ] Should the Task be split smaller?

### Knowledge Transfer Issues
- [ ] Are Prerequisites clear?
- [ ] Is previous Task's Output path recorded?
- [ ] Does Context for Agent section have necessary info?

### Verification Issues
- [ ] Can agent verify Success Criteria itself?
- [ ] Is Test Method specific?

---

## Conclusion

The key to multi-agent is **memory, not intelligence**.

- Context is limited, and earlier decisions get buried as it lengthens
- Both the **size** and **structure** of memory must be engineered

Split tasks, transfer knowledge via files, and make success criteria clear.

Then Claude Code escapes the failure loop.

---

## Version
v1.0 - 2025-01-06
