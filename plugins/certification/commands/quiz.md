---
description: Mock exam simulation for Claude Certified Architect – Foundations. Randomly selects scenarios, distributes questions by domain weight, scores with explanations.
allowed-tools: Read, Glob, Grep, Bash
---

# Quiz Command

Simulates the Claude Certified Architect – Foundations exam with scenario selection, domain-weighted questions, and detailed scoring.

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

Read: `${CLAUDE_PLUGIN_ROOT}/skills/scoring.md`

This provides the question selection algorithm, scoring formula, and result format. Internalize its rules before proceeding.

### Step 2: Parse Arguments

Determine mode from the user's arguments:

- **No args** — full exam simulation
- **--domain N** — domain focus mode (questions from domain N only)
- **--scenario N** — scenario focus mode (questions matching scenario N only)
- **--count N** — override question count (default: 30 for full, 15 for focused)
- **--easy / --hard / --mixed** — difficulty filter (default: mixed)

Multiple flags combine naturally: `--domain 2 --hard --count 5` means 5 hard questions from Domain 2.

### Step 3: Select Questions

Read all question bank files:

- `${CLAUDE_PLUGIN_ROOT}/resources/question-bank/1-agentic-architecture-and-orchestration.md`
- `${CLAUDE_PLUGIN_ROOT}/resources/question-bank/2-tool-design-and-mcp-integration.md`
- `${CLAUDE_PLUGIN_ROOT}/resources/question-bank/3-claude-code-configuration-and-workflows.md`
- `${CLAUDE_PLUGIN_ROOT}/resources/question-bank/4-prompt-engineering-and-structured-output.md`
- `${CLAUDE_PLUGIN_ROOT}/resources/question-bank/5-context-management-and-reliability.md`

Parse each question and extract: ID, scenario, difficulty, domain, task statement, question text, options A–D, correct answer, and explanation.

Follow the selection algorithm from the scoring skill:

- **Full mode**: Pick 4 random scenarios, then distribute questions by domain weight — D1: 8, D2: 5, D3: 6, D4: 6, D5: 5 (totaling 30). Select randomly within each domain from the chosen scenarios.
- **Domain mode**: Gather all questions from the specified domain, then randomly select up to --count.
- **Scenario mode**: Gather all questions matching the specified scenario, then randomly select up to --count.

Apply the difficulty filter if specified. Shuffle the final selection so questions appear in random order.

### Step 4: Present Quiz

Announce the quiz parameters:

---

## Claude Certified Architect – Foundations Mock Exam

**Mode:** [Full Simulation / Domain N Focus / Scenario N Focus]
**Scenarios:** [list selected scenarios by number and name]
**Questions:** [N]
**Difficulty:** [Mixed / Easy / Hard]

---

Present questions one at a time. For each question, display:

**Question [current]/[total]** — Domain [N] | [Scenario Name]

[Question text]

A) [option A]
B) [option B]
C) [option C]
D) [option D]

Then wait for the user's answer. Accept: A, B, C, or D (case-insensitive), or the full option text.

After each answer:

- **Correct**: respond with "Correct." and move on (keep the pace brisk).
- **Incorrect**: respond with "Incorrect. The correct answer is [X]." Then show the full explanation from the question bank so the user learns immediately.

Track throughout the quiz: question ID, domain, task statement, user answer, correct answer, and whether it was right or wrong.

### Step 5: Score and Report

After all questions are answered, compute results using the scoring formula from the scoring skill.

Present the report:

---

## Exam Results

### Score: [scaled_score] / 1000 — [PASS / FAIL]

(Passing score: 720)

### Domain Breakdown

| Domain | Weight | Correct | Total | Accuracy | Status |
|--------|--------|---------|-------|----------|--------|
| 1. Agentic Architecture | 27% | [n] | [n] | [%] | [pass/fail] |
| 2. Tool Design & MCP | 18% | [n] | [n] | [%] | [pass/fail] |
| 3. Claude Code Config | 20% | [n] | [n] | [%] | [pass/fail] |
| 4. Prompt Engineering | 20% | [n] | [n] | [%] | [pass/fail] |
| 5. Context Management | 15% | [n] | [n] | [%] | [pass/fail] |

### Questions Missed

| # | ID | Domain | Task | Your Answer | Correct |
|---|-----|--------|------|-------------|---------|
| [table of wrong answers] |

### Recommendations

Suggest next steps based on performance:

- Use `/training` for weak domains that fell below passing threshold.
- Use `/quiz --domain N` for targeted practice on specific domains.
- Use `/analysis` to review full quiz history and track improvement over time.

---

### Step 6: Save Results

Persist the quiz results for later analysis:

```
mkdir -p ~/.claude/certification && echo '{...}' >> ~/.claude/certification/results.jsonl
```

Write one JSONL line per quiz attempt with this structure:

```json
{"date":"ISO-8601","mode":"full|domain|scenario","scenarios":[1,3,4,6],"scaled_score":820,"total":30,"correct":24,"by_domain":{"1":{"total":8,"correct":6},"2":{"total":5,"correct":4},"3":{"total":6,"correct":5},"4":{"total":6,"correct":5},"5":{"total":5,"correct":4}},"by_task":{"1.1":{"total":2,"correct":1},"1.2":{"total":3,"correct":2}},"wrong":["Q1.3.2","Q2.1.4"]}
```

This enables `/analysis` to compute trends, identify persistent weaknesses, and recommend study plans.
