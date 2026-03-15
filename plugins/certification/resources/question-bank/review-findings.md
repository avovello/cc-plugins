# Question Bank Review Findings

## Summary
- Total questions reviewed: 166 (D1: 32, D2: 31, D3: 34, D4: 34, D5: 35)
- Issues found: 18 (critical: 2, moderate: 10, minor: 6)
- Coverage gaps: 0 task statements under-covered (all have >= 3 questions)
- Scenario gaps: 0 scenarios under-represented (all 6 scenarios appear with 22-30 questions each)

## Critical Issues (must fix)

### [All files] --- Footer total and distribution claims are wrong
- **Problem**: Every file's footer metadata is incorrect. The footers all claim "28 questions" but actual counts are: D1=32, D2=31, D3=34, D4=34, D5=35. Difficulty distributions are also wrong in every file:
  - D1 footer: "5 Easy, 13 Medium, 12 Hard" -- actual: 5 Easy, 17 Medium, 10 Hard
  - D2 footer: "6 Easy, 12 Medium, 8 Hard" -- actual: 7 Easy, 15 Medium, 9 Hard
  - D3 footer: "5 Easy, 14 Medium, 9 Hard" -- actual: 7 Easy, 19 Medium, 8 Hard
  - D4 footer: "4 Easy, 14 Medium, 10 Hard" -- actual: 6 Easy, 19 Medium, 9 Hard
  - D5 footer: "3 Easy, 15 Medium, 10 Hard" -- actual: 4 Easy, 20 Medium, 11 Hard
- **Suggested fix**: Update all five footer lines to reflect actual question counts and difficulty distributions.

### [Q1.7.5 + Q4.6.1] --- Near-duplicate testing same concept
- **Problem**: Q1.7.5 ("Session context isolation for quality") and Q4.6.1 ("Self-review limitations") test the same concept -- that a model retains reasoning context from generation and is less effective at self-review. Both have nearly identical correct answers and explanations. Additionally, Q1.7.5 is arguably misplaced under Task 1.7 (session state/resumption/forking) when the self-review limitation is explicitly a Task 4.6 concept (multi-instance review architectures).
- **Suggested fix**: Either remove Q1.7.5 and replace it with a genuine session management question, or reframe it to focus on the session forking angle (e.g., using fork_session or a new session for review) rather than the self-review limitation itself.

## Moderate Issues (should fix)

### [Q1.7.5] --- Misplaced under wrong task statement
- **Problem**: Q1.7.5 tests self-review limitations, which maps to Task Statement 4.6 ("Design multi-instance and multi-pass review architectures"), not Task Statement 1.7 ("Manage session state, resumption, and forking"). The knowledge tag "Session context isolation for quality" tries to bridge the gap, but the question body and answer are entirely about self-review, not session management.
- **Suggested fix**: Move or replace with a question that genuinely tests session management knowledge (e.g., choosing between --resume and fork_session for a review workflow).

### [Q2.5.3] --- Distractor B is arguably a valid first approach
- **Problem**: Q2.5.3 asks about the fallback when Edit fails due to non-unique text. The correct answer is A (Read+Write). However, Option B ("Retry Edit with a longer text anchor") is acknowledged in the explanation as "reasonable" and is a natural first step a practitioner would try before falling back to Read+Write. The question asks for "the correct fallback approach" which implies Edit has already been abandoned, but the stem says Edit failed once -- most practitioners would retry with more context before switching strategies.
- **Suggested fix**: Clarify the stem to say "you've already tried expanding the text anchor but the file has highly repetitive structure making unique anchors impractical" to make A the unambiguous answer.

### [Q1.4] --- Only 3 questions (meets minimum but sparse for a 27% domain)
- **Problem**: Task Statement 1.4 (multi-step workflows with enforcement and handoff patterns) has exactly 3 questions. While this meets the minimum target, Domain 1 has 27% weight and this task statement covers critical concepts (programmatic enforcement, handoff protocols) that are heavily tested in exam sample questions.
- **Suggested fix**: Add 1-2 more questions to 1.4, perhaps one on handoff summary compilation and one on parallel investigation of multi-concern requests.

### [Q1.6] --- Only 3 questions (meets minimum but sparse for a 27% domain)
- **Problem**: Task Statement 1.6 (task decomposition strategies) has exactly 3 questions. This covers prompt chaining vs dynamic decomposition, a concept that appears across multiple domains.
- **Suggested fix**: Add 1-2 more questions, perhaps one on adaptive investigation plans and one on decomposing open-ended tasks.

### [Q5.3] --- Only 4 questions for error propagation
- **Problem**: Task Statement 5.3 (error propagation in multi-agent systems) has 4 questions. While above minimum, this is a concept that interacts with Tool Design (Domain 2) and could benefit from more coverage.
- **Suggested fix**: Consider adding 1 question distinguishing access failures from valid empty results in a multi-agent scenario.

### [Q5.5] --- Only 4 questions for human review workflows
- **Problem**: Task Statement 5.5 (human review workflows and confidence calibration) has 4 questions. This covers stratified sampling, field-level confidence, and accuracy-by-segment -- dense material.
- **Suggested fix**: Consider adding 1 question on calibrating review thresholds using labeled validation sets.

### [Difficulty balance] --- Easy questions are under-represented
- **Problem**: Only 29 of 166 questions (17.5%) are Easy. Domain 5 has only 4 Easy questions out of 35 (11.4%). The exam tests "foundational knowledge" and easy questions serve as accessible entry points for each task statement.
- **Suggested fix**: Consider converting some Medium questions to Easy for under-represented difficulty levels, particularly in Domains 1 and 5.

