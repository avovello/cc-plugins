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
- Passing threshold: ~68.89% raw maps to 720 scaled (exact: raw >= 620/900)

## Question Selection Algorithm

### Full Exam Simulation (/quiz or /quiz --full)
1. Randomly select 4 of 6 scenarios
2. Target question count: 30 (mirrors real exam density)
3. Distribute by domain weight:
   - Domain 1 Agentic Architecture & Orchestration (27%): 8 questions
   - Domain 2 Tool Design & MCP Integration (18%): 5 questions
   - Domain 3 Claude Code Configuration & Workflows (20%): 6 questions
   - Domain 4 Prompt Engineering & Structured Output (20%): 6 questions
   - Domain 5 Context Management & Reliability (15%): 5 questions
4. For each domain allocation, prefer questions matching the 4 chosen scenarios
5. If insufficient scenario-matched questions, backfill from same domain (any scenario)
6. Shuffle final question order

### Domain Focus (/quiz --domain N)
1. Select all questions from domain N
2. Optionally filter by scenario with --scenario M
3. Default: 15 questions randomly selected
4. Override count with --count X

### Difficulty Modes
- --easy: Only Easy-tagged questions
- --hard: Only Hard-tagged questions
- --mixed (default): Proportional mix matching question bank distribution

## Scenarios Reference

| # | Scenario | Primary Domains |
|---|----------|----------------|
| 1 | Customer Support Resolution Agent | 1, 2, 5 |
| 2 | Code Generation with Claude Code | 3, 5 |
| 3 | Multi-Agent Research System | 1, 2, 5 |
| 4 | Developer Productivity with Claude | 2, 3, 1 |
| 5 | Claude Code for CI/CD | 3, 4 |
| 6 | Structured Data Extraction | 4, 5 |

## Result Persistence

Each quiz session appends one JSON line to `~/.claude/certification/results.jsonl`:

Format:
```json
{
  "date": "ISO-8601 timestamp",
  "mode": "full|domain|scenario",
  "scenarios": [1, 3, 4, 6],
  "scaled_score": 820,
  "total": 30,
  "correct": 24,
  "by_domain": {"1": {"total": 8, "correct": 6}, "2": {"total": 5, "correct": 4}},
  "by_task": {"1.1": {"total": 2, "correct": 1}, "1.2": {"total": 1, "correct": 1}},
  "wrong": ["Q1.3.2", "Q2.1.4"]
}
```

Training sessions append to `~/.claude/certification/training-progress.jsonl`:

```json
{
  "date": "ISO-8601 timestamp",
  "domain": 1,
  "task_statements": ["1.1", "1.2", "1.3"],
  "mini_quiz": {"total": 4, "correct": 3}
}
```

## Question Bank File Paths

- Domain 1: plugins/certification/resources/question-bank/1-agentic-architecture-and-orchestration.md
- Domain 2: plugins/certification/resources/question-bank/2 Tool Design & MCP Integration.md
- Domain 3: plugins/certification/resources/question-bank/3 Claude Code Configuration & Workflows.md
- Domain 4: plugins/certification/resources/question-bank/4 Prompt Engineering & Structured Output.md
- Domain 5: plugins/certification/resources/question-bank/5 Context Management & Reliability.md

## Question Format in Bank

Questions follow this pattern:
```
### Q{domain}.{task}.{number} — Scenario: {scenario name}
**Difficulty: Easy|Medium|Hard | Knowledge: {topic}**
[question text]
A) ...
B) ...
C) ...
D) ...
**Correct Answer: {letter}**
**Explanation:** ...
```
