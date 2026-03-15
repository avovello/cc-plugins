# Certification Plugin Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a Claude Certified Architect – Foundations exam preparation plugin with training, quiz, analysis, and update commands.

**Architecture:** No runtime agents — 4 commands + 7 skills. Commands orchestrate the user experience; skills provide domain curriculum, scoring logic, and study recommendations. Quiz results persist to `~/.claude/certification/` as JSONL for cross-session analysis. Question bank stays in markdown (166 questions across 5 domains).

**Tech Stack:** Claude Code plugin system (YAML frontmatter commands/skills), markdown question bank, JSONL result persistence.

**Build strategy:** Three-wave parallel execution. Wave 1 (skills + question review) has no internal dependencies. Wave 2 (commands + question fixes) depends on Wave 1 — but Task 5 (question fixer) specifically depends on Task 4 (reviewer) completing, so it runs after the reviewer finishes while commands run in parallel with the reviewer. Wave 3 (sync files) depends on everything else.

---

## File Map

### Files to Create

| File | Responsibility |
|------|----------------|
| `plugins/certification/skills/domain-1-agentic-architecture.md` | Domain 1 curriculum: 7 task statements, key concepts, misconceptions |
| `plugins/certification/skills/domain-2-tool-design-mcp.md` | Domain 2 curriculum: 5 task statements, key concepts, misconceptions |
| `plugins/certification/skills/domain-3-claude-code-config.md` | Domain 3 curriculum: 6 task statements, key concepts, misconceptions |
| `plugins/certification/skills/domain-4-prompt-engineering.md` | Domain 4 curriculum: 6 task statements, key concepts, misconceptions |
| `plugins/certification/skills/domain-5-context-management.md` | Domain 5 curriculum: 6 task statements, key concepts, misconceptions |
| `plugins/certification/skills/scoring.md` | Scoring formula, question selection algorithm, domain weighting |
| `plugins/certification/skills/study-path.md` | Adaptive recommendation logic based on quiz history |
| `plugins/certification/commands/training.md` | Interactive domain study command (replaces placeholder) |
| `plugins/certification/commands/quiz.md` | Mock exam simulation command (replaces placeholder) |
| `plugins/certification/commands/analysis.md` | Performance analysis command (replaces placeholder) |
| `plugins/certification/commands/update.md` | Content update command (new file) |
| `plugins/certification/README.md` | Plugin documentation (replaces placeholder) |

### Files to Modify

| File | Change |
|------|--------|
| `plugins/certification/.claude-plugin/plugin.json` | Update version, description, keywords |
| `plugins/certification/resources/question-bank/*.md` | Fix issues found by reviewer |
| `.claude-plugin/marketplace.json` | Add certification plugin entry, bump root version |
| `README.md` | Add certification plugin section, update counts |
| `QUICKSTART.md` | Add certification plugin examples |
| `CLAUDE.md` | Update plugin counts, structure tree |

---

## Chunk 1: Wave 1 — Skills + Question Review (Parallel)

Wave 1 tasks have zero dependencies on each other. All 4 can run simultaneously.

### Task 1: Write Domain Training Skills

Write 5 domain skills, one per exam domain. Each skill is the curriculum backbone for `/training`.

**Source material:** `plugins/certification/resources/exam-guide-reference.md` (distilled) and the full exam guide PDF text extracted to `resources/`.

**Files:**
- Create: `plugins/certification/skills/domain-1-agentic-architecture.md`
- Create: `plugins/certification/skills/domain-2-tool-design-mcp.md`
- Create: `plugins/certification/skills/domain-3-claude-code-config.md`
- Create: `plugins/certification/skills/domain-4-prompt-engineering.md`
- Create: `plugins/certification/skills/domain-5-context-management.md`

Each skill follows this structure:

```markdown
---
name: domain-N-<slug>
description: Training curriculum for Domain N: <Name> (<weight>% of exam). Use when /training targets this domain.
---

# Domain N: <Name> (<weight>%)

## Overview
- Weight: <N>% of scored content
- Task statements: N.1–N.M
- Primary scenarios: <list which of the 6 scenarios exercise this domain>
- Cross-domain connections: <which other domains overlap>

## Task Statement N.1: <Title>

### Key Concepts
<2-3 paragraphs explaining the core knowledge items from the exam guide, with concrete examples>

### Common Misconceptions
<Derived from distractor archetypes in the question bank — what wrong answers look plausible and why>

### Practice Focus
<What to pay attention to, what traps to avoid>

## Task Statement N.2: <Title>
...

## Recurring Themes
<Cross-cutting themes that appear across multiple task statements in this domain>
```