### [Q4.6] --- Only 4 questions for multi-instance review
- **Problem**: Task Statement 4.6 (multi-instance and multi-pass review architectures) has only 4 questions. The exam guide lists 3 knowledge items and 3 skills for this task statement, and the sample questions in the exam guide directly test these concepts.
- **Suggested fix**: Add 1-2 questions, perhaps one on confidence-based review routing (already partially covered by Q4.6.4) and one on when multi-pass is needed vs single-pass.

### [Scenario coverage for CI] --- Scenario 5 (CI) has fewest questions
- **Problem**: Scenario 5 (Claude Code for Continuous Integration) has 22 questions, the lowest count. While all scenarios are represented, CI is a primary domain for both Domain 3 and Domain 4, and the exam guide lists it as a key scenario.
- **Suggested fix**: When adding new questions to fill gaps above, prefer Scenario 5 as the context.

## Minor Issues (nice to fix)

### [File naming] --- Inconsistent file naming conventions
- **Problem**: File 1 uses hyphens and lowercase (`1-agentic-architecture-and-orchestration.md`) while files 2-5 use spaces and title case (`2 Tool Design & MCP Integration.md`). The ampersand and spaces in filenames require quoting in shell commands.
- **Suggested fix**: Standardize all filenames to use hyphens and lowercase (e.g., `2-tool-design-and-mcp-integration.md`).

### [Q1.7.4 option D] --- Diff-based approach dismissed too quickly
- **Problem**: Q1.7.4's explanation says providing a diff is "less effective than describing the change with context." The exam guide says to "inform a resumed session about specific file changes for targeted re-analysis" -- providing a diff IS a way to inform about specific changes. The dismissal is a judgment call presented as fact.
- **Suggested fix**: Rephrase D's explanation to be clearer about why describing intent is better than raw diff syntax, e.g., "a diff shows what changed syntactically but doesn't explain the purpose, making it harder for the agent to assess downstream impact."

### [Domain 1 question count in footer] --- Footer claims "all 7 task statements"
- **Problem**: The footer is technically correct that all 7 task statements are covered, but the count "28 questions" is wrong (actual: 30 + 2 cross-domain = 32). This metadata is unreliable for anyone using it for coverage planning.
- **Suggested fix**: Already covered in Critical Issue 1.

### [Q3.3] --- 4 questions but exam guide has 3 knowledge + 3 skill items
- **Problem**: Task 3.3 (path-specific rules) has 4 questions for a relatively narrow topic (glob patterns in .claude/rules/). This is well-covered relative to its scope.
- **Suggested fix**: No action needed, but if questions need to be redistributed, this is a candidate to move one to an under-covered task.

### [Cross-domain questions] --- All are Hard difficulty
- **Problem**: All 10 cross-domain questions are Hard. This is appropriate for multi-domain synthesis but means cross-domain reasoning is only tested at the highest difficulty level.
- **Suggested fix**: Consider adding 1-2 Medium cross-domain questions that combine two domains in a straightforward way.

### [Distractor archetypes] --- Generally well-applied but some patterns repeat
- **Problem**: The "over-engineered" distractor (routing classifier, ML infrastructure, separate classifier model) appears frequently across domains. While realistic, candidates may learn to eliminate any answer mentioning classifiers or ML infrastructure.
- **Suggested fix**: Vary the over-engineered distractors occasionally -- use alternatives like excessive abstraction layers, premature optimization, or custom middleware.

## Coverage Analysis

### By Task Statement
| Task Statement | Questions | Min Target (3) | Status |
|---|---|---|---|
| 1.1 | 5 | 3 | PASS |
| 1.2 | 5 | 3 | PASS |
| 1.3 | 5 | 3 | PASS |
| 1.4 | 3 | 3 | PASS (at minimum) |
| 1.5 | 4 | 3 | PASS |
| 1.6 | 3 | 3 | PASS (at minimum) |
| 1.7 | 5 | 3 | PASS |
| 2.1 | 6 | 3 | PASS |
| 2.2 | 6 | 3 | PASS |
| 2.3 | 6 | 3 | PASS |
| 2.4 | 5 | 3 | PASS |
| 2.5 | 6 | 3 | PASS |
| 3.1 | 6 | 3 | PASS |
| 3.2 | 6 | 3 | PASS |
| 3.3 | 4 | 3 | PASS |
| 3.4 | 5 | 3 | PASS |
| 3.5 | 5 | 3 | PASS |
| 3.6 | 6 | 3 | PASS |
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
| 3: Multi-Agent Research System | 30 | PASS |
| 4: Developer Productivity with Claude | 28 | PASS |
| 5: Claude Code for Continuous Integration | 22 | PASS (lowest) |
| 6: Structured Data Extraction | 29 | PASS |

### Cross-Domain Questions
- Count: 10 (2 per domain file)
- All are Hard difficulty
- Domain combinations covered: 1+2+5, 1+5, 2+1+5, 2+3, 3+2+5, 3+4, 4+5, 4+3+1, 5+1+2, 5+1+4
- Good spread of domain combinations

## Recommendations
- Fix all five footer metadata lines immediately (critical data integrity issue)
- Replace Q1.7.5 with a genuine session management question to eliminate the near-duplicate with Q4.6.1
- Clarify Q2.5.3 stem to make the Edit-already-exhausted context explicit
- Add 2-3 questions to Task Statements 1.4 and 1.6 (both at minimum for a 27%-weight domain)
- Add 1-2 Medium cross-domain questions to test cross-domain reasoning below Hard difficulty
- Prefer Scenario 5 (CI) when writing new questions to balance scenario distribution
- Standardize file naming to hyphenated-lowercase format
- Overall quality is high: correct answers align with exam guide, explanations address all distractors, scenarios are appropriate, and distractor quality follows the four archetypes well
