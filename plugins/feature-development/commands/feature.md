---
description: Systematic feature development with codebase exploration, architecture design, and team-based implementation loops
allowed-tools: Task, Bash
---

# Feature Development Command

Systematic feature development: understand the codebase, resolve ambiguities, design architecture with a review loop, implement with a review loop, test with a review loop.

> **Prerequisite**: Requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` for Phase 4, Phase 5, and Phase 6.

## Usage

```bash
/feature "Add user authentication with OAuth2"
```

## Workflow

### Phase 1: Discovery

Clarify what needs to be built.

1. Gather context: problem, users, constraints, success criteria
2. Summarize requirements back to user, identify gaps
3. Get confirmation before proceeding

**Output**: Clear problem statement and requirements summary

### Phase 2: Codebase Exploration

Understand relevant existing code at both high and low levels.

Launch parallel Explore agents:

```
Task(subagent_type="Explore", prompt="Trace how [similar feature] works end-to-end")
Task(subagent_type="Explore", prompt="Map architecture layers for [relevant domain]")
Task(subagent_type="Explore", prompt="Find all integration points for [system component]")
Task(subagent_type="Explore", prompt="Analyze data models and relationships for [domain]")
```

**Output**: Codebase analysis with file references and patterns

### Phase 3: Clarifying Questions

Resolve ALL ambiguities before designing.

> **CRITICAL**: DO NOT proceed until all questions are answered.

Load the `clarifying-questions` skill. Present unresolved ambiguities:
- Edge cases, error handling, performance, security, backward compatibility, design preferences

```markdown
## Questions Requiring Answers

### Must Answer (Blocking)
1. [Critical question]

### Should Clarify (Recommended)
1. [Important question]
```

**PAUSE**: Wait for user answers before Phase 4.

### Phase 4: Architecture Do-Loop (agent team)

Load the `do-loop` skill. Spawn a doer+critic pair to design and review the architecture.

Start a team of 2:

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| architect | Design architecture applying SOLID, Clean Architecture, Design Patterns, DDD principles. Produce implementation blueprint with specific files, interfaces, data flow. Follow existing codebase patterns. | opus | Requirements + exploration results + solid-principles, clean-architecture, design-patterns, domain-driven-design skills |
| arch-reviewer | Review architecture for feasibility, principle compliance, gaps, over-engineering. Challenge assumptions. | opus | Same context + do-loop iteration protocol (max 3 rounds, structured feedback format, exit conditions) |

**Task**: `Design architecture for [feature] — blueprint with files, interfaces, data flow, dependency order`

**Done means**: Blueprint specifies all components, their interfaces, data flow between them, and implementation order. Every component has a clear responsibility and placement in the codebase.

**Quality bar for the arch-reviewer**:
- Architecture follows SOLID principles and Clean Architecture layering
- No circular dependencies between components
- Interfaces are minimal and focused (ISP)
- Data flow is explicit with clear ownership
- No over-engineering — each component earns its existence
- Implementation order respects dependency chain

Iterate per do-loop protocol (max 3 rounds). If the arch-reviewer and architect deadlock, you (the team lead) decide based on the exploration results and requirements.

**PAUSE**: Present the approved blueprint to the user. Wait for user confirmation before Phase 5.

### Phase 5: Implementation Do-Loop (agent team)

Load the `do-loop` skill. Spawn a new doer+critic pair to implement and review the code.

Start a team of 2:

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| implementer | Write code per approved blueprint. Follow codebase conventions from Phase 2. Implement in dependency order. | opus | Approved blueprint + codebase conventions + exploration results |
| code-reviewer | Review implementation for bugs, security, architecture violations, edge cases. Reference blueprint as source of truth. | opus | Blueprint + do-loop iteration protocol (max 3 rounds, structured feedback format, exit conditions) + review-loop skill |

**Task**: `Implement [feature] per approved blueprint — [acceptance criteria from blueprint]`

**Done means**: All components from the blueprint are implemented, follow codebase conventions, and handle edge cases identified in Phase 3.

**Quality bar for the code-reviewer**:
- Implementation matches the approved blueprint structure
- No architecture violations (layer boundaries, dependency direction)
- Error handling covers failure modes identified in the analysis
- Code follows existing codebase patterns and conventions
- Edge cases from Phase 3 are handled
- No security issues introduced

Iterate per do-loop protocol (max 3 rounds). If the code-reviewer and implementer deadlock, you (the team lead) decide based on the approved blueprint.

### Phase 6: Test Do-Loop (agent team)

Load the `do-loop` skill. Spawn a new doer+critic pair to write and review tests.

Start a team of 2:

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| test-writer | Write unit, integration, and E2E tests. Follow existing test patterns. Target 80%+ coverage of new code. | opus | Implementation changes + blueprint + testing-loop skill |
| test-reviewer | Review test quality: coverage, assertions, edge cases, naming, mocking strategy. | opus | Do-loop iteration protocol (max 2 rounds, structured feedback format, exit conditions) |

**Task**: `Write tests for [feature] — unit + integration + E2E covering all blueprint components`

**Done means**: Tests cover all new components, edge cases from Phase 3, and integration points. Tests follow existing test patterns and pass.

**Quality bar for the test-reviewer**:
- Test names describe the behavior, not the implementation
- Tests follow Arrange-Act-Assert structure
- Assertions verify correct behavior, not just "doesn't crash"
- Edge cases and error paths are covered
- Mocking strategy is appropriate (no over-mocking)
- Tests are placed near related existing tests

One mini-loop (1-2 rounds). The test-reviewer approves once coverage and quality are adequate.

### Phase 7: Summary

After all do-loops complete:

1. Run the tests to confirm everything passes
2. Run `git diff` to review all changes

Present the summary to the user:

```markdown
## Feature Implementation Summary

### Completed
- [x] Component 1 implemented
- [x] Component 2 implemented

### Key Decisions
1. Used [approach] because [reason]

### Files Changed
- `src/services/Feature.js` (new) — [purpose]
- `src/routes/index.js` (modified) — [what changed]

### Tests Added
- `tests/Feature.test.js` — [what's covered]

### Verification
- [x] Unit tests pass
- [x] Integration tests pass
- [ ] Ready for commit
```

The user decides whether to commit.

## Skills

| Skill | Used In | Purpose |
|-------|---------|---------|
| `clarifying-questions` | Phase 3 | Structured approach to identifying and resolving ambiguities |
| `solid-principles` | Phase 4 | SRP, OCP, LSP, ISP, DIP |
| `clean-architecture` | Phase 4 | Layered structure with Dependency Rule |
| `design-patterns` | Phase 4 | Creational, Structural, Behavioral patterns |
| `domain-driven-design` | Phase 4 | Bounded Contexts, Aggregates, Domain Events |
| `do-loop` | Phase 4, 5, 6 | Iteration protocol for doer+critic agent team loops |
| `review-loop` | Phase 5 | Code review guidelines and quality criteria |
| `testing-loop` | Phase 6 | Test writing guidelines and coverage strategy |