- [ ] **Step 1:** Read the exam guide text for Domain 1 (task statements 1.1–1.7 with all Knowledge/Skills items)
- [ ] **Step 2:** Read the Domain 1 question bank (`resources/question-bank/1-agentic-architecture-and-orchestration.md`) to extract misconception patterns from distractors
- [ ] **Step 3:** Write `skills/domain-1-agentic-architecture.md` following the structure above
- [ ] **Step 4:** Repeat steps 1-3 for Domains 2-5
- [ ] **Step 5:** Verify all 5 skill files have consistent structure and frontmatter

---

### Task 2: Write Scoring Skill

**Files:**
- Create: `plugins/certification/skills/scoring.md`

```markdown
---
name: scoring
description: Exam scoring formula, question selection algorithm, and domain weighting for quiz simulation. Used by /quiz command.
---

# Scoring & Question Selection

## Exam Format
- 4 of 6 scenarios randomly selected per sitting
- Multiple choice: 1 correct, 3 distractors
- Scaled score 100–1000, passing at 720

## Scoring Formula
- Raw score = correct / total
- Scaled score = 100 + (raw_score × 900)
- Example: 80% raw → 820 scaled (pass), 68% raw → 712 (fail)
- Passing threshold: ~69% raw maps to 720 scaled

## Question Selection Algorithm

### Full Exam Simulation (`/quiz` or `/quiz --full`)
1. Randomly select 4 of 6 scenarios
2. Target question count: 30 (mirrors real exam density)
3. Distribute by domain weight:
   - Domain 1 (27%): 8 questions
   - Domain 2 (18%): 5 questions
   - Domain 3 (20%): 6 questions
   - Domain 4 (20%): 6 questions
   - Domain 5 (15%): 5 questions
4. For each domain allocation, select questions that match the 4 chosen scenarios
5. If insufficient scenario-matched questions, backfill from same domain (any scenario)
6. Shuffle final question order

### Domain Focus (`/quiz --domain N`)
1. Select all questions from domain N
2. Optionally filter by scenario with `--scenario M`
3. Default: 15 questions randomly selected
4. Override count with `--count X`

### Difficulty Modes
- `--easy`: Only Easy questions
- `--hard`: Only Hard questions
- `--mixed` (default): Proportional mix matching question bank distribution

## Scenarios Reference

| # | Scenario | Primary Domains |
|---|----------|----------------|
| 1 | Customer Support Resolution Agent | 1, 2, 5 |
| 2 | Code Generation with Claude Code | 3, 5 |
| 3 | Multi-Agent Research System | 1, 2, 5 |
| 4 | Developer Productivity with Claude | 2, 3, 1 |
| 5 | Claude Code for CI/CD | 3, 4 |
| 6 | Structured Data Extraction | 4, 5 |

## Result Format (JSONL)

Each quiz session writes one line to `~/.claude/certification/results.jsonl`:

{
  "date": "ISO-8601",
  "mode": "full|domain|scenario",
  "scenarios": [1,3,4,6],
  "scaled_score": 820,
  "total": 30,
  "correct": 24,
  "by_domain": {"1": {"total": 8, "correct": 6}, ...},
  "by_task": {"1.1": {"total": 2, "correct": 1}, ...},
  "wrong": ["Q1.3.2", "Q2.1.4"]
}
```

- [ ] **Step 1:** Write `skills/scoring.md` with the content above
- [ ] **Step 2:** Verify the scoring formula: 69% × 900 + 100 = 721 (just passing) ✓

---

### Task 3: Write Study Path Skill

**Files:**
- Create: `plugins/certification/skills/study-path.md`

