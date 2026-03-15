---
name: study-path
description: Adaptive study recommendation engine. Analyzes quiz history to identify weak domains, task statements, and scenarios, then recommends targeted study paths. Used by /analysis and /training.
---

# Study Path Recommendations

## Analysis Inputs

Read quiz history: `~/.claude/certification/results.jsonl` (one JSON line per quiz session)
Read training history: `~/.claude/certification/training-progress.jsonl` (one JSON line per training session)

Each quiz result line contains: `session_id`, `timestamp`, `domain_scores` (map of domain → {correct, total}), `task_scores` (map of task statement → {correct, total}), `scenarios` (array of scenario IDs tested), `scaled_score`, `pass`.

Each training progress line contains: `session_id`, `timestamp`, `domain`, `task_statements` (array of task IDs covered), `mode` (study | practice | review).

## Metrics to Compute

### Per-Domain Performance

- Accuracy = domain_correct / domain_total across all sessions
- Trend = compare last 3 sessions vs all-time average (improving / plateau / regressing)
- Weighted gap = domain_weight * (1 - accuracy) — higher means more impactful to fix

Domain weights:

| Domain | Weight |
|--------|--------|
| D1: Agentic Architecture | 0.27 |
| D2: Tool Design & MCP | 0.18 |
| D3: Claude Code Config | 0.20 |
| D4: Prompt Engineering | 0.20 |
| D5: Context Management | 0.15 |

### Per-Task-Statement Performance

- Accuracy per task statement (e.g., 1.1, 1.2, 2.1, ...)
- Critical gaps: < 60% accuracy
- Needs review: 60-80% accuracy
- Strong: > 80% accuracy

Aggregate across all sessions. Require a minimum of 3 attempts on a task statement before classifying; fewer than 3 attempts counts as "insufficient data" and is treated as under-tested (priority similar to critical gaps).

### Scenario Exposure

- Count sessions per scenario (from the `scenarios` array in each result)
- Under-tested: < 2 exposures
- Adequately tested: 2-3 exposures
- Well-tested: >= 4 exposures

### Progress Over Time

- Track scaled scores session-over-session
- Improving: score increased > 20 points from previous session
- Plateau: 3+ sessions with < 20-point improvement across the span
- Regression: score dropped > 50 points from previous session

## Recommendation Algorithm

Priority order:

1. **Critical gaps first**: Task statements < 60% accuracy, ordered by parent domain weight (highest weight = highest priority). If multiple task statements share the same domain, group them.
2. **High-impact domains**: Domains with highest weighted gap score. A domain with weight 0.27 and 70% accuracy (gap = 0.081) ranks above a domain with weight 0.15 and 50% accuracy (gap = 0.075).
3. **Under-tested scenarios**: Recommend `/quiz --scenario N` for scenarios with < 2 exposures. Prioritize scenarios tied to weak domains.
4. **Insufficient-data task statements**: Task statements with < 3 attempts. Recommend targeted quizzes to gather baseline data.
5. **Plateau breaker**: If plateau detected (3+ sessions, < 20-point improvement), recommend switching study mode:
   - If mostly quizzing: suggest `/training` for the weakest domain
   - If mostly training: suggest `/quiz` to test retention
   - If mixed: suggest focusing on a single weak domain rather than broad review
6. **Ready assessment**: If ALL of the following are true, declare readiness:
   - All domains > 80% accuracy
   - Scaled score > 750 for 2+ consecutive sessions (the two most recent)
   - No task statement below 60% accuracy
   - Output: "You are likely ready for the exam. Consider one final full-length practice quiz to confirm."

## Edge Cases

### No History (neither file exists or both empty)

Output a welcome message:

```
No quiz or training history found. Recommended starting path:
1. /training 1 — Begin with Domain 1 (Agentic Architecture, 27% of exam)
2. /quiz --count 15 — Take a baseline quiz across all domains
```

Skip all metric tables. Do not show empty tables.

### Single Session Only

Show the results table but add a note:

```
Note: Only 1 session on record. Trend data requires at least 3 sessions.
Accuracy values are preliminary — take 2 more quizzes for reliable recommendations.
```

Trend column displays "—" for all domains.

### All Domains Strong but Scaled Score < 720

This indicates weakness on cross-domain or scenario-based questions rather than individual domain knowledge. Recommend:

1. `/quiz --hard` — Higher difficulty questions that combine multiple domains
2. `/quiz --scenario N` for the least-tested scenarios
3. Review training material on how domains interact (e.g., how prompt engineering affects agentic architecture)

### Training History but No Quiz History

Recommend taking a baseline quiz:

```
You have training sessions but no quiz results yet.
Take a baseline quiz to measure your current level: /quiz --count 20
```

### Very Old Data (most recent session > 30 days ago)

Add a staleness warning:

```
Your most recent session was {N} days ago. Knowledge may have decayed.
Recommend retaking a baseline quiz before following older recommendations.
```

## Output Format

### Readiness Summary

| Domain | Weight | Accuracy | Trend | Weighted Gap | Status |
|--------|--------|----------|-------|--------------|--------|
| 1. Agentic Architecture | 27% | 75% | ↑ Improving | 0.068 | Needs Work |
| 2. Tool Design & MCP | 18% | 90% | → Plateau | 0.018 | Strong |
| 3. Claude Code Config | 20% | 85% | ↑ Improving | 0.030 | Strong |
| 4. Prompt Engineering | 20% | 60% | ↓ Regressing | 0.080 | Critical |
| 5. Context Management | 15% | 70% | → Plateau | 0.045 | Needs Work |

**Estimated scaled score**: ~780 (passing is 720)
**Sessions analyzed**: 8 (most recent: 2 days ago)
**Overall trend**: Improving

### Critical Task Statements

| Task | Domain | Accuracy | Attempts | Priority |
|------|--------|----------|----------|----------|
| 4.3 | Prompt Engineering | 40% | 10 | Highest |
| 1.3 | Agentic Architecture | 55% | 8 | High |
| 5.2 | Context Management | 58% | 6 | Medium |

### Scenario Coverage

| Scenario | Exposures | Status |
|----------|-----------|--------|
| 6. Structured Data Extraction | 1 | Under-tested |
| 8. Multi-Agent Pipeline | 0 | Not tested |
| 3. MCP Server Development | 4 | Well-tested |

### Priority Study Path

1. **Focus on Domain 4, Task 4.3** (structured output via tool_use) — 40% accuracy, highest weighted gap
2. **Review Domain 1, Task 1.3** (subagent context passing) — 55% accuracy
3. **Practice Scenario 6** (Structured Data Extraction) — only 1 exposure
4. **Get baseline on Scenario 8** (Multi-Agent Pipeline) — never tested

### Recommended Next Actions

- `/training 4` — Study Domain 4 with focus on task statements 4.3 and 4.5
- `/quiz --scenario 6 --count 10` — Get more exposure to Scenario 6
- `/quiz --domain 1 --hard` — Test Domain 1 at higher difficulty
- `/quiz --scenario 8 --count 5` — Establish baseline on untested scenario
