# Question Bank Review Findings

## Summary
- Total questions: 176 (D1: 37, D2: 31, D3: 36, D4: 35, D5: 37)
- Last updated: 2026-03-15 (via /update command)
- Coverage gaps: 0 task statements under-covered (all have >= 3 questions)
- Scenario gaps: 0 scenarios under-represented (all 6 scenarios appear with 23-31 questions each)

## Resolved Issues (2026-03-15)

- **[RESOLVED] Footer metadata** — All 5 footers now match actual counts and distributions
- **[RESOLVED] Q1.7.5 near-duplicate** — Replaced with genuine session management question (git worktrees for parallel agent isolation)
- **[RESOLVED] Q2.5.3 stem ambiguity** — Stem already clarified in prior update (Edit-already-exhausted context explicit)
- **[RESOLVED] File naming** — All 5 files standardized to hyphenated-lowercase format via `git mv`
- **[RESOLVED] Domain 1 footer count** — Now correctly shows 37 questions
- **[RESOLVED] New topic coverage** — Added Q1.5.5 (Agent Teams), Q3.2.7 (Plugins), Q3.6.7 (Hooks for CI/CD)

## Moderate Issues (should fix)

### [Q1.4] --- Only 3 questions (meets minimum but sparse for a 27% domain)
- **Problem**: Task Statement 1.4 (multi-step workflows with enforcement and handoff patterns) has exactly 3 questions. Domain 1 has 27% weight and this task statement covers critical concepts.
- **Suggested fix**: Add 1-2 more questions to 1.4, perhaps one on handoff summary compilation.

### [Q1.6] --- Only 3 questions (meets minimum but sparse for a 27% domain)
- **Problem**: Task Statement 1.6 (task decomposition strategies) has exactly 3 questions.
- **Suggested fix**: Add 1-2 more questions, perhaps one on adaptive investigation plans.

### [Q5.3] --- Only 4 questions for error propagation
- **Problem**: Task Statement 5.3 (error propagation in multi-agent systems) has 4 questions.
- **Suggested fix**: Consider adding 1 question distinguishing access failures from valid empty results in a multi-agent scenario.

### [Q5.5] --- Only 4 questions for human review workflows
- **Problem**: Task Statement 5.5 (human review workflows and confidence calibration) has 4 questions.
- **Suggested fix**: Consider adding 1 question on calibrating review thresholds using labeled validation sets.

### [Difficulty balance] --- Easy questions are under-represented
- **Problem**: 35 of 176 questions (19.9%) are Easy. Improved from 17.5% but still below ideal ~25%.
- **Suggested fix**: Consider converting some Medium questions to Easy, particularly in Domains 1 and 5.

### [Q4.6] --- Only 4 questions for multi-instance review
- **Problem**: Task Statement 4.6 has only 4 questions for 3 knowledge + 3 skill items.
- **Suggested fix**: Add 1-2 questions on when multi-pass is needed vs single-pass.

### [Scenario coverage for CI] --- Scenario 5 (CI) has fewest questions
- **Problem**: Scenario 5 (Claude Code for CI/CD) has the lowest count. Improved by Q3.6.7 addition.
- **Suggested fix**: Continue preferring Scenario 5 when adding new questions.

## Minor Issues (nice to fix)

### [Q1.7.4 option D] --- Diff-based approach dismissed too quickly
- **Problem**: Q1.7.4's explanation dismisses providing a diff as "less effective" — a judgment call presented as fact.
- **Suggested fix**: Rephrase to explain why describing intent is better than raw diff syntax.

### [Q3.3] --- 4 questions for a relatively narrow topic
- **Problem**: Task 3.3 (path-specific rules, glob patterns) has 4 questions. Well-covered relative to scope.
- **Suggested fix**: No action needed, but candidate for redistribution if needed.

### [Cross-domain questions] --- All are Hard difficulty
- **Problem**: All 10 cross-domain questions are Hard.
- **Suggested fix**: Consider adding 1-2 Medium cross-domain questions.

### [Distractor archetypes] --- "Over-engineered" pattern repeats
- **Problem**: The routing classifier / ML infrastructure distractor appears frequently.
- **Suggested fix**: Vary with alternatives: excessive abstraction layers, premature optimization, custom middleware.

## New Topics for Future Coverage

These significant Claude Code features are not yet covered in the question bank:

| Topic | Relevant Domain | Priority |
|-------|----------------|----------|
| Auto-memory (automatic learning persistence) | Domain 3 (Task 3.1) | Medium |
| Remote Control / Teleport (cross-device sessions) | Domain 3 (Task 3.6) | Medium |
| MCP elicitation (interactive structured input) | Domain 2 (Task 2.4) | Low |
| Files API and Skills API (beta) | Domain 4 (Task 4.5) | Low |
| `inference_geo` data residency parameter | Domain 4 (Task 4.5) | Low |

## Coverage Analysis

### By Task Statement
| Task Statement | Questions | Min Target (3) | Status |
|---|---|---|---|
| 1.1 | 5 | 3 | PASS |
| 1.2 | 5 | 3 | PASS |
| 1.3 | 5 | 3 | PASS |
| 1.4 | 3 | 3 | PASS (at minimum) |
| 1.5 | 5 | 3 | PASS |
| 1.6 | 3 | 3 | PASS (at minimum) |
| 1.7 | 5 | 3 | PASS |
| 2.1 | 6 | 3 | PASS |
| 2.2 | 6 | 3 | PASS |
| 2.3 | 6 | 3 | PASS |
| 2.4 | 5 | 3 | PASS |
| 2.5 | 6 | 3 | PASS |
| 3.1 | 6 | 3 | PASS |
| 3.2 | 7 | 3 | PASS |
| 3.3 | 4 | 3 | PASS |
| 3.4 | 5 | 3 | PASS |
| 3.5 | 5 | 3 | PASS |
| 3.6 | 7 | 3 | PASS |
| 4.1 | 5 | 3 | PASS |
| 4.2 | 6 | 3 | PASS |
| 4.3 | 6 | 3 | PASS |
| 4.4 | 5 | 3 | PASS |
| 4.5 | 6 | 3 | PASS |
| 4.6 | 4 | 3 | PASS |
| 5.1 | 7 | 3 | PASS |
| 5.2 | 6 | 3 | PASS |
| 5.3 | 4 | 3 | PASS |
| 5.4 | 6 | 3 | PASS |
| 5.5 | 4 | 3 | PASS |
| 5.6 | 6 | 3 | PASS |

### By Scenario
| Scenario | Questions | Status |
|---|---|---|
| 1: Customer Support Resolution Agent | 30 | PASS |
| 2: Code Generation with Claude Code | 27 | PASS |
| 3: Multi-Agent Research System | 31 | PASS |
| 4: Developer Productivity with Claude | 30 | PASS |
| 5: Claude Code for Continuous Integration | 24 | PASS |
| 6: Structured Data Extraction | 29 | PASS |

### Cross-Domain Questions
- Count: 10 (2 per domain file)
- All are Hard difficulty
- Domain combinations covered: 1+2+5, 1+5, 2+1+5, 2+3, 3+2+5, 3+4, 4+5, 4+3+1, 5+1+2, 5+1+4
- Good spread of domain combinations