```markdown
---
name: study-path
description: Adaptive study recommendation engine. Analyzes quiz history to identify weak domains, task statements, and scenarios, then recommends targeted study paths. Used by /analysis and /training.
---

# Study Path Recommendations

## Analysis Inputs
Read `~/.claude/certification/results.jsonl` — each line is a quiz session result.

## Metrics to Compute

### Per-Domain Performance
- Accuracy = domain_correct / domain_total across all sessions
- Trend = compare last 3 sessions vs all-time average
- Weighted gap = (domain_weight × (1 - accuracy)) — higher = more impactful to fix

### Per-Task-Statement Performance
- Accuracy per task statement (e.g., 1.1, 1.2, ...)
- Identify task statements with < 60% accuracy as "critical gaps"
- Identify task statements with 60-80% accuracy as "needs review"

### Scenario Exposure
- Count how many times each scenario has been tested
- Flag scenarios with < 2 exposures as "under-tested"

### Progress Over Time
- Track scaled scores session-over-session
- Identify plateau (3+ sessions with < 20-point improvement)
- Identify regression (score dropped > 50 points)

## Recommendation Algorithm

1. **Critical gaps first**: Task statements < 60% accuracy, ordered by parent domain weight
2. **High-impact domains**: Domains with highest weighted gap score
3. **Under-tested scenarios**: Recommend `/quiz --scenario N` for low-exposure scenarios
4. **Plateau breaker**: If plateau detected, recommend switching study mode (e.g., from quiz to training)
5. **Ready to test**: If all domains > 80% and scaled score > 750 for 2+ sessions → "You're likely ready"

## Output Format

### Readiness Summary
| Domain | Weight | Accuracy | Status |
|--------|--------|----------|--------|
| 1. Agentic Architecture | 27% | 75% | ⚠ Needs work |
| 2. Tool Design & MCP | 18% | 90% | ✓ Strong |
| ... | | | |

**Estimated scaled score: ~780 (passing)**

### Priority Study Path
1. Focus on Domain 1, Task 1.3 (subagent context passing) — 40% accuracy
2. Review Domain 5, Task 5.1 (context management) — 55% accuracy
3. Practice Scenario 6 (Structured Data Extraction) — only tested once

### Recommended Next Actions
- `/training 1` — Study Domain 1 with focus on task statements 1.3 and 1.5
- `/quiz --scenario 6 --count 10` — Get more exposure to Scenario 6
- `/quiz --domain 1 --hard` — Test Domain 1 at high difficulty
```

- [ ] **Step 1:** Write `skills/study-path.md` with the content above
- [ ] **Step 2:** Verify recommendation algorithm covers all edge cases (no history, single session, etc.)

---

### Task 4: Review Question Bank

Review all 166 questions across 5 domain files against the exam guide for accuracy, quality, and coverage.

**Files:**
- Read: `plugins/certification/resources/question-bank/*.md` (all 5 files)
- Read: `plugins/certification/resources/exam-guide-reference.md`
- Create: `plugins/certification/resources/question-bank/review-findings.md` (output)

**Review checklist for each question:**

1. **Factual accuracy**: Does the correct answer align with what the exam guide states?
2. **Distractor quality**: Do wrong answers follow the 4 archetypes (over-engineered, wrong problem, probabilistic when deterministic needed, doesn't exist)? Are distractors plausible but clearly wrong?
3. **Scenario alignment**: Is the tagged scenario appropriate for this question's domain?
4. **Difficulty calibration**: Does the difficulty tag (Easy/Medium/Hard) match actual complexity?
5. **Task statement coverage**: Does every task statement have at least 3 questions? Are Knowledge and Skills items adequately covered?
6. **Scenario coverage**: Do all 6 scenarios have questions? Are Scenarios 4 and 6 covered?
7. **Cross-domain opportunities**: Are there questions that test reasoning across 2 domains?
8. **Format consistency**: Does every question follow the standard format (`### Q{D}.{T}.{N}`, difficulty tag, 4 options A-D, correct answer, explanation)?
9. **Explanation quality**: Does the explanation address why the correct answer is right AND why each distractor is wrong?
10. **Duplicate/overlap detection**: Are any questions testing the same concept in the same way?

**Output format** (`review-findings.md`):

```markdown
# Question Bank Review Findings

## Summary
- Total questions reviewed: N
- Issues found: N (critical: N, moderate: N, minor: N)
- Coverage gaps: N task statements under-covered
- Scenario gaps: N scenarios under-represented

## Critical Issues (must fix)
### [Q{id}] — {issue type}
- **Problem**: {description}
- **Suggested fix**: {specific correction}

## Moderate Issues (should fix)
...

## Coverage Analysis
### By Task Statement
| Task Statement | Questions | Min Target (3) | Status |
|...

### By Scenario
| Scenario | Questions | Status |
|...

## Recommendations
- {list of new questions to write to fill gaps}
```

- [ ] **Step 1:** Read all 5 question bank files completely
- [ ] **Step 2:** Read the exam guide reference for ground truth
- [ ] **Step 3:** Check each question against the 10-point checklist
- [ ] **Step 4:** Compile findings into `review-findings.md`
- [ ] **Step 5:** Compute coverage statistics by task statement and scenario

---

### Commit Point: Wave 1

After all Wave 1 tasks complete:
```bash
git add plugins/certification/skills/ plugins/certification/resources/question-bank/review-findings.md
git commit -m "feat(certification): add domain training skills, scoring, study-path, and question review findings"
```

---

## Chunk 2: Wave 2 — Commands + Question Fixes

Wave 2 depends on Wave 1 completing. Tasks 6–9 (commands) can run simultaneously. Task 5 (question fixer) depends on Task 4 (reviewer) finishing first — dispatch it after the reviewer returns findings.

### Task 5: Fix Question Bank Issues

Takes the findings from Task 4 and fixes all critical and moderate issues.

**Files:**
- Read: `plugins/certification/resources/question-bank/review-findings.md`
- Modify: `plugins/certification/resources/question-bank/1-agentic-architecture-and-orchestration.md`
- Modify: `plugins/certification/resources/question-bank/2 Tool Design & MCP Integration.md`
- Modify: `plugins/certification/resources/question-bank/3 Claude Code Configuration & Workflows.md`
- Modify: `plugins/certification/resources/question-bank/4 Prompt Engineering & Structured Output.md`
- Modify: `plugins/certification/resources/question-bank/5 Context Management & Reliability.md`

- [ ] **Step 1:** Read `review-findings.md` for all critical issues
- [ ] **Step 2:** Fix each critical issue in the appropriate question bank file
- [ ] **Step 3:** Fix moderate issues
- [ ] **Step 4:** Write any new questions needed to fill coverage gaps identified by the reviewer
- [ ] **Step 5:** Verify question numbering remains consistent after additions/changes

---

### Task 6: Build `/training` Command

Interactive domain study mode. Replaces the placeholder.

**Files:**
- Modify: `plugins/certification/commands/training.md`
- Read: `plugins/certification/skills/domain-*.md` (for reference)
- Read: `plugins/certification/skills/study-path.md` (for reference)

The command content:

```markdown
---
description: Interactive study of Claude Certified Architect – Foundations exam domains. Teaches concepts, tests understanding with mini-quizzes, and adapts to weak areas.
allowed-tools: Read, Glob, Grep, Bash
---

# Training Command

Interactive domain study for the Claude Certified Architect – Foundations exam.

## Usage

/training              # Recommended domain based on quiz history (or Domain 1 if no history)
/training 1            # Study Domain 1: Agentic Architecture & Orchestration
/training 2            # Study Domain 2: Tool Design & MCP Integration
/training 3            # Study Domain 3: Claude Code Configuration & Workflows
/training 4            # Study Domain 4: Prompt Engineering & Structured Output
/training 5            # Study Domain 5: Context Management & Reliability
/training 1.3          # Study specific task statement 1.3

## Workflow

### Step 1: Determine Study Target

If a domain number is provided, use it. If a task statement (e.g., 1.3) is provided, focus on that specific statement within its domain.

If no argument is provided:
1. Check if `~/.claude/certification/results.jsonl` exists
2. If yes: load the study-path skill (`plugins/certification/skills/study-path.md`), compute weak areas, recommend the highest-priority domain
3. If no: start with Domain 1

Tell the user which domain/task statement you'll cover and why.

### Step 2: Load Domain Skill

Load the matching domain skill:
- Domain 1: `plugins/certification/skills/domain-1-agentic-architecture.md`
- Domain 2: `plugins/certification/skills/domain-2-tool-design-mcp.md`
- Domain 3: `plugins/certification/skills/domain-3-claude-code-config.md`
- Domain 4: `plugins/certification/skills/domain-4-prompt-engineering.md`
- Domain 5: `plugins/certification/skills/domain-5-context-management.md`

### Step 3: Teach Task Statements

For each task statement in the domain (or the single targeted one):

1. **Explain** the key concepts using the skill content. Use concrete examples grounded in the exam scenarios. Explain WHY things work the way they do, not just WHAT they are.

2. **Highlight misconceptions** — explain what the common wrong assumptions are and why they fail. This is critical for exam prep since distractors exploit these exact misconceptions.

3. **Mini-quiz** — pull 1-2 Easy questions from the question bank matching this task statement.
   - Read the question bank: `plugins/certification/resources/question-bank/<domain-file>.md`
   - Find questions tagged to this task statement
   - Present the question and wait for the user's answer
   - If correct: briefly confirm and note the key insight
   - If wrong: explain thoroughly — why their choice was wrong, why the correct answer is right, and connect it back to the concept just taught

4. **Check-in** — after each task statement, ask if the user wants to:
   - Continue to the next task statement
   - Dive deeper into this one (present Medium/Hard questions)
   - Switch to a different domain
   - Stop and take a quiz

### Step 4: Session Summary

When the user stops or finishes the domain:

## Training Session Summary

**Domain studied:** [N] — [Name]
**Task statements covered:** [list]
**Mini-quiz results:** [correct] / [total]

### Weak spots to revisit
- [task statements where mini-quiz answers were wrong]

### Recommended next steps
- `/training [next domain]` — Continue studying
- `/quiz --domain [N]` — Test this domain
- `/quiz` — Take a full mock exam

Append a training record to `~/.claude/certification/training-progress.jsonl`:
{"date": "ISO-8601", "domain": N, "task_statements": ["1.1", "1.2"], "mini_quiz": {"total": 4, "correct": 3}}

Use Bash to create the directory and append:
mkdir -p ~/.claude/certification && echo '{"date":"...","domain":N,...}' >> ~/.claude/certification/training-progress.jsonl
```

- [ ] **Step 1:** Read the existing placeholder `commands/training.md`
- [ ] **Step 2:** Replace with the full command content above
- [ ] **Step 3:** Verify all skill file paths referenced in the command are correct
- [ ] **Step 4:** Verify the question bank file paths match actual filenames

---

### Task 7: Build `/quiz` Command

Mock exam simulation. Replaces the placeholder.

**Files:**
- Modify: `plugins/certification/commands/quiz.md`
- Read: `plugins/certification/skills/scoring.md` (for reference)

The command content:

```markdown
---
description: Mock exam simulation for Claude Certified Architect – Foundations. Randomly selects scenarios, distributes questions by domain weight, scores with explanations.
allowed-tools: Read, Glob, Grep, Bash
---

# Quiz Command

Simulates the Claude Certified Architect – Foundations exam with scenario selection, domain-weighted questions, timed mode, and detailed scoring.

## Usage

/quiz                          # Full exam simulation (30 questions, 4 random scenarios)
/quiz --domain 1               # Focus on Domain 1 (15 questions)
/quiz --scenario 3             # Focus on Scenario 3
/quiz --count 10               # Custom question count
/quiz --easy                   # Easy questions only
/quiz --hard                   # Hard questions only
/quiz --domain 2 --hard --count 5  # 5 hard Domain 2 questions

## Workflow

### Step 1: Load Scoring Skill

Read: `plugins/certification/skills/scoring.md`

This provides the question selection algorithm, scoring formula, and result format.

### Step 2: Parse Arguments

Determine mode from arguments:
- No args → full exam simulation
- `--domain N` → domain focus mode
- `--scenario N` → scenario focus mode
- `--count N` → override question count (default: 30 for full, 15 for focused)
- `--easy` / `--hard` / `--mixed` → difficulty filter (default: mixed)

### Step 3: Select Questions

Read all question bank files:
- `plugins/certification/resources/question-bank/1-agentic-architecture-and-orchestration.md`
- `plugins/certification/resources/question-bank/2 Tool Design & MCP Integration.md`
- `plugins/certification/resources/question-bank/3 Claude Code Configuration & Workflows.md`
- `plugins/certification/resources/question-bank/4 Prompt Engineering & Structured Output.md`
- `plugins/certification/resources/question-bank/5 Context Management & Reliability.md`

Parse each question: extract ID, scenario, difficulty, domain, task statement, question text, options A-D, correct answer, explanation.

Follow the selection algorithm from the scoring skill:
- **Full mode**: Pick 4 random scenarios, distribute by domain weight
- **Domain mode**: All questions from that domain, randomly select --count
- **Scenario mode**: All questions matching that scenario, randomly select --count

Apply difficulty filter if specified. Shuffle the selected questions.

### Step 4: Present Quiz

Announce the quiz parameters:

## Claude Certified Architect – Foundations Mock Exam

**Mode:** [Full Simulation / Domain N Focus / Scenario N Focus]
**Scenarios:** [list selected scenarios, or "All" for focused modes]
**Questions:** [N]
**Difficulty:** [Mixed / Easy / Hard]

---

Present questions one at a time. For each question:

**Question [current]/[total]** — Domain [N] | [Scenario Name]

[Question text]

A) [option A]
B) [option B]
C) [option C]
D) [option D]

Wait for the user's answer. Accept: A, B, C, D (case-insensitive), or the full text.

After each answer:
- If correct: "✓ Correct." (brief, keep pace moving)
- If wrong: "✗ Incorrect. The correct answer is [X]." Then show the full explanation.

Track: question ID, domain, task statement, user answer, correct answer, right/wrong.

### Step 5: Score and Report

After all questions are answered, compute results using the scoring formula.

## Exam Results

### Score: [scaled_score] / 1000 — [PASS ✓ / FAIL ✗]
(Passing score: 720)

### Domain Breakdown
| Domain | Weight | Correct | Total | Accuracy | Status |
|--------|--------|---------|-------|----------|--------|
| 1. Agentic Architecture | 27% | [n] | [n] | [%] | [✓/✗] |
| 2. Tool Design & MCP | 18% | [n] | [n] | [%] | [✓/✗] |
| 3. Claude Code Config | 20% | [n] | [n] | [%] | [✓/✗] |
| 4. Prompt Engineering | 20% | [n] | [n] | [%] | [✓/✗] |
| 5. Context Management | 15% | [n] | [n] | [%] | [✓/✗] |

### Questions Missed
| # | ID | Domain | Task | Your Answer | Correct |
|---|-----|--------|------|-------------|---------|
[table of wrong answers]

### Recommendations
[Based on performance, suggest: /training for weak domains, /quiz --domain for targeted practice, /analysis for full history]

### Step 6: Save Results

Write the result to `~/.claude/certification/results.jsonl`:

mkdir -p ~/.claude/certification && echo '{...}' >> ~/.claude/certification/results.jsonl

Use the JSONL format defined in the scoring skill.
```

- [ ] **Step 1:** Read the existing placeholder `commands/quiz.md`
- [ ] **Step 2:** Replace with the full command content above
- [ ] **Step 3:** Verify all file paths match actual question bank filenames
- [ ] **Step 4:** Verify scoring formula matches the scoring skill

---

### Task 8: Build `/analysis` Command

Performance analysis and recommendations. Replaces the placeholder.

**Files:**
- Modify: `plugins/certification/commands/analysis.md`
- Read: `plugins/certification/skills/study-path.md` (for reference)
- Read: `plugins/certification/skills/scoring.md` (for reference)

The command content:

```markdown
---
description: Analyze quiz and training performance across sessions. Shows domain strengths, weak task statements, scenario coverage, and recommends a targeted study path.
allowed-tools: Read, Glob, Grep, Bash
---

# Analysis Command

Reads your quiz and training history, computes performance metrics, and generates a personalized study plan.

## Usage

/analysis                # Full performance analysis
/analysis --domain 1     # Deep dive into Domain 1 performance
/analysis --history      # Show all quiz session results

## Workflow

### Step 1: Load Data

Read the study-path skill: `plugins/certification/skills/study-path.md`
Read the scoring skill: `plugins/certification/skills/scoring.md`

Read quiz history:
cat ~/.claude/certification/results.jsonl 2>/dev/null

Read training history:
cat ~/.claude/certification/training-progress.jsonl 2>/dev/null

If no data exists:

## No Quiz History Found

You haven't taken any quizzes yet. Start with:
- `/training` — Study a domain interactively
- `/quiz` — Take a full mock exam

Then come back to `/analysis` to track your progress.

Exit.

### Step 2: Compute Metrics

Follow the study-path skill's metrics computation:

1. **Per-domain accuracy** across all sessions
2. **Per-task-statement accuracy** across all sessions
3. **Weighted gap** = domain_weight × (1 - accuracy)
4. **Trend** = last 3 sessions vs all-time (improving / plateau / regressing)
5. **Scenario exposure** = count of sessions per scenario
6. **Training coverage** = which task statements have been studied

### Step 3: Generate Report

If `--history` flag: show a table of all quiz sessions first.

## Quiz History
| Date | Mode | Score | Pass? | Strongest | Weakest |
|------|------|-------|-------|-----------|---------|
[one row per session]

Then the full analysis:

## Performance Analysis — Claude Certified Architect – Foundations

### Overall Readiness
**Average scaled score:** [N] / 1000
**Pass rate:** [N] / [total] sessions ([%])
**Trend:** [Improving ↑ / Plateau → / Regressing ↓]

### Domain Performance
| Domain | Weight | Accuracy | Weighted Gap | Trend | Status |
|--------|--------|----------|--------------|-------|--------|
| 1. Agentic Architecture | 27% | [%] | [N] | [↑→↓] | [status] |
| 2. Tool Design & MCP | 18% | [%] | [N] | [↑→↓] | [status] |
| 3. Claude Code Config | 20% | [%] | [N] | [↑→↓] | [status] |
| 4. Prompt Engineering | 20% | [%] | [N] | [↑→↓] | [status] |
| 5. Context Management | 15% | [%] | [N] | [↑→↓] | [status] |

Status: ✓ Strong (>80%) | ⚠ Needs Work (60-80%) | ✗ Critical (<60%)

### Critical Gaps (Task Statements < 60%)
| Task Statement | Accuracy | Questions | Domain |
|----------------|----------|-----------|--------|
[sorted by weighted impact]

### Scenario Exposure
| Scenario | Times Tested | Status |
|----------|-------------|--------|
[flag under-tested scenarios]

### Training Coverage
| Domain | Task Statements Studied | Total | Coverage |
|--------|------------------------|-------|----------|
[from training-progress.jsonl]

### If `--domain N` flag, add deep dive:

### Domain [N] Deep Dive: [Name]
| Task Statement | Accuracy | Attempts | Last Result | Trend |
|...

**Most missed questions:**
[list specific question IDs the user consistently gets wrong, with their topics]

### Step 4: Recommendations

Follow the study-path skill's recommendation algorithm:

## Recommended Study Path

### Immediate Priority
1. [Highest weighted-gap domain + specific task statements]
2. [Second highest]

### Under-Tested Areas
- [Scenarios with < 2 exposures]
- [Task statements never quizzed]

### Suggested Actions
- `/training [N]` — [reason]
- `/quiz --domain [N] --hard` — [reason]
- `/quiz --scenario [N]` — [reason]

### Readiness Assessment
[If ready: "Your scores suggest you're prepared. Consider taking one more full simulation to confirm."]
[If not ready: "Focus on the priority areas above. Target >80% in all domains before attempting the exam."]
```

- [ ] **Step 1:** Read the existing placeholder `commands/analysis.md`
- [ ] **Step 2:** Replace with the full command content above
- [ ] **Step 3:** Verify JSONL paths match what quiz and training commands write

---

### Task 9: Build `/update` Command

Content freshness checker.

**Files:**
- Create: `plugins/certification/commands/update.md`

The command content:

```markdown
---
description: Check for updates to Claude documentation, release notes, and certification materials. Compares current question bank against latest docs to flag outdated content.
allowed-tools: Read, Glob, Grep, Bash, WebFetch
---

# Update Command

Fetches the latest Claude documentation and release notes, compares against the current question bank, and flags content that may need updating.

## Usage

/update                # Full update check
/update --docs         # Only check documentation changes
/update --exam         # Only check exam guide for updates

## Workflow

### Step 1: Fetch Latest Sources

Fetch content from these sources (use WebFetch):

1. **Claude Code docs**: https://docs.anthropic.com/en/docs/claude-code
2. **Claude API docs**: https://docs.anthropic.com/en/api
3. **MCP specification**: https://modelcontextprotocol.io
4. **Anthropic changelog**: https://docs.anthropic.com/en/docs/changelog
5. **Skilljar courses**: https://anthropic.skilljar.com

For each source, extract key topics, features, and any recent changes.

### Step 2: Load Current Content

Read the exam guide reference:
- `plugins/certification/resources/exam-guide-reference.md`

Read all question bank files:
- `plugins/certification/resources/question-bank/*.md`

### Step 3: Compare and Analyze

For each fetched source, identify:

1. **New features/concepts** not covered in the question bank
   - New CLI flags, new tool capabilities, new MCP features
   - New best practices or pattern changes

2. **Changed behavior** that contradicts existing questions
   - Deprecated features still tested
   - Changed defaults or configuration paths
   - Updated API behavior

3. **New exam guide versions** (if the exam guide URL is accessible)
   - New task statements
   - Changed domain weights
   - New scenarios

### Step 4: Report

## Content Update Report

**Checked:** [date]
**Sources fetched:** [N] / [total]

### ⚠ Potentially Outdated Questions
| Question ID | Issue | Source |
|-------------|-------|--------|
[questions that may reference outdated information]

### 🆕 New Topics Not Yet Covered
| Topic | Source | Relevant Domain |
|-------|--------|----------------|
[new features or concepts from docs that aren't in the question bank]

### ✓ Up-to-Date Areas
[domains/topics confirmed current]

### Recommended Actions
- [Specific questions to update]
- [New questions to write]
- [Topics to add to training skills]

Note: This command identifies potential updates but does not modify files automatically.
Review suggestions and apply changes manually or with `/quiz` regeneration.
```

- [ ] **Step 1:** Write `commands/update.md` with the content above
- [ ] **Step 2:** Verify WebFetch is included in allowed-tools

---

### Commit Point: Wave 2

After all Wave 2 tasks complete:
```bash
git add plugins/certification/commands/ plugins/certification/resources/question-bank/
git commit -m "feat(certification): add training, quiz, analysis, update commands and fix question bank issues"
```

---

## Chunk 3: Wave 3 — Sync Files & Documentation

Depends on Wave 2 completing. Single task, must be done last.

### Task 10: Update All Sync Files

Update every file that must stay in sync per CLAUDE.md conventions.

**Files:**
- Modify: `plugins/certification/.claude-plugin/plugin.json`
- Modify: `plugins/certification/README.md`
- Modify: `.claude-plugin/marketplace.json`
- Modify: `README.md`
- Modify: `QUICKSTART.md`
- Modify: `CLAUDE.md`

- [ ] **Step 1:** Update `plugins/certification/.claude-plugin/plugin.json`:

```json
{
  "name": "certification",
  "description": "Claude Certified Architect – Foundations exam preparation with interactive training, mock exam simulation, performance analysis, and content updates",
  "version": "1.0.0",
  "author": {
    "name": "Claude Code Marketplace Contributors",
    "email": "noreply@anthropic.com"
  },
  "keywords": ["certification", "training", "quiz", "exam-prep", "architect", "foundations"]
}
```

- [ ] **Step 2:** Rewrite `plugins/certification/README.md` with full documentation:
  - Plugin overview
  - All 4 commands with usage examples
  - All 7 skills listed
  - Resources directory structure
  - Question bank statistics
  - Workflow examples (study path from beginner to exam-ready)

- [ ] **Step 3:** Add certification entry to `.claude-plugin/marketplace.json` plugins array:

```json
{
  "name": "certification",
  "source": "./plugins/certification",
  "description": "Claude Certified Architect – Foundations exam preparation with interactive training, mock exam simulation, performance analysis, and content updates",
  "version": "1.0.0",
  "author": {
    "name": "Claude Code Marketplace Contributors",
    "email": "noreply@anthropic.com"
  },
  "category": "education",
  "keywords": ["certification", "training", "quiz", "exam-prep", "architect"]
}
```

Bump root marketplace version from `2.3.0` to `2.4.0`.

- [ ] **Step 4:** Update `README.md`:
  - Change "5 plugins" → "6 plugins" in overview
  - Change "10 agents" count if needed (certification has 0 agents, so stays 10)
  - Change "12 skills" → "19 skills" (adding 7)
  - Change "4 commands" → "8 commands" (adding 4)
  - Add Certification section matching the format of other plugin entries
  - Update version to 2.4.0

- [ ] **Step 5:** Update `QUICKSTART.md`:
  - Add certification install line: `/plugin install https://github.com/avovello/cc-plugins.git#plugins/certification`
  - Add certification examples section:
    ```
    ### 5. Prepare for Certification
    /training 1        # Study Domain 1
    /quiz              # Take a mock exam
    /analysis          # Check your progress
    /update            # Check for content updates
    ```
  - Update plugin table to include certification

- [ ] **Step 6:** Update `CLAUDE.md`:
  - Update project structure tree to include certification
  - Update counts: "6 plugins, 10 agents, 8 commands, 19 skills"
  - Add certification directory to the structure block:
    ```
    ├── certification/        # 0 agents, 4 commands, 7 skills
    ```

- [ ] **Step 7:** Commit all sync file changes together
